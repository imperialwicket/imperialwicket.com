{
  "title": "PostgreSQL: Getting now() in functions",
  "description": "PostgreSQL: Getting now() in functions",
  "date": "2011-01-03",
  "url": "postgresql-getting-now-in-functions/",
  "type": "post",
  "tags": [
    "postgresql"
  ]
}
I come from a world where databases are less SQL standards compliant.  So as I was getting into PostgreSQL, I remember spending a lot of time learning about time.  One of these time-consuming time investigations was troubleshooting my functions to figure out why I couldn't seem to get more than one current timestamp value.  I recently made some updates to a few procedures with logging, and could not remember the preferred way to accomplish current timestamps, so I went digging again.  Since it always takes me more than a few moments to find this information, I thought I would post it here.  This way I'll remember it, and hopefully it will save some people the agony of searching the docs for a simple function or two.

#### clock_timestamp() and timeofday()

The short answer is: you probably want to remove "now()" or "CURRENT_TIMESTAMP", and replace it with "clock_timestamp()" or "timeofday()".  

The reference for this information is available in the docs  (of course, rtfm) here:  [PostgreSQL 9.0.2 Documentation: Date/Time Functions and Operators](http://www.postgresql.org/docs/9.0/interactive/functions-datetime.html#FUNCTIONS-DATETIME-CURRENT).  

Before I highlight some bits from the documenation, here is an example of our "problem".

#### MySQL current_timestamp behavior

In MySQL - generate a function that depends on time progressing as it runs, notice that the current timestamps for ABC and DEF differ by approximately 10 seconds after running this function.
<pre>
DELIMITER $$

DROP PROCEDURE IF EXISTS test.testCurrentTimestamp$$
CREATE PROCEDURE test.testCurrentTimestamp()
BEGIN

  CREATE TABLE testTimestamp (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(64),
    a_timestamp TIMESTAMP, 
    PRIMARY KEY (id)
  );

  INSERT INTO testTimestamp (name, a_timestamp) 
    VALUES ('CURRENT_TIMESTAMP A',CURRENT_TIMESTAMP),
            ('CURRENT_TIMESTAMP B',CURRENT_TIMESTAMP),
            ('CURRENT_TIMESTAMP C',CURRENT_TIMESTAMP);
  SELECT SLEEP(10);
  INSERT INTO testTimestamp (name, a_timestamp) 
    VALUES ('CURRENT_TIMESTAMP D',CURRENT_TIMESTAMP),
            ('CURRENT_TIMESTAMP E',CURRENT_TIMESTAMP),
            ('CURRENT_TIMESTAMP F',CURRENT_TIMESTAMP);

END$$

DELIMITER ;

CALL test.testCurremtTimestamp();

SELECT * FROM test.testTimestamp;
</pre>

#### PostgreSQL current_timestamp behavior

In PostgreSQL - generating an equivalent function for our tests results in something like the following.  Notice that CURRENT_TIMESTAMP remains consistent with the timestamp at the beginning of the transaction (In MySQL you would persist this value by storing it manually):
<pre>
-- Function: testCurrentTimestamp()

-- DROP FUNCTION testCurrentTimestamp();

CREATE OR REPLACE FUNCTION testCurrentTimestamp()
  RETURNS VOID AS 

$BODY$    
BEGIN

 RAISE NOTICE 'CURRENT_TIMESTAMP: %', CURRENT_TIMESTAMP;
 RAISE NOTICE 'CLOCK_TIMESTAMP(): %', CLOCK_TIMESTAMP();
 PERFORM PG_SLEEP(10);
 RAISE NOTICE 'CURRENT_TIMESTAMP: %', CURRENT_TIMESTAMP;
 RAISE NOTICE 'CLOCK_TIMESTAMP(): %', CLOCK_TIMESTAMP();
 PERFORM PG_SLEEP(10);
 RAISE NOTICE 'CURRENT_TIMESTAMP: %', CURRENT_TIMESTAMP;
 RAISE NOTICE 'CLOCK_TIMESTAMP(): %', CLOCK_TIMESTAMP();

END;

$BODY$
LANGUAGE 'plpgsql';

SELECT testCurrentTimestamp();
</pre>

In many cases it won't matter, because these efforts will generally be focused on logging and documentation, but it's worth noting that "timeofday()" will return you the same time as "clock_timestamp()".  However, "timeofday()" returns the value as text, while "clock_timestamp()" returns a timestamp.

So, the basic rundown is:

*   transaction_timestamp() == CURRENT_TIMESTAMP == now()
*   clock_timestamp()::TEXT == timeofday()
*   clock_timestamp() == timeofday()::TIMESTAMP
