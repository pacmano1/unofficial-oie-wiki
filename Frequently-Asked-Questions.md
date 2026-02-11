# Frequently Asked Questions

Common questions about using the Open Integration Engine.

---

## General

### What is the relationship between OIE and Mirth Connect?
OIE (Open Integration Engine) is an open-source fork of Mirth Connect, created after Mirth Connect shifted to a proprietary model. OIE continues the legacy of open healthcare integration under the Mozilla Public License 2.0.

### What Java version is required?
Java 17 or later. The server launcher script automatically searches for a compatible runtime.

### What databases are supported?
Apache Derby (embedded, default), PostgreSQL, MySQL, Oracle, SQL Server, and SQLite. See [Database Support](Database-Support) for details.

### Is OIE free to use?
Yes. OIE is licensed under the Mozilla Public License 2.0 (MPL 2.0), which allows free use, modification, and distribution.

---

## Installation and Setup

### What are the default login credentials?
Username: `admin`, Password: `admin`. Change this immediately after first login.

### What are the default ports?
HTTP: `8080` (web server/launch page), HTTPS: `8443` (REST API/Administrator connections).

### Should I use Derby in production?
No. Derby is suitable for development and evaluation. For production, use PostgreSQL, MySQL, Oracle, or SQL Server. See [Database Support](Database-Support).

### How do I change the database after installation?
Edit `conf/mirth.properties` to update the `database`, `database.url`, `database.username`, and `database.password` properties. Restart the server. See [Server Configuration](Server-Configuration).

### How do I reset the admin password?
Use the CLI: `user changepw admin "newpassword"`. If you cannot access the CLI, you can reset the password directly in the database.

---

## Channels

### What is a channel?
A channel is the fundamental processing unit in OIE. It defines a complete message pipeline: how messages are received (source connector), how they are transformed, and where they are sent (destination connectors). See [Channels and Connectors](Channels-and-Connectors).

### How many destinations can a channel have?
There is no hard limit. A channel can have as many destinations as needed. Destinations process in order by default, but can be configured for parallel processing.

### What is the difference between "Deploy" and "Start"?
**Deploy** loads the channel into the engine and starts it. **Start** resumes a channel that has been deployed but stopped or paused. A channel must be deployed before it can be started.

### How do I pass data between channels?
Use the **Channel Writer** (destination) and **Channel Reader** (source) connectors. You can also use the `VMRouter` class in JavaScript to route messages programmatically. The `globalMap` can share state, but for message passing, use the VM connectors.

### What happens when a destination fails?
Depending on the queue settings: the message is either marked as ERROR immediately (no queue), placed in the queue for retry (queue on failure), or was already queued (always queue). See the queue modes in [Message Processing](Message-Processing).

### What is the difference between the source filter and destination filter?
The **source filter** runs once and determines if the message is processed at all. **Destination filters** run per-destination and determine if each individual destination processes the message.

### How do I route a message to only some destinations?
Use destination filters on each destination, or use the `destinationSet` object in the source transformer to dynamically remove destinations:
```javascript
destinationSet.remove(['Destination 2']);
```

---

## Transformers and Scripts

### What is the difference between `msg` and `tmp`?
`msg` is the parsed inbound message (read-only reference). `tmp` is the outbound message template that you modify to change what gets sent. Changes to `msg` affect how you read data; changes to `tmp` affect what is output.

### What JavaScript engine does OIE use?
Mozilla Rhino, configured for ES6 by default (configurable via `rhino.languageversion` in `mirth.properties`).

### Can I use Java classes in my JavaScript?
Yes. Rhino runs on the JVM, so you can access any Java class directly:
```javascript
var uuid = java.util.UUID.randomUUID().toString();
```

### What is E4X?
ECMAScript for XML — the syntax used to access and manipulate XML message content in transformers. Regardless of the original data type, messages are represented as XML internally:
```javascript
var value = msg['PID']['PID.5']['PID.5.1'].toString();
```

### How do I add a segment to an HL7 message?
Use E4X to create and append segments in a JavaScript transformer step. See the [JavaScript Scripting Reference](JavaScript-Scripting-Reference) for detailed examples.

### How do I delete a segment from an HL7 message?
Use the `delete` keyword in E4X:
```javascript
delete tmp['ZZ1'];
```

---

## Data Types

### What data types are supported?
HL7 v2.x, HL7 v3.x, DICOM, XML, JSON, EDI/X12, NCPDP, Delimited Text, and Raw. See [Data Types and Formats](Data-Types-and-Formats).

### Can I convert between data types in a single channel?
Yes. Set different inbound/outbound data types on each connector. The engine handles serialization/deserialization at each boundary automatically.

---

## Performance

### How do I improve channel throughput?
- Set **Message Storage** to the minimum level you need (Production or Raw instead of Development)
- Use **Attachment Handlers** for large messages to reduce memory usage
- Enable **source queuing** if auto-generated ACKs are sufficient
- Enable **destination queuing** if you don't need synchronous responses
- Increase **Max Processing Threads** if message order doesn't matter
- Increase **Queue Threads** if the downstream system can handle parallel connections
- Uncheck **Wait for previous destination** unless ordering between destinations matters
- Use **Custom Metadata Columns** to improve message search performance on frequently queried fields
- Use **Code Template Libraries** to organize reusable code (vs. duplicating in every channel)

### What Message Storage mode should I use?
| Environment | Recommended Mode |
|---|---|
| Development/Testing | Development (stores everything) |
| Production (need troubleshooting) | Production (raw + encoded) |
| Production (high throughput) | Raw or Metadata Only |
| Maximum performance | Disabled (no storage) |

### How do I handle large messages?
Use an Attachment Handler to extract large content (images, documents) from the message body and store it separately. This reduces the in-memory message size and improves throughput.

---

## Security

### How do I replace the self-signed TLS certificate?
Use the **TLS Manager** web application at `http://<host>:8080/tls-manager/`, or manually import a CA-signed certificate into the keystore using Java's `keytool` utility.

### How do I encrypt the database password in mirth.properties?
The server can store encrypted values in the keystore. Refer to the server's encryption settings in [Security and Compliance](Security-and-Compliance).

### Is OIE HIPAA compliant?
OIE provides the technical controls needed for HIPAA compliance (encryption, access control, audit logging), but compliance also requires organizational policies and procedures. See [Security and Compliance](Security-and-Compliance).

---

## Maintenance

### How do I back up my OIE instance?
1. Export the server configuration: `exportcfg "backup.xml"` (via CLI)
2. Back up the `appdata/` directory (includes keystore and embedded database if using Derby)
3. Back up `conf/mirth.properties`
4. If using an external database, back up that database separately

### How do I prevent the database from growing too large?
Enable the **Data Pruner** in Settings and configure per-channel pruning policies. Set content pruning shorter than metadata pruning (e.g., prune content after 7 days, metadata after 30 days). See [Data Pruning and Maintenance](Data-Pruning-and-Maintenance).

[Back to Home](Home)
