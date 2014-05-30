{
  "title": "PostgreSQL: Automating monthly table partitions",
  "description": "PostgreSQL: Automating monthly table partitions",
  "date": "2010-12-29",
  "url": "postgresql-automating-monthly-table-partitions",
  "type": "post",
  "tags": [
    "postgresql"
  ]
}
Table partitioning in PostgreSQL is a great way to speed up queries that require particular portions of very large data sets.  The standard example is business intelligence reporting.  Lots of data goes into the database, but generally your queries only work with one month's data.  As the data set grows, you want to avoid the indefinite lengthening of your query times for reporting.  By using table partitioning and PostgreSQL's constraint_exclusion, you can be sure that your queries are only scanning the data that is appropriate.

The concept for table partitions is to make one parent table, and a number (probably ever-growing) of child tables that actually hold your data.  There are plenty of tutorials out there for creating these tables, indexes, functions, and triggers, so I will not go into great detail on how/why partitioning works, but I will offer a quick synopsis.  

Child tables are created with CHECK constraints that ensure only particular data is stored in each Child table.  In the reporting example, the Parent and Child tables would have a CREATE statements as below.

After creating the tables, create a function that returns a trigger.  The function simply redirects the values to the appropriate child table (usually, you can do more if necessary).  Finally, create the trigger to use the function whenever a row is inserted in the parent table.  This looks something like:

<script src="https://gist.github.com/2720074.js?file=setup.sql"></script>

This is not terribly difficult.  Now, when an insert occurs on parent, it will automatically get passed through to the appropriate child table.  If it is outside of the scope of the existing child table(s), an exception is raised in the previous sample.  Note that if you are using PostgreSQL older than 8.4, you will need to manually turn on "constraint_exclusion" in your postgresql.conf file.  Otherwise, all of your efforts to optimize will be in vain.  8.4 and newer changes "constraint_exclusion" from a bool to an enum, and defaults it to "partition", which is sufficient for optimizing queries against parent/child partitioned table schemes.

The automation problem is that putting this together over and over again is a giant pain.  Not to mention the fact that when I set out to find a script/function/procedure that could accomplish it for me, I did not uncover much.  So, when I put this together, I decided to make it as generic as possible, so that I could reuse it for all of my table partitioning needs.  As I was dusting it off recently to put together a test database with massive partitioning, I decided to clean it up and post it here.  

### update_partitions(begin_time, schema_name, primary_table_name, table_owner, date_column)

Thanks to Javier for adding support for multiple intervals, and not just monthly partitions:

<script src="https://gist.github.com/2720074.js?file=update_partitions.sql"></script>

### Notes:

*   The function is created in the public schema and is owned by user postgres.
*   The function takes params:

        *   begin_time - time of your earliest month's data.  This allows for backfilling and for reducing trigger function overhead by avoiding legacy date logic.
    *   schema_name - name of the schema that contains the parent table.  Child tables are created here.
    *   primary_table_name - name of the parent table.  This is used to generate monthly tables ([primary_table_name]_YYYYMM) and an unknown table ([primary_table_name]_unknowns).  It is also used in the trigger and trigger function names.
    *   table_owner - name of PostgreSQL Role to be assigned as owner of the child tables.
    *   date_column - name of the timestamp/date column that is used for check constraints and insert trigger function.

*   The insert trigger function is recreated everytime you run.
*   If tables already exist, the function simply updates the trigger function and moves to the next table in the series.*   This function does not raise exceptions when errant data is encountered.  Any data that does not have a matching child table is stored in the unknowns table.
*   The function returns the number of tables that it created.

### Sample Usage:

Backfill to start a group of child tables for optimizing an existing data set.  Creates monthly tables that inherit the 'partition_test' table and are named 'partition_test_200908', 'partition_test_200909', ... , 'partition_test_201101' (given that today's date is 2010-12-29).  Check constraints are generated for the child tables, a trigger insert function is created, and a trigger is applied to the parent table.  
<pre>
SELECT public.update_partitions('2009-08-01'::timestamp,'public','partition_test','postgres','date_column');
</pre>
If you have a large number of tables (dating back to 200908, for example), but you only insert current data, you may want to backfill and then immediately run an update_partitions using a more current begin_date.  This will reduce execution time for your trigger insert function.  It will not delete older tables, and since no older data is being inserted, you need not worry about inconsistency between your trigger insert function and the various child table's check constraints.  This will update your trigger function to check for dates in 201011, 201012, and 201101; instead of checking for the entire span of months as the previous example would.  
<pre>
SELECT public.update_partitions('2010-11-01'::timestamp,'public','partition_test','postgres','date_column');
</pre>

Add a crontab entry that calls this function once a month, using either the origin date (if your usage requires the ability to enter legacy data at any time) or using this month/last month as a begin_time (if legacy data is never entered into these tables).  

### Some Simple Hacks:

*   This could pretty easily be modified to use non-date constraints.
*   I intentionally avoided any table dropping efforts, but dropping tables with constraints before the begin_time might be useful for your data.
*   I prefer to update periodically, but if you have a discrete interval, an end_time could replace the currentMonth+1 value.
*   Adding primary keys.  I almost added a default 'id' primary key addition, but since this isn't universal, I left the primary keys out.  You will _probably_ want to add a primary key to your child tables.
*   and...?

I'm sure there are some more elegant ways to accomplish some of these actions.  Nonetheless, this works for me, and I hope it saves you some time.  If anyone has suggestions for updates or alternatives, don't hesitate to comment.
