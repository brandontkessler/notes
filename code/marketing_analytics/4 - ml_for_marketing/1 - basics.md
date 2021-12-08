# Basics

## Preprocessing

```python
# Identify target variable and identifiers
custid = ['customerID']
target = ['Churn']

# Separate categorical and numerical columns
#   The 10 is arbitrary as part of categorical so may need to adapt
#   based on whatever dataset
#   This is basically stating, give me the column names where there 
#   are less than 10 unique values. If there are more, it must not
#   be categorical and instead is numerical.
categorical = raw_df.nunique()[raw_df.nunique()<10].keys().tolist()
categorical.remove(target[0])
numerical = [col for col in raw_df.columns if col not in custid+target+categorical]

# Encode categorical
raw_df = pd.get_dummies(data=raw_df, columns=categorical, drop_first=True)

# Scale numerical
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaled_numerical = scaler.fit_transform(raw_df[numerical])
scaled_numerical = pd.DataFrame(scaled_numerical, columns=numerical)

# Drop non-scaled numerical from raw
raw_df = raw_df.drop(columns=numerical, axis=1)

# Merge the non-numerical with the scaled numerical data
data = raw_df.merge(right=scaled_numerical,
                    how='left',
                    left_index=True,
                    right_index=True)
```

## Supervised Learning Steps

1. Split data into training and testing
2. Initialize the model
3. Fit the model on training data
4. Predict values on testing data
5. Measure model performance on testing data

```python
from sklearn import tree
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

train_X, test_X, train_Y, test_Y = train_test_split(X, Y, test_size=0.25)
mytree = tree.DecisionTreeClassifier()
treemodel = mytree.fit(train_X, train_Y)
pred_Y = treemodel.predict(test_X)
accuracy_score(test_Y, pred_Y)
```

## Unsupervised Learning Steps

1. Initialize model
2. Fit model
3. Assign cluster values
4. Explore results

```python
from sklearn.cluster import KMeans
import pandas as pd

kmeans = KMeans(n_clusters=3)
kmeans.fit(data)
data.assign(Cluster=kmeans.labels_)
data.groupby('Cluster').mean()
```

