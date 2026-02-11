# Glossary

Key terms and concepts used in OIE.

---

| Term | Definition |
|---|---|
| **ACK** | Acknowledgment message sent back to a sender confirming receipt of a message (common in HL7) |
| **Attachment** | Binary or large content extracted from a message and stored separately |
| **Batch** | A single inbound payload that is split into multiple discrete messages |
| **Channel** | The fundamental processing unit in OIE; defines a complete message pipeline from source to destination(s) |
| **Channel Group** | An organizational container for grouping related channels |
| **Channel Map** | A key-value store scoped to a single message across all connectors in a channel |
| **Channel Writer/Reader** | Internal connectors for routing messages between channels within the same OIE instance |
| **Code Template** | A reusable JavaScript function or code snippet shared across channels |
| **Configuration Map** | Server-wide key-value pairs accessible in all channel scripts; configured in Settings |
| **Connector** | A protocol-specific endpoint (source or destination) that handles message I/O |
| **Connector Map** | A key-value store scoped to a single message within a single connector |
| **Dashboard** | The main operational view showing deployed channels and their real-time statistics |
| **Data Pruner** | A background process that automatically removes old message data |
| **Data Type** | A message format parser/serializer (e.g., HL7 v2.x, XML, JSON, DICOM) |
| **Deploy** | Loading a channel into the engine so it can process messages |
| **Derby** | Apache Derby; the embedded database used by default for development |
| **Destination** | A connector that sends processed messages to an external system or another channel |
| **Destination Set** | The set of destinations that will process a given message; can be modified dynamically in scripts |
| **DICOM** | Digital Imaging and Communications in Medicine; a standard for medical imaging |
| **Donkey** | The internal message processing engine that handles queuing, storage, and message lifecycle |
| **E4X** | ECMAScript for XML; the XML syntax used in JavaScript transformers to access message fields |
| **EDI** | Electronic Data Interchange; a standard for business document exchange |
| **Extension** | A plugin that adds functionality to OIE (connectors, data types, plugins, etc.) |
| **FHIR** | Fast Healthcare Interoperability Resources; a modern healthcare data exchange standard |
| **Filter** | A set of rules that determine whether a message should be processed or rejected |
| **Global Channel Map** | A key-value store shared across all messages within a specific channel |
| **Global Map** | A key-value store shared across all channels on the server |
| **HL7** | Health Level Seven; a set of international standards for healthcare data exchange |
| **Iterator** | A transformer step type that loops over repeating segments or elements |
| **Mapper** | A transformer step type that extracts a value from the source message into a variable |
| **Message** | A discrete unit of data processed by a channel |
| **Message Browser** | The UI for searching, viewing, and managing individual messages |
| **Message Builder** | A transformer step type that sets a field in the outbound message |
| **Message Storage Mode** | Controls how much message content is persisted (Development, Production, Raw, Metadata Only, Disabled) |
| **MLLP** | Minimum Lower Layer Protocol; the standard framing protocol for HL7 over TCP |
| **NACK** | Negative Acknowledgment; indicates a message was received but could not be processed |
| **NCPDP** | National Council for Prescription Drug Programs; a pharmacy transaction standard |
| **OIE** | Open Integration Engine; this project |
| **Postprocessor** | A channel script that runs after all destinations have processed a message |
| **Preprocessor** | A channel script that runs before the source transformer, receiving the raw message |
| **Pruning** | The process of removing old message data from the database |
| **Queue** | A persistent buffer for messages waiting to be sent by a destination connector |
| **Response** | Data returned by a destination system after receiving a message |
| **Response Map** | A key-value store for destination responses, used to return responses to the source |
| **Rhino** | Mozilla Rhino; the JavaScript engine used by OIE |
| **Rule** | A single condition within a filter (Rule Builder, JavaScript, or External Script) |
| **Serialization** | Converting an internal message representation to an output format string |
| **Source** | The connector that receives inbound messages for a channel |
| **Source Map** | A key-value store populated by the source connector with metadata (e.g., remote address) |
| **Step** | A single operation within a transformer (Mapper, Message Builder, JavaScript, etc.) |
| **Tag** | A label applied to channels for organizational filtering |
| **Template** | The content pattern used by a destination connector, supporting variable substitution |
| **Transformer** | A chain of steps that modify message content as it passes through a connector |
| **Transmission Mode** | The framing protocol used by TCP connectors (MLLP, Basic, Frame) |
| **Undeploy** | Removing a channel from the engine so it stops processing messages |
| **VM** | Virtual Machine connector; the internal channel-to-channel routing mechanism |

[Back to Home](Home)
