# Using hardcoded data in sql tables

## Postgres

```sql
SELECT * FROM (VALUES (1), (2)) AS q (col1);
```

This doesn't work in older versions of postgres

Alternative solution:

```sql
DROP TABLE IF EXISTS hardcoded_data;

CREATE TEMP TABLE hardcoded_data (
    random_nums int,
    random_strings varchar(25)
);

INSERT INTO hardcoded_data
    (random_nums, random_strings)
VALUES
    (12, 'hello'),
    (2, 'hardcoded'),
    (6, 'sample data');

SELECT  * FROM hardcoded_data;
```


