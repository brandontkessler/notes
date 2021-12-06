```python
from tweepy import Stream

listen = SListener(api)
stream = Stream(auth, listen)
stream.sample()
```