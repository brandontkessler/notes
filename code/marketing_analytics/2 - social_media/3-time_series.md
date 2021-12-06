# Time Series

```python
df['dt'] = pd.to_datetime(df['dt'])
df = df.set_index('dt')

df['dog'] = check_word_in_df('dog', df)
# Using prior made func, return col of bools for if the word 'dog'
#   is contained in the text in the defined rows of the df

mean_dog = df['dog'].resample('1 min').mean()
# Resample lets us define a time window of our choice and then
#   call an aggregation function. in this case, agg by 1 min window.

plt.plot(
    mean_dog.index.minute,
    mean_dog, color = 'blue'
)
```
