# Debugger

The OIE Debugger allows you to step through channel processing interactively, inspecting message content and variable state at each filter rule and transformer step.

---

## Overview

The Debugger is a development tool for troubleshooting channel logic. When a channel is deployed in debug mode, you can:

- Set breakpoints on filter rules and transformer steps
- Step through execution one step at a time
- Inspect message content (`msg`, `tmp`) at each step
- View variable map contents (channelMap, connectorMap, etc.)
- Evaluate expressions in real time

---

## Deploying in Debug Mode

1. In the **Channels** view, select the channel
2. Click **Deploy Channel in Debug Mode** (or use the channel task menu)
3. The channel deploys and the Debugger window opens

> **Note:** Debug mode affects performance. Only use it during development, not in production.

---

## Debugger Window

The Debugger interface provides:

### Coding Area
- Displays the current filter rule or transformer step code
- Highlights the currently executing line
- Read-only during debug execution

### Debug Controls

| Control | Action |
|---|---|
| **Step Into** | Execute the current step and move to the next |
| **Step Over** | Execute the current step without entering sub-functions |
| **Resume** | Continue execution until the next breakpoint |
| **Stop** | Halt debug execution |

### Variable Inspector
- View the contents of all variable maps at the current execution point
- See `msg` and `tmp` XML content
- Inspect `channelMap`, `connectorMap`, `sourceMap`, and other maps

---

## Debugger Menus

### File Menu
- Open and close debug sessions
- Save debug session information

### Edit Menu
- Copy content from the debug view
- Search within the code display

### Debug Menu
- Step controls (Step Into, Step Over, Resume, Stop)
- Breakpoint management
- Reset debug session

### Window Menu
- Arrange debug window panels
- Show/hide variable inspector and other panes

---

## Setting Breakpoints

1. In the filter/transformer editor, click on the gutter (left margin) next to a rule or step
2. A breakpoint indicator appears
3. When the debugger reaches that point, execution pauses
4. Inspect the state and use step controls to continue

---

## JVM Configuration

To use the debugger, the server JVM may need specific options enabled. Ensure the debug-related module options are included in the server's vmoptions files.

---

## Tips

- Deploy only one channel in debug mode at a time for clarity
- Use debug mode to verify transformer step order and variable values
- Check filter rule evaluation to confirm accept/reject behavior
- The debugger is especially useful for complex JavaScript steps
- Remember to redeploy in normal mode when finished debugging

[Back to Home](Home)
