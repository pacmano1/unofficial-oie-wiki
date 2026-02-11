# Security and Compliance

OIE is designed to handle sensitive healthcare data securely. This page covers TLS configuration, access control, HIPAA considerations, and security hardening.

---

## Transport Layer Security (TLS)

### Server TLS Configuration

The server uses HTTPS for all API and Administrator communications:

```properties
# Supported TLS protocol versions
https.client.protocols = TLSv1.3,TLSv1.2
https.server.protocols = TLSv1.3,TLSv1.2,SSLv2Hello

# Cipher suites (strong ciphers only)
https.ciphersuites = TLS_CHACHA20_POLY1305_SHA256,...

# Ephemeral DH key size
https.ephemeraldhkeysize = 2048
```

### Keystore Management

The server keystore stores TLS certificates:

```properties
keystore.path = ${dir.appdata}/keystore.jks
keystore.storepass = <change-me>
keystore.keypass = <change-me>
keystore.type = JCEKS
```

**For production:**
1. Replace the default self-signed certificate with a CA-signed certificate
2. Change the default keystore passwords
3. Use the **TLS Manager** web application (`http://<host>:8080/tls-manager/`) to manage certificates

### Connector-Level TLS

Individual connectors support TLS:
- **TCP Listener/Sender** - TLS mode with configurable client authentication
- **HTTP Listener/Sender** - HTTPS endpoints
- **SMTP Sender** - STARTTLS or SSL/TLS
- **File Reader/Writer** - FTPS, SFTP
- **Web Service** - HTTPS SOAP endpoints

---

## CSRF Protection

The REST API requires an `X-Requested-With` header by default:

```properties
server.api.require-requested-with = true
```

This prevents Cross-Site Request Forgery attacks. All API clients must include this header.

---

## CORS Configuration

Control cross-origin access to the REST API:

```properties
server.api.accesscontrolalloworigin = *           # Restrict in production
server.api.accesscontrolallowcredentials = false
server.api.accesscontrolallowmethods = GET, POST, DELETE, PUT
server.api.accesscontrolallowheaders = Content-Type
```

**For production:** Replace `*` with the specific origin(s) that need access.

---

## Authentication and Access Control

### Password Policy
Enforce strong passwords (see [User Management](User-Management)):

```properties
password.minlength = 8
password.minupper = 1
password.minlower = 1
password.minnumeric = 1
password.minspecial = 1
password.retrylimit = 5
password.lockoutperiod = 1
password.expiration = 90
password.reuselimit = 5
```

### Account Lockout
Configure automatic lockout after failed login attempts:
- `password.retrylimit` - Number of failed attempts before lockout
- `password.lockoutperiod` - Hours until automatic unlock (0 = manual unlock only)

### Multi-Factor Authentication
OIE supports extensible authentication through the `MultiFactorAuthenticationPlugin` interface.

### Role-Based Access Control (RBAC)
The `rbac` extension provides role-based permissions for controlling which users can access specific channels and features.

---

## Encryption

### Data at Rest
- Database credentials can be encrypted in the keystore
- The `EncryptionUtil` scripting class provides AES encryption for message data
- Attachments can be stored encrypted

### Data in Transit
- All Administrator-to-server communication uses TLS
- Connector-level encryption via TLS for supported protocols

### Keystore
The JCEKS keystore provides:
- TLS certificate storage
- Encryption key management
- Secure credential storage

---

## HIPAA Considerations

OIE is used in HIPAA-regulated environments. Key considerations:

### Technical Safeguards
| Requirement | OIE Feature |
|---|---|
| **Access Control** | User authentication, RBAC extension |
| **Audit Controls** | Event logging, message tracking |
| **Integrity Controls** | Message validation, checksums |
| **Transmission Security** | TLS encryption on all channels |

### Administrative Safeguards
| Requirement | Recommendation |
|---|---|
| **Unique User IDs** | Create individual accounts (no shared logins) |
| **Password Management** | Configure strong password policy |
| **Access Logging** | Enable and retain event logs |
| **Automatic Logoff** | Configure session timeouts |

### Audit Trail
OIE maintains an audit trail through:
- **Event Browser** - All user and system actions are logged
- **Message Browser** - Full message lifecycle tracking
- **Server Logs** - Detailed system logs

---

## Security Hardening Checklist

### Server Configuration
- [ ] Change default admin password
- [ ] Change default keystore passwords
- [ ] Replace self-signed TLS certificate with CA-signed
- [ ] Restrict bind addresses (`http.host`, `https.host`) if not all interfaces needed
- [ ] Set strong password policy
- [ ] Enable account lockout
- [ ] Restrict CORS origins
- [ ] Keep `server.api.require-requested-with = true`

### Network
- [ ] Use a firewall to restrict access to ports 8080 and 8443
- [ ] Place OIE behind a reverse proxy for additional security layers
- [ ] Use network segmentation for healthcare systems
- [ ] Restrict connector ports to necessary interfaces

### Database
- [ ] Use a dedicated database user with minimal privileges
- [ ] Enable TLS for database connections
- [ ] Use strong database passwords
- [ ] Enable database audit logging

### Operations
- [ ] Enable and monitor the Event Browser
- [ ] Configure log retention
- [ ] Set up alerts for errors and security events
- [ ] Regularly back up the database and configuration
- [ ] Keep OIE updated to the latest version
- [ ] Review and prune old message data

### Custom Libraries
- [ ] Set `server.includecustomlib = false` unless needed
- [ ] Use Resources instead of custom-lib for channel-specific libraries
- [ ] Audit third-party libraries for vulnerabilities

---

## Reporting Security Vulnerabilities

Report security vulnerabilities to: **security@openintegrationengine.org**

See [SECURITY.md](https://github.com/OpenIntegrationEngine/engine/blob/main/SECURITY.md) for the full security policy.

[Back to Home](Home)
