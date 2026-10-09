# Arrow Flight Samples

If writing your client in Python or Node.js, we highly recommend using the respective [Python SDK](../../sdks/python-sdk/) or [Node.js SDK](../../sdks/node.js-sdk/) instead of using the Arrow Flight endpoint directly.

Alternatively, you can use the [PyArrow](https://arrow.apache.org/docs/python/index.html) package directly.

_The Arrow Python bindings (also named “PyArrow”) have first-class integration with NumPy, pandas, and built-in Python objects. They are based on the C++ implementation of Arrow._

Queries can be sent and results retrieved as Pandas DataFrames. An example follows:

```python
from pyarrow import flight

apiKey = '[API_KEY]'
query = 'SELECT * FROM taxi_trips LIMIT 10;'

# Connect to the endpoint
client = flight.connect(f'grpc+tls://flight.spiceai.io')

# Authenticate with your project's API key
token_pair = client.authenticate_basic_token('', apiKey)
options = flight.FlightCallOptions(headers=[token_pair])

# Query and fetch a reader for the results
flight_info = client.get_flight_info(flight.FlightDescriptor.for_command(query), options)
reader = client.do_get(flight_info.endpoints[0].ticket, options)

# Convert to a Pandas DataFrame and print the results
print(reader.read_pandas())
```

Stand-alone samples can be found in our [sample repository](https://github.com/spicehq/samples):

* [Blocks Schema ](https://github.com/spicehq/samples/blob/trunk/python/blocks\_schema.py)- Retrieve the structure only of blocks information.
* [Block Mining Time](https://github.com/spicehq/samples/blob/trunk/python/block\_mining\_time.py) - Aggregation example to plot Average mining time of block per day of the week (last 1M blocks)
* [Weekly Gas Usage](https://github.com/spicehq/samples/blob/trunk/python/weekly\_gas\_usage.py) - Aggregation example to plot the average gas usage per weeks (last 3M blocks)
