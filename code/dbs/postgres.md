# venv package needs

```bash
>>> pip install pandas
>>> pip install psycopg2-binary
```

# setup

```python
import pandas as pd
import psycopg2
import io

class Postgres:
    def __init__(self, db_name, host, port, username, password):
        self.dbname = db_name
        self.host = host
        self.port = port
        self.username = username
        self.password = password

    def connect(self):
        conn = psycopg2.connect(
            dbname = self.dbname, 
            host = self.host, 
            port = self.port, 
            user = self.username, 
            password = self.password
        )
        return conn, conn.cursor()

    def query(self, query):
        conn, cur = self.connect()

        cur.execute(query)

        colnames = [desc[0] for desc in cur.description]
        results = pd.DataFrame(data=cur.fetchall(), columns=colnames)

        conn.close()
    
        return results

    def insert_df_to_postgres(self, df, schema):
        conn, cur = self.connect()

        output = io.StringIO()
        df.to_csv(output, header=False, index=False)
        output.seek(0)
        cur.copy_from(output, schema, null="", sep=',')

        conn.commit()
        conn.close()
        print('successfully inserted data')

        return

if __name__=='__main__':
    import seaborn as sns
    import ssl

    # using unverified ssl is dangerous for requests but for this case is ok
    ssl._create_default_https_context = ssl._create_unverified_context

    iris = sns.load_dataset('iris')

    db = Postgres(
        db_name='postgres', 
        host='localhost', 
        port=5432, 
        username='postgres', 
        password='postgres'
    )

    db.insert_df_to_postgres(iris, 'iris')

    query = """
        select species, avg(sepal_length)
        from iris
        group by 1
        order by 1 desc;
    """

    df = db.query(query)
    print(df)
```