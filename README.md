# `pxnd`

**WIP**: This is being actively developed and doesn't work yet.

Parallel/Plasma xnd.

## Usage
First start a Plasma store:

```bash
plasma_store -m 1000000000 -s /tmp/plasma
```

Then, connect to it and create some data in it:

```python
>>> import pyarrow.plasma as plasma
>>> import pxnd
>>> client = plasma.connect("/tmp/plasma", "", 0)
>>> object_id = plasma.ObjectID.from_random()
>>> object_id
ObjectID(e0abeb2aa9b91b0676a4bd46f1d27e58cdc5574b)
>>> x = pxnd.put(client, object_id, [[0, 1, 2], [3, 4, 5]])
xnd([[0, 1, 2], [3, 4, 5]], type="2 * 3 * int64")
```

In another process, you can then also read the data:

```python
>>> import pyarrow.plasma as plasma
>>> import pxnd
>>> import bytes
>>> client = plasma.connect("/tmp/plasma", "", 0)
>>> object_id = plasma.ObjectID(bytes.fromhex('e0abeb2aa9b91b0676a4bd46f1d27e58cdc5574b'))
>>> pxnd.get(client, object_id)
xnd([[0, 1, 2], [3, 4, 5]], type="2 * 3 * int64")
```

The type of the data (as a string) is stored in the Plasma metadata.

## Install/Dev
I opted to not go [the distutils route](https://docs.python.org/3/extending/building.html#building-c-and-c-extensions-with-distutils) for building the C Python extension. Instead, I build the packages manually. That way, I can use [flit](https://flit.readthedocs.io/en/latest/) to install the Python package and can seperate that logic from building the extension.

```bash
pip install flit
make
cd python
flit install --symlink
```

