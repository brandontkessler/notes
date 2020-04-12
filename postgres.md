
## Setup
**Linux:**
```
$ sudo apt-get install postgresql
$ sudo passwd postgres
$ su - postgres
$ psql
```

Note: su - postgres requires the making of a password

If not working:
```
$ su - 		OR 		$ su - <root username>
$ sudo service postgresql start
$ su - postgres
$ psql
```

**Mac:**
```
$ brew install postgresql
$ pg_ctl -D /usr/local/var/postgres start
$ psql postgres
```

Stop the server:
```
$ pg_ctl -D /usr/local/var/postgres stop
```

## Logging In
```
$ psql -U dev -d dev
```
Logs in as user dev connected to the database called dev


## Commands
```
# create role dev with login password ‘dev’;
```
Creates a new user named dev with the password of dev.

```
# alter role dev createdb;
```
Alters the dev role to allow the user to create databases

```
# create database development;
```
Creates a database named development

```
# \c development
```
Changes to database named development (if connected to a different db)

```
# \dt —> view tables
# \l —> view databases
# \du —> view users
```
