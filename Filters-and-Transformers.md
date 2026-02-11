# Filters and Transformers

Filters and transformers are the core message manipulation components in OIE. They determine which messages are processed and how they are modified before delivery.

---

## Filters

Filters determine whether a message should be processed by a connector. They evaluate rules and return an accept/reject decision.

### Filter Location
- **Source Filter** - Applied to all incoming messages before any destination processing
- **Destination Filter** - Applied per-destination; controls whether a specific destination processes the message

### Filter Rules

Rules are evaluated in order. Multiple rules can be combined with boolean operators.

#### Rule Builder
A GUI-based rule builder for common conditions:

| Condition | Description |
|---|---|
| **Equals** | Field equals a specific value |
| **Not Equals** | Field does not equal a value |
| **Contains** | Field contains a substring |
| **Does Not Contain** | Field does not contain a substring |
| **Exists** | Field exists (is not empty) |
| **Does Not Exist** | Field is empty or missing |
| **Regex Match** | Field matches a regular expression |

Example: Accept only ADT messages
- Field: `msg['MSH']['MSH.9']['MSH.9.1']`
- Condition: Equals
- Value: `ADT`

#### JavaScript Rule
Full JavaScript control over filtering:

```javascript
// Accept messages from specific sending facilities
var facility = msg['MSH']['MSH.4']['MSH.4.1'].toString();
var allowedFacilities = ['HOSP_A', 'HOSP_B', 'LAB_1'];
return allowedFacilities.indexOf(facility) !== -1;
```

Return `true` to accept the message, `false` to filter it out.

#### External Script Rule
Reference a JavaScript file on the filesystem:

```javascript
// Path to external script
/opt/oie/scripts/admission_filter.js
```

### Boolean Operators
Combine multiple rules with:
- **AND** - All rules must pass
- **OR** - At least one rule must pass

Rules can be grouped and nested for complex logic.

---

## Transformers

Transformers modify message content as it passes through a connector. Each connector has its own transformer chain.

### Transformer Chain
Steps execute in order from top to bottom. Each step can:
- Read values from the source message (`msg`)
- Write values to the outbound message (`tmp`)
- Store values in maps for later use
- Perform external lookups or calculations

### Transformer Steps

#### Mapper
Maps a value from the source message into a variable.

| Field | Description |
|---|---|
| **Variable** | The variable name (stored in `connectorMap`) |
| **Mapping** | Source expression (e.g., `msg['PID']['PID.3']['PID.3.1'].toString()`) |
| **Default Value** | Value to use if the mapping evaluates to empty |

The mapped variable is available as `${variableName}` in templates and subsequent steps.

#### Message Builder
Sets a field in the outbound message template.

| Field | Description |
|---|---|
| **Message Segment** | Target field in the outbound message (drag from message tree) |
| **Mapping** | Value expression or variable reference (`${variableName}`) |

#### JavaScript
Custom JavaScript transformation step:

```javascript
// Complex multi-field transformation
var dob = msg['PID']['PID.7']['PID.7.1'].toString();
if (dob.length === 8) {
    var year = dob.substring(0, 4);
    var month = dob.substring(4, 6);
    var day = dob.substring(6, 8);
    tmp['PID']['PID.7']['PID.7.1'] = year + '-' + month + '-' + day;
}

// Lookup patient in external database
var conn = DatabaseConnectionFactory.createDatabaseConnection(
    'org.postgresql.Driver', 'jdbc:postgresql://localhost/emr', 'user', 'pass'
);
var result = conn.executeCachedQuery(
    'SELECT mrn FROM patients WHERE last_name = ?',
    [msg['PID']['PID.5']['PID.5.1'].toString()]
);
if (result.next()) {
    tmp['PID']['PID.3']['PID.3.1'] = result.getString('mrn');
}
conn.close();
```

#### Iterator
Loops over repeating segments or elements:

1. Set the **Target** to the repeating element path
2. Add child steps (Mapper, Message Builder, JavaScript) inside the iterator
3. Use the iteration variable to access the current element

Example: Process all OBX segments:
- Target: `msg['OBX']`
- Child JavaScript step:
```javascript
var observationValue = iteratorElement['OBX.5']['OBX.5.1'].toString();
// Process each observation...
```

#### External Script
Loads and executes a JavaScript file from the filesystem.

#### XSLT
Applies an XSLT transformation to the XML representation of the message.

---

## Message Trees

The transformer editor displays two message trees:

| Tree | Description |
|---|---|
| **Inbound Message Tree** | Parsed structure of the incoming message (`msg`) |
| **Outbound Message Tree** | Structure of the outgoing message template (`tmp`) |

### Using Message Trees
- **Drag and drop** fields from the inbound tree to Mapper steps
- **Drag and drop** fields from the outbound tree to Message Builder steps
- Click on any field to see its path expression
- Load sample messages to populate the tree structure

### Loading Templates
1. In the template panel, paste a sample message
2. The message tree auto-populates with the parsed structure
3. This makes it easy to drag fields into transformer steps

---

## Execution Order

```
Source Filter Rules (in order)
    ↓ (if accepted)
Source Transformer Steps (in order)
    ↓
Destination 1 Filter Rules (in order)
    ↓ (if accepted)
Destination 1 Transformer Steps (in order)
    ↓
Destination 1 Connector sends message
    ↓
Destination 1 Response Transformer Steps (in order)
    ↓
Destination 2 Filter...
    ↓
... (repeat for each destination)
```

---

## Tips

- Use **Mapper** and **Message Builder** steps for simple field-level transformations (they are easier to read and maintain)
- Use **JavaScript** steps for complex logic, conditional transformations, and external lookups
- Keep transformer chains short and focused; break complex workflows into multiple channels
- Use **Code Templates** for reusable transformation functions
- Test transformers with representative sample messages
- Check the **Errors** column in the Message Browser when transformations fail

[Back to Home](Home)
