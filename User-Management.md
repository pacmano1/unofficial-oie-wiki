# User Management

OIE supports multiple user accounts with configurable password policies for managing access to the server.

---

## Default Account

A new OIE installation includes a default administrator account:

| Field | Value |
|---|---|
| **Username** | `admin` |
| **Password** | `admin` |

> **WARNING:** Change the default password immediately after the first login.

---

## Managing Users via the Administrator GUI

### Viewing Users
Navigate to the **Users** panel in the Administrator to see all accounts.

### Creating a User
1. Click **New User** in the task panel
2. Fill in the required fields:
   - **Username** - Unique login name
   - **Password** - Must meet password policy requirements
   - **First Name** / **Last Name**
   - **Organization** (optional)
   - **Email** (optional)
3. Click **Save**

### Editing a User
1. Select the user in the list
2. Click **Edit User**
3. Modify fields as needed
4. Click **Save**

### Deleting a User
1. Select the user in the list
2. Click **Delete User**
3. Confirm the deletion

### Changing Passwords
1. Select the user
2. Click **Change Password** (or use **Settings > Administrator** for your own password)
3. Enter the new password (must satisfy the password policy)

---

## Managing Users via the CLI

```bash
# List all users
user list

# Add a user
user add jdoe "SecureP@ss1" "John" "Doe" "Hospital A" "jdoe@hospital.com"

# Remove a user
user remove jdoe
user remove 3         # by user ID

# Change a user's password
user changepw jdoe "NewP@ssw0rd"
```

---

## Password Policy

Configure password requirements in `conf/mirth.properties`:

| Property | Description | Default |
|---|---|---|
| `password.minlength` | Minimum password length | `0` (no minimum) |
| `password.minupper` | Minimum uppercase characters | `0` |
| `password.minlower` | Minimum lowercase characters | `0` |
| `password.minnumeric` | Minimum numeric characters | `0` |
| `password.minspecial` | Minimum special characters | `0` |
| `password.retrylimit` | Failed login attempts before lockout | `0` (unlimited) |
| `password.lockoutperiod` | Lockout duration in hours | `0` (until admin unlock) |
| `password.expiration` | Days until password expires | `0` (never) |
| `password.graceperiod` | Grace period in days after expiration | `0` |
| `password.reuseperiod` | Days before a password can be reused | `0` |
| `password.reuselimit` | Previous passwords that cannot be reused | `0` |

### Recommended Production Settings
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

---

## Username Case Sensitivity

Usernames are case-insensitive by default. For example, `Admin`, `admin`, and `ADMIN` are treated as the same user. However, pre-existing accounts with mixed-case usernames are preserved for backward compatibility.

---

## First Login

On the very first login to a new OIE instance, the **First Login Dialog** prompts you to:
1. Change the default admin password
2. Optionally configure server settings
3. Register the installation (optional)

---

## Authentication

OIE supports extensible authentication through the `MultiFactorAuthenticationPlugin` interface. The base installation uses username/password authentication against the OIE database.

[Back to Home](Home)
