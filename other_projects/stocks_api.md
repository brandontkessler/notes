# Alpha Vantage Stock API

This is a way to read stock prices in real time via API. This also assumes receipt of an API key from: [alpha vantage]('https://www.alphavantage.co/')

Put the api key in a json file called `keys.json`:

```json
{
  "ALPHA_VANTAGE_API_KEY": "<enter key here>"
}
```

Then use the key to read stock data for any stock symbol you want:

```js
import pandas as pd 
from alpha_vantage.timeseries import TimeSeries
import json

with open('keys.json') as f:
  keys = json.load(f)
  alpha_vantage_api_key = keys['ALPHA_VANTAGE_API_KEY']

api_key = alpha_vantage_api_key 
# get key from website

ts = TimeSeries(key=api_key, output_format='pandas')

data, metadata = ts.get_intraday(symbol='MSFT', interval='1min', outputsize='full')

print(data)
```
