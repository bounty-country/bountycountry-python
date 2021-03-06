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
```


### Stream a live dataset

The stream data is returned in JSON batches via a **python generator**. You can iterate over each batch/page of items returned using any loop. By default a batch will consist of 250 items.  

```python
results = bountycountry.getLiveStream('dataset-id-goes-here')

for batch in results:
    #do something with each batch of results
    print(batch)
```
Batch Format
```python
{'Items':[
    {'ttl':1550188588,
    'upload_timestamp':1550102188.420983,
    'item_data':'the actual data goes here as a string or JSON object'
    },
    {'ttl':1550189000,
    'upload_timestamp':1550109000.420456,
    'item_data':{"somekey":"somevalue","somelist":[204, 306, 99]}
    },    
    ]
}   
```

The `getLiveStream` function will indefinitely poll Bounty Country for the latest data and implement an exponential backoff (starting with a 2 second wait) if there is no new data before retrying. By default, the function will also attempt to minimize the requests used (and resultant costs to the user) by introducing additional waits if the result set returned is less than the BatchSize (which is set to the maximum of 250 items by default). 

You can also cap your request expenditure rate by setting the MaxHourlyRequests parameter. 

#### OPTIONS
* **BatchSize** - the number of results to return per request/page (maximum of 250, default=250, format = integer)
* **MinimizeRequests** - whether to reduce request frequency to maximize result set size (reduce cost). Set to False if you have a strong requirement for minimal data latency and are less concerned by cost. (default=True)
* **MaxHourlyRequests** - the maximum number of requests to perform per hour, if exceeded the function will sleep for OnMaxWait seconds (default=None, format = integer)
* **OnMaxWait** - the number of seconds to wait if MaxHourlyRequests is exceeded (default = 0, format = integer)


### Get a specific time range within a stream 

A specific time range in the last 24 hours of data in a stream can be queried using `bountycountry.getStreamRange`. 

Timestamps must be expressed in EpochTime format integers:
```python
import time 

# get the current time as integer epochtime
currenttime = int(time.time())

# convert ISO8601 time stamp to integer epochtime 
epochtime = int(time.mktime(time.strptime("2019-04-01 19:20:00", "%Y-%m-%d %H:%M:%S")))

```

The Stream Range is a **python generator** so you can iterate over each batch/page of items returned using any loop. 
By default a batch will return up to 250 items.   

```python
# create generator
results = bountycountry.getStreamRange('dataset-id-goes-here', FromTime = 1554106800, ToTime = 1554109000)

# iterate over batches/pages and print the raw data of each item 
for batch in results:
    for item in batch['Items']:
        print(item['item_data'])
```
#### OPTIONS
If a FromTime and ToTime are not provided the function returns the 250 newest items in the stream
* **FromTime** - epoch timestamp of the earliest point in time to query
* **ToTime** - epoch timestamp of the latest point in time to query
* **Order** - the order in which to return results (options = 'Newest','Oldest', default = 'Newest')
* **Limit** - function will stop when Limit number of items have been returned (default = None, format = integer)
* **Last** - instead of passing a 'ToTime' or 'FromTime' you can manually pass the 'Last' result timestamp to get the next batch/page of results (format = integer epoch timestamp)
* **BatchSize** - the number of results to return per request/page (maximum of 250, default=250, format = integer)



### Post items to a Stream

The `getLiveStream` function will upload your items in batches of 25. Items can be accepted in one of three formats specified in the **format** parameter:
* **'array'** - accepts a python array of strings or objects. Objects will be json-serialized.
* **'lines'** - reads a file (provide a string path) line by line and uploads each line as an individual item
* **'dir'** - reads all files in a directory (provide a string path) and uploads the text contexts. Non-utf-8 encoded files are skipped.

```python
items = [
    {"somekey":[{"something nested":"value","somethingelsenested":"somevalue"}],
    "a string",
    "another string",
    "final string"
]

bountycountry.postStream('dataset-id-goes-here', items, format='array')
```

<!--
# vim: set tw=79:
-->
