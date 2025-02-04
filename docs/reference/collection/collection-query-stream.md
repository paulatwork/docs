Stream paged results from query.

```javascript
import { collection } from '@nitric/sdk';

const profiles = collection('profiles');

const profileQuery = profiles.query();

const results = await profileQuery.stream();
```

## Examples

### Streaming results from a query

```javascript
import { collection } from '@nitric/sdk';

const profiles = collection('profiles');

const profileQuery = profiles.query();

let results = await profileQuery.stream();

results.on('data', (doc) => {
  // handle stream results
});

results.on('end', () => {
  // handle stream closing
});
```

## See also

- [query().where()]()
- [query().limit()]()
- [query().pagingFrom()]()
