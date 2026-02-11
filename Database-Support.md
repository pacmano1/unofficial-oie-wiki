# Database Support

OIE uses a relational database to store channel configurations, message data, user accounts, and server settings. Multiple database backends are supported.

---

## Supported Databases

| Database | Driver Class | Recommended For |
|---|---|---|
| **Apache Derby** | (embedded) | Development, evaluation, small deployments |
| **PostgreSQL** | `org.postgresql.Driver` | Production (recommended) |
| **MySQL** | `com.mysql.cj.jdbc.Driver` | Production |
| **Oracle** | `oracle.jdbc.driver.OracleDriver` | Production |
| **SQL Server (jTDS)** | `net.sourceforge.jtds.jdbc.Driver` | Production |
| **Microsoft SQL Server** | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | Production |
| **SQLite** | `org.sqlite.JDBC` | Lightweight/testing |

---

## Connection URL Examples

Configure in `conf/mirth.properties`:

### Derby (Default, Embedded)
```properties
database = derby
database.url = jdbc:derby:${dir.appdata}/mirthdb;create=true;upgrade=true
```

### PostgreSQL
```properties
database = postgres
database.url = jdbc:postgresql://localhost:5432/mirthdb
database.username = oie
database.password = secretpassword
```

### MySQL
```properties
database = mysql
database.url = jdbc:mysql://localhost:3306/mirthdb
database.username = oie
database.password = secretpassword
```

### Oracle
```properties
database = oracle
database.url = jdbc:oracle:thin:@localhost:1521:DB
database.username = oie
database.password = secretpassword
```

### SQL Server (jTDS driver - default)
```properties
database = sqlserver
database.url = jdbc:jtds:sqlserver://localhost:1433/mirthdb
database.username = oie
database.password = secretpassword
```

### Microsoft SQL Server (Microsoft driver)
```properties
database = sqlserver
database.url = jdbc:sqlserver://localhost:1433;databaseName=mirthdb
database.driver = com.microsoft.sqlserver.jdbc.SQLServerDriver
database.username = oie
database.password = secretpassword
```

---

## Connection Pool Configuration

```properties
# Maximum connections for read/write operations
database.max-connections = 20

# Maximum connections for read-only operations
database-readonly.max-connections = 20

# Enable separate read-only connection pool
database.enable-read-write-split = true
```

### Read-Write Split
When enabled, SELECT queries are routed to a separate read-only connection pool. This improves performance on busy systems. You can optionally point the read-only pool to a different database (e.g., a read replica):

```properties
database-readonly.url = jdbc:postgresql://replica-host:5432/mirthdb
database-readonly.username = readonly_user
database-readonly.password = readonly_password
```

---

## Startup Retry

If the database is not immediately available at server startup:

```properties
# Maximum retry attempts
database.connection.maxretry = 2

# Wait time between retries (milliseconds)
database.connection.retrywaitinmilliseconds = 10000
```

---

## Database Drivers

Available drivers are defined in `conf/dbdrivers.xml`:

```xml
<drivers>
    <driver class="com.mysql.cj.jdbc.Driver"
            name="MySQL"
            template="jdbc:mysql://host:port/dbname"
            selectLimit="SELECT * FROM ? LIMIT 1" />
    <driver class="oracle.jdbc.driver.OracleDriver"
            name="Oracle"
            template="jdbc:oracle:thin:@host:port:dbname"
            selectLimit="SELECT * FROM ? WHERE ROWNUM &lt; 2" />
    <driver class="org.postgresql.Driver"
            name="PostgreSQL"
            template="jdbc:postgresql://host:port/dbname"
            selectLimit="SELECT * FROM ? LIMIT 1" />
    <driver class="net.sourceforge.jtds.jdbc.Driver"
            name="SQL Server/Sybase (jTDS)"
            template="jdbc:jtds:sqlserver://host:port/dbname"
            selectLimit="SELECT TOP 1 * FROM ?" />
    <driver class="com.microsoft.sqlserver.jdbc.SQLServerDriver"
            name="Microsoft SQL Server"
            template="jdbc:sqlserver://host:port;databaseName=dbname"
            selectLimit="SELECT TOP 1 * FROM ?" />
    <driver class="org.sqlite.JDBC"
            name="SQLite"
            template="jdbc:sqlite:dbfile.db"
            selectLimit="SELECT * FROM ? LIMIT 1" />
</drivers>
```

These drivers are used by the **Database Reader** and **Database Writer** connectors. The server's own database drivers are bundled separately.

---

## Database Schema

OIE automatically creates and migrates its database schema on startup. The schema includes tables for:

- **Channels** - Channel definitions and configuration
- **Messages** - Message content and metadata (per-channel tables)
- **Users** - User accounts and credentials
- **Events** - Audit log entries
- **Alerts** - Alert configurations
- **Code Templates** - Shared code template libraries
- **Configuration** - Server settings and configuration map
- **Extensions** - Extension state and properties

### Per-Channel Tables
Message data is stored in per-channel tables to improve performance and enable independent pruning:
- `d_m{n}` - Messages
- `d_mc{n}` - Message content
- `d_mm{n}` - Message metadata
- `d_ma{n}` - Message attachments
- `d_ms{n}` - Message statistics

---

## Migration

When upgrading OIE, the server automatically runs database migration scripts. The current schema version is tracked in `mirth.properties`:

```properties
# Only used for migration purposes, do not modify
version = 4.5.2
```

### Database Tasks
Pending migration tasks are visible in **Settings > Database Tasks** in the Administrator.

---

## Recommendations

| Scenario | Database | Notes |
|---|---|---|
| Development/Testing | Derby | No setup required, embedded |
| Small production (<50 channels) | PostgreSQL | Best balance of performance and ease of use |
| Large production (50+ channels) | PostgreSQL or MySQL | Tune connection pools and enable read-write split |
| Enterprise | Oracle or SQL Server | When required by organizational standards |

### Production Checklist
1. Use an external database (not Derby)
2. Enable `database.enable-read-write-split`
3. Size `database.max-connections` appropriately (2x the number of active channels is a starting point)
4. Set up database backups
5. Configure pruning to manage data growth
6. Monitor database disk usage and query performance

[Back to Home](Home)
