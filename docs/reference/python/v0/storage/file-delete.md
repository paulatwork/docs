Deletes a file within a bucket.

```python
from nitric.resources import bucket

// Create a readable/writable reference to an 'assets' bucket
assets = bucket('assets').allow(['deleting']);

logo = assets.file('images/logo.png');

await logo.delete();
```

## Examples

### Delete a file

```python
from nitric.resources import bucket

// Create a readable/writable reference to an 'assets' bucket
assets = bucket('assets').allow(['deleting']);

logo = assets.file('images/logo.png');

await logo.delete();
```
