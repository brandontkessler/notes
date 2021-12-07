# Summary

```python
import pandas as pd
import numpy as np
from mlxtend.preprocessing import TransactionEncoder
from mlxtend.frequent_patterns import apriori, association_rules

itemsets = np.load('itemsets.npy')
print(itemsets) 
# This is a list of lists:
# [['banana','apple'],
# ['orange', 'apple, 'pear'],
# ['banana', 'pear']]

encoder = TransactionEncoder()
onehot = encoder.fit(itemsets).transform(itemsets)
onehot = pd.DataFrame(onehot, columns = encoder.columns_)

frequent_itemsets = apriori(onehot, use_colnames=True, min_support=0.001)
print(frequent_itemsets)

rules = association_rules(frequent_itemsets)

filtered_rules = targeted_rules[
    (targeted_rules['antecedent support'] > 0.01) &
    (targeted_rules['support'] > 0.009) &
    (targeted_rules['confidence'] > 0.85) &
    (targeted_rules['lift'] > 1)
]

# Find dissociated rules
rules['zhang'] = zhangs_rule(rules)
print(rules[rules['zhang'] < 0][['antecedents','consequents']])
```