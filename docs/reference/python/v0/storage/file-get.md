Get a file from a bucket.

```python
from nitric.resources import bucket

// Create a readable/writable reference to an 'assets' bucket
assets = bucket('assets').allow(['reading']);

logo = assets.file('images/logo.png');

logo_data = await logo.get();
```

## Examples

### Get a file

```python
from nitric.resources import bucket

// Create a readable/writable reference to an 'assets' bucket
assets = bucket('assets').allow(['reading']);

logo = assets.file('images/logo.png');

logo_data = await logo.get();
```
