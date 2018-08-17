# Funpicker - A way of collecting price information for your trading bot.
 


`Funpicker` is a library funguana created to get the price information for exchanges. This relies most on the cryptocompare api. However, there are parts that use ccxt to pull exchange specific information.

This library is to make our application highly modular. The basic premise of it is very simple: 

1. You explain which information you want to collect in a context
2. You get a return with that information
3. You store what you intend using another library (such as funtime or sql)

**Understand `funpicker` is a firm wrapper around `ccxt` and `request`. Its job is to make pricing easier to access for the common bot maker**


## What makes `funpicker` better?
The single thing that makes funpicker better than all other platforms is that it relies 100% on making the process simipler for the user. There's only a couple of functions to be able to start getting information from exchanges immediately for either storage or analytics:

It is a layer on top of `request` and ccxt. We added the following:

* An easy way to find data
* Robust fallback for price data (will fall back to `cryptocompare`)
* The user will have easy query options to get data with extra grainularity (thanks to `lagerfeuer`)
* Easy access to orderbook information. Has an option of an in-memory queue to deal with rate limits. Or you could let it fail and not acknowledge it.


## How does it work?
Using a few functions, we reach out to various API's to do the following:

* Get orderbook information
* Get pricing

### Example:
---
```python
from funtime import Store, Converter
import mimesis # this is used to seed data for our test
import time

# Create a library and access the store you want
store = Store().create_lib("hello.World").get_store()

# store the data with a timestamp
store['hello.World'].store({
    "type": "price",
    "currency": "ETH_USD",
    "timestamp": time.time(),
    "candlestick": {
        "open": 1234,
        "close": 1234.41,
        "other": "etc"
    }
})


# Query general information. It returns a generator
runs = store['hello.World'].query({
    "type": "price"
})

# Check for results
for r in runs:
    print(r)


# Even get information with complex time queries
runs2 = store['hello.World'].query_time(time_type="before", start=time.time(), query_type="price")


# Check for results
for r in runs:
    print(r)

```

## Using the Pandas/Dask converter

As a data scientist, you may want to handle your data in dataframe format. With `funtime`, you can get your timestamp information in both `pandas.DataFrame` and `dask.DataFrame` format. You would use the `Converter` import. 

```python
from funtime import Store, Converter


runs = store['hello.World'].query({
    "type": "price"
})

# if you want a pandas object
df = Converter.to_dataframe(runs)

# If you want to do parallel processing within dask
ddf = Converter.to_dataframe(runs, "dask")
```


## How to install

Make sure to install mongodb at the very beginning. The instructions are different for different operating systems. Then run:

```
pip install funtime
```

Or you can use `pipenv` for it:

```
pipenv install funtime
```
