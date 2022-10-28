Nitric makes it easy to create functions that run on a schedule. Schedules are most useful for batch workloads, reporting and other activities that happen on a set cadence.

### Frequencies

Frequencies define when your functions should run. Nitric supports expressive schedules which run as often as once per minute. Frequencies can be configured in minutes, hours and days.

## The basics

The guide below highlights the features of Nitric Schedules.

### Create a schedule

Creating schedules with Nitric can be done with a single line of code to define resources.

```python
from nitric.resources import schedule
from nitric.faas import EventContext

@schedule('process-transactions', '5 minutes')
async def process_transactions(ctx: EventContext):
  # do some processing
  pass

@schedule('send-reminder', '3 hours')
async def process_transactions(ctx: EventContext):
  # do some processing
  pass

@schedule('send-reports', '1 days')
async def process_transactions(ctx: EventContext):
  # do some processing
  pass
```

