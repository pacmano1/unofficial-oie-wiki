# Troubleshooting

Common issues and solutions when running OIE.

---

## Server Won't Start

### Java Not Found
**Symptom:** Error about missing Java or unsupported version.

**Solution:**
1. Ensure Java 17+ is installed: `java -version`
2. Set `OIE_JAVA_PATH` environment variable to the correct Java path
3. Or configure `-java-cmd` in `oieserver.vmoptions`
4. Use SDKMAN to install the correct version: `sdk env install`

### Port Already in Use
**Symptom:** `Address already in use` error on startup.

**Solution:**
1. Check which process is using the port:
   - Linux/macOS: `lsof -i :8443`
   - Windows: `netstat -ano | findstr 8443`
2. Stop the conflicting process
3. Or change OIE ports in `conf/mirth.properties`:
   ```properties
   http.port = 8080
   https.port = 8443
   ```

### Database Connection Failed
**Symptom:** `Unable to connect to database` error.

**Solution:**
1. Verify the database server is running
2. Check `database.url`, `database.username`, and `database.password` in `mirth.properties`
3. Ensure the database exists and the user has permissions
4. Check firewall rules between OIE and the database
5. Review `database.connection.maxretry` and retry settings

### Out of Memory
**Symptom:** `OutOfMemoryError` in logs.

**Solution:**
1. Increase heap size in `oieserver.vmoptions`:
   ```
   -Xmx1024m
   ```
2. Reduce `database.max-connections` if too many connections are open
3. Check for channels with unbounded map growth

---

## Administrator Can't Connect

### Connection Refused
**Symptom:** Administrator shows "Connection refused" or timeout.

**Solution:**
1. Verify the server is running
2. Check that port 8443 is accessible (firewall rules)
3. Verify the server URL is correct (`https://host:8443`)
4. Check if `https.host` is set to `127.0.0.1` (only allows local connections)

### Certificate Errors
**Symptom:** SSL/TLS certificate warnings or errors.

**Solution:**
1. Self-signed certificates trigger browser warnings - click through to continue
2. For production, install a CA-signed certificate via the TLS Manager
3. Ensure `https.client.protocols` includes protocols supported by the client

### Login Fails
**Symptom:** "Invalid username or password" error.

**Solution:**
1. Verify the username and password are correct
2. Check if the account is locked (exceeded `password.retrylimit`)
3. Reset the password via the CLI: `user changepw admin "newpassword"`
4. Check if the password has expired

---

## Channel Issues

### Channel Won't Start
**Symptom:** Channel stays in STOPPED state after clicking Start.

**Solution:**
1. Check **Dashboard > Server Log** for errors
2. Verify the source connector port is not already in use
3. Check channel dependencies - required channels must be started first
4. Look for script errors in deploy scripts
5. Verify external resources (databases, file paths) are accessible

### Messages Not Being Received
**Symptom:** Channel is started but no messages appear.

**Solution:**
1. Verify the sending system is pointing to the correct host and port
2. Check firewall rules
3. For TCP/MLLP: ensure the transmission mode matches (MLLP framing)
4. For File Reader: verify the directory path and file name filter
5. For Database Reader: test the SQL query manually
6. Check the Dashboard Connector Status panel for connection events

### Messages Stuck in Queue
**Symptom:** Queue count increases, messages not being sent.

**Solution:**
1. Check destination connector errors in the Message Browser
2. Verify the destination system is reachable
3. Check for connection timeouts or authentication failures
4. Review queue settings (retry interval, max retries)
5. Stop and restart the channel to reset queue threads

### Transformer Errors
**Symptom:** Messages show ERROR status with transformer exceptions.

**Solution:**
1. Open the message in the Message Browser and check the **Errors** tab
2. Common issues:
   - `TypeError` - Accessing a field that doesn't exist; check the message structure
   - `ReferenceError` - Using an undefined variable
   - `NullPointerException` - Accessing properties on null/undefined
3. Load a sample message in the transformer editor to verify field paths
4. Use `logger.debug()` to trace variable values
5. Wrap risky operations in try/catch blocks

### Filter Not Working
**Symptom:** Messages are being accepted/rejected unexpectedly.

**Solution:**
1. Check filter rule order and boolean operators (AND vs OR)
2. Verify field paths match the actual message structure
3. For JavaScript filters, add logging to verify conditions:
   ```javascript
   logger.debug('Message type: ' + msg['MSH']['MSH.9']['MSH.9.1'].toString());
   ```
4. Remember: `return true` = accept, `return false` = reject

---

## Performance Issues

### High CPU Usage
**Solution:**
1. Check for channels with high message volume and complex transformers
2. Reduce polling frequency on File Reader and Database Reader sources
3. Review JavaScript code for inefficient loops or operations
4. Increase channel thread counts for parallel processing
5. Check for infinite loops in scripts

### High Memory Usage
**Solution:**
1. Increase heap size in `oieserver.vmoptions`
2. Reduce message storage mode (Development uses the most memory)
3. Enable content pruning
4. Check for large messages consuming memory
5. Review global map usage - avoid storing large objects

### Slow Database Performance
**Solution:**
1. Enable `database.enable-read-write-split`
2. Run database maintenance (VACUUM for PostgreSQL, OPTIMIZE for MySQL)
3. Ensure database indexes are up to date
4. Increase `database.max-connections` if connections are saturated
5. Configure message pruning to keep the database lean

---

## Common Error Messages

| Error | Cause | Solution |
|---|---|---|
| `Address already in use` | Port conflict | Change port or stop conflicting process |
| `Unable to connect to database` | Database unreachable | Check DB settings and connectivity |
| `OutOfMemoryError` | Insufficient heap | Increase `-Xmx` in vmoptions |
| `SSLHandshakeException` | TLS mismatch | Check protocol/cipher settings |
| `ConnectException: Connection refused` | Destination unreachable | Verify host/port and network |
| `SocketTimeoutException` | Network timeout | Increase timeout settings |
| `ScriptCompileException` | JavaScript syntax error | Fix the script syntax |
| `TransformerException` | Runtime transformer error | Check error details in Message Browser |

---

## Getting Help

- **Logs:** Check `logs/mirth.log` for detailed error information
- **Event Browser:** Review system events for operational issues
- **Discord:** [Join the community](https://discord.gg/azdehW2Zrx)
- **GitHub Issues:** [Report a bug](https://github.com/OpenIntegrationEngine/engine/issues)
- **Security Issues:** security@openintegrationengine.org

[Back to Home](Home)
