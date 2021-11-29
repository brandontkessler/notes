## File Types

Overall, columnar storage is preferred to row storage because compression can be done to each column. Row storage will have multiple data types per row making this not possible.

* **CSV**: Row Storage
* **Parquet**: Columnar Storage -- Better for long term storage - more expensive to write with more encoding and compression but smaller file size than feather.
* **Feather**: Columnar Storage -- Better for temp storage - unmodified raw columnar storage.

**SUMMARY: Parquet is best for size/storage, feather is best for speed, CSV is never best.**

---

For parquet and feather files, the `pyarrow` package is preferred with Pandas. 

Alternatively for parquet, `fastparquet` is apparently smaller overall in size and so would be better in a situation like a Lambda function on AWS.

For general usecase, `pip install pyarrow` will satisfy all code below.


---

### Code

```bash
> pip install pandas pyarrow
```

Assuming a folder in root called data and a file inside of that called prices.csv.

```python
import pandas as pd

df = pd.read_csv('data/prices.csv')

# parquet
df.to_parquet('data/prices.parq')
df_parq = pd.read_parquet('data/prices.parq')

# feather
df.to_feather('data/prices.feather')
df_feather = pd.read_feather('data/prices.feather')


```