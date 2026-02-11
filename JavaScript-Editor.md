# JavaScript Editor

The JavaScript Editor is the built-in code editor used throughout OIE for writing filter rules, transformer steps, channel scripts, and code templates. It provides syntax highlighting, auto-completion, and other features to aid development.

---

## Features

### Syntax Highlighting
The editor provides color-coded syntax highlighting for JavaScript, making code easier to read and write.

### Auto-Completion
Press `Ctrl+Space` to open the auto-completion popup. It provides suggestions for:
- Built-in JavaScript functions and objects
- OIE utility classes and methods (`ChannelUtil`, `DateUtil`, `FileUtil`, etc.)
- Variable map methods (`channelMap.put`, `globalMap.get`, etc.)
- Code template functions available in the current context
- E4X XML access patterns

### Find and Replace
`Ctrl+F` opens the Find and Replace dialog:
- **Find**: Search for text in the current script
- **Replace**: Find and replace text
- **Regex**: Use regular expressions for search patterns
- **Case sensitive**: Toggle case sensitivity
- **Wrap**: Search wraps around the end of the file

### Code Folding
Collapse and expand code blocks:
- Click the fold icons in the gutter (left margin)
- Collapse functions, if/else blocks, loops, and other block structures
- Useful for navigating long scripts

---

## Context Menu

Right-click in the editor to access:

| Option | Description |
|---|---|
| **Cut / Copy / Paste** | Standard clipboard operations |
| **Select All** | Select all code in the editor |
| **Undo / Redo** | Undo/redo recent changes |
| **Find/Replace** | Open the find/replace dialog |
| **Go to Line** | Jump to a specific line number |
| **Toggle Comment** | Comment/uncomment selected lines |

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+Space` | Open auto-completion popup |
| `Ctrl+F` | Find and Replace |
| `Ctrl+Z` | Undo |
| `Ctrl+Y` | Redo |
| `Ctrl+/` | Toggle line comment |
| `Ctrl+G` | Go to line number |
| `Tab` | Indent selected lines |
| `Shift+Tab` | Unindent selected lines |
| `Ctrl+A` | Select all |

### Remapping Shortcut Keys
Some keyboard shortcuts can be customized. Check the Administrator settings for code editor preferences.

---

## Reference Tab

Adjacent to the script editor, the **Reference Tab** provides quick access to:

### Reference List
A categorized list of available functions and code snippets:
- **User Utility Functions** - Code template functions
- **Built-In Functions** - OIE utility methods
- **Date Functions** - Date formatting and conversion
- **Database Functions** - Database connection helpers
- **Message Functions** - Message access and manipulation
- **Channel Functions** - Channel utility methods
- **Map Functions** - Variable map operations

Click an item to see its description and insert it into the editor.

### Available Variables
Lists variables available in the current script context:
- `msg`, `tmp` (in transformers)
- `message` (in pre/postprocessors)
- Map objects (`channelMap`, `sourceMap`, `globalMap`, etc.)
- `logger`
- `connectorMessage`

---

## Code Editor Preferences

Configure editor behavior in **Settings > Administrator > Code Editor Preferences**:

| Preference | Description |
|---|---|
| **Tab size** | Number of spaces per tab |
| **Use soft tabs** | Use spaces instead of tab characters |
| **Show line numbers** | Display line numbers in the gutter |
| **Highlight current line** | Highlight the line where the cursor is |
| **Auto-indent** | Automatically indent new lines |
| **Word wrap** | Wrap long lines |

---

## Tips

1. **Use auto-completion liberally** - `Ctrl+Space` helps discover available methods
2. **Reference Tab is your friend** - Browse available functions without leaving the editor
3. **Comment your code** - Use `//` for single-line and `/* */` for multi-line comments
4. **Use `logger.debug()`** for troubleshooting - Log variable values during development
5. **View Generated Script** - In the filter/transformer editor, click "View Generated Script" to see the complete JavaScript that will execute, including all steps combined

[Back to Home](Home)
