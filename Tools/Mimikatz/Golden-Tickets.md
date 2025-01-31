Golden tickets are forget Ticket Granting Tickets (TGTs) as part of Kerberos, see [[Active-Directory-Kerberos-Authenication-Defined]] and it one of many techniques of [[Attacking-Kerberos]]. It is a ticket that grants any ticket, which requires KRBTGT accounts password hash, maybe try [[Mimikatz-Cheatsheet]]. This article is notes on the subject of Golden Tickets:

Requirement:
- KRBTGT account's password hash, 
- domain name, domain SID, and user ID of account to be impersonated

- With KRBTGT user hash - we don't need the password hash of the account we want to impersonate, ince it was signed by the KRBTGT hash, this verification passes and the TGT is declared valid no matter its contents.
- KDC will only validate the user account specified in the TGT **if the account is older than 20 minutes**
	- Allowing for disabled, deleted, or non-existent account in the TGT 
- TGT no older than 20 minutes!
- TGT sets the policies and rules, Golden Tickets can overwrite the KDC 
- By default, the KRBTGT account's password never changes
	- Blue team would have to rotate the KRBTGT account password atleast twice, is an incredibly painful process for the blue team
		- Not all services are smart enough to release the TGT is no longer valid (since the timestamp is still valid) and thus won't auto-request a new TGT.
- Golden Tickets allow even bypass smart card authenication as smart cards are verified by the DC before it creates the TGT.
- Golden ticket can created nonm domain joined machines!



## References

[THM AD Persistence Room](https://tryhackme.com/room/persistingad)