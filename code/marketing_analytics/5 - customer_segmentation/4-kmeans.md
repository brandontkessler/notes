# KMeans Clustering

## Preprocessing

```python
import numpy as np
datamart_log = np.log(datamart_rfm)

from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(datamart_log)

datamart_normalized = scaler.transform(datamart_log)
```

## Running KMeans

```python
from sklearn.cluster import KMeans
kmeans = KMeans(n_clusters=2)

kmeans.fit(datamart_normalized)

cluster_labels = kmeans.labels_
```

## Analyzing average RFM values of each cluster

```python
datamart_rfm_k2 = datamart_rfm.assign(Cluter = cluster_labels)

datamart_rfm_k2.groupby(['Cluster']).agg({
    'Recency': 'mean',
    'Frequency': 'mean',
    'MonetaryValue': ['mean', 'count']
}).round(0)
```

## Determine Number of Clusters

```python
from sklearn.cluster import KMeans
import seaborn as sns
import matplotlib.pyplot as plt

sse = {}
for k in range(1, 11):
    kmeans=KMeans(n_cluters=k)
    kmeans.fit(wholesale_scaled_df)
    sse[k] = kmeans.inertia_

plt.title('Elbow criterion method')
plt.xlabel('k')
plt.ylabel('SSE')
sns.pointplot(x=list(sse.keys()), y=list(sse.values()))
plt.show()

```

## Build Customer Personas

Snakeplot to gain insights on clusters

```python
# Prepare data for snake plot
datamart_normalized = pd.DataFrame(datamart_normalized,
                                   index=datamart_rfm.index,
                                   columns=datamart_rfm.columns)
datamart_normalized['Cluster'] = datamart_rfm_k3['Cluster']

datamart_melt = pd.melt(datamart_normalized.reset_index(),
                        id_vars=['CustomerID', 'Cluster'],
                        value_vars=['Recency', 'Frequency', 'MonetaryValue'],
                        var_name='Attribute',
                        value_name='Value')

plt.title('Snake plot of standardized variables')
sns.lineplot(x='Attribute', y='Value', hue='Cluster', data=datamart_melt)
plt.show()
```

Relative Importance of segment attributes

```python
cluster_avg = datamart_rfm_k3.groupby(['Cluster']).mean()
population_avg = datamart_rfm.mean()
relative_imp = cluster_avg / population_avg - 1

# Further away relative importance is from 0, the more important the
#   attribute is

relative_imp.round(2)

plt.figure(figsize=(8,2))
plt.title('Relative importance of attributes')
sns.heatmap(data=relative_imp, annot=True, fmt='.2f', cmap='RdYlGn')
plt.show()
```
