# Plugins and Extensions

OIE has a modular architecture where most features are implemented as extensions. Extensions include connectors, data types, plugins, and transmission mode providers.

---

## Installed Extensions

The following extensions ship with OIE:

### Connectors
| Extension | Description |
|---|---|
| `dicom` | DICOM Listener and Sender |
| `doc` | Document Writer (PDF/RTF) |
| `file` | File/FTP/SFTP/S3/SMB Reader and Writer |
| `http` | HTTP Listener and Sender |
| `jdbc` | Database Reader and Writer |
| `jms` | JMS Listener and Sender |
| `js` | JavaScript Reader and Writer |
| `smtp` | SMTP Email Sender |
| `tcp` | TCP/MLLP Listener and Sender |
| `vm` | Channel Reader and Writer (internal routing) |
| `ws` | Web Service (SOAP) Listener and Sender |

### Data Types
| Extension | Format |
|---|---|
| `datatype-delimited` | Delimited text (CSV, TSV, custom) |
| `datatype-dicom` | DICOM |
| `datatype-edi` | EDI / X12 |
| `datatype-hl7v2` | HL7 v2.x |
| `datatype-hl7v3` | HL7 v3.x / CDA |
| `datatype-json` | JSON |
| `datatype-ncpdp` | NCPDP |
| `datatype-raw` | Raw (unstructured) |
| `datatype-xml` | XML |

### Plugins
| Extension | Description |
|---|---|
| `dashboardstatus` | Connector-level status monitoring on the Dashboard |
| `datapruner` | Automated message data pruning |
| `destinationsetfilter` | Dynamic destination routing |
| `directoryresource` | File-based custom library resources |
| `globalmapviewer` | View global and channel map variables |
| `httpauth` | HTTP authentication providers (Basic, Digest, JavaScript, OAuth2) |
| `serverlog` | Real-time server log viewer in the Administrator |
| `simple-channel-history` | Channel revision history tracking |
| `tls-manager` | TLS certificate management web application |
| `rbac` | Role-based access control |

### Transformer/Filter Step Types
| Extension | Description |
|---|---|
| `javascriptrule` | JavaScript filter rules |
| `javascriptstep` | JavaScript transformer steps |
| `mapper` | Mapper transformer steps |
| `messagebuilder` | Message Builder transformer steps |
| `rulebuilder` | Rule Builder filter rules |
| `scriptfilerule` | External script filter rules |
| `scriptfilestep` | External script transformer steps |
| `xsltstep` | XSLT transformer steps |

### Viewers
| Extension | Description |
|---|---|
| `dicomviewer` | DICOM image viewer |
| `imageviewer` | General image viewer |
| `pdfviewer` | PDF document viewer |
| `textviewer` | Text content viewer |

### Transmission Modes
| Extension | Description |
|---|---|
| `mllpmode` | MLLP framing for TCP connections |

---

## Extension Directory Structure

Extensions are stored in:
```
OIE_HOME/extensions/
```

Each extension has its own subdirectory containing:
```
extensions/
└── http/
    ├── http-server.jar       # Server-side classes
    ├── http-client.jar       # Client-side classes (downloaded to Administrator)
    ├── http-shared.jar       # Shared classes
    ├── libs/                 # Extension dependencies
    └── plugin.xml            # Extension metadata descriptor
```

---

## Extension Metadata

Each extension has a `plugin.xml` descriptor that defines:
- Extension name and version
- Extension type (connector, plugin, data type, etc.)
- Server-side and client-side class paths
- Dependencies on other extensions
- Configuration properties

---

## Managing Extensions

### In the Administrator
Navigate to **Extensions** in the main menu:
- View all installed extensions
- See extension metadata (version, author, description)
- Enable or disable extensions
- Some extensions expose configuration properties

### Enabling/Disabling
1. Select an extension in the list
2. Click **Enable** or **Disable**
3. Restart the server for the change to take effect

---

## Installing New Extensions

1. Obtain the extension package (typically a ZIP file)
2. Stop the OIE server
3. Extract the extension into the `extensions/` directory
4. Start the OIE server
5. The extension is automatically discovered by the `ExtensionLoader`

---

## TLS Manager

The TLS Manager is a web-based extension for managing TLS certificates:
- Access at `http://<host>:8080/tls-manager/`
- Manage the server keystore
- Import/export certificates
- Generate self-signed certificates

The TLS Manager is deployed as a web application (`webapps/tls-manager.war`).

---

## Developing Custom Extensions

Custom extensions can be developed by implementing the appropriate extension interfaces:

| Extension Type | Interface |
|---|---|
| **Server Plugin** | `ServerPlugin` |
| **Service Plugin** | `ServicePlugin` |
| **Channel Plugin** | `ChannelPlugin` |
| **Data Type** | `DataTypeServerPlugin` |
| **Resource Plugin** | `ResourcePlugin` |
| **Library Plugin** | `LibraryPlugin` |
| **Authorization Plugin** | `AuthorizationPlugin` |
| **Transmission Mode** | `TransmissionModeProvider` |

Extension development requires:
1. Creating server-side and/or client-side JAR files
2. Writing a `plugin.xml` descriptor
3. Packaging everything into the extension directory structure
4. Testing with a running OIE instance

[Back to Home](Home)
