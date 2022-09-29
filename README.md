# Bin-API-Python
Binance API in Python

<hr>

### Contents
- [Setup](#setup)
- [Api keys](#api keys)

<hr>

### Setup

Setup Python-Binance libraries

```
pip install python-binance
```

### Api keys

You can store your API keys(api and secret key) in your 'config.py' file 
but better and secure option is to sthore them as environment variables.

```
set binance_api=your_api_key_here
set binance_secret=your_api_secret_here
```

To access your api_key and api_secret

```
C:\>python
>>> import os
>>> api_key = os.environ.get('binance_api')
>>> api_secret = os.environ.get('binance_secret')
>>> api_key, api_secret
('your_api_key_here', 'your_api_secret_here')
>>> _
```

