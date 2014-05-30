{
  "title": "Colosimo: Chicago Boss, PostgreSQL, and bcrypt",
  "description": "Colosimo: Chicago Boss, PostgreSQL, and bcrypt",
  "date": "2012-06-10",
  "url": "colosimo-chicago-boss-postgresql-and-bcrypt",
  "type": "post",
  "tags": [
    "postgresql",
    "erlang",
    "chicago boss"
  ]
}
If you are interested in Chicago Boss and PostgreSQL (then hopefully you'll like this!), and you have not reviewed it already, go check out [An Evening With Chicago Boss](https://github.com/evanmiller/ChicagoBoss/wiki/An-Evening-With-Chicago-Boss), I'm going to borrow a good deal from that example.

The items I want to cover that enhance the example in An Evening with Chicago Boss are:

*   PostgreSQL DB shard for content
*   Static content: CSS, images, javascript
*   More in-depth Django templates with extensions and blocks
*   Bcrypt for password storage
*   Front-end user registration

The source for Colosimo is available on [github](https://github.com/imperialwicket/colosimo); I did not include boss.config, logs, or ebin. If you clone the repo, add a boss.config with appropriate credentials (my boss.config is below for reference) and make sure you run "./rebar compile", or start it in dev mode.

### Install Chicago Boss and create our project

I documented the effort for [installing Chicago Boss on Amazon Linux](http://imperialwicket.com/aws-install-chicago-boss-on-amazon-linux), and there are good instructions on the Chicago Boss wiki. I'm going to skip the actual installation procedures and go right to project creation. Feel free to comment here, or hit the [Chicago Boss mailing list](http://groups.google.com/group/chicagoboss) if you encounter issues with installation.

Related to installation, I want to note that I'm going to show the colosimo/boss.config file with my Chicago Boss, cb_admin, and colosimo projects all in my home directory. If you install these in unique locations, some path updates will obviously be necessary.

After installing Chicago Boss (not necessary if you clone the github repo):
<pre>
cd ~/ChicagoBoss/
make app PROJECT=colosimo
</pre>

### boss.config: DB shards, models, and cb_admin

Here is a boss.config file that supports cb_admin, uses Mnesia for most storage, and uses PostgreSQL for our colosimo_user model. We're also using Mochiweb as our server.

<pre>
[{boss, [
    {path, "/home/imperialwicket/ChicagoBoss"},
    {vm_cookie, "abc123"},
    {applications, [cb_admin,colosimo]},
    {db_host, "localhost"},
    {db_port, 1978},
    {db_adapter, mock},
    {log_dir, "log"},
    {server, mochiweb},
    {port, 8001},
    {session_adapter, mock},
    {session_key, "_boss_session"},
    {session_exp_time, 525600},
    {db_shards, [
        [{db_host, "localhost"},
        {db_adapter, pgsql},
        {db_port, 5432},
        {db_username, "colosimo"},
        {db_password, "colosimo"},
        {db_database, "colosimo"},
        {db_shard_id, shard_pg},
        {db_shard_models, [colosimo_user]}]
    ]}
]},
{cb_admin, [
        {path, "../cb_admin"},
        {base_url, "/admin"}
]},
{colosimo, [
    {path, "../colosimo"},
    {base_url, "/"}
]}
].
</pre>

Note that if you receive this error upon running init.sh or init-dev.sh:
<pre>
./init.sh: 55: ERROR:: not found
</pre>
You probably have a syntax error in the boss.config file that resides in the same directory as the init script you executed.

### PostgreSQL database

I am using a vanilla PostgreSQL installation, and you'll notice I'm using the terribly secure user/pass of 'colosimo' and 'colosimo' for my database named 'colosimo'. 

Chicago Boss comes with a nice README_DATABASE that details the generation of your tables in PostgreSQL (and other databases), and my aim is not PostgreSQL in this article. Nonetheless, here are the statements to get you up and running with this example (assuming PostgreSQL is installed, a database is initialized, and the database server is running).

<pre>
postgres=#CREATE USER colosimo WITH PASSWORD 'colosimo';
postgres=# CREATE DATABASE colosimo WITH OWNER colosimo;
postgres=# GRANT ALL ON DATABASE colosimo TO colosimo;
postgres=# \c colosimo colosimo
postgres=# CREATE TABLE colosimo_users (id SERIAL UNIQUE PRIMARY KEY, email VARCHAR(200), username VARCHAR(24), password VARCHAR(64));
NOTICE:  CREATE TABLE will create implicit sequence "colosimo_users_id_seq" for serial column "colosimo_users.id"
NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "colosimo_users_pkey" for table "colosimo_users"
CREATE TABLE
</pre>

### Static content (html5-boilerplate)

I am using [html5 boilerplate](http://html5boilerplate.com/) as a starter for my template. All you need to do is extract the boilerplate files to the ~/colosimo/priv/ directory, and then update the relative paths to map to "/<file>" instead of just "/<file>". To fill out the templating effort, I removed a bunch of items that were unnecessary for this project, and added a couple template blocks to the index file. I also made some basic changes to the css for things like inline display of the nav li.

In order to use the index.html file as the base for my templates I moved it from the /priv/ directory to the colosimo view directory (and renamed it to 'base.html'):
<pre>
mv ~/colosimo/priv/index.html ~/colosimo/src/view/base.html
</pre>

### Template extensions and blocks

Templates are Django-style, and as mentioned I am using the 'base.html' as a base template for colosimo. You can review any of the views to see the 'extends "base.html"' bit. You can review all of the views on [github](https://github.com/imperialwicket/colosimo/tree/master/src/view), or check the ~/colosimo/src/views directory if you've already cloned the repo.

Mostly, you just need to keep in mind that the template extends our base.html file and provides input for the title and content blocks.

### Routing and Controllers

Routing in Chicago Boss is likely familiar, and I'll just note here that I used the following routes to support my index page and the about page (404 and 500 are in there for good measure):

##### /priv/colosimo.routes:

<pre>
% Front page
{"/", [{controller, "main"}, {action, "index"}]}.

% About
{"/about", [{controller, "main"}, {action, "about"}]}.

% 404 File Not Found handler
{404, [{controller, "main"}, {action, "nope"}]}.

% 500 Internal Error handler (only invoked in production)
{500, [{controller, "main"}, {action, "oops"}]}.
</pre>

We will cover the user controller elsewhere, but I wanted to explicitly detail my 'main' controller here:

##### ~/colosimo/src/controller:

<pre>
-module(colosimo_main_controller, [Req]).
-compile(export_all).

before_(_) ->
    user_lib:require_login(Req).

index('GET', [], ColosimoUser) ->
  {ok, [{colosimo_user, ColosimoUser}]}.

nope('GET', []) ->
  {ok, []}.

oops('GET', []) ->
  {ok, []}.

about('GET', []) ->
  {ok, []}.
</pre>

Notice we handle the login check only on the index page.

### Install and use bcrypt for passwords

I wanted to install bcrypt at the Erlang level, not just for Chicago boss. On Debian the default location for Erlang libs is the '/usr/local/lib/erlang/lib/' directory. I am new to Erlang, and if there's a better way to accomplish this, please add it in the comments and I'll update the text accordingly. On Debian:

<pre>
sudo su -
cd /usr/local/lib/erlang/lib/
git clone https://github.com/mrinalwadhwa/erlang-bcrypt.git
cd erlang-bcrypt
make
</pre>

This puts the necessary *.beam files in your Erlang libs, so you can use bcrypt wherever you want (in Erlang).

As I mentioned, we're going to carry over a lot of the code from An Evening with Chicago Boss, but the appropriate bcrypt modifications to the user controller, the user model and user library are as follows:

##### ~/colosimo/src/controller/colosimo_user_controller.erl

Add registration to the user controller:
<pre>
-module(colosimo_user_controller, [Req]).
-compile(export_all).

login('GET', []) ->
    {ok, [{redirect, Req:header(referer)}]};

login('POST', []) ->
    Username = Req:post_param("username"),
    case boss_db:find(colosimo_user, [{username, Username}], 1) of
        [ColosimoUser] ->
            case ColosimoUser:check_password(Req:post_param("password")) of
                true ->
                   {redirect, proplists:get_value("redirect",
                       Req:post_params(), "/"), ColosimoUser:login_cookies()};
                false ->
                    {ok, [{error, "Authentication error"}]}
            end;
        [] ->
            {ok, [{error, "Authentication error"}]}
    end.

register('GET', []) ->
    {ok, []};

register('POST', []) ->
    Email = Req:post_param("email"),
    Username = Req:post_param("username"),
    Password = bcrypt:hashpw(Req:post_param("password"),bcrypt:gen_salt()),
    ColosimoUser = colosimo_user:new(id, Email, Username, Password),
    Result = ColosimoUser:save(),
    {ok, [Result]}.
</pre>

##### ~/colosimo/src/model/colosimo_user.erl

Update the model with additional fields and use bcrypt:
<pre>
-module(colosimo_user, [Id, Email, Username, Password]).
-compile(export_all).

-define(SETEC_ASTRONOMY, "Too many secrets").

session_identifier() ->
    mochihex:to_hex(erlang:md5(?SETEC_ASTRONOMY ++ Id)).

check_password(PasswordAttempt) ->
    StoredPassword = erlang:binary_to_list(Password),
    StoredPassword =:= bcrypt:hashpw(PasswordAttempt, StoredPassword).

login_cookies() ->
    [ mochiweb_cookies:cookie("user_id", Id, [{path, "/"}]),
        mochiweb_cookies:cookie("session_id", session_identifier(), [{path, "/"}]) ].
</pre>

##### ~/colosimo/src/lib/user_lib.erl

Update the user lib to use bcrypt:
<pre>
-module(user_lib).
-compile(export_all).

hash_password(Password)->
    bcrypt:hashpw(Password, bcrypt:gen_salt()).

require_login(Req) ->
    case Req:cookie("user_id") of
        undefined -> {redirect, "/user/login"};
        Id ->
            case boss_db:find(Id) of
                undefined -> {redirect, "/user/login"};
                ColosimoUser ->
                    case ColosimoUser:session_identifier() =:= Req:cookie("session_id") of
                        false -> {redirect, "/user/login"};
                        true -> {ok, ColosimoUser}
                    end
            end
     end.
</pre>

### Registration and Login

We already saw the sections of the user controller that support registration and login behavior. Here are the views that setup the POST requests for our users (sorry, I'm linking to github here, because I'm too lazy to update the blog for embedding html code).

##### [~/colosimo/src/view/user/register.html](https://github.com/imperialwicket/colosimo/blob/master/src/view/user/register.html)

##### [~/colosimo/src/view/user/login.html](https://github.com/imperialwicket/colosimo/blob/master/src/view/user/login.html)

### Colosimo

Hopefully this works for others and provides some useful examples as more people get started with Chicago Boss. Don't hesistate to comment if things don't work, or if there are any errors that should be addressed. 

Again, thanks to the Chicago Boss team for a great framework, and particularly to the team that generated, updated, and maintains the wiki page: [An Evening with Chicago Boss](https://github.com/evanmiller/ChicagoBoss/wiki/An-Evening-With-Chicago-Boss), it definitely got me started and I used a lot of the sample code in the Colosimo project.
