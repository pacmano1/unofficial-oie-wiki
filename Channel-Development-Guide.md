# Channel Development Guide

This guide covers the complete workflow for designing, building, testing, and deploying channels in OIE.

---

## Planning a Channel

Before building, identify:

1. **Source system** - Where messages come from
2. **Message format** - HL7 v2.x, XML, JSON, etc.
3. **Protocol** - TCP/MLLP, HTTP, file, database, etc.
4. **Transformation requirements** - What needs to change
5. **Destination system(s)** - Where messages go
6. **Error handling** - What happens when delivery fails
7. **Volume** - Expected message throughput

---

## Creating a Channel

### Using the Channel Wizard
1. Click **New Channel** in the Channels view
2. The wizard walks you through:
   - Channel name and description
   - Source connector type and settings
   - Destination connector type and settings
3. Click **Finish** to create the channel

### Manual Creation
1. Click **New Channel**
2. Configure each tab manually (Summary, Source, Destinations, Scripts)
3. Save with `Ctrl+S`

---

## Setting Data Types

1. In the **Summary** tab, click **Set Data Types**
2. For each connector, set:
   - **Inbound** data type (how the received message is parsed)
   - **Outbound** data type (how the message is serialized for output)

Common patterns:
| Scenario | Source Inbound | Source Outbound | Dest Inbound | Dest Outbound |
|---|---|---|---|---|
| HL7-to-HL7 | HL7 v2.x | HL7 v2.x | HL7 v2.x | HL7 v2.x |
| HL7-to-JSON | HL7 v2.x | HL7 v2.x | HL7 v2.x | JSON |
| File CSV to DB | Delimited | XML | XML | Raw |
| JSON API relay | JSON | JSON | JSON | JSON |

---

## Building Transformers

### Step Types

| Type | Use Case |
|---|---|
| **Mapper** | Extract a value from the source message and store it in a variable |
| **Message Builder** | Set a value in the outbound message from a variable or expression |
| **JavaScript** | Custom transformation logic |
| **Iterator** | Loop over repeating segments/elements |
| **External Script** | Reference an external JavaScript file |
| **XSLT** | Apply an XSLT stylesheet |

### Mapper Step
1. Add a Mapper step to the transformer
2. Set the **Variable** name (e.g., `patientName`)
3. Drag a field from the **Message Tree** to the **Mapping** field, or type the path manually:
   ```
   msg['PID']['PID.5']['PID.5.1'].toString()
   ```
4. The variable is now available in `channelMap` as `${patientName}`

### Message Builder Step
1. Add a Message Builder step
2. Drag a field from the outbound **Message Tree** to the **Field** box
3. Set the **Mapping** to a variable reference or expression:
   ```
   ${patientName}
   ```

### JavaScript Step
For complex logic that goes beyond simple mapping:

```javascript
// Conditional transformation
var gender = msg['PID']['PID.8']['PID.8.1'].toString();
if (gender === 'M') {
    tmp['PID']['PID.8']['PID.8.1'] = 'Male';
} else if (gender === 'F') {
    tmp['PID']['PID.8']['PID.8.1'] = 'Female';
} else {
    tmp['PID']['PID.8']['PID.8.1'] = 'Unknown';
}
```

### Iterator Step
For processing repeating segments:

1. Add an Iterator step
2. Set the **Target** to the repeating element (e.g., `msg['OBX']`)
3. Add child steps inside the iterator
4. Use `iteratorElement` to access the current iteration

---

## Building Filters

### Filter Rules

| Rule Type | Use Case |
|---|---|
| **Rule Builder** | GUI-based conditions (field equals, contains, regex, etc.) |
| **JavaScript** | Custom filtering logic (return `true` to accept, `false` to reject) |
| **External Script** | Reference an external JavaScript file |

### Rule Builder
1. Add a Rule Builder rule
2. Set the **Field** (drag from message tree)
3. Set the **Condition** (equals, not equals, contains, regex, etc.)
4. Set the **Value** to compare against

### JavaScript Filter
```javascript
// Accept only ADT^A01 messages
var messageType = msg['MSH']['MSH.9']['MSH.9.1'].toString();
var triggerEvent = msg['MSH']['MSH.9']['MSH.9.2'].toString();
return (messageType === 'ADT' && triggerEvent === 'A01');
```

### Combining Rules
- Use **AND** / **OR** operators between rules
- Rules evaluate in order; short-circuit evaluation applies

---

## Channel Scripts

### Preprocessor
Modify raw message content before parsing:

```javascript
// Fix common HL7 formatting issues
message = message.replace(/\n/g, '\r');
return message;
```

### Deploy Script
Initialize resources when the channel starts:

```javascript
// Set up a shared database connection
var conn = DatabaseConnectionFactory.createDatabaseConnection(
    'org.postgresql.Driver',
    'jdbc:postgresql://localhost/mydb',
    'user', 'password'
);
globalChannelMap.put('dbConnection', conn);
```

### Undeploy Script
Clean up resources when the channel stops:

```javascript
// Close the database connection
var conn = globalChannelMap.get('dbConnection');
if (conn) conn.close();
```

### Postprocessor
Final processing after all destinations:

```javascript
// Log completion status
var status = message.getMergedConnectorMessage().getStatus();
logger.info('Message ' + message.getMessageId() + ' completed with status: ' + status);
return;
```

---

## Response Handling

### Auto-Generated Responses
For HL7, the source can auto-generate ACK/NACK responses based on processing results.

### Response from Destination
Configure the source to return the response from a specific destination:
1. In **Source Settings**, set **Response** to the desired destination
2. The destination's response (e.g., an ACK from a downstream system) is returned to the sender

### Custom Responses
Build custom responses in the postprocessor:

```javascript
var ack = ResponseFactory.getSentResponse('Custom ACK: Message processed');
responseMap.put('customResponse', ack);
```

---

## Testing Channels

### Send Test Messages
1. Deploy the channel
2. In the Dashboard, right-click the channel and select **Send Message**
3. Paste or type a test message
4. Click **Process Message**
5. Check the Message Browser for results

### Debug Mode
Deploy a channel in **Debug Mode** to step through transformer and filter execution.

### Message Storage
Set **Message Storage** to **Development** during testing to capture all content types. Switch to **Production** or **Raw** for production deployments to reduce storage.

---

## Best Practices

1. **Name channels descriptively** - Include source system, destination, and message type
2. **Use channel tags** - Organize channels by department, system, or type
3. **Use code templates** - Reuse common transformation logic
4. **Set appropriate message storage** - Development for testing, Production for live
5. **Configure pruning** - Avoid unbounded database growth
6. **Use the configuration map** - Store environment-specific values (URLs, credentials) separate from channel logic
7. **Test with realistic data** - Use sanitized copies of production messages
8. **Document transformations** - Use channel descriptions and code comments
9. **Version control** - Export channels as XML and store in source control
10. **Use channel dependencies** - Ensure correct deployment order for multi-channel workflows

[Back to Home](Home)
