# Message Processing

This page describes how messages flow through an OIE channel from receipt to final delivery.

---

## Message Lifecycle

```
 External System
       │
       ▼
 ┌─────────────┐
 │   Source     │  1. Receive raw message
 │  Connector   │
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │ Preprocessor │  2. Modify raw data before processing (optional)
 │   Script     │
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │   Source     │  3. Filter: accept or reject the message
 │   Filter     │     Transform: map/modify message content
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │ Destination  │  4. Each destination gets a copy of the transformed message
 │  Filter(s)   │     Destination filter: route to specific destinations
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │ Destination  │  5. Transform message for the specific destination
 │ Transformer  │
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │ Destination  │  6. Send to external system
 │  Connector   │
 └──────┬──────┘
        ▼
 ┌─────────────┐
 │ Response     │  7. Process response from destination
 │ Transformer  │
 └──────┬──────┘
        ▼
 ┌──────────────┐
 │ Postprocessor │  8. Final processing after all destinations complete
 │   Script      │
 └───────────────┘
```

---

## Message Content Types

As a message moves through a channel, its content is stored in different forms:

| Content Type | Description |
|---|---|
| **Raw** | The original message as received by the source connector |
| **Processed Raw** | The message after the preprocessor script modifies it |
| **Transformed** | The message after the source transformer |
| **Encoded** | The message serialized to the outbound data type |
| **Sent** | The actual data sent by the destination connector |
| **Response** | The response received from the destination system |

---

## Message Statuses

Each connector message has a status:

| Status | Meaning |
|---|---|
| **RECEIVED** | Message received by the source |
| **FILTERED** | Message was filtered out (not processed) |
| **TRANSFORMED** | Source transformer completed |
| **SENT** | Successfully sent by destination |
| **QUEUED** | Placed in the destination queue (pending send) |
| **ERROR** | An error occurred during processing |
| **PENDING** | Awaiting processing |

---

## Source Processing

### Preprocessor Script
Runs before the source transformer. Receives the raw message as a string and can:
- Modify the raw content
- Return a modified string to replace the raw message
- Perform logging or external lookups

```javascript
// Example: Strip leading whitespace
return message.trim();
```

### Source Transformer
Converts the inbound message from its source data type. Transformer steps can:
- **Map** values from the message into channel variables
- **Build** new message structures
- Execute custom **JavaScript**
- Use **Iterator** steps for repeating segments

### Source Filter
Determines whether a message should continue processing. Filter rules can:
- Accept or reject based on field values
- Use **Rule Builder** for simple conditions
- Use custom **JavaScript** for complex logic
- Use **External Script** files

If a message is filtered, its status is set to `FILTERED` and no destinations process it.

---

## Destination Processing

### Destination Sets
Multiple destinations are processed in order (by default). You can:
- **Skip** destinations dynamically using the `destinationSet` object in scripts
- **Queue** messages that fail for later retry
- **Process in parallel** by configuring thread counts

### Destination Filter
Each destination can have its own filter to determine if that particular destination should process the message.

### Destination Transformer
Each destination has its own transformer chain. This allows you to transform the same source message differently for each destination.

### Response Processing
After a destination sends a message:
1. The response from the external system is captured
2. A **Response Transformer** can process the response
3. The response can be mapped back into channel variables
4. The source connector can return a response based on destination results

---

## Postprocessor Script

Runs after all destinations have completed for a given message. Use it to:
- Perform cleanup
- Send summary notifications
- Update external systems with processing results
- Access the message status and response data

```javascript
// Example: Log message completion
logger.info("Message " + message.getMessageId() + " processed successfully");
return;
```

---

## Message Queuing

When a destination is configured with queuing:

1. Messages that fail initial delivery are placed in a **persistent queue**
2. A background thread retries queued messages at configurable intervals
3. Queue settings include:
   - **Retry interval** (milliseconds between attempts)
   - **Rotate queue** (move failed messages to the back of the queue)
   - **Retry count** (maximum attempts before marking as ERROR)
   - **Queue threads** (parallel queue processing)

### Queue Modes
| Mode | Behavior |
|---|---|
| **Queue on failure** | Attempt immediate send; queue only on failure |
| **Always queue** | Always queue first, then send from queue thread |
| **Never queue** | Fail immediately on error (no retry) |

---

## Batch Processing

Source connectors can split a single inbound payload into multiple discrete messages:

- **File Reader** can split files by delimiter, record length, or JavaScript
- **Database Reader** creates one message per result row
- A custom **Batch Script** can define arbitrary splitting logic

---

## Attachments

Large or binary content can be extracted from messages and stored separately as attachments:

| Attachment Mode | Description |
|---|---|
| **None** | No attachment handling |
| **Identity** | Store the entire raw message as an attachment |
| **Regex** | Extract content matching regular expressions |
| **JavaScript** | Custom extraction logic |
| **DICOM** | DICOM-specific attachment handling |
| **Custom** | Plugin-defined attachment handling |

Attachments are stored in the database and can be reattached to outgoing messages as needed.

[Back to Home](Home)
