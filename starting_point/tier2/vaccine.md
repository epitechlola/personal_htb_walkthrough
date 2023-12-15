# Vaccine
## Initial Reconnaissance
- **Port Scanning:**
```nmap -sC -sV -p- 10.129.95.174```
# HTB Box Walkthrough: Exploiting Multiple Vulnerabilities

## Initial Reconnaissance

- **Discovered Open Ports:**
  - Identified open ports: SSH, HTTP, FTP.

- **FTP Access:**
  - Accessed FTP using the `anonymous` username.
  - Downloaded a file named `backup.zip`.

- **Cracking ZIP Password:**
  - Extracted hash from `backup.zip` using `zip2john backup.zip > myhashzip`.
  - Cracked the password using John the Ripper: `john --show myhashzip`.

- **Analyzing Archive Contents:**
  - Investigated contents, especially `index.php` from the extracted archive.
  - Found another hash related to the admin page.

- **Cracking Admin Password Hash:**
  - Created a hash file named `adminpassword.txt`.
  - Attempted hash cracking using John the Ripper.

## Web Application Exploitation

- **SQL Injection via sqlmap:**
  - Utilized sqlmap for SQL injection attack.
  - Obtained OS shell by using the `PHPSESSID` cookie.

- **Acquiring Reverse Shell:**
  - Set up a netcat listener.
  - Executed commands in the OS shell to establish a reverse shell.

## User Access and Flag Retrieval

- **User Flag (PostgreSQL Directory):**
  - Retrieved the user flag located at `/var/lib/postgresql/user.txt`.

## Privilege Escalation

- **Locating PostgreSQL Password:**
  - Extracted PostgreSQL password from PHP files.
  - Identified sudo access to edit `/etc/postgresql/11/main/pg_hba.conf`.

- **Exploiting sudo with vi:**
  - Connected as the `postgres` user through SSH.
  - Edited the file using `vi` to elevate privileges.
  - Achieved root access and obtained the root flag at `/root/root.txt`.

