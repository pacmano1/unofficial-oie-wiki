# Command Line Interface

The OIE CLI provides a terminal-based interface for managing the server. It connects over the same REST API as the Administrator GUI and is useful for scripting, automation, and headless administration.

---

## Configuration

The CLI configuration file is located at:
```
OIE_HOME/conf/mirth-cli-config.properties
```

Default contents:
```properties
address=https://127.0.0.1:8443
user=admin
password=admin
version=0.0.0
```

---

## Launching the CLI

```bash
# Using the launcher
java -jar mirth-cli-launcher.jar

# With command-line options
java -jar mirth-cli-launcher.jar -a https://host:8443 -u admin -p password
```

### Command-Line Options
| Flag | Description |
|---|---|
| `-a <address>` | Server address (e.g., `https://localhost:8443`) |
| `-u <user>` | Username |
| `-p <password>` | Password |
| `-s <script>` | Execute a script file and exit |
| `-v <version>` | Server version |
| `-h` | Show help |
| `-d` | Enable debug output |

---

## Commands Reference

### Channel Status and Control

```
status
    Returns status of all deployed channels

channel list
    Lists all channels

channel start id|"name"|*
    Starts specified channel(s)

channel stop id|"name"|*
    Stops specified channel(s)

channel halt id|"name"|*
    Force-stops specified channel(s) immediately

channel pause id|"name"|*
    Pauses specified channel(s)

channel resume id|"name"|*
    Resumes paused channel(s)

channel deploy id|"name"|*
    Deploys specified channel(s)

channel undeploy id|"name"|*
    Undeploys specified channel(s)

channel stats id|"name"|*
    Shows statistics for specified channel(s)

channel enable id|"name"|*
    Enables specified channel(s)

channel disable id|"name"|*
    Disables specified channel(s)

channel remove id|"name"|*
    Removes specified channel(s)
```

Use `*` to target all channels.

### Deploy

```
deploy [timeout]
    Deploys all channels with optional timeout (in seconds)
```

### Import / Export Channels

```
import "path" [force]
    Imports channel from file. 'force' overwrites existing.

export id|"name"|* "path"
    Exports channel(s) to file
```

### Import / Export Server Configuration

```
importcfg "path" [nodeploy] [overwriteconfigmap]
    Imports full server configuration.
    'nodeploy' prevents auto-deployment.
    'overwriteconfigmap' replaces the Configuration Map.

exportcfg "path"
    Exports full server configuration to file
```

### Alerts

```
importalert "path" [force]
    Imports alert from file. 'force' overwrites existing.

exportalert id|"name"|* "path"
    Exports alert(s) to file
```

### Scripts

```
importscripts "path"
    Imports global scripts from file

exportscripts "path"
    Exports global scripts to file
```

### Code Templates

```
codetemplate library list [includecodetemplates]
    Lists all code template libraries.
    'includecodetemplates' shows templates within each library.

codetemplate list
    Lists all code templates

codetemplate import "path" [force]
    Imports code templates from file

codetemplate library import "path" [force]
    Imports code template libraries from file

codetemplate export id|"name" "path"
    Exports a code template to file

codetemplate library export id|"name"|* "path"
    Exports code template library(ies) to file

codetemplate remove id|"name"
    Removes a code template

codetemplate library remove id|"name"|*
    Removes code template library(ies)
```

### Messages

```
importmessages "path" id
    Imports messages from file into the specified channel

exportmessages "path/file-pattern" id [format] [pageSize]
    Exports all messages for a channel.
    Formats: xml, xml-attach, raw, processedraw, transformed, encoded, response
```

### Configuration Map

```
importmap "path"
    Imports configuration map from file

exportmap "path"
    Exports configuration map to file
```

### Statistics and Events

```
resetstats [lifetime]
    Resets all channel statistics. 'lifetime' also resets lifetime stats.

clearallmessages
    Removes all messages from all channels (running channels will restart)

dump stats "path"
    Dumps channel statistics to file

dump events "path"
    Dumps events to file
```

### User Management

```
user list
    Lists all users

user add username "password" "firstName" "lastName" "organization" "email"
    Creates a new user

user remove id|username
    Removes a user

user changepw id|username "newpassword"
    Changes a user's password
```

### Session

```
quit
    Exits the CLI shell
```

---

## Scripting

The CLI can execute commands from a script file:

```bash
java -jar mirth-cli-launcher.jar -s /path/to/script.txt
```

### Script File Example
```
deploy
channel start *
status
exportcfg "/backups/config_backup.xml"
quit
```

This is useful for:
- Automated deployments
- Scheduled backups
- CI/CD pipelines
- Bulk channel management

[Back to Home](Home)
