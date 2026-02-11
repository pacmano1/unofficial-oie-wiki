# Alerts and Notifications

OIE provides a configurable alerting system that monitors channel activity and sends notifications when specified conditions are met.

---

## Alert Components

Each alert consists of three parts:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Trigger    │────▶│  Channels   │────▶│   Actions    │
│  (When)      │     │  (Where)    │     │  (What)      │
└─────────────┘     └─────────────┘     └─────────────┘
```

### Trigger
Defines the condition that fires the alert:
- **Error events** - Alert on connector errors
- **Specific error codes** - Match particular error patterns
- **Custom conditions** - Regex or pattern matching on error messages

### Channel Selection
Defines which channels are monitored:
- Select specific channels
- Select all channels
- Select by channel tag or group

### Actions
Defines what happens when the alert fires:
- **Email notification** - Send an email to specified recipients
- **Channel notification** - Route alert to another channel for custom handling

---

## Creating an Alert

1. Navigate to **Alerts** in the main menu
2. Click **New Alert**
3. Configure the three tabs:

### Trigger Tab
- **Trigger Type:** Error-based trigger
- **Error Event Types:** Select which error types to monitor
  - Source connector errors
  - Destination connector errors
  - Filter/transformer errors
- **Regex Match:** Optional regex to match against error messages

### Channel Tab
- Check the channels this alert should monitor
- Use **Select All** for global monitoring
- Alerts only fire for selected channels

### Action Tab
Configure one or more notification actions:

| Setting | Description |
|---|---|
| **Subject** | Email subject line (supports variables) |
| **Template** | Notification body (supports variables) |
| **Recipients** | Email addresses |
| **Protocol** | Email (SMTP) or Channel |

---

## Alert Variables

Use these variables in alert subjects and templates:

| Variable | Description |
|---|---|
| `${error}` | The error message |
| `${errorMessage}` | Detailed error description |
| `${channelName}` | Channel name |
| `${channelId}` | Channel UUID |
| `${connectorName}` | Connector name |
| `${connectorType}` | Source or Destination |
| `${messageId}` | Message ID |
| `${date}` | Alert timestamp |
| `${serverName}` | Server name |

### Example Template
```
Alert: Error on ${channelName}

Channel: ${channelName} (${channelId})
Connector: ${connectorName} (${connectorType})
Message ID: ${messageId}
Date: ${date}

Error Details:
${errorMessage}
```

---

## Email Configuration

For email alerts, configure the SMTP settings in **Settings > Server**:

| Setting | Description |
|---|---|
| **SMTP Host** | Mail server hostname |
| **SMTP Port** | Mail server port |
| **Send Timeout** | Connection timeout |
| **Username** | SMTP authentication username |
| **Password** | SMTP authentication password |
| **Encryption** | None, STARTTLS, or SSL/TLS |
| **From Address** | Sender email address |

---

## Managing Alerts

### Enable / Disable
Toggle alerts on/off without deleting them.

### Import / Export
- **GUI:** Right-click to export/import as XML
- **CLI:**
  ```
  importalert "path" [force]
  exportalert id|"name"|* "path"
  ```

### Testing
1. Create a channel with a transformer that intentionally throws an error
2. Configure an alert to monitor that channel
3. Send a test message and verify the notification is received

---

## Best Practices

1. **Alert on errors, not successes** - Avoid notification fatigue
2. **Use specific triggers** - Filter by error type or regex to reduce noise
3. **Include context in templates** - Channel name, message ID, and error details help with troubleshooting
4. **Set up escalation** - Use multiple alerts with different severities
5. **Monitor alert delivery** - Ensure SMTP settings are correct and test periodically
6. **Channel-based alerts** - Route alerts to a dedicated channel for custom notification logic (Slack, PagerDuty, etc.)

[Back to Home](Home)
