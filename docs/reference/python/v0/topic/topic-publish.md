Publishes a topic.

```python
from nitric.resources import topic;
from nitric.api import Event

updates = topic('updates').allow(['publishing'])

await updates.publish(Event(
  payload={'something': 'amazing happened'}
));
```


## Parameters

---

**event** required `Event`

The event to publish to the topic

| Properties                                                                       |
| -------------------------------------------------------------------------------- |
| **id** optional `string` <br/> unique id to apply to the event.                  |
| **payload** required `dict` <br/> payload to send with the event. |
| **payloadType** optional `string` <br/> a hint to the type of payload supplied.  |

---

## Examples

### Publish a topic

```python
from nitric.resources import topic;
from nitric.api import Event

updates = topic('updates').allow(['publishing'])

await updates.publish(Event(
  payload={'something': 'amazing happened'}
));
```

## Notes

- If an id is not supplied with an event a UUID(v4) will be generated for you.
- A function may subscribe to OR publish to a topic but not both.
