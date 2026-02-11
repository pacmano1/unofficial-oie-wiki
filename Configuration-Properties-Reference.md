# Configuration Properties Reference

Complete reference for all properties in `conf/mirth.properties`.

---

## Directories

| Property | Default | Description |
|---|---|---|
| `dir.appdata` | `appdata` | Application data directory |
| `dir.tempdata` | `${dir.appdata}/temp` | Temporary data directory |

---

## Network Ports

| Property | Default | Description |
|---|---|---|
| `http.port` | `8080` | HTTP port (web server, launch page) |
| `https.port` | `8443` | HTTPS port (REST API, Administrator) |
| `http.host` | `0.0.0.0` | HTTP bind address |
| `https.host` | `0.0.0.0` | HTTPS bind address |
| `http.contextpath` | `/` | Web server context path |

---

## TLS / SSL

| Property | Default | Description |
|---|---|---|
| `https.client.protocols` | `TLSv1.3,TLSv1.2` | Client TLS protocol versions |
| `https.server.protocols` | `TLSv1.3,TLSv1.2,SSLv2Hello` | Server TLS protocol versions |
| `https.ciphersuites` | (strong defaults) | Comma-separated cipher suites |
| `https.ephemeraldhkeysize` | `2048` | Ephemeral DH key size |

---

## Keystore

| Property | Default | Description |
|---|---|---|
| `keystore.path` | `${dir.appdata}/keystore.jks` | Keystore file path |
| `keystore.storepass` | (generated) | Keystore password |
| `keystore.keypass` | (generated) | Key password |
| `keystore.type` | `JCEKS` | Keystore type |

---

## Database

| Property | Default | Description |
|---|---|---|
| `database` | `derby` | Database type: `derby`, `mysql`, `postgres`, `oracle`, `sqlserver` |
| `database.url` | (Derby embedded) | JDBC connection URL |
| `database.driver` | (auto) | Custom JDBC driver class |
| `database.username` | (empty) | Database username |
| `database.password` | (empty) | Database password |
| `database.max-connections` | `20` | Read/write connection pool size |
| `database-readonly.max-connections` | `20` | Read-only connection pool size |
| `database.enable-read-write-split` | `true` | Enable separate read-only pool |
| `database.connection.maxretry` | `2` | Startup connection retry attempts |
| `database.connection.retrywaitinmilliseconds` | `10000` | Wait between retries (ms) |

### Read-Only Pool Override
| Property | Default | Description |
|---|---|---|
| `database-readonly.url` | (same as primary) | Read-only JDBC URL |
| `database-readonly.username` | (same as primary) | Read-only username |
| `database-readonly.password` | (same as primary) | Read-only password |

---

## Password Policy

| Property | Default | Description |
|---|---|---|
| `password.minlength` | `0` | Minimum password length |
| `password.minupper` | `0` | Minimum uppercase characters |
| `password.minlower` | `0` | Minimum lowercase characters |
| `password.minnumeric` | `0` | Minimum numeric characters |
| `password.minspecial` | `0` | Minimum special characters |
| `password.retrylimit` | `0` | Failed attempts before lockout (0 = unlimited) |
| `password.lockoutperiod` | `0` | Lockout duration in hours (0 = until admin unlock) |
| `password.expiration` | `0` | Days until expiration (0 = never) |
| `password.graceperiod` | `0` | Grace days after expiration |
| `password.reuseperiod` | `0` | Days before password reuse |
| `password.reuselimit` | `0` | Number of previous passwords blocked |

---

## Server Behavior

| Property | Default | Description |
|---|---|---|
| `server.startupdeploy` | `true` | Auto-deploy channels on startup |
| `server.includecustomlib` | `false` | Include custom-lib/ on classpath |
| `server.url` | (empty) | Server URL (auto-detected if empty) |

---

## API Security

| Property | Default | Description |
|---|---|---|
| `server.api.require-requested-with` | `true` | Require X-Requested-With header |
| `server.api.accesscontrolalloworigin` | `*` | CORS allowed origins |
| `server.api.accesscontrolallowcredentials` | `false` | CORS allow credentials |
| `server.api.accesscontrolallowmethods` | `GET, POST, DELETE, PUT` | CORS allowed methods |
| `server.api.accesscontrolallowheaders` | `Content-Type` | CORS allowed headers |
| `server.api.accesscontrolexposeheaders` | (empty) | CORS exposed headers |
| `server.api.accesscontrolmaxage` | (empty) | CORS preflight cache (seconds) |

---

## JavaScript Engine

| Property | Default | Description |
|---|---|---|
| `rhino.languageversion` | `es6` | Rhino JS language version (`1.0`-`1.8`, `es6`) |

---

## Administrator Client

| Property | Default | Description |
|---|---|---|
| `administrator.maxheapsize` | `512m` | Max JVM heap for the Administrator |

---

## Configuration Map

| Property | Default | Description |
|---|---|---|
| `configurationmap.path` | `${dir.appdata}/configuration.properties` | Configuration map file location |

---

## Version

| Property | Default | Description |
|---|---|---|
| `version` | `4.5.2` | Schema version (migration tracking; do not modify) |

[Back to Home](Home)
