# Sentiment

VADER - SentimentIntensityAnalyzer

* Good for small text like tweets
* Can measure sentiment using text, emojis, capitalizations 
  * (ex. Nice vs NICE)

```python
from nltk.sentiment.vader import SentimentIntensityAnalyzer
sid = SentimentIntensityAnalyzer()
sentiment_scores = df['text'].apply(sid.polarity_scores)
```

Analyzing the output:

* neg, neu, pos - measure how many words fall within each category
* compound - the overall score (>0 means positive, <0 means negative)

```python
sentiment = sentiment_scores.apply(lambda x: x['compound'])
df_dog = sentiment[check_word_in_df('dog', df)].resample('1 min').mean()
plt.plot(
    df_dog.index.minute,
    df_dog, color='blue'
)
```