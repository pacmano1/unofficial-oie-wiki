# Code Templates

Code templates are reusable code snippets and function libraries that can be shared across multiple channels. They reduce duplication and centralize common logic.

---

## Overview

Code templates are organized into **libraries**. Each library can be assigned to specific channels or made available globally.

```
Code Template Library
├── Code Template 1 (function)
├── Code Template 2 (function)
└── Code Template 3 (code snippet)
```

---

## Managing Code Templates

### Code Template Panel
Access via the main menu or navigation panel:
- View all libraries and their templates
- Create, edit, and delete templates
- Assign libraries to channels

### Creating a Library
1. Open the **Code Templates** panel
2. Click **New Library**
3. Name the library
4. Select which channels should have access to this library

### Creating a Code Template
1. Select a library
2. Click **New Code Template**
3. Configure:
   - **Name** - Descriptive name
   - **Type** - Function or Code (snippet)
   - **Context** - Where it can be used (see below)
   - **Code** - The JavaScript implementation

---

## Template Types

### Function
A named function that can be called from channel scripts:

```javascript
/**
 * Formats a patient name from HL7 components.
 * @param {string} lastName
 * @param {string} firstName
 * @return {string} Formatted name
 */
function formatPatientName(lastName, firstName) {
    return lastName.toUpperCase() + ', ' + firstName;
}
```

Usage in a channel transformer:
```javascript
var name = formatPatientName(
    msg['PID']['PID.5']['PID.5.1'].toString(),
    msg['PID']['PID.5']['PID.5.2'].toString()
);
```

### Code Snippet
A block of code that is included (injected) into the script context. Useful for shared initialization code or constants.

---

## Execution Contexts

Code templates can be restricted to specific contexts:

| Context | Description |
|---|---|
| **Global Scripts** | Deploy, undeploy, preprocessor, postprocessor |
| **Channel Scripts** | All channel-level scripts |
| **Source Connector** | Source filter and transformer |
| **Destination Connector** | Destination filter and transformer |
| **Response Transformer** | Response transformer scripts |

Select the appropriate contexts when creating a template. Templates are only available in their assigned contexts.

---

## Library Assignment

Each library must be explicitly assigned to channels:

1. Open the library properties
2. Check the channels that should have access
3. Use **Select All** to make the library globally available
4. Save and redeploy affected channels

Changes to code templates require **redeployment** of all channels that use the affected library.

---

## Import / Export

### GUI
- Right-click a library or template to export as XML
- Use **Import** to load from XML files

### CLI
```
codetemplate library list [includecodetemplates]
codetemplate list
codetemplate import "path" [force]
codetemplate library import "path" [force]
codetemplate export id|name "path"
codetemplate library export id|name|* "path"
codetemplate remove id|name
codetemplate library remove id|name|*
```

---

## Best Practices

1. **Organize by domain** - Group related functions into libraries (e.g., "HL7 Utilities", "Database Helpers", "Validation Functions")
2. **Document functions** - Include JSDoc comments with parameter and return type descriptions
3. **Minimize library scope** - Only assign libraries to channels that need them
4. **Version control** - Export templates regularly and store in source control
5. **Avoid side effects** - Keep functions pure where possible; avoid relying on global state
6. **Test independently** - Verify functions work correctly before deploying to production channels

---

## Reference List

The Administrator includes a built-in **Reference List** panel in the script editor that provides quick access to:
- Available code template functions
- Built-in utility functions (`ChannelUtil`, `DateUtil`, `FileUtil`, etc.)
- Variable references (`msg`, `tmp`, `channelMap`, etc.)
- Example code snippets

[Back to Home](Home)
