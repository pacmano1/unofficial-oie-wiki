# Velocity Variable Replacement

OIE uses **Apache Velocity** syntax for variable substitution in connector templates, file names, directory paths, and other text fields throughout the channel editor. This is distinct from JavaScript and provides a lightweight way to insert dynamic values.

---

## Basic Syntax

Variables stored in maps are referenced using the `${}` syntax:

```
${variableName}
```

This pulls the value from the map lookup sequence (see [Variable Map Lookup Sequence](#variable-map-lookup-sequence) below).

### Examples

**In a File Writer filename:**
```
message_${message.messageId}_${date.get('yyyyMMdd')}.hl7
```

**In an HTTP Sender URL:**
```
https://api.example.com/patients/${patientId}/records
```

**In a destination template:**
```
Patient: ${patientLastName}, ${patientFirstName}
MRN: ${medicalRecordNumber}
```

---

## Conditional Statements

Velocity supports `#if` / `#else` / `#elseif` / `#end` for conditional logic within templates:

```velocity
#if(${gender} == "M")
Male
#elseif(${gender} == "F")
Female
#else
Unknown
#end
```

This is useful when the output format varies based on message content without needing a full JavaScript transformer step.

---

## For Loops

Velocity supports `#foreach` loops for iterating over collections:

```velocity
#foreach($item in ${itemList})
Item: ${item}
#end
```

---

## Variable Map Lookup Sequence

When you reference `${variableName}`, OIE searches the following maps in order, returning the first match:

1. **Connector Map** - Variables scoped to the current connector
2. **Channel Map** - Variables scoped to the current message across all connectors
3. **Source Map** - Variables populated by the source connector (e.g., remote address)
4. **Response Map** - Destination response data
5. **Global Channel Map** - Variables shared across all messages in this channel
6. **Global Map** - Variables shared across all channels
7. **Configuration Map** - Server-wide key-value pairs from Settings

If no match is found, the literal string `${variableName}` is output.

---

## Standard Variables and Templates

Several built-in variables are always available in destination templates:

| Variable | Description |
|---|---|
| `${message.messageId}` | The current message ID |
| `${message.encodedData}` | The encoded (outbound) message content |
| `${message.rawData}` | The raw inbound message |
| `${CHANNELID}` | The current channel's UUID |
| `${CHANNELNAME}` | The current channel's name |
| `${message.destinationName}` | The name of the current destination |

### Date Variables

```
${date.get('yyyyMMdd')}          → 20240101
${date.get('yyyy-MM-dd HH:mm')} → 2024-01-01 12:00
${date.get('yyyyMMddHHmmss')}   → 20240101120000
```

---

## Where Velocity is Used

| Location | Example |
|---|---|
| **Destination templates** | Message body content |
| **File Writer directory/filename** | `/output/${CHANNELNAME}/${date.get('yyyyMMdd')}/` |
| **HTTP Sender URL** | `https://host/api/${patientId}` |
| **HTTP headers/parameters** | Header values with dynamic content |
| **Email subject/body** | SMTP sender fields |
| **Database Writer SQL** | Parameterized INSERT statements |
| **Alert templates** | Alert notification content |

---

## Velocity vs. JavaScript

| Aspect | Velocity `${}` | JavaScript |
|---|---|---|
| **Use case** | Simple variable insertion in templates | Complex logic and transformations |
| **Syntax** | `${varName}` | `channelMap.get('varName')` |
| **Where** | Template fields, filenames, URLs | Transformer/filter steps, scripts |
| **Conditionals** | `#if/#else/#end` | `if/else` |
| **Loops** | `#foreach` | `for`, `while` |
| **Power** | Lightweight substitution | Full programming language |

Use Velocity for simple value insertion. Use JavaScript transformer steps for complex logic, calculations, and external lookups.

[Back to Home](Home)
