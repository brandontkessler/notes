# AB Test

## Test allocation

```python
email = df[df['channel'] == 'Email']
allocation = email.groupby(['variant'])['user_id'].nunique()
allocation.plot(kind='bar')
plt.show()

# using max to identify bool = True
subs = email.groupby(['user_id', 'variant'])['converted'].max()
subs = pd.DataFrame(subs.unstack(level=1))

control = subs['control'].dropna()

personalization = subs['personalization'].dropna()
```

## Lift and Stat Sig

```python
# Lift = (treatment conv rate - control conv rate) / control conv rate
# mean of True and False is = count(True) / count(Total)
a_mean = np.mean(control)
b_mean = np.mean(personalization)

lift = (b_mean-a_mean)/a_mean
print("lift: ", str(round(lift*100, 2)) + '%')
# lift: 194.23% - we should keep doing personalization


# 2 sample t test
# - determine likelihood that the variation between the two samples
#   occurs by chance
# - T-stat > |1.96| is statistically significant at 95% level
from scipy.stats import ttest_ind
t = ttest_ind(control, personalized)
print(t)


# Segmentation within AB tests
for lang in np.unique(df['languages_displayed'].values):
    lang_data = df[(df['channel'] == 'Email') & 
                   (df['languages_displayed'] == lang)]
    
    subs = lang_data.groupby(['user_id', 'variant'])['converted'].max()
    subs = pd.DataFrame(subs.unstack(level=1))
    control = subs['control'].dropna()
    personalization = subs['personalization'].dropna()

    print('lift: ', lift(control, personalization))
    print('t-statistic: ' stats.ttest_ind(control, personalization))
```