Create a reference to a file within a bucket.

```python
from nitric.resources import bucket

// Create a readable/writable reference to an 'assets' bucket
assets = bucket('assets').allow(['reading'])

logo = assets.file('images/logo.png')
```

## Parameters

---

**name** required `string`

The unique name/reference to the file.

---

## Notes

The file does not need to exist, only a reference to that file is being created.

## Available Operations

- [file.get()](./file-get.md)
- [file.write()](./file-write.md)
- [file.delete()](./file-delete.md)
- [file.signUrl()](./file-signurl.md)
