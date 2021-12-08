# Preprocessing for KMeans

* One of most popular unsupervised learning method
* Simple and fast
* Works well with correct assumptions

Sequencing matters in the case of KMeans due to log transformation only working on positive data. Scaling data will force negatives to exist so log transform has to come first.

1. Unskew the data - log transform
2. Standardize to the same average values
3. Scale to the same standard deviation
4. Store as a separate array to be used for clustering

## Key Assumptions

* Symmetric distribution of variables (not skewed)
* Variables have same average values
* Variables have same variance

## Managing Skewed Variables

* Log Transform - only works on positive values
  * Dealing w/ Negative Values - apply constant before log transform
* BoxCox

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.distplot(datamart['Recency'])
plt.show()

import numpy as np
frequency_log = np.log(datamart['Frequency'])
sns.distplot(frequency_log)
plt.show()
```

## Centering and Scaling Variables

* Given variables all have different means, we have to center them
* Given variabls have different variances, we have to scale them

```python
datamart_rfm.describe()

## Manual method ##
datamart_centered = datamart_rfm - datamart_rfm.mean()
datamart_centered.describe().round(2)

datamart_scaled = datamart_rfm / datamart_rfm.std()
datamart_scaled.describe().round(2)

# combine the above:
datamart_normalized = (datamart_rfm / datamart_rfm.mean()) / datamart_rfm.std()


## Better method ##
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(datamart_rfm)
datamart_normalized = scaler.transform(datamart_rfm)
datamart_normalized = pd.DataFrame(datamart_normalized, 
                                   index=datamart_rfm.index, 
                                   columns=datamart_rfm.columns)

```


