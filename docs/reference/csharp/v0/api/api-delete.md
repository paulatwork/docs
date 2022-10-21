Register an API route and set a specific HTTP DELETE handler on that route.

> This method is a convenient short version of [api().route().delete()](./api-route-delete)

```csharp
using static Nitric.Sdk.Nitric;

var api = Api("public");

api.Delete("/customer/:customer_id", ctx => 
{
  // perform the delete and send a response.
  return ctx;
});

Run();
```

## Parameters

---

**route** required `string`

The path matcher to use for the route. Matchers accept path parameters in the form of a colon prefixed string. The string provided will be used as that path parameter's name when calling middleware and handlers. See [create a route with path params](#create-a-route-with-path-params)

---

**...middleware** required `HttpMiddleware`

One or more middleware functions to use as the handler for HTTP requests. Handlers can be sync or async.

---
