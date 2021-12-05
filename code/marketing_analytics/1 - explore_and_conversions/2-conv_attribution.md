# Conversion Attribution

## Functions to automate

```python
def retention_rate(df, col_names):
    retained = df[df['is_retained'] == True].groupby(col_names)\
                   ['user_id'].nunique()

    converted = df[df['converted'] == True].groupby(col_names)\
                    ['user_id'].nunique()

    return retained/converted

daily_retention = retention_rate(marketing, 
                                 ['date_subscribed', 
                                 'subscribing channel'])

daily_retention = pd.DataFrame(daily_retention.unstack(level=1))

def plotting(df):
    for col in df:
        plt.plot(df.index, df[col])
        plt.title('Daily ' + col + 'retention rate\n', size = 16)
        plt.ylabel('Retention rate (%)', size = 14)
        plt.xlabel('Date', size = 14)
        plt.show()

plotting(daily_channel_retention)
```


## Identifying inconsistencies

```python
# Assume bug arose in '2018-01-11'
# Use ads prior to when bug arose
house_ads_no_bug = house_ads[house_ads['date_served'] < '2018-01-11']
lang_conv = conversion_rate(house_ads_no_bug, ['language_displayed'])

# Index other language conversion rate against English
spanish_index = lang_conv['Spanish']/lang_conv['English']
german_index = lang_conv['German']/lang_conv['English']

print("Spanish index: ", spanish_index)
# Spanish speakers convert at 1.7x the rate of English speakers

language_conversion = house_ads.groupby(['date_served', 
                                         'language_preferred'])
                               .agg({
                                   'user_id': 'nunique',
                                   'converted': 'sum'
                               })

expected_conversion = pd.DataFrame(language_conversion.unstack(level=1))

# Conversion rate for affected period
language_conversion['actual_english_conversions'] = \
    language_conversion.loc['2018-01-11':'2018-01-31']\
    [('converted', 'English')]

# Calculate daily expected conversion rate using indexes
language_conversion['expected_spanish_rate'] = \
    language_conversion['actual_english_rate'] * spanish_index

# Calculate expected conversions
language_conversion['expected_spanish_conversions'] = \
    language_conversion['expected_spanish_rate']/100
    *language_conversion[('user_id', 'Spanish')]
```