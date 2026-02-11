# Data Pruning and Maintenance

OIE stores message data in the database. Without regular maintenance, this data can grow unbounded and impact performance. The Data Pruner automates message cleanup.

---

## Data Pruner

The Data Pruner is a built-in plugin that automatically removes old message data based on configurable retention policies.

### Accessing the Data Pruner
Navigate to **Settings > Data Pruner** in the Administrator.

### Global Settings

| Setting | Description |
|---|---|
| **Enabled** | Enable/disable the data pruner |
| **Pruning Schedule** | Cron expression or interval for when pruning runs |
| **Block Size** | Number of messages processed per batch |
| **Archiving** | Enable message archiving before pruning |

### Per-Channel Pruning

Each channel can have its own pruning settings configured in the channel properties:

| Setting | Description |
|---|---|
| **Prune metadata older than N days** | Remove message metadata after this many days |
| **Prune content older than N days** | Remove message content (but keep metadata) after this many days |
| **Archive before pruning** | Write messages to archive before deletion |

### Pruning Strategy

The pruner operates in two phases:
1. **Content Pruning** - Removes message content (raw, transformed, encoded, etc.) while preserving metadata
2. **Metadata Pruning** - Removes message metadata (and any remaining content)

This allows you to keep lightweight metadata longer than full content. For example:
- Keep content for 7 days (for troubleshooting)
- Keep metadata for 30 days (for audit/reporting)

---

## Message Storage Modes

Message storage settings work together with pruning to control data volume:

| Mode | What's Stored | Pruning Impact |
|---|---|---|
| **Development** | All content types | Largest storage footprint |
| **Production** | Raw and encoded content | Moderate storage |
| **Raw** | Only raw content | Smaller storage |
| **Metadata Only** | Only metadata | Minimal storage |
| **Disabled** | Nothing | No pruning needed |

Set storage mode per-channel in the channel properties.

---

## Manual Message Cleanup

### Administrator GUI
1. Open the **Message Browser** for a channel
2. Use filters to find messages
3. Select messages and click **Remove**

### CLI
```bash
# Remove all messages from all channels
clearallmessages

# Reset all channel statistics
resetstats [lifetime]
```

### REST API
```bash
# Remove messages for a specific channel
curl -k -X DELETE \
  -H "X-Requested-With: OIE" \
  -u admin:admin \
  "https://localhost:8443/api/channels/{channelId}/messages"
```

---

## Database Maintenance

### Database Tasks
The **Settings > Database Tasks** panel shows pending and completed database migration and maintenance tasks. Some tasks run automatically during server startup; others can be triggered manually.

### Database-Specific Maintenance

#### PostgreSQL
```sql
-- Analyze tables for query optimization
ANALYZE;

-- Reclaim storage (run during maintenance windows)
VACUUM ANALYZE;
```

#### MySQL
```sql
-- Optimize message tables
OPTIMIZE TABLE d_m1, d_mc1, d_mm1, d_ma1;
```

#### Derby (Embedded)
Derby compacts automatically, but for heavily used instances consider migrating to PostgreSQL or MySQL.

---

## Performance Considerations

1. **Set appropriate storage modes** - Don't store full content in production unless needed
2. **Enable pruning** - Configure content pruning at 7-14 days and metadata pruning at 30-90 days
3. **Size the database** - Ensure adequate disk space for your message volume
4. **Monitor queue sizes** - Growing queues indicate delivery problems
5. **Use read-write split** - Enable `database.enable-read-write-split = true` for better performance
6. **Connection pool sizing** - Adjust `database.max-connections` based on channel count and throughput

---

## Archiving

When archiving is enabled, the Data Pruner exports messages before deleting them:

- Messages are written to files in a configurable archive directory
- Archive format is XML
- Archives can be re-imported if needed

Configure archiving in the Data Pruner settings panel.

---

## Monitoring Database Size

Keep an eye on:
- **Total database size** on disk
- **Per-channel message counts** in the Dashboard
- **Queue depths** for destinations
- **Error counts** (errored messages are retained until pruned)

Use the `dump stats` CLI command to export statistics for analysis.

[Back to Home](Home)
