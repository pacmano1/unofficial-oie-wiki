# Installation Guide

## System Requirements

### Minimum Requirements
| Component | Requirement |
|---|---|
| **Java** | JDK 17 or later |
| **RAM** | 512 MB minimum (1 GB+ recommended) |
| **Disk** | 500 MB for installation; additional for message storage |
| **OS** | Windows, Linux (RPM/DEB), macOS |

### Supported Databases
| Database | Notes |
|---|---|
| **Apache Derby** | Embedded, default for evaluation/development |
| **PostgreSQL** | Recommended for production |
| **MySQL** | Production-ready |
| **Oracle** | Production-ready |
| **SQL Server** | Via jTDS or Microsoft JDBC driver |
| **SQLite** | Lightweight alternative |

---

## Installation Methods

### 1. Platform Installers

Pre-built installers are available for each operating system:

| Platform | Format |
|---|---|
| Windows | `.exe` installer |
| Linux | `.rpm`, `.sh` |
| macOS | `.dmg` |

Download the latest release from the [Releases page](https://github.com/OpenIntegrationEngine/engine/releases).

The installer provides options to:
- Install the OIE Server
- Install as a system service (runs in the background)
- Install the Server Manager GUI
- Install the Command Line Interface (CLI)

### 2. Docker

```bash
docker pull openintegrationengine/oie-server:latest
docker run -d -p 8080:8080 -p 8443:8443 openintegrationengine/oie-server
```

See [Docker Hub](https://hub.docker.com/u/openintegrationengine) for available images and tags.

### 3. Pre-packaged Distributions

Pre-packaged archives are available:
- **Windows**: `.zip`
- **Linux**: `.tar.gz`
- **macOS**: `.tar.gz`

Extract and run directly without a formal installer.

### 4. Building from Source

```bash
# Clone the repository
git clone https://github.com/OpenIntegrationEngine/engine.git
cd engine

# Install tooling via SDKMAN (recommended)
sdk env install

# Build with Ant
ant
```

The project uses **Apache Ant** as its build system and requires **Java 17+**. See [CONTRIBUTING.md](https://github.com/OpenIntegrationEngine/engine/blob/main/CONTRIBUTING.md) for details.

---

## Directory Layout

After installation, the directory structure is:

```
OIE_HOME/
├── appdata/              # Application data directory
│   ├── mirthdb/          # Embedded Derby database (if using Derby)
│   ├── keystore.jks      # TLS keystore
│   └── configuration.properties  # Configuration map
├── cli-lib/              # CLI libraries
├── client-lib/           # Administrator client libraries
├── conf/                 # Configuration files
│   ├── mirth.properties          # Main server configuration
│   ├── dbdrivers.xml             # Database driver definitions
│   ├── log4j2.properties         # Logging configuration
│   └── mirth-cli-config.properties  # CLI defaults
├── custom-lib/           # User-provided libraries (added to server classpath)
├── docs/                 # Documentation and license info
│   └── javadocs/         # API javadocs
├── extensions/           # Plugins and connector extensions
├── logs/                 # Server log files
├── manager-lib/          # Server Manager libraries
├── public_html/          # Web server root
├── server-lib/           # Server libraries
├── webapps/              # Web applications
├── oieserver              # Server launcher script (Linux/macOS)
├── oieserver.ps1          # Server launcher script (Windows PowerShell)
└── oieserver.vmoptions    # JVM options for the server
```

---

## Post-Installation Setup

### 1. Start the Server

**Linux/macOS:**
```bash
./oieserver
```

**Windows (PowerShell):**
```powershell
.\oieserver.ps1
```

The server launcher script automatically:
- Finds a compatible Java 17+ runtime (checking `OIE_JAVA_PATH`, then `-java-cmd` in `oieserver.vmoptions`, then `JAVA_HOME`, then `PATH`)
- Parses `oieserver.vmoptions` for JVM settings
- Launches the server

### 2. Access the Administrator

1. Open a browser and navigate to `http://localhost:8080`
2. Click **Launch Administrator** to download and start the desktop client
3. Connect to `https://localhost:8443`
4. Log in with the default credentials:
   - **Username:** `admin`
   - **Password:** `admin`

> **WARNING:** Change the default password immediately after first login.

### 3. Configure the Database

For production environments, switch from the embedded Derby database to a production-grade database. See [Database Support](Database-Support) for details.

---

## Upgrading

The installer supports in-place upgrades from previous versions. Back up your `appdata/` directory and `conf/mirth.properties` before upgrading.

[Back to Home](Home)
