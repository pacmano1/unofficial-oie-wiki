# Server Configuration

The primary server configuration file is `conf/mirth.properties`. This file controls ports, database connections, TLS settings, password policies, and more.

---

## Configuration File Location

```
OIE_HOME/conf/mirth.properties
```

Changes to this file require a server restart to take effect.

---

## Ports

```properties
# HTTP port for the web server and Administrator launch page
http.port = 8080

# HTTPS port for the REST API and Administrator connections
https.port = 8443
```

### Bind Addresses

```properties
# Listen on all interfaces (default)
http.host = 0.0.0.0
https.host = 0.0.0.0

# Or restrict to localhost only
# http.host = 127.0.0.1
# https.host = 127.0.0.1
```

---

## Context Path

```properties
# Root context path (default: /)
http.contextpath = /
```

---

## TLS / SSL Configuration

```properties
# Client-side TLS protocol versions
https.client.protocols = TLSv1.3,TLSv1.2

# Server-side TLS protocol versions
https.server.protocols = TLSv1.3,TLSv1.2,SSLv2Hello

# Allowed cipher suites (comma-separated)
https.ciphersuites = TLS_CHACHA20_POLY1305_SHA256,...

# Ephemeral Diffie-Hellman key size
https.ephemeraldhkeysize = 2048
```

### Keystore

```properties
keystore.path = ${dir.appdata}/keystore.jks
keystore.storepass = <store-password>
keystore.keypass = <key-password>
keystore.type = JCEKS
```

> **Security Note:** Change the default keystore passwords for production deployments.

---

## Database Configuration

```properties
# Database type: derby, mysql, postgres, oracle, sqlserver
database = derby

# JDBC connection URL
database.url = jdbc:derby:${dir.appdata}/mirthdb;create=true;upgrade=true

# Credentials
database.username =
database.password =

# Connection pool sizes
database.max-connections = 20
database-readonly.max-connections = 20

# Enable read/write connection pool splitting
database.enable-read-write-split = true

# Retry configuration
database.connection.maxretry = 2
database.connection.retrywaitinmilliseconds = 10000
```

See [Database Support](Database-Support) for connection URL examples for each database type.

---

## Password Policy

```properties
password.minlength = 0        # Minimum password length
password.minupper = 0         # Minimum uppercase characters
password.minlower = 0         # Minimum lowercase characters
password.minnumeric = 0       # Minimum numeric characters
password.minspecial = 0       # Minimum special characters
password.retrylimit = 0       # Failed attempts before lockout (0 = unlimited)
password.lockoutperiod = 0    # Lockout duration in hours (0 = until admin unlock)
password.expiration = 0       # Days until password expires (0 = never)
password.graceperiod = 0      # Days of grace after expiration
password.reuseperiod = 0      # Days before a password can be reused
password.reuselimit = 0       # Number of previous passwords that cannot be reused
```

---

## Server Behavior

```properties
# Deploy channels automatically on server startup
server.startupdeploy = true

# Include jars from custom-lib/ on the classpath
server.includecustomlib = false

# Server URL (leave empty for auto-detection)
server.url =
```

---

## CORS and API Security

```properties
# Require X-Requested-With header (CSRF protection)
server.api.require-requested-with = true

# CORS headers
server.api.accesscontrolalloworigin = *
server.api.accesscontrolallowcredentials = false
server.api.accesscontrolallowmethods = GET, POST, DELETE, PUT
server.api.accesscontrolallowheaders = Content-Type
server.api.accesscontrolexposeheaders =
server.api.accesscontrolmaxage =
```

---

## JavaScript Engine

```properties
# Rhino JavaScript language version
# Supported: 1.0, 1.1, ..., 1.8, es6
rhino.languageversion = es6
```

---

## Administrator Client

```properties
# Maximum JVM heap size for the Administrator client
administrator.maxheapsize = 512m
```

---

## Application Data

```properties
# Application data directory
dir.appdata = appdata

# Temp data directory
dir.tempdata = ${dir.appdata}/temp

# Configuration map file
configurationmap.path = ${dir.appdata}/configuration.properties
```

---

## JVM Options

JVM settings are configured in `oieserver.vmoptions`:

```
# Set heap size
-Xms256m
-Xmx512m

# Custom Java path (alternative to OIE_JAVA_PATH environment variable)
# -java-cmd /path/to/java
```

Additional JVM options files:
- `conf/base_includes.vmoptions` - Base JVM module options
- `conf/default_modules.vmoptions` - Default module inclusions
- `conf/custom.vmoptions` - User-defined custom JVM options

### Environment Variables

| Variable | Purpose |
|---|---|
| `OIE_JAVA_PATH` | Highest-priority path to the `java` executable |
| `JAVA_HOME` | Standard Java installation path |

[Back to Home](Home)
