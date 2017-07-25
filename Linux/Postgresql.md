# PostgreSQL
PostgreSQL is the worlds most advanced relational database, they say. In recent years support for features like hstore and JSONB
has made PostgreSQL a great choice if you need to really learn a database system. 

Best of both worlds?

This file attempts to document my personal PostgreSQL adventure. From a complete newbie who has dabbled in MySQL to
Asian-level dba skills.

## Installing PostgreSQL
Meh. You might want to skip this part.

### Mac OS X
If you are using OS X, Postgres.app is probably the quickest solution to get up and running.

* http://postgresapp.com/

### CentOS 7.0
Check out Digital Ocean's nice guide on setting up PostgreSQL on CentOS 7

* https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-centos-7

## The psql command
The psql command is used to interact with the PostgreSQL database from the commandline. The rest of this document
assumes that you are comfortable with the command line.

*This is the point where you get off if you don't like commandlines.*

You can usually get a psql shell by issuing `psql` from your favorite shell. If you're on OS X,
you might want to create a symlink to the psql binary somewhere in your path. In my case ~/bin

```bash
ln -s /Applications/Postgres.app/Contents/Versions/9.5/bin/psql ~/bin/psql
```

`psql` looks for various environment variables when it launches. Most of these are unknown to me at this point, 
and they are mostly uninteresting. The PGUSER variable can be set to let psql know which user you wish to connect as.

Selecting the user can also easily be done with the -U switch, you will be doing this a lot, probably.

```bash
psql -U postgres
```

Many non-SQL commands start with a \ (backslash) prefix. Heres a list of a few you should remember

| Command	| Action			|
| ------------- | ----------------------------- |
| \l		| List databases		|
| \c dbname	| Connect to db (USE DATABASE on roids)	|
| \dt		| Show tables when db used	|
| \du		| Show users (Roles)		|
| \i file.sql	| Execute file			|
| \! cmd	| Execute shell command		|
| \?		| Show help			|
| \q		| Quit psql			|

### Execute shell command
A really useful feature in psql is the ability to run shell commands
from within the psql shell. This is done by prefixing \!

For example:

```bash
postgres=# \! for i in $(seq 1 3); do echo $i; done
1
2
3
```

## Access Control
*Users and groups are both represented by `Roles` in postgres.*

\du lists current users and/or roles

### Creating a Super User (Super Role?)
Normally this is done with the `createuser` command, which is a normal
shell command, which may or may not be available in your PATH. 
Once again, if you're using Postgres.app, the createuser should
be available in:

*/Applications/Postgres.app/Contents/versions/9.5/bin/createuser*

Also note that the -U in this case is for specifying the user
we are going to use in order to create the new super user. This user
needs to be a super user.

```bash
createuser -e -s -U postgres newsuper
```

### Creating a super user with SQL from within psql
```sql
CREATE ROLE newsuper SUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;
CREATE DATABASE newsuper;
\password newsuper
```

*Important*
We create the database `newsuper` since the psql tool automatically
attempts to connect to a database with the same name as the user 
we are connecting with. You may skip the database creation and instead
speciy an alternate database, for example the old postgres database.

```bash
psql -U newsuper postgres
postgres=#
```

At this point you may notice that the prompt actually shows what
database you are connected to, and not the current user.

To check which user you are currently logged in as:

```sql
SELECT current_user;
```

### Creating a user and database for a project
A user and a database is what you need for just about every project.
Luckily its easy:

```sql
CREATE USER alpha WITH PASSWORD 'MyUser911';
CREATE DATABASE alpha OWNER alpha;
```

For production you might look into restricing the privileges of the user. 
Your client might not need the ability to DROP
DATABASES for example

## Connecting to databases
### Go
```go
package main

import (
	"database/sql"
	"log"

	_ "github.com/bmizerany/pq"
)

func main() {
	db, err := sql.Open("postgres", "dbname=mydb user=myuser password=mypass port=5432 sslmode=disable")
	defer db.Close()
	if err != nil {
		log.Panic(err)
	}

	err = db.Ping()
	if err != nil {
		log.Panic(err)
	}

	log.Println("Connected!")
}
```
### PHP
```php
$c = pg_connect('port=5432 dbname=test_db user=test_user password=test_password');
echo 'Connected to ' . pg_dbname($c);
```

## Using JSONB
In recent years PostgreSQL has added support for JSON and JSONB 
data types. The JSONB data type allows you to store JSON data in
a field and have PostgreSQL actually interpret and understand the data.
This allows you to do queries directly on the json data in the field

This means that PostgreSQL can be used as a Document Database. 

### Using JSONB
First you need to create a table with a field that is of the JSONB type.
This is luckily streight forward:
```sql
CREATE DATABASE books (
	id serial primary key,
	data json
)
```

You can insert data into the table normally.
```sql
INSERT INTO books(data) VALUES('{"name": "Foo Bar"}');
```

And to probe the actual json data, you use something similar to SQL, probably best described by an example:

```sql
SELECT id, data->>'name' as name from books;
```

Have a look at some more examples:

```sql
sample_db=# select id, data->>'name' as name, data->'author'->>'first_name' from books;
 id |           name            |  ?column?   
----+---------------------------+-------------
  1 | Dune                      | Frank
  2 | Seveneves                 | Neal
  3 | Enders Game               | Orson Scott
  4 | Foundation                | Isaac
  5 | The Left Hand of Darkness | Ursula K
  6 | Ringworld                 | Larry
  7 | The Book on Gayness       | Lasse
(7 rows)

sample_db=# select id, data->>'name' as name, data->'author'->>'first_name' as First from books;
 id |           name            |    first    
----+---------------------------+-------------
  1 | Dune                      | Frank
  2 | Seveneves                 | Neal
  3 | Enders Game               | Orson Scott
  4 | Foundation                | Isaac
  5 | The Left Hand of Darkness | Ursula K
  6 | Ringworld                 | Larry
  7 | The Book on Gayness       | Lasse
(7 rows)

sample_db=# select id, data->>'name' as name, data->'author'->>'first_name' as First, data->'author'->>'last_name' as last from books;
 id |           name            |    first    |    last    
----+---------------------------+-------------+------------
  1 | Dune                      | Frank       | Herbert
  2 | Seveneves                 | Neal        | Stephenson
  3 | Enders Game               | Orson Scott | Card
  4 | Foundation                | Isaac       | Asimov
  5 | The Left Hand of Darkness | Ursula K    | Lee Guin
  6 | Ringworld                 | Larry       | Niven
  7 | The Book on Gayness       | Lasse       | Haugen
(7 rows)
```

*Note the use of -> and ->> arrows*

## Cheatsheet

### Create user
```sql
CREATE USER username WITH PASSWORD 'password';
```

### Remove user
```sql
DROP USER username;
```

### Create database
```sql
CREATE DATABASE dbname;
```

### Remove database
```sql
DROP DATABASE dbname;
```

### Create table
```sql
CREATE TABLE tablename(
	field_name datatype,
	field_name datatype
);
```

### Remove table
```sql
DROP TABLE tablename;
```

### Change table owner
```sql
ALTER TABLE table_name OWNER TO user_name;
```

### Change database owner
```sql
ALTER DATABASE dbname OWNER TO new_owner_username;
```

## Resources
### Go specific
* http://go-database-sql.org/retrieving.html

