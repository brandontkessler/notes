# Marketing Analytics Course

## dataframe statistics

```python
df.head()
df.describe()
df.info()

# Print top five of a column
print(df['column'].value_counts().head())
```

## datatypes

```python
df.dtypes
df['converted'].dtype
df['converted'] = df['converted'].astype('bool')

# Create a new field if converted == house ads or not
df['is_house_ads'] = np.where(
    df['converted'] == 'house ads',
    True, False
)

# Store values in df as numbers and map as needed
channel_dict = {
    "House Ads": 1, "Instagram": 2, "Facebook": 3, "Email": 4, "Push": 5
}
df['channel_code'] = df['marketing_channel'].map(channel_dict)

# Dates
df = pd.read_csv('data.csv', parse_dates=['dt'])
df['dt'] = pd.to_datetime(df['dt'])
df['day_served'] = df['dt'].dt.dayofweek
```

## Exploratory Analysis

```python
import matplotlib.pyplot as plt

daily_users = df.groupby(['date_served'])['user_id'].nunique()
daily_users.plot()
plt.title('Daily number of users')
plt.xlabel('Date')
plt.ylabel('Number of users')
plt.xticks(rotation=45)
plt.show()
```

## Common Metrics

```python
subscribers = df[df['converted'] == True]['user_id'].nunique()
total = df['user_id'].nunique()
conv_rate = subscribers/total

retained = df[df['is_retained'] == True]['user_id'].nunique()
retention = retained/subscribers
```

## Customer Segmentation

```python
house_ads = df[df['subscribing_channel'] == 'House Ads']
retained = house_ads[houe_ads['is_retained'] == True]['user_id'].nunique()
subscribers = house_ads[houe_ads['converted'] == True]['user_id'].nunique()

retention_rate = retained/subscribers

# Better way
retained = df[df['is_retained'] == True].groupby(['subscribing_channel'])\
    ['user_id'].nunique()
subscribers = df[df['converted'] == True].groupby(['subscribing_channel'])\
    ['user_id'].nunique()
channel_retention_rate = (retained/subscribers)*100
```


## Plotting Campaign Results

```python
import matplotlib.pyplot as plt

conversion_rate.plot(kind = 'bar')
plt.xlabel('Language', size=14)
plt.ylabel('Conversion rate', size=14)
plt.show()


# Formatted into dataframe to be plotted
daily_retention_rate = pd.DataFrame(daily_retention_rate.reset_index())
daily_retention_rate.columns = ['date_subscribed', 'retention_rate']

daily_retention_rate.plot('date_subscribed', 'retention_rate')
# place titles and labels
plt.ylim(0)
plt.show()


# multi line plot with legend
language.plot()
# titles and labels
plt.legend(loc = 'upper right',
           labels = language.columns.values)
plt.show()


# grouped bar
language_age = marketing.groupby(['language_preferred', 
                                  'age_group'])\
                                 ['user_id'].count()
language_age = pd.DataFrame(language_age.unstack(level=1))
language_age.plot(kind='bar')
plt.legend(loc = 'upper right',
           labels = language_age.columns.values)
plt.show()
```

