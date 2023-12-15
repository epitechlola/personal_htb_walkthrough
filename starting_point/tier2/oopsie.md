# Oopsie

## Initial Steps

- **Port Scan:**
```nmap -sC -sV 10.129.109.85```
- Identified SSH and HTTP ports.

- **Setting Up BurpSuite Proxy:**
- Configured browser proxy settings to `12.0.0.1:8080`.
- Enabled BurpSuite intercept.

## Web Page Exploitation

- **Discovering the Login Page:**
- Located the login page at `/cdn-cgi/login`.
- Logged in as a guest, identified limited access.

- **Cookie Manipulation:**
- Altered cookie values (`role=admin`, `user=34322`) to gain admin access.
- Accessed the upload form successfully.

- **Uploading PHP Reverse Shell:**
- Uploaded a PHP reverse shell from `/usr/share/webshells/php/php-reverse-shell.php`, edited IP and port.

- **Locating Uploaded Files:**
- Used `gobuster` to find the `/uploads` directory.
- Insufficient permissions to access the directory.

- **Preparing Netcat Listener:**
- Set up Netcat to listen on port `1234` to check responses.
- Triggered the uploaded PHP file at `http://10.129.109.85/uploads/php-reverse-shell.php`, obtained a reverse shell.

- **Elevating Access:**
- Investigated users via `cat /etc/passwd`, discovered user `robert`.
- Obtained login credentials from `/var/www/html/cdn-cgi/login/db.php`.
- Logged in as `robert` with password `M3g4C0rpUs3r!`.
- Found user flag at `/home/robert`.

## Privilege Escalation

- **Exploiting SUID Binary:**
- Identified `/usr/bin/bugtracker` with SUID permissions owned by the `bugtracker` group.
- Created a custom `cat` file with `/bin/sh`.
- Modified the `PATH` variable to execute the custom `cat` file.
- Executed `bugtracker`, obtaining root access.

