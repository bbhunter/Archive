s# DNS Passive Recon

The domain registrar is responsible for maintaining the WHOIS records for the domain names it is leasing. `whois` will query the WHOIS server to provide all saved records.
```bash
whois $url
```
`whois` will return:
-   Registrar WHOIS server
-   Registrar URL
-   Record creation date
-   Record update date
-   Registrant contact info and address (unless withheld for privacy)
-   Admin contact info and address (unless withheld for privacy)
-   Tech contact info and address (unless withheld for privacy)
-   Name Servers

```bash
nslookup [-type=] $url A=ipv4,AAAA=ipv6,cname=CanonicalName,MX=MailServers,SOA=stateofAuthority,txt=txtrecords 

dig $url [type]		# Domain Information Groper
```

BE AWARE !! consider DNSdumpster tool for subdomains, and Shodan.io for exposed IoT anythings connected to networks

## ViewDNS

[ViewDNS.info](https://viewdns.info/) is third party to yourself and target that offer reverse lookup.


## Threat Intelligence Platform
[Threat Intelligence Platform](https://threatintelligenceplatform.com/) is visualisation tool for `dig` and `whois` that requires you to provide a domain name or an IP address then launching tests from malware checks to WHOIS and DNS queries.

## Censys Search
[Censys Search](https://search.censys.io) for extensive IP information


## References

[THM Red Team Recon Room](https://tryhackme.com/room/redteamrecon)
[ViewDNS.info](https://viewdns.info/)