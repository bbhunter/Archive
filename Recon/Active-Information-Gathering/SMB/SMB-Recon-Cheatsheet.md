# Server Message Block 
SMB oringally ran on top of NetBIOS using port 139. NetBIOS is an older transport layer that allows Windows computer to communicate on the same network. Later versions of SMB(after Windows 2000) use port 445 on top of a TCP stack, TCP allows for internet communication.

## smbmap
Shows Share **Permissions** and Comment
```bash
smbmap -H $IP
smbmap -H $IP -u user -p password # requires credentals
```

## nbtscan 
Scan NetBIOS name servers, enumerate connection points across a network
```bash
nbtscan -r $IP/$CIDR
```

## RPCclient
Query MS-RPC for commands to try and estanblish a null session
```bash
rpcclient -U "" -N $IP
# Authenticated Session
rpcclient -U <username> --password=<password> $ip
```
See enumeration commands [[RPCClient-Cheatsheet]] and its usage [[RPCClient-Usage]]

## smbget
Recursively download an entire share
```bash
smbget -R smb://$IP/$share
```

## smbclient
```bash
# list share with valid user and password
smbclient -L $IP -U username%password
# connect to share 
smbclient //$IP/$share -U username%password	

smbclient -L $IP			# List shares using NULL session
smbclient -L //$IP/tmp
smbclient -U '' -L //$IP/anon
smbclient -N //$IP/tmp --option='client min protocol=NT1' # legacy
smbclient -U //$IP/share\ with\ whitespace # \ to escape whitespace
```

To escape white space in smb client!
```bash
smb: \> prompt off 
smb: \> recurse on 
smb: \> mget * # Download everything instead of manually 
smb: \> cd "Active Directory\ # will work to escape the whitespace
```

```bash
## Transfering files!
# Download a file from a specific share
smbclient //$IP/$share -c 'cd folder; ls' password -U username # list
smbclient //$IP/$sahre -c 'cd folder;get desired_file_name' password -U username 
# Copy a file to a specific share
smbclient //$IP/$share -c 'put /var/www/my_local_file.txt .\target_folder\target_file.txt' password -U username 

```

## Crackmapexec!
[[Crackmapexec-Cheatsheet]]

## enum4linux Enumeration
```bash
enum4linux -a $IP #  Anonymous session
enum4linux -a $IP -u <user> -p <pass> # Authenticated session
enum4linux -u <user> -p <pass> -U $IP # Users enumeration
enum4linux -u <user> -p <pass> -G $IP # Group and members enumeration
enum4linux -u <user> -p <pass> -P $IP # Password policy
enum4linux -a $IP | tee -a enumFourLinux # output to file nicely :)
```

## nmap - Enum Users
Oneliner for enumerating SMB shares
```bash
nmap -p 139,445 --script=smb-enum-shares.nse,smb-enum-users.nse $IP
```

RPC-Bind port 111,  Server convert a remote procedure call program nu,mber to into universal addresses
```bash
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount $IP
```

```bash
nmap -p 445 --script smb-enum-users <target> --script-args smbuser=username,smbpass=password,smbdomain=domain nmap -p 445 --script smb-enum-users <target> --script-args smbuser=username,smbhash=LM:NTLM,smbdomain=domain
nmap --script smb-enum-users.nse --script-args smbusername=User1,smbpass=Pass@1234,smbdomain=workstation -p445 $ip
nmap --script smb-enum-users.nse --script-args smbusername=User1,smbhash=aad3b435b51404eeaad3b435b51404ee:C318D62C8B3CA508DD753DDA8CC74028,smbdomain=mydomain -p445 $ip

# nmap - Enum Groups
nmap -p 445 --script smb-enum-groups <target> --script-args smbuser=username,smbpass=password,smbdomain=domain nmap -p 445 --script smb-enum-groups <target> --script-args smbuser=username,smbhash=LM:NTLM,smbdomain=domain

# nmap - Enum Shares
nmap -p 445 --script smb-enum-shares <target> --script-args smbuser=username,smbpass=password,smbdomain=domain nmap -p 445 --script smb-enum-shares <target> --script-args smbuser=username,smbpass=LM:NTLM,smbdomain=domain

# nmap - OS Discovery
nmap -p 445 --script smb-os-discovery <target>

# nmap - SMB Vulnerabilities on Windows
nmap -p 445 --script smb-vuln-ms06-025 $ip
nmap -p 445 --script smb-vuln-ms07-029 $ip
nmap -p 445 --script smb-vuln-ms08-067 $ip
nmap -p 445 --script smb-vuln-ms10-054 $ip
nmap -p 445 --script smb-vuln-ms10-061 $ip
nmap -p 445 --script smb-vuln-ms17-010 $ip

# nmap - Brute Force Accounts (be aware of account lockout!)
nmap –p 445 --script smb-brute –script-args userdb=user-list.txt,passdb=pass-list.txt $ip
```

## Search msfconsole!


## References

[toxsec](https://toxsec.com/smb-cheatsheet/)
[lzone](https://lzone.de/cheat-sheet/SMB)
[irgoncalves](https://github.com/irgoncalves/smbclient_cheatsheet)
[referenced by the above](https://sharingsec.blogspot.com/)