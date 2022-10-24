Register an API route and set a specific HTTP GET handler on that route.

```python
from nitric.resources import api
from nitric.application import Nitric

publicApi = api("public")

@publicApi.post("/customer")
async def hello_world(ctx):
    ctx.res.body = f"Creating new customer"

Nitric.run()
```

## Parameters

---

**match** required `string`

The path matcher to use for the route. Matchers accept path parameters in the form of a colon prefixed string. The string provided will be used as that path parameter's name when calling middleware and handlers. See [create a route with path params](#create-a-route-with-path-params)

---

**options** required `MethodOptions`

Options to configure the route such as security options

---

## Examples

### Register a handler for POST requests

```python
from nitric.resources import api
from nitric.application import Nitric

publicApi = api("public")

@publicApi.post("/customer")
async def hello_world(ctx):
    ctx.res.body = f"Creating new customer"

Nitric.run()
```

### Get request body as JSON

```python
from nitric.resources import api
from nitric.application import Nitric

publicApi = api("public")

@publicApi.post("/customer")
async def hello_world(ctx):
    # returns None if request body is not JSON
    body = ctx.req.json

    ctx.res.body = f"Creating new customer"

Nitric.run()
```
