# Dashboard and Monitoring

The Dashboard is the primary operational view for monitoring channel health, message throughput, and system status in real time.

---

## Dashboard Overview

The Dashboard displays all deployed channels in a table with the following columns:

| Column | Description |
|---|---|
| **Status** | Current state icon (Started, Stopped, Paused, etc.) |
| **Name** | Channel name (expandable to show individual connectors) |
| **Revision** | Channel revision delta (indicates undeployed changes) |
| **Last Deployed** | Timestamp of the last deployment |
| **Received** | Total messages received by the source |
| **Filtered** | Messages filtered (not processed) |
| **Queued** | Messages currently in destination queues |
| **Sent** | Messages successfully sent |
| **Errored** | Messages that encountered errors |

### Expanding Channels
Click the expand arrow next to a channel name to see per-connector statistics:
- Source connector stats
- Individual destination connector stats

---

## Dashboard Actions

The left-hand task panel provides channel operations:

| Action | Description |
|---|---|
| **Deploy All** | Deploy all channels |
| **Deploy** | Deploy selected channel(s) |
| **Undeploy All** | Undeploy all channels |
| **Undeploy** | Undeploy selected channel(s) |
| **Start** | Start selected channel(s) |
| **Stop** | Stop selected channel(s) |
| **Pause** | Pause selected channel(s) |
| **Halt** | Force-stop selected channel(s) immediately |
| **Start/Resume Connector** | Start individual connectors |
| **Stop/Pause Connector** | Stop individual connectors |

---

## Message Browser

Double-click a channel in the Dashboard to open its **Message Browser**.

### Search Filters
- **Date range** - Start and end dates
- **Message status** - RECEIVED, SENT, ERROR, FILTERED, QUEUED, TRANSFORMED
- **Connector** - Filter by specific connector
- **Message ID** - Search by specific message ID
- **Content search** - Full-text search across message content
- **Metadata search** - Search by custom metadata columns

### Advanced Filters
- **Has errors** - Show only messages with errors
- **Content types** - Which content to include in results
- **Send attempts** - Filter by number of send attempts
- **Import ID** - Search imported messages
- **Server ID** - For clustered environments

### Message Detail View
Select a message to view:
- **Raw** - Original received content
- **Processed Raw** - After preprocessor
- **Transformed** - After source transformer
- **Encoded** - Serialized to outbound data type
- **Sent** - Actual bytes sent to destination
- **Response** - Response received from destination
- **Mappings** - Channel/connector map variables
- **Errors** - Error messages and stack traces
- **Attachments** - Associated attachment data

### Message Operations
- **Reprocess** - Reprocess one or more messages through the channel
- **Export** - Export messages to XML or other formats
- **Import** - Import messages from files
- **Remove** - Delete messages from storage
- **Send Message** - Manually send a test message to the channel

---

## Connector Status Panel

The **Dashboard Status** plugin provides a detailed log of connector-level activity:

- Connection events (connected, disconnected)
- Message send/receive events
- Error events
- Idle/waiting status

This is useful for debugging connectivity issues and monitoring real-time activity per connector.

---

## Statistics

### Current Statistics
The Dashboard shows statistics for the current session (since last channel deploy). Counters include:
- Received, Filtered, Transformed, Queued, Sent, Errored

### Lifetime Statistics
Cumulative statistics across all deployments. View via the channel statistics panel.

### Resetting Statistics
- Right-click a channel to reset its current statistics
- Use the CLI command `resetstats [lifetime]` to reset all channels

---

## Event Browser

Access via **Other > Events** in the menu:

| Field | Description |
|---|---|
| **Date** | When the event occurred |
| **Level** | INFO, WARNING, ERROR |
| **Name** | Event type |
| **User** | User who triggered the event |
| **Outcome** | SUCCESS, FAILURE |
| **Description** | Detailed event description |

### Event Types
- Channel deploy/undeploy/start/stop
- User login/logout
- Configuration changes
- Server start/stop
- Extension events

### Filtering Events
- Filter by date range, level, user, and event name
- Export events to file for audit purposes

---

## Global Map Viewer

The **Global Map** plugin provides a view of server-wide and channel-specific map variables:

- **Global Map** - Variables shared across all channels
- **Global Channel Map** - Variables scoped to a specific channel
- **Configuration Map** - Key-value pairs from server settings

Useful for debugging inter-channel communication and shared state.

[Back to Home](Home)
