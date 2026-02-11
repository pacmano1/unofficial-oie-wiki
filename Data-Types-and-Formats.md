# Data Types and Formats

OIE supports a wide range of healthcare and general-purpose data formats. Data types control how messages are parsed, serialized, and presented in the transformer editor.

---

## Supported Data Types

| Data Type | Description | Common Use |
|---|---|---|
| **HL7 v2.x** | Health Level Seven Version 2 pipe-delimited messages | ADT, ORM, ORU, SIU, and other clinical messages |
| **HL7 v3.x** | Health Level Seven Version 3 XML-based messages | CDA documents, clinical messaging |
| **FHIR** | Via HL7 v3 / JSON / XML data types | Modern healthcare interoperability |
| **DICOM** | Digital Imaging and Communications in Medicine | Medical imaging |
| **XML** | Generic XML documents | General-purpose structured data |
| **JSON** | JavaScript Object Notation | REST APIs, modern integrations |
| **EDI / X12** | Electronic Data Interchange | Insurance claims, eligibility |
| **NCPDP** | National Council for Prescription Drug Programs | Pharmacy transactions |
| **Delimited Text** | CSV, TSV, and custom-delimited flat files | Data imports/exports |
| **Raw** | Unstructured text/binary (no parsing) | Pass-through or custom handling |

---

## Data Type Configuration

Each connector in a channel has separate **inbound** and **outbound** data type settings.

### Setting Data Types
1. Open the channel in the editor
2. Click **Set Data Types** in the **Summary** tab
3. For each connector, configure:
   - **Inbound** data type: how the incoming message is parsed
   - **Outbound** data type: how the outgoing message is serialized

### Data Type Properties

Each data type exposes configurable properties:

#### HL7 v2.x Properties
| Property | Description |
|---|---|
| **Use strict parser** | Enforce strict HL7 structure validation |
| **Use strict validation** | Validate message against HL7 schema |
| **Segment delimiter** | Character(s) separating segments (default: `\r`) |
| **Convert line breaks** | Convert `\n` to `\r` |
| **Handle subcomponents** | Parse subcomponent delimiters |
| **Handle repetitions** | Parse repetition delimiters |

#### XML Properties
| Property | Description |
|---|---|
| **Strip namespaces** | Remove XML namespace prefixes |

#### JSON Properties
| Property | Description |
|---|---|
| No special properties | Parsed as standard JSON |

#### Delimited Text Properties
| Property | Description |
|---|---|
| **Column delimiter** | Field separator (default: `,`) |
| **Record delimiter** | Row separator (default: `\n`) |
| **Quote character** | Quote character for escaping |
| **Column names** | Optional header row or manual column names |

---

## Data Type Processing

### Serialization and Deserialization

When a message passes through a transformer:

1. **Inbound Deserialization** - Raw message string is parsed into an internal XML representation
2. **Transformer Processing** - Message is manipulated as an E4X XML object in JavaScript
3. **Outbound Serialization** - Internal representation is serialized back to the outbound format

This means regardless of the original format, transformers always work with a consistent XML-based internal representation.

### E4X XML Representation

In transformer JavaScript, messages are accessed using E4X syntax:

```javascript
// HL7 v2.x message access
var patientLastName = msg['PID']['PID.5']['PID.5.1'].toString();
var messageType = msg['MSH']['MSH.9']['MSH.9.1'].toString();

// XML message access
var value = msg['root']['element']['child'].toString();

// JSON (converted to XML internally)
var field = msg['root']['key'].toString();
```

### Cross-Format Transformation

A key capability is converting between data types within a single channel:

- **Source inbound**: HL7 v2.x (received from clinical system)
- **Source outbound**: XML (intermediate format)
- **Destination inbound**: XML
- **Destination outbound**: JSON (sent to REST API)

The engine handles serialization/deserialization automatically at each boundary.

---

## Working with Specific Formats

### HL7 v2.x

The most common format in healthcare integration. OIE includes:
- Full segment and field-level parsing
- Automatic ACK/NACK generation
- Support for all HL7 v2.x versions (2.1 through 2.8+)
- Repeating fields, components, and subcomponents

### DICOM

For medical imaging workflows:
- Parse DICOM metadata tags
- Extract/embed image data
- Route studies between PACS, modalities, and viewers
- Dedicated `DICOMUtil` scripting class

### EDI / X12

For insurance and claims processing:
- Segment-level parsing with configurable delimiters
- Support for common transaction sets (837, 835, 270/271, etc.)

### JSON

Native JSON support with:
- Automatic conversion to/from internal XML representation
- Ideal for REST API integrations
- Use `JSON.parse()` / `JSON.stringify()` in scripts for raw JSON manipulation

---

## Custom Data Types

The data type system is extensible. Custom data types can be developed as plugins that provide:
- A custom serializer/deserializer
- A custom message tree representation in the UI
- Custom properties dialogs

[Back to Home](Home)
