# Server Manager

The OIE Server Manager is a lightweight system tray application for managing the OIE server service on your local machine.

---

## Overview

The Server Manager provides a graphical interface for:
- Starting and stopping the OIE server service
- Viewing server status
- Configuring JVM heap size
- Launching the Administrator client
- Viewing server logs

---

## Installation

The Server Manager is optionally installed alongside the OIE server via the platform installer. It resides in the `manager-lib/` directory.

---

## Platform Support

The Server Manager includes platform-specific service controllers:

| Platform | Controller | Service Type |
|---|---|---|
| **Windows** | `WindowsServiceController` | Windows Service |
| **Linux** | `LinuxServiceController` | systemd / init.d |
| **macOS** | `MacServiceController` | launchd |

---

## Features

### System Tray
The Server Manager runs in the system tray and provides quick access to:
- **Start Service** - Start the OIE server
- **Stop Service** - Stop the OIE server
- **Restart Service** - Stop and restart
- **Status** - View current service state
- **Open Administrator** - Launch the Administrator GUI
- **View Logs** - Open the server log file

### Heap Size Configuration
Configure the maximum JVM heap size for the server:
1. Open the Server Manager dialog
2. Adjust the **Max Heap Size** setting
3. Restart the service for changes to take effect

---

## Command-Line Server Management

If the Server Manager is not installed, you can manage the server directly:

### Starting the Server

**Linux/macOS:**
```bash
./oieserver
```

**Windows:**
```powershell
.\oieserver.ps1
```

### Stopping the Server
- Send `SIGTERM` to the server process (Linux/macOS)
- Use `Ctrl+C` in the terminal
- Stop the system service

### Running as a Service

**Linux (systemd):**
```ini
[Unit]
Description=Open Integration Engine
After=network.target

[Service]
Type=simple
ExecStart=/opt/oie/oieserver
WorkingDirectory=/opt/oie
User=oie
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

**Windows:**
The installer can automatically register OIE as a Windows service.

[Back to Home](Home)
