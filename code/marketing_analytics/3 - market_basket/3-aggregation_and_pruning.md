# Aggregation and Pruning

Overall, transaction datasets can be way too big to return useful rules.

Two ways of working with that are aggregation and pruning.

1. Aggregation: Groups into categories/aggregates
2. Pruning: Removes items with low support

## Aggregating

```python
onehot = pd.read_csv('datasets/online_retail.csv')

# assume we want to combine all columns containing the word bag as a 
#   column and all columns containing the word box as another column
bag_headers = [i for i in onehot.columns if i.lower().find('bag')>=0]
box_headers = [i for i in onehot.columns if i.lower().find('box')>=0]

bags = onehot[bag_headers]
boxes = onehot[box_headers]

bags = (bags.sum(axis=1) > 0).values
boxes = (boxes.sum(axis=1) > 0).values

aggregated = pd.DataFrame(np.vstack([bags, boxes]).T, columns = ['bags', 'boxes'])
```

## Apriori

```python
from mlxtend.frequent_patterns import apriori

frequent_itemsets = apriori(onehot, min_support = 0.0005,
                            max_len = 4, use_colnames = True)
print(len(frequent_itemsets))
```

## Pruning with Apriori

```python
from mlxtend.frequent_patterns import apriori, association_rules

frequent_itemsets = apriori(onehot,
                            use_colnames=True,
                            min_support=0.0001,
                            max_len = 2)

rules = association_rules(frequent_itemsets,
                          metric = 'support',
                          min_threshold = 0.0)

rules = association_rules(frequent_itemsets,
                          metric = 'support',
                          min_threshold = 0.001)

rules = association_rules(frequent_itemsets,
                          metric = 'lift',
                          min_threshold = 1)

rules = association_rules(frequent_itemsets,
                          metric = 'antecedent support',
                          min_threshold = 0.001)
```

