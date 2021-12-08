# Recency, Frequency, Monetary (RFM) Segmentation

## Calculating Percentiles

```python
quartiles = pd.qcut(data['Spend'], q=4, labels=range(1,5))

# Reverse the labels since lower recency is better
quartiles = pd.qcut(data['Recency'], q=4, labels=list(range(4,0,-1)))
```

## RFM Data Prep

```python
snapshot_date = max(online.InvoiceDate) + datetime.timedelta(days=1)

datamart = online.groupby(['CustomerID']).agg({
    'InvoiceDate': lambda x: (snapshot_date - x.max()).days,
    'InvoiceNo': 'count',
    'TotalSum': 'sum'
})

datamart.rename(columns = {
    'InvoiceDate': 'Recency',
    'InvoiceNo': 'Frequency',
    'TotalSum': 'MonetaryValue'
}, inplace = True)
```

## RFM Segments

```python
r_labels = range(4,0,-1)
r_quartiles = pd.qcut(datamart['Recency'], 4, labels=r_labels)
datamart = datamart.assign(R = r_quartiles.values)

f_labels = range(1,5)
m_labels = range(1,5)

f_quartiles = pd.qcut(datamart['Frequency'], 4, labels=f_labels)
m_quartiles = pd.qcut(datamart['MonetaryValue'], 4, labels=m_labels)

datamart = datamart.assign(F = f_quartiles.values, 
                           M = m_quartiles.values)

def join_rfm(x): return str(x['R']) + str(x['F']) + str(x['M'])
datamart['RFM_Segment'] = datamart.apply(join_rfm, axis=1)
datamart['RFM_Score'] = datamart[['R', 'F', 'M']].sum(axis=1)
```

## Analyzing segments

```python
datamart.groupby('RFM_Segment').size().sort_values(ascending=False)[:10]
datamart[datamart['RFM_Segment'] == '111'][:5]

datamart.groupby('RFM_Score').agg({
    'Recency': 'mean',
    'Frequency': 'mean',
    'MonetaryValue': ['mean', 'count']
}).round(1)

def segment_me(df):
    if df['RFM_Score'] >= 9: 
        return 'Gold'
    elif (df['RFM_Score'] >= 5) and (df['RFM_Score'] < 9):
        return 'Silver'
    else:
        return 'Bronze'

datamart['General_Segment'] = datamart.apply(segment_me, axis=1)

datamart.groupby('General_Segment').agg({
    'Recency': 'mean',
    'Frequency': 'mean',
    'MonetaryValue': ['mean', 'count']
}).round(1)
```