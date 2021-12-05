# Processing Text

```python
# df with text
df.head()

hello_search = df['colname'].str.contains('hello', case=False)

# Print % of rows that contain 'hello'
# sum of True gives total count
# shape[0] returns number of rows
print(np.sum(hello_search) / hello_search.shape[0])
```

## Function to check for word

```python
def check_word_in_df(word, data):
    contains_column = data['text'].str.contains(word, case = False)
    contains_column |= data['other_field'].str.contains(word, case = False)
    contains_column |= data['another'].str.contains(word, case = False)
    
    return contains_column
```