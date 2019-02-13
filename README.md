# bountycountry-python
Python library for the Bounty Country API

The Bounty Country Python library provides convenient access to the Bounty Country API for applications written in the Python language. 

## Documentation

See the [Python API docs](https://bountycountry.com/apidocs/).

## Installation

You don't need this source code unless you want to modify the package. If you just
want to use the package, just run:

    pip install --upgrade bountycountry

Install from source with:

    python setup.py install

### Requirements

- Python 3+ 
- Requests >= 2.20

## Usage

The library needs to be configured with your account's public key and secret key which are
available in your [Bounty Country Dashboard](https://bountycountry.com/api). 

Set `bountycountry.BC_PUBLIC_KEY` to your public key value. 
Set `bountycountry.BC_SECRET_KEY` to your secret key value. 


```python
import bountycountry
bountycountry.BC_PUBLIC_KEY = "....your.public.key.here...."
bountycountry.BC_SECRET_KEY = "....your.secret.key.here...."

# start a live stream of dataset 

# First define a handler that will do 'something' (you decide) to each batch of items received
def myHandler(batch):
  for item in batch:
    #do something with each item
    print(item)
  
bountycountry.getLiveStream('dataset-id-goes-here', StreamHandler=myHandler)
```
The `getLiveStream` function will indefinitely poll Bounty Country for the latest data and implement an exponential backoff (starting with a 2 second wait) if there is no new data before retrying. 


### Stream a Specific Time Range

The last 24 hours of data for a stream can be queried as a specific range using `bountycountry.getStreamRange`. 



Timestamps must be expressed in EpochTime format integers:
```python
import time 

# get the current time as integer epochtime
currenttime = int(time.time())

# convert ISO8601 time stamp to integer epochtime 
epochtime = int(time.mktime(time.strptime("2019-04-01 19:20:00", "%Y-%m-%d %H:%M:%S")))

```

The Stream Range is a **python generator** meaning we iterate over each item batch returned using a for loop. By default a batch will return 250 items.   

```python


# list charges
stripe.Charge.list(
    api_key="sk_test_...",
    stripe_account="acct_..."
)

# retrieve single charge
stripe.Charge.retrieve(
    "ch_1A2PUG2eZvKYlo2C4Rej1B9d",
    api_key="sk_test_...",
    stripe_account="acct_..."
)
```


<!--
# vim: set tw=79:
-->
