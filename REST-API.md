# REST API

OIE exposes a comprehensive REST API that powers both the Administrator GUI and the CLI. You can use it directly for custom integrations, automation, and monitoring.

---

## Base URL

```
https://<host>:8443/api
```

The API requires HTTPS and authentication. By default, all requests must include an `X-Requested-With` header for CSRF protection.

---

## Authentication

### Session-Based
1. POST to `/api/users/_login` with credentials
2. Use the returned session cookie for subsequent requests

### HTTP Headers
| Header | Required | Description |
|---|---|---|
| `X-Requested-With` | Yes (by default) | CSRF protection header (any value) |
| `Authorization` | Yes | Basic auth or session token |
| `Content-Type` | For POST/PUT | `application/xml` or `application/json` |
| `Accept` | Optional | `application/xml` or `application/json` |

### Example (curl)
```bash
curl -k -X GET \
  -H "X-Requested-With: OIE" \
  -u admin:admin \
  https://localhost:8443/api/channels
```

---

## API Endpoints

### Channels

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/channels` | List all channels |
| `GET` | `/api/channels/{channelId}` | Get a specific channel |
| `POST` | `/api/channels` | Create a new channel |
| `PUT` | `/api/channels/{channelId}` | Update a channel |
| `DELETE` | `/api/channels/{channelId}` | Delete a channel |

### Channel Status and Control

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/channels/statuses` | Get all channel statuses |
| `POST` | `/api/channels/_deploy` | Deploy channels |
| `POST` | `/api/channels/_undeploy` | Undeploy channels |
| `POST` | `/api/channels/{channelId}/_start` | Start a channel |
| `POST` | `/api/channels/{channelId}/_stop` | Stop a channel |
| `POST` | `/api/channels/{channelId}/_pause` | Pause a channel |
| `POST` | `/api/channels/{channelId}/_resume` | Resume a channel |
| `POST` | `/api/channels/{channelId}/_halt` | Halt a channel |

### Channel Groups

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/channelgroups` | List all channel groups |
| `POST` | `/api/channelgroups` | Create/update channel groups |

### Channel Statistics

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/channels/statistics` | Get statistics for all channels |
| `GET` | `/api/channels/{channelId}/statistics` | Get statistics for a channel |
| `DELETE` | `/api/channels/statistics` | Reset statistics |

### Messages

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/channels/{channelId}/messages` | Search messages |
| `GET` | `/api/channels/{channelId}/messages/{messageId}` | Get a specific message |
| `POST` | `/api/channels/{channelId}/messages` | Send a new message |
| `DELETE` | `/api/channels/{channelId}/messages` | Remove messages |
| `POST` | `/api/channels/{channelId}/messages/_reprocess` | Reprocess messages |
| `GET` | `/api/channels/{channelId}/messages/{messageId}/attachments` | Get attachments |

### Configuration

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/server/configuration` | Get full server configuration |
| `PUT` | `/api/server/configuration` | Update server configuration |
| `GET` | `/api/server/settings` | Get server settings |
| `PUT` | `/api/server/settings` | Update server settings |
| `GET` | `/api/server/configurationMap` | Get configuration map |
| `PUT` | `/api/server/configurationMap` | Update configuration map |

### Users

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/users` | List all users |
| `GET` | `/api/users/{userId}` | Get a specific user |
| `POST` | `/api/users` | Create a user |
| `PUT` | `/api/users/{userId}` | Update a user |
| `DELETE` | `/api/users/{userId}` | Delete a user |
| `PUT` | `/api/users/{userId}/password` | Change password |
| `POST` | `/api/users/_login` | Log in |
| `POST` | `/api/users/_logout` | Log out |

### Alerts

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/alerts` | List all alerts |
| `GET` | `/api/alerts/{alertId}` | Get a specific alert |
| `POST` | `/api/alerts` | Create an alert |
| `PUT` | `/api/alerts/{alertId}` | Update an alert |
| `DELETE` | `/api/alerts/{alertId}` | Delete an alert |

### Code Templates

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/codeTemplateLibraries` | List code template libraries |
| `GET` | `/api/codeTemplates` | List code templates |
| `PUT` | `/api/codeTemplateLibraries` | Update libraries |

### Events

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/events` | Search events |
| `GET` | `/api/events/{eventId}` | Get a specific event |

### Extensions

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/extensions` | List installed extensions |
| `GET` | `/api/extensions/{extensionName}` | Get extension metadata |

### Engine

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/engine` | Get engine status |

### System

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/system/info` | Get system information |
| `GET` | `/api/system/stats` | Get system statistics |

### Database Tasks

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/databaseTasks` | List database tasks |

---

## Javadocs

The full API documentation is available as generated Javadocs at:
```
http://<host>:8080/javadocs/
```

This includes detailed parameter descriptions, request/response schemas, and example payloads.

---

## Error Handling

API errors return standard HTTP status codes:

| Code | Meaning |
|---|---|
| `200` | Success |
| `400` | Bad request (invalid parameters) |
| `401` | Unauthorized (invalid credentials) |
| `403` | Forbidden (insufficient permissions) |
| `404` | Not found |
| `500` | Internal server error |

Error responses include an error message in the response body.

[Back to Home](Home)
