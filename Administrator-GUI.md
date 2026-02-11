# Administrator GUI

The OIE Administrator is a Java Swing desktop application that provides full graphical management of the integration engine.

---

## Launching the Administrator

### From the Browser
1. Navigate to `http://<server-host>:8080`
2. Click **Launch Administrator**
3. The client libraries are downloaded automatically from the server

### From the Start Menu (Windows)
If installed via the platform installer, use the Start Menu shortcut.

### Direct Connection
The Administrator connects to the server over HTTPS on port `8443` by default.

---

## Login

- Enter the server URL: `https://<host>:8443`
- Provide your username and password
- Default credentials for new installations: `admin` / `admin`
- You will be prompted to change the default password on first login

---

## Main Interface Layout

```
┌─────────────────────────────────────────────────────────┐
│  Menu Bar                                                │
├───────────┬─────────────────────────────────────────────┤
│           │                                              │
│  Task     │              Main Content Area               │
│  Panel    │                                              │
│  (Left)   │  (Dashboard / Channels / Users / Settings /  │
│           │   Events / Extensions / etc.)                │
│           │                                              │
├───────────┴─────────────────────────────────────────────┤
│  Status Bar                                              │
└─────────────────────────────────────────────────────────┘
```

### Task Panel (Left Sidebar)
Context-sensitive action buttons. Changes based on the active view:
- **Dashboard**: Deploy, Start, Stop, Pause, Undeploy channels
- **Channels**: New Channel, Import, Export, Deploy
- **Users**: New User, Edit User, Delete User

---

## Key Views

### Dashboard
The default landing view showing all deployed channels with:
- Channel name and current state (Started/Stopped/Paused)
- Message statistics (Received, Filtered, Queued, Sent, Errored)
- Per-connector status breakdown
- Real-time statistics refresh

See [Dashboard and Monitoring](Dashboard-and-Monitoring) for details.

### Channels
Channel management view:
- List all channels organized by groups
- Create, edit, clone, import, export channels
- Set channel tags for filtering
- View channel dependencies
- Enable/disable channels
- Bulk operations (deploy all, export all)

### Message Browser
Accessed by double-clicking a channel in the Dashboard:
- Search messages by date range, status, content, and metadata
- View all message content types (Raw, Transformed, Encoded, Sent, Response)
- Reprocess messages
- Export messages
- View errors and stack traces

### Event Browser
System-wide event log:
- Filter by date, user, event type, and outcome
- View server events, user actions, and channel operations
- Export event data

### Users
User account management:
- Create, edit, and delete user accounts
- Change passwords
- View user details and roles

### Settings
Server configuration panels:
- **Server** - Server name, environment, default metadata columns
- **Administrator** - UI preferences, dashboard refresh interval
- **Configuration Map** - Key-value pairs accessible in channel scripts
- **Database Tasks** - Pending and completed database migration tasks
- **Resources** - Custom library resource management
- **Tags** - Channel tag management
- **Data Pruner** - Global pruning configuration

### Extensions
Plugin and connector management:
- View installed extensions
- Enable/disable extensions
- View extension metadata and properties

---

## Channel Editor

The channel editor is a multi-tabbed interface:

| Tab | Purpose |
|---|---|
| **Summary** | Channel name, description, data types, tags, dependencies |
| **Source** | Source connector configuration and settings |
| **Destinations** | Destination connectors, transformers, and filters |
| **Scripts** | Deploy, Undeploy, Preprocessor, Postprocessor, Attachment, Batch scripts |

### Transformer Editor
- Drag-and-drop step ordering
- Step types: Mapper, Message Builder, JavaScript, Iterator, External Script, XSLT
- Message tree panel showing parsed message structure
- Variable mapping with auto-completion
- Template panel for building outbound content

### Filter Editor
- Rule-based filtering
- Rule types: Rule Builder (GUI-based), JavaScript, External Script
- Boolean operators (AND/OR) between rules
- Accept or reject matching messages

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+S` | Save current channel |
| `Ctrl+Z` | Undo |
| `Ctrl+Y` | Redo |
| `Ctrl+F` | Find/Replace (in script editors) |
| `Ctrl+Space` | Auto-complete (in script editors) |

---

## Themes

The Administrator supports visual theming, including a **Dark Mode** theme. Theme selection may be configured in the Administrator settings.

[Back to Home](Home)
