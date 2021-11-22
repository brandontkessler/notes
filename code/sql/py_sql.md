# Using Python to connect to Postgres DB

## packages

```bash
> pip install psycopg2-binary
> pip install pandas
```

## Connecting to the Database

```python
import psycopg2 as db

conn = db.connect(
  user = "my_user_name",
  password = "my_password",
  dbname = 'my_database_name',
  host = 'my_host_name',
  port = 5439
)

query = """
  select * from some_table
  order by name
  limit 50;
"""
```

## Querying the Database

Using a psycopg2 cursor to execute the query:

```python
cur = conn.cursor()

cur.execute(query)

for record in cur:
  print(record)
```

Alternatively, using pandas:

```python
import pandas as pd

df = pd.read_sql_query(query, conn)

print(df.columns)
```
