# Visualizations

## Heatmap

```python

# This is only needed if multiple items can be antecedents or consequents
rules['antecedents'] = rules['antecedents'].apply(lambda x: ','.join(list(x)))
rules['consequents'] = rules['consequents'].apply(lambda x: ','.join(list(x)))


support_table = rules.pivot(index='consequents', columns='antecedents',
                            values='support')

sns.heatmap(support_table, cbar=False, cmap='ocean')
plt.yticks(rotation=0)
plt.show()
```

## Scatterplot

```python
import pandas as pd
import seaborn as sns
from mlxtend.frequent_patterns import association_rules, apriori

onehot = pd.read_csv(...)
frequent_itemsets = apriori(onehot, ...)
rules = association_rules(frequent_itemsets, ...)

sns.scatterplot(x='antecedent support', 
                y='consequent support', 
                size='lift', 
                data=rules)
```

## Parallel coordinates plot

```python
import pandas as pd
import seaborn as sns
from mlxtend.frequent_patterns import association_rules, apriori

onehot = pd.read_csv(...)
frequent_itemsets = apriori(onehot, ...)
rules = association_rules(frequent_itemsets, ...)

# convert rules to coordinates
rules['antecedents'] = rules['antecedents'].apply(lambda x: list(x)[0])
rules['consequents'] = rules['consequents'].apply(lambda x: list(x)[0])
rules['rule'] = rules.index

# define coordinates and label
coords = rules[['antecedent', 'consequent', 'rule']]

from pandas.plotting import parralel_coordinates
parallel_coordinates(coords, 'rule', colormap = 'ocean')
```