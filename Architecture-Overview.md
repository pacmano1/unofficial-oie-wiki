# Architecture Overview

The Open Integration Engine is a multi-tier Java application consisting of several major modules that work together to process, transform, and route healthcare messages.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Administrator GUI                     │
│              (Java Swing Desktop Client)                 │
└──────────────────────┬──────────────────────────────────┘
                       │ HTTPS (REST API)
┌──────────────────────▼──────────────────────────────────┐
│                     OIE Server                           │
│  ┌─────────────┐ ┌──────────────┐ ┌──────────────────┐  │
│  │  REST API    │ │  Web Server  │ │  Web Admin       │  │
│  │  (Servlets)  │ │  (Jetty)     │ │  (JSP/Stripes)   │  │
│  └──────┬──────┘ └──────────────┘ └──────────────────┘  │
│         │                                                │
│  ┌──────▼──────────────────────────────────────────────┐ │
│  │              Controller Layer                        │ │
│  │  Channel | Config | User | Alert | Engine | ...      │ │
│  └──────┬──────────────────────────────────────────────┘ │
│         │                                                │
│  ┌──────▼──────────────────────────────────────────────┐ │
│  │            Donkey Message Engine                      │ │
│  │  Message Processing | Queuing | Storage | Recovery   │ │
│  └──────┬──────────────────────────────────────────────┘ │
│         │                                                │
│  ┌──────▼──────────────────────────────────────────────┐ │
│  │              Extensions / Plugins                     │ │
│  │  Connectors | Data Types | Filters | Transformers    │ │
│  └─────────────────────────────────────────────────────┘ │
│         │                                                │
│  ┌──────▼──────────────────────────────────────────────┐ │
│  │              Database Layer                           │ │
│  │  Derby | PostgreSQL | MySQL | Oracle | SQL Server     │ │
│  └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## Module Breakdown

### Server (`server/`)

The core OIE server application. Key components:

| Package | Responsibility |
|---|---|
| `server.Mirth` | Main server entry point and lifecycle management |
| `server.MirthWebServer` | Embedded Jetty web server |
| `server.controllers/` | Business logic controllers (Channel, User, Alert, Configuration, etc.) |
| `server.api/servlets/` | REST API endpoints |
| `server.channel/` | Channel deployment and lifecycle |
| `server.transformers/` | JavaScript-based message transformation |
| `server.userutil/` | Scripting API classes exposed to user JavaScript |
| `server.migration/` | Database schema migration |
| `server.mybatis/` | MyBatis SQL mapping configuration |

### Donkey Engine (`donkey/`)

The message processing engine responsible for:

| Component | Responsibility |
|---|---|
| `donkey.model/` | Core data model (messages, channels, connectors) |
| `donkey.server/` | Message queuing, dispatching, storage, and recovery |
| `donkey.util/` | Serialization, cryptography, and utility classes |

Donkey handles the low-level mechanics of message persistence, queue management, and channel thread coordination.

### Client / Administrator (`client/`)

A Java Swing desktop application providing the graphical administration interface:

| Package | Responsibility |
|---|---|
| `client.ui/` | Main UI frames, panels, and dialogs |
| `client.ui.browsers/` | Message and Event browsers |
| `client.ui.editors/` | Transformer and filter editors |
| `client.ui.panels/` | Connector configuration panels |
| `client.ui.components/` | Reusable UI components |
| `client.ui.codetemplate/` | Code template management UI |
| `client.ui.alert/` | Alert configuration UI |

### Command Line Interface (`command/`)

A terminal-based client (`CommandLineInterface.java`) for scripting and headless administration. Connects to the server over the same REST API as the GUI.

### Server Manager (`manager/`)

A lightweight system tray application for managing the OIE service. Provides platform-specific service controllers:
- `WindowsServiceController`
- `LinuxServiceController`
- `MacServiceController`

### Web Admin (`webadmin/`)

A web-based dashboard (JSP/Stripes framework) providing basic channel status monitoring via a browser.

### Build System (`build/`)

Apache Ant build configuration for compiling, packaging, and assembling the distribution.

---

## Extension Architecture

OIE uses a plugin architecture where connectors, data types, filters, transformers, and other features are loaded as extensions.

Extensions reside in the `extensions/` directory and are discovered at server startup by the `ExtensionLoader`. Each extension has:

- A metadata descriptor (XML) defining its type, name, and dependencies
- Server-side classes (loaded into the server JVM)
- Client-side classes (downloaded to the Administrator on connection)

Extension types include:
- **Connectors** - Source and destination communication protocols
- **Data Type Plugins** - Message format parsers and serializers
- **Server Plugins** - Background services and event handlers
- **Transmission Mode Providers** - Frame-level protocol handling (e.g., MLLP)

---

## Communication Flow

1. **Administrator GUI** communicates with the **Server** over HTTPS using a RESTful API
2. The **CLI** uses the same REST API client library
3. The **Server** manages channel lifecycle through **Controllers**
4. **Controllers** delegate message processing to the **Donkey Engine**
5. The **Donkey Engine** manages threads, queues, and database persistence
6. **Connectors** handle protocol-specific I/O (TCP, HTTP, File, Database, etc.)
7. **Transformers** and **Filters** execute user-defined JavaScript

[Back to Home](Home)
