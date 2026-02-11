# Upgrade Guide

This guide covers how to upgrade your OIE installation to a newer version.

---

## Before You Upgrade

### 1. Back Up Everything

Before upgrading, create backups of:

- **Server configuration**: Export via CLI (`exportcfg "path"`) or Administrator
- **Application data directory** (`appdata/`): Contains keystore, embedded database, and configuration map
- **Configuration files** (`conf/`): Especially `mirth.properties` and any customized files
- **Custom libraries** (`custom-lib/`): Any user-provided JARs
- **External database**: If using PostgreSQL, MySQL, Oracle, or SQL Server, back up the database

### 2. Review Release Notes

Check the [Releases page](https://github.com/OpenIntegrationEngine/engine/releases) for:
- Breaking changes
- New features
- Deprecated features
- Database migration notes
- Java version requirements

### 3. Plan Downtime

Upgrading requires stopping the server. Plan a maintenance window during low-traffic periods.

### 4. Test in a Non-Production Environment

If possible, test the upgrade in a staging environment first:
1. Restore your production backup to a test instance
2. Perform the upgrade
3. Verify channels start and process correctly
4. Test critical workflows end-to-end

---

## Upgrade Process

### Using the Installer

The platform installer supports in-place upgrades:

1. Stop the OIE server
2. Run the new version installer
3. Point it to the existing installation directory
4. The installer preserves your `appdata/`, `conf/`, `custom-lib/`, and other user data
5. Start the server
6. The server automatically runs any required database migrations

### Using Pre-Packaged Distributions

For archive-based installations (ZIP/TAR.GZ):

1. Stop the OIE server
2. Back up the entire installation directory
3. Extract the new version to a temporary location
4. Copy your existing `conf/mirth.properties` to the new installation
5. Copy your `appdata/` directory to the new installation
6. Copy any `custom-lib/` files
7. Copy any custom extensions from `extensions/`
8. Start the server from the new installation directory

### Using Docker

Update the image tag in your Docker configuration:

```bash
docker pull openintegrationengine/oie-server:<new-version>
```

Ensure your data volumes are preserved across container updates.

---

## After the Upgrade

### 1. Verify Server Startup

Check the server logs (`logs/mirth.log`) for:
- Successful startup messages
- Database migration completion
- Extension loading
- Any errors or warnings

### 2. Check Database Tasks

Navigate to **Settings > Database Tasks** in the Administrator. Some upgrades create database tasks that may need to run:
- Some tasks run automatically on startup
- Others may require manual initiation
- Monitor task progress for large databases

### 3. Verify Channels

1. Check that all channels are in their expected state (deployed/undeployed)
2. Start channels and verify message processing
3. Check the Message Browser for any errors
4. Test critical interfaces end-to-end

### 4. Verify Extensions

Check **Extensions** in the Administrator to ensure all plugins and connectors loaded correctly.

---

## Rollback Plan

If the upgrade fails:

1. Stop the server
2. Restore from your backup:
   - Replace the installation with the backup
   - Restore the database from backup (if using an external database)
   - Restore `conf/mirth.properties`
3. Start the old version

> **Important:** If database migrations have already run, you must restore the database to its pre-upgrade state. You cannot simply run the old version against a migrated database.

---

## Common Upgrade Issues

### Java Version Mismatch
Newer OIE versions may require a newer Java version. Check the release notes for the minimum Java requirement and update Java before upgrading.

### Extension Compatibility
Custom or third-party extensions may not be compatible with the new version. Check with extension providers for updated versions.

### Configuration Changes
New versions may introduce new configuration properties or change defaults. Compare the new `mirth.properties` template with your existing file and merge any new properties.

### Large Database Migrations
Some version upgrades include significant database schema changes. These migrations can take a long time on large databases. Plan accordingly and monitor the `logs/mirth.log` for progress.

---

## Version-Specific Notes

Check the [GitHub Releases](https://github.com/OpenIntegrationEngine/engine/releases) page for version-specific upgrade instructions, known issues, and migration notes.

[Back to Home](Home)
