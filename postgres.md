<div align='center'>
    <h1>PostgreSQL</h1>
</div>

#### Run docker instance

```bash
docker run --name db-postgres-tut 
           -p 5432:5432 
           -e POSTGRES_PASSWORD=mypasswd 
           -d postgres
# inside instance
docker exec -it db-postgres-tut bash
```

#### Run psql

```bash
psql -U postgres
psql -d <database_name> -U <username>
```

#### Basic Command

```sql
\l --> show all database
\c postgres --> connect to database
\dt --> show tables
\q --> quit
```

#### Create or Drop Database

```sql
CREATE DATABASE <database_name>;
DROP DATABASE <database_name>;
```


