# Unified
## Initial Reconnaissance
- **Port Scan:**
```nmap -sC -sV -p- 10.129.11.162```
- Identified port `8443` hosting Unifi Network version `6.4.54`.
- Accessed the login form on `http://10.129.11.162:8443`.

## Exploiting CVE-2021-44228 Vulnerability

- **Identifying Vulnerability:**
- Discovered CVE-2021-44228 affecting Unifi Network version `6.4.54`.
- Attempted LDAP exploitation to retrieve files.

- **Intercepting Requests:**
- Used BurpSuite to intercept a connection.
- Attempted various payloads but encountered errors.

- **Creating LDAP Payload:**
- Created an LDAP server using `rogue-jndi`.
- Generated a base64-encoded reverse shell payload.

- **Executing Payload:**
- Started a Netcat listener on port `8083`.
- Launched the LDAP server with the reverse shell payload.
- Modified BurpSuite request to trigger the LDAP server.
- Successfully obtained a reverse shell.

## User Access and Exploration

- **User Shell:**
- Accessed the shell and improved its usability.
- Retrieved the user flag at `/home/michael/user.txt`.

## Privilege Escalation and Database Access

- **MongoDB Discovery:**
- Identified MongoDB running on port `27117` with default database name "ace"
## Accessing MongoDB

- **Connecting to MongoDB:**
  - Connected to MongoDB using `mongo --port 27117 ace`.
  - Retrieved administrator details using `db.admin.find()`.

- **Modifying Administrator Password:**
  - Generated a SHA-512 encrypted password with `mkpasswd`.
  - Updated the administrator password in MongoDB.
  - Connected to the Unifi DB using the modified password.

## Accessing Root and Root Flag

- **SSH Access:**
  - Discovered SSH credentials (root with a modified password).
  - Accessed the root account using SSH: `ssh root@10.129.11.162`.
  - Obtained the root flag at `/root/root.txt`.

