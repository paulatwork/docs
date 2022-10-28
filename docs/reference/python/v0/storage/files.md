Get a list of file references for files that exist on the bucket.

```python
from nitric.resources import bucket

// Create a readable/writable reference to an 'assets' bucket
assets = bucket('assets').allow(['reading'])

files = await assets.files()
```

## Examples

Deleting all files in a bucket

```python
from nitric.resources import bucket

assets = bucket('assets').allow(['reading', 'deleting'])

files = await assets.files()
for f in files:
    await f.delete()
```

## Notes

This method returns a list of [File](./file) references that exist on the bucket.
