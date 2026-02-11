# JavaScript Scripting Reference

OIE uses the **Mozilla Rhino** JavaScript engine (configured for ES6 by default) for all scripting. JavaScript is used in transformers, filters, channel scripts, and connector scripts.

---

## Script Contexts

| Context | When It Executes | Available Objects |
|---|---|---|
| **Preprocessor** | Before source transformer | `message` (raw string), `sourceMap`, `globalMap`, `globalChannelMap`, `configurationMap` |
| **Source Filter** | During source processing | `msg` (parsed XML), `sourceMap`, maps, `connectorMessage` |
| **Source Transformer** | During source processing | `msg`, `tmp`, `sourceMap`, maps, `connectorMessage` |
| **Destination Filter** | Per destination | `msg`, maps, `connectorMessage`, `destinationSet` |
| **Destination Transformer** | Per destination | `msg`, `tmp`, maps, `connectorMessage`, `responseMap` |
| **Response Transformer** | After destination send | `msg`, `response`, maps |
| **Postprocessor** | After all destinations | `message` (Message object), maps, `response` |
| **Deploy Script** | On channel deploy | `globalMap`, `globalChannelMap`, `configurationMap` |
| **Undeploy Script** | On channel undeploy | `globalMap`, `globalChannelMap`, `configurationMap` |
| **Attachment Script** | During source processing | `message` (raw string), maps |
| **Batch Script** | During source processing | `message` (raw string), maps |

---

## Core Objects

### Message (`msg`)
The parsed message as an E4X XML object. Available in transformers and filters.

```javascript
// HL7 v2.x field access
var lastName = msg['PID']['PID.5']['PID.5.1'].toString();
var messageType = msg['MSH']['MSH.9']['MSH.9.1'].toString();

// Set a field value
msg['PID']['PID.5']['PID.5.1'] = 'NEWNAME';

// XML field access
var value = msg['root']['element'].toString();

// Iterate repeating segments
for each (var obx in msg['OBX']) {
    var result = obx['OBX.5']['OBX.5.1'].toString();
}
```

### Template (`tmp`)
The outbound message template (also E4X XML). Modify this to change what is sent to the destination.

```javascript
tmp['MSH']['MSH.3']['MSH.3.1'] = 'NEW_SENDING_APP';
tmp['MSH']['MSH.5']['MSH.5.1'] = 'NEW_RECEIVING_APP';
```

### Connector Message (`connectorMessage`)
The full `ConnectorMessage` Java object with access to all content types, metadata, and status.

```javascript
var rawContent = connectorMessage.getRawData();
var channelId = connectorMessage.getChannelId();
var messageId = connectorMessage.getMessageId();
```

---

## Maps

Maps provide shared variable storage across the message lifecycle.

| Map | Scope | Access |
|---|---|---|
| `sourceMap` | Current message, source only | Read/write in source scripts |
| `connectorMap` | Current message, current connector | Read/write in current connector scripts |
| `channelMap` | Current message, all connectors | Read/write in all channel scripts |
| `responseMap` | Current message, response data | Read/write; stores destination responses |
| `globalChannelMap` | All messages in this channel | Persists across messages |
| `globalMap` | All channels on the server | Persists across channels |
| `configurationMap` | Server-wide configuration | Read-only; set in server settings |

### Usage
```javascript
// Write to a map
channelMap.put('patientId', msg['PID']['PID.3']['PID.3.1'].toString());

// Read from a map
var patientId = channelMap.get('patientId');

// Access in templates using variable syntax
// ${patientId}

// Global map (persists across messages)
globalMap.put('lastProcessedTime', new Date().toString());
var lastTime = globalMap.get('lastProcessedTime');

// Configuration map (read-only, set in Settings)
var apiKey = configurationMap.get('externalApiKey');
```

---

## Utility Classes

These Java classes are available in all script contexts:

### ChannelUtil
```javascript
// Get channel name/ID
var name = ChannelUtil.getDeployedChannelName(channelId);
var id = ChannelUtil.getDeployedChannelId(channelName);

// Get channel status
var status = ChannelUtil.getConnectorState(channelId, metaDataId);
```

### DateUtil
```javascript
// Format dates
var formatted = DateUtil.formatDate('yyyyMMddHHmmss', date);

// Parse date strings
var date = DateUtil.getDate('yyyyMMddHHmmss', '20240101120000');

// Convert between formats
var result = DateUtil.convertDate('yyyyMMddHHmmss', 'yyyy-MM-dd', '20240101120000');
```

### FileUtil
```javascript
// Read file contents
var content = FileUtil.read('/path/to/file.txt');

// Write file
FileUtil.write('/path/to/output.txt', false, content); // false = don't append
```

### DatabaseConnectionFactory
```javascript
// Create a database connection
var conn = DatabaseConnectionFactory.createDatabaseConnection(driver, url, user, pass);
var result = conn.executeCachedQuery('SELECT * FROM patients WHERE id = ?', [patientId]);
while (result.next()) {
    var name = result.getString('name');
}
conn.close();
```

### HTTPUtil
```javascript
// Utility methods for HTTP operations
var headers = HTTPUtil.getTableFromHTTPHeaders(connectorMessage.getConnectorMapContent());
```

### SMTPConnectionFactory
```javascript
// Send email from scripts
var smtp = SMTPConnectionFactory.createSMTPConnection();
smtp.send('to@example.com', 'cc@example.com', 'from@example.com', 'Subject', 'Body');
```

### VMRouter
```javascript
// Route messages to other channels
var router = new VMRouter();
var response = router.routeMessage('targetChannelName', 'message content');
router.routeMessageByChannelId('channel-uuid', 'message content');
```

### AttachmentUtil
```javascript
// Access message attachments
var attachments = AttachmentUtil.getMessageAttachments(connectorMessage);
```

### SerializerFactory
```javascript
// Create data type serializers
var serializer = SerializerFactory.getSerializer('HL7V2');
var xml = serializer.toXML('MSH|^~\\&|...');
var hl7 = serializer.fromXML(xmlString);
```

### ResponseFactory
```javascript
// Create custom responses
var response = ResponseFactory.getSentResponse('Custom ACK message');
var errorResponse = ResponseFactory.getErrorResponse('Processing failed');
```

### DICOMUtil
```javascript
// DICOM-specific utilities
var tags = DICOMUtil.getDICOMTags(connectorMessage);
```

### HashUtil
```javascript
// Generate hashes
var md5 = HashUtil.generate('MD5', data);
var sha256 = HashUtil.generate('SHA-256', data);
```

### EncryptionUtil
```javascript
// Encrypt/decrypt data
var encrypted = EncryptionUtil.encrypt(data);
var decrypted = EncryptionUtil.decrypt(encrypted);
```

---

## Logging

```javascript
// Logger is available in all contexts
logger.info('Processing message: ' + connectorMessage.getMessageId());
logger.debug('Patient name: ' + patientName);
logger.warn('Missing field PID.5');
logger.error('Failed to process message', e);
```

Logs are written according to the `log4j2.properties` configuration. Channel-specific loggers:
- `transformer` - Transformer script logs
- `filter` - Filter script logs
- `preprocessor` - Preprocessor logs
- `postprocessor` - Postprocessor logs
- `deploy` / `undeploy` - Deploy/undeploy script logs
- `db-connector` / `js-connector` - Connector-specific logs

---

## Destination Set

Control which destinations process the current message:

```javascript
// Remove a destination (skip it)
destinationSet.remove([destinationName]);

// Remove all except specified
destinationSet.removeAllExcept([destinationName]);

// Remove all destinations
destinationSet.removeAll();
```

---

## Java Interop

Since Rhino runs on the JVM, you can access Java classes directly:

```javascript
// Import Java classes
var ArrayList = java.util.ArrayList;
var list = new ArrayList();
list.add('item1');

// Use Java packages
var File = java.io.File;
var file = new File('/path/to/file');

// Access static methods
var uuid = java.util.UUID.randomUUID().toString();
var base64 = java.util.Base64.getEncoder().encodeToString(bytes);
```

[Back to Home](Home)
