# Market Basket

Example: 
A book store has 4 genres: Fiction, History, Biography and Poetry.
It only has two aisles so much group genres in pairs.

## What is market basket

1. Identify products frequently purchased together
   1. Biography + History
   2. Fiction + Poetry
2. Construct recommendations based on these findings
   1. Place biography and history sections together
   2. Keep fiction and history apart

## Use-cases

1. Recommendation engines
2. Cross-sell products
3. Improve inventory management
4. Upsell products

## Method

* Construct association rules
* Identify items frequently purchased together

{antecedent} -> {consequent}


## Basic code

```python
from itertools import permutations

flattened = [item for transaction in transactions for item in transaction]
items = list(set(flattened))

rules = list(permutations(items, 2))
print(rules)
```

## Metrics

**Support**: measures the share of transactions that contain an itemset.

```math
support = \dfrac{number of transactions with items}{number of transactions}
```

Ex. In ten transactions, humor appears in 2 of them. Therefore, humor has a support of 0.2.

Ex. Instead, checking the support for an association rule like {Humor} -> {Poetry}, we look at all transactions that contain both. 


## Preparing the Data

We need to start with data in a list of lists

```python
[['travel', 'humor', 'fiction'],
...
['fiction', 'biography']]
```

Once we have it in that format, we can prep:

```python
from mlxtend.preprocessing import TransactionEncoder

encoder = TransactionEncoder().fit(transactions)
onehot = encoder.transform(transactions)

onehot = pd.DataFrame(onehot, columns = encoder.columns_)
print(onehot)

import numpy as np

# Define itemset that contains fiction and poetry
onehot['fiction+poetry'] = np.logical_and(onehot['fiction'], onehot['poetry'])

print(onehot.mean())
# The column averages will give support values since all are in Bools
```