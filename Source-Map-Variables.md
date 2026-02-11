# Source and Connector Map Variables

Each connector type automatically populates certain map variables with metadata about the connection and message. These variables are available in transformers, filters, and scripts.

---

## Source Connector Map Variables

### TCP Listener
| Variable | Description |
|---|---|
| `remoteAddress` | IP address of the sending system |
| `remotePort` | Port of the sending system |
| `localAddress` | Local IP address |
| `localPort` | Local listening port |

### HTTP Listener
| Variable | Description |
|---|---|
| `remoteAddress` | IP address of the HTTP client |
| `remotePort` | Port of the HTTP client |
| `localAddress` | Local IP address |
| `localPort` | Local listening port |
| `method` | HTTP method (GET, POST, PUT, DELETE) |
| `url` | Full request URL |
| `uri` | Request URI path |
| `protocol` | HTTP protocol version |
| `query` | Query string |
| `parameters` | Map of query/form parameters |
| `headers` | Map of HTTP request headers |
| `contentType` | Request Content-Type header value |

### File Reader
| Variable | Description |
|---|---|
| `originalFilename` | Original filename of the file being read |
| `fileDirectory` | Directory the file was read from |
| `fileSize` | Size of the file in bytes |
| `fileLastModified` | Last modified timestamp of the file |

### Database Reader
| Variable | Description |
|---|---|
| `resultCount` | Total number of rows returned by the query |

### Channel Reader
| Variable | Description |
|---|---|
| `sourceChannelId` | Channel ID of the sending channel |
| `sourceChannelIds` | Set of source channel IDs (if message was routed through multiple channels) |
| `sourceMessageId` | Message ID from the sending channel |
| `sourceMessageIds` | Set of source message IDs |
| `sourceConnectorName` | Name of the sending connector |

### JMS Listener
| Variable | Description |
|---|---|
| `headers` | JMS message headers/properties |

### DICOM Listener
| Variable | Description |
|---|---|
| `remoteAddress` | IP address of the sending DICOM node |
| `remotePort` | Port of the sending node |
| `localAddress` | Local IP address |
| `localPort` | Local listening port |

### Web Service Listener
| Variable | Description |
|---|---|
| `remoteAddress` | IP address of the SOAP client |

### JavaScript Reader
No automatic variables. The script can populate the source map manually using `sourceMap.put()`.

---

## Destination Connector Map Variables

### File Writer
| Variable | Description |
|---|---|
| `fileDirectory` | Directory the file was written to |
| `fileName` | Name of the written file |

### HTTP Sender
| Variable | Description |
|---|---|
| `responseStatusLine` | HTTP response status line |
| `responseStatusCode` | HTTP response status code |
| `responseHeaders` | Map of response headers |

### Channel Writer
| Variable | Description |
|---|---|
| `sourceChannelId` | Populated on the receiving channel's source map |
| `sourceMessageId` | Populated on the receiving channel's source map |

---

## Accessing Map Variables

### In Transformers/Filters (JavaScript)
```javascript
// Read source map variables
var remoteIP = sourceMap.get('remoteAddress');
var filename = sourceMap.get('originalFilename');

// Read connector map variables (destination)
var statusCode = connectorMap.get('responseStatusCode');
```

### In Templates (Velocity)
```
Remote address: ${remoteAddress}
Original file: ${originalFilename}
```

### In Postprocessor
```javascript
var sourceAddr = sourceMap.get('remoteAddress');
logger.info('Message from: ' + sourceAddr);
```

---

## Custom Map Variables

You can add your own variables to any map:

```javascript
// In a transformer
channelMap.put('processedTimestamp', DateUtil.getCurrentDate('yyyy-MM-dd HH:mm:ss'));
connectorMap.put('customFlag', 'URGENT');

// In a preprocessor
sourceMap.put('receivedAt', new Date().toString());
```

These custom variables are then available in subsequent steps and in Velocity templates using `${variableName}`.

[Back to Home](Home)
