# Python SDK

The Python SDK `spicepy` is the easiest way to use and query Spice in Python.

### Requirements

* Python 3.8+

The following packages are required and will be automatically installed by pip:

* `pyarrow`
* `pandas`
* `web3`

<details>

<summary>Apple M1 Mac Requirements - <a href="https://support.apple.com/en-us/HT211814">How do I know if I have an M1?</a></summary>

Apple M1 Macs require an arm64 compatible version of `pyarrow` which can be installed using [miniforge](https://github.com/conda-forge/miniforge). We recommend the following procedure:

* Install [Homebrew](https://brew.sh)
* Install [miniforge](https://github.com/conda-forge/miniforge) with:

```
brew install --cask miniforge
```

* Initialize conda in your terminal with:

```
conda init "$(basename "${SHELL}")"
```

* Install `pyarrow` and `pandas` with:

```
conda install pyarrow pandas
```

While [Anaconda](https://www.anaconda.com/) can be used to install pyarrow, the installed version is old (4.0.0) so we recommend using the [miniforge](https://github.com/conda-forge/miniforge) distribution.

</details>

### Installation

Install the `spicepy` package directly from the Spice Github Repository at [https://github.com/spiceai/spicepy](https://github.com/spiceai/spicepy):

```
pip install git+https://github.com/spiceai/spicepy
```

### Usage

Import `spicepy` and create a `Client` by providing your API Key.

You can then submit queries using the query function.

```python
import spicepy
client = spicepy.Client('API_KEY')
block_data = client.query('SELECT * FROM eth.recent_blocks LIMIT 10;').read_pandas()
transaction_Data = client.query('SELECT * FROM eth.recent_transactions LIMIT 10;').read_pandas()
```

`Client` objects has the following arguments:

* **key** (required) : the API key too authenticate with.
* **url** (optional) : URL to the endpoint to connect to (default: grpc+tls://flight.spiceai.io).
* **tls\_root\_cert** (optional): path to specific certificate to use for the TLS connection.

The query will timeout if it has not completed within 15 mins and a TimeoutError exception will be raised. 
### Contributing

Contribute to or file an issue with the `spicepy` library at [https://github.com/spiceai/spicepy](https://github.com/spiceai/spicepy)
