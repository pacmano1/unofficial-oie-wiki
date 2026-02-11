# Channels and Connectors

Channels are the core building blocks of OIE. Each channel defines a complete message processing pipeline: how messages are received, how they are transformed, and where they are sent.

---

## Channel Anatomy

A channel consists of:

```
┌──────────────────────────────────────────────────────────┐
│                        Channel                            │
│                                                           │
│  ┌────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │   Source    │───▶│  Transformer │───▶│ Destination  │  │
│  │ Connector   │    │  & Filters   │    │ Connector(s) │  │
│  └────────────┘    └──────────────┘    └──────────────┘  │
│                                                           │
│  Scripts: Deploy | Undeploy | Preprocessor | Postprocessor│
└──────────────────────────────────────────────────────────┘
```

### Source Connector
- **One per channel** - Defines how messages enter the system
- Listens on a port, polls a directory, queries a database, etc.
- Configured with a **Source Transformer** and **Source Filter**

### Destination Connectors
- **One or more per channel** - Defines where messages are sent
- Each destination has its own **Transformer** and **Filter**
- Destinations can be chained, queued, or processed in parallel
- Each destination can have different connector types

### Channel Scripts
| Script | When It Runs |
|---|---|
| **Deploy** | When the channel is deployed |
| **Undeploy** | When the channel is undeployed |
| **Preprocessor** | Before each message enters the source transformer |
| **Postprocessor** | After all destinations have processed a message |
| **Attachment** | For extracting/handling attachments |
| **Batch** | For splitting a single inbound payload into multiple messages |

---

## Channel Properties

### Summary Tab
- **Name** - Unique channel name
- **Description** - Free-text description
- **Channel ID** - Auto-generated UUID
- **Data Types** - Inbound and outbound data formats per connector
- **Channel Tags** - Organizational tags for grouping channels
- **Dependencies** - Channels that must be deployed before/after this one

### Source & Destination Settings

Source settings control:
- **Response** handling (auto-generate, respond from destination, etc.)
- **Processing** behavior (batch mode, source queue)

Destination settings control:
- **Queue** behavior (queue on failure, always queue, never queue)
- **Thread count** for parallel processing
- **Retry** logic and intervals
- **Reattach attachments** option

### Message Storage

Controls how much message content is persisted:

| Mode | Description |
|---|---|
| **Development** | Stores all content (raw, transformed, encoded, sent, response) |
| **Production** | Stores raw and encoded content |
| **Raw** | Stores only raw content |
| **Metadata Only** | Stores only metadata (no content) |
| **Disabled** | No message storage |

### Pruning

Per-channel settings for automatic message cleanup:
- **Prune metadata after N days**
- **Prune content after N days**
- **Archive before pruning** (optional)

---

## Channel Groups

Channels can be organized into **groups** for easier management. Groups are purely organizational and do not affect processing.

- Create groups in the **Channels** panel
- Drag and drop channels between groups
- Deploy/undeploy entire groups at once

---

## Channel States

| State | Description |
|---|---|
| **Started** | Channel is actively processing messages |
| **Stopped** | Channel is deployed but not processing |
| **Paused** | Channel is deployed, source is paused (queued messages still process) |
| **Undeployed** | Channel exists but is not loaded into the engine |

---

## Channel Dependencies

Channels can declare dependencies on other channels:
- A channel will not start until its dependencies are deployed and started
- Useful for channels that use the **Channel Writer** connector to pass messages to other channels
- Configure in channel properties or the **Channel Dependencies** dialog

---

## Connector Types Overview

See [Connector Reference](Connector-Reference) for detailed documentation on each connector type.

### Source Connectors
| Connector | Protocol/Method |
|---|---|
| TCP Listener | TCP/MLLP |
| HTTP Listener | HTTP/HTTPS |
| Web Service Listener | SOAP/WSDL |
| Channel Reader | Internal VM channel-to-channel |
| Database Reader | JDBC polling |
| File Reader | File/FTP/SFTP/S3/SMB |
| JMS Listener | Java Message Service |
| JavaScript Reader | Custom scripted source |
| DICOM Listener | DICOM protocol |

### Destination Connectors
| Connector | Protocol/Method |
|---|---|
| TCP Sender | TCP/MLLP |
| HTTP Sender | HTTP/HTTPS |
| Web Service Sender | SOAP/WSDL |
| Channel Writer | Internal VM channel-to-channel |
| Database Writer | JDBC insert/update |
| File Writer | File/FTP/SFTP/S3/SMB |
| JMS Sender | Java Message Service |
| JavaScript Writer | Custom scripted destination |
| SMTP Sender | Email |
| Document Writer | PDF/RTF document generation |
| DICOM Sender | DICOM protocol |

[Back to Home](Home)
