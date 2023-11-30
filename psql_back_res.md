From linux server
command:
```
    pg_dump -U <username> -d <databasename> > <outputfile>
```
example:
```
    pg_dump -U font -d phddata > 19octbackup.sql
```
Noted: pg_dump create a copy sql script, which can be used to re-create a new database with similar data.


From this, we can restore our data by runing the sql backup file. Two options are available:
First option:
To restore <dump> file to fleshly created databasename
*** we cannot use retore option in PGadmin due to pg_demp was used.
Execute the command:
```
    psql -U <usernam> -d <fleshly created databasename> -f <dumpfile>
    psql -U font -d oct19backup -f 19octbackup.sql
```

Second option:
The old database can be discarded
```
    DROP DATABASE <databasename> 
    CREATE DATABASE <databasename>
    psql -f backup.sql -d <databasename>
```
Example:
```
    DROP DATABASE linuxdata 
    CREATE DATABASE linuxdata
    psql -f 19octbackup.sql -d linuxdata
```

*** psql -f >> -f means filename

for more information
pg_dump: https://www.postgresql.org/docs/current/app-pgdump.html
pg_dump example: https://www.postgresql.org/docs/current/backup-dump.html
psql: https://www.postgresql.org/docs/devel/app-psql.html
