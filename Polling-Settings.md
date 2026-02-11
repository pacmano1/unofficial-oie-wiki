# Polling Settings

Source connectors that poll for data (Database Reader, File Reader, JavaScript Reader) support configurable polling schedules. This page covers the available scheduling options.

---

## Polling Schedule Types

### Interval

Polls at a fixed interval, starting from when the channel is deployed.

| Setting | Description |
|---|---|
| **Polling Frequency** | Time between polls (in milliseconds) |

Example: Poll every 5 seconds = `5000` ms

This is the simplest option. The timer starts when the channel deploys, and polls occur at the configured interval regardless of time of day.

---

### Time Schedule

Polls at specific times of day, with optional day-of-week restrictions.

| Setting | Description |
|---|---|
| **Time(s)** | One or more specific times to poll (e.g., 08:00, 12:00, 17:00) |
| **Days of Week** | Optional restriction to specific days |
| **Time Zone** | Time zone for schedule interpretation |

Example: Poll at 6:00 AM, 12:00 PM, and 6:00 PM on weekdays only.

Use this when data should be picked up at specific business hours.

---

### Cron Schedule

Polls according to a cron expression, providing the most flexible scheduling.

| Setting | Description |
|---|---|
| **Cron Expression** | Standard cron syntax |

#### Cron Expression Format

```
second minute hour day-of-month month day-of-week [year]
```

| Field | Values | Special Characters |
|---|---|---|
| Second | 0-59 | `, - * /` |
| Minute | 0-59 | `, - * /` |
| Hour | 0-23 | `, - * /` |
| Day of Month | 1-31 | `, - * ? / L W` |
| Month | 1-12 or JAN-DEC | `, - * /` |
| Day of Week | 1-7 or SUN-SAT | `, - * ? / L #` |
| Year (optional) | 1970-2099 | `, - * /` |

#### Examples

| Expression | Schedule |
|---|---|
| `0 0/5 * * * ?` | Every 5 minutes |
| `0 0 8 * * ?` | Daily at 8:00 AM |
| `0 0 8,17 * * ?` | Daily at 8:00 AM and 5:00 PM |
| `0 0 */2 * * ?` | Every 2 hours |
| `0 0 9-17 * * MON-FRI` | Hourly from 9 AM to 5 PM, weekdays only |
| `0 30 6 ? * MON` | Every Monday at 6:30 AM |
| `0 0 0 1 * ?` | First day of every month at midnight |

---

## Advanced Polling Settings

Additional settings available across all schedule types:

| Setting | Description |
|---|---|
| **Schedule Type** | Interval, Time, or Cron |
| **Poll Once on Start** | Execute a poll immediately when the channel starts, before the scheduled poll |
| **Polling Frequency / Schedule** | Type-specific scheduling configuration |

### Poll Once on Start
When enabled, the connector immediately polls for data when the channel is started or deployed, without waiting for the first scheduled poll time. This is useful for catching up on data that arrived while the channel was stopped.

---

## Polling vs. Listener Connectors

| Behavior | Polling (Reader) | Listener |
|---|---|---|
| **Initiation** | OIE pulls data on a schedule | External system pushes data |
| **Connectors** | Database Reader, File Reader, JavaScript Reader | TCP Listener, HTTP Listener, Channel Reader, JMS Listener, Web Service Listener, DICOM Listener |
| **Schedule** | Interval, time, or cron | N/A (always listening) |
| **Resource usage** | Active only during polls | Continuous (thread + port) |
| **Latency** | Depends on poll interval | Near real-time |

---

## Best Practices

1. **Match polling frequency to data arrival patterns** - Don't poll every second if data arrives hourly
2. **Use cron for complex schedules** - Business-hours-only processing, monthly jobs, etc.
3. **Enable "Poll Once on Start"** for recovery - Catches data that arrived while the channel was stopped
4. **Consider the source system** - Frequent polling on databases or file systems can impact performance on the source
5. **Monitor polling behavior** - Check the Dashboard Connector Status panel to verify polls are executing as expected

[Back to Home](Home)
