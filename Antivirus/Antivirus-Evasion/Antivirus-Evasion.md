# Antivirus Evasion

Understanding to evading [Antivirus software](https://en.wikipedia.org/wiki/Antivirus_software) and the [techniques, tools and methodology](https://attack.mitre.org/) of Malware and APTs can help protect against and improve defenses and make ethical hacking more interesting.

## Introduction to AV and Detection
[AV software](https://en.wikipedia.org/wiki/Antivirus_software) is a host-based security solution designed prevent, detect, and remove malicious software. This software is reliant on blacklisting technologies like signature based static analysis with usual MD5 sums of known malicious files. Antivirus signatures are a continuous sequence of bytes within malware that uniquely identifies it. Files that are potentially [Malware](https://en.wikipedia.org/wiki/Malware) are scanned if the scanner detects identifying features, it quarantines it. 

Traditionally AV tries to detect within current and downloaded files:
-   Gain full access to a target machine.
-   Steal sensitive information.
-   Encrypt files.
-   Damage to files or hardware.
-   Inject other malicious software or unwanted advertisements.
-   Code to perform further attacks such as botnet attacks.

Name | Requires | Description
--- | --- | ---
Scanner | Signatures to detect indications | Scan real-time/on-demand files and directories - Some can detect vulnerabilities, Email, Windows Memory and Registry  
Detection techniques | AV Engine | See section on Detection Techniques
Compressors and Archives | Local-AV trusted decompression software | AV must decompress files to check if malware often compresses and archives itself within files 
Unpackers | PE Header parser | The payload of a malware is often encrypted and packed(packer are used to transform code and rearrange itself to then arrange itself to execute itself functionality) 
Emulators | Sandbox Environment | Run software virtualised environment to log actions(see list in detection techniques) to detect malware 
Self-Protect Driver | | Defend AV against Malware
Firewall/Network Inspection | Ruleset access |  
Own Daemon/Service | | 
Management Console | | Ease of use

## Detection Techniques

The simplist form of detection is the use of [Static Detection](), a predefined set of signatures of malicious files that are used in conjunction with pattern matching to find unique strings, Checksums, sequences of bytecode or hex values and crytographic hashes. After which the application performing the [Static Detection]() will perform a set of comparisons between existing files within the operating system and a database of signatures.

[Dynamic Detection]() in contrast to Static techniques takes place during runtime. One method is  inspects Windows application calls and monitor Windows API calls using Windows [Hooks](https://docs.microsoft.com/en-us/windows/win32/winmsg/about-hooks).  Another being Sandboxing.

With sandboxing, rulesets and partial execution, [Heuristic-Based Detection](https://en.wikipedia.org/wiki/Heuristic_analysis) and [Behaviour-Based Detection](https://www.microsoft.com/en-us/research/video/behavior-based-malware-detection/) provide some Software functionality to perform more than just signature lookup automating some of the process of detection and analysis. This aids partially in preserving the sanity of those trying to defend networks as extreme example of psychological warfare of reverse engineering possible outlined in the [Chris Domas DEF CON 23 talk suggests](https://www.youtube.com/watch?v=HlUe0TUHOIc). There is also Static and Dynamic variates of Heuristic Analysis 
- Static HA - decompling and extracting source code and then comparing it to well known virus source code
- Dynamic HA - [[Malware-Analysis]] by analylists analyze potential malicious software in isolated and controlled environments to create behavioural rules or are matched to establish behaviour rulesets to detirmine malware strain. 

[Heuristic-Based Detection](https://en.wikipedia.org/wiki/Heuristic_analysis) uses sandboxed stepping through the instruction set of a binary file, without actual execution or by attempting to decompile and then analyze the source code checking known malicious patterns and program calls. 

[Behaviour-Based Detection](https://www.microsoft.com/en-us/research/video/behavior-based-malware-detection/) dynamically analyzes the *"what does this executable do"* without compromising the system or virtual machine sandboxed environment, hopefully. Generally in a SOC  a small special section of the nework is dedicated to analysis of this kind. Typically a closed off portion network, like demilitarised zone, but  functionally disconnected is allowing for executable to attempt to connect to it C2 operators addresses without actually connecting or observable how a section of malicious code attempts to exploit some known or unknown vulnerability.

And so with combining approaches to detection and analysis, higher detection rates can be achieved. With more data means meta-information about how malicious actions take place providing firewalls, local vulnerability and website scanners, SOC, Blue and Purple teams more implementable rules and understanding of what could be happening when an incident is occuring or occured. Any findings can be tested against or added to the [VirusTotal](https://www.virustotal.com/gui/home/upload) database.

To evade all of this requires some obfuscation or changing of the contents of known malicious files to break the identifying signature. This can range from changing the strings from lower to uppercase to increasingly complex forms of evasion. Reverse-engineering malware, configuring compilation options, refactoring the code it to change the MD5 hash or any signatures that would be detected. Running in memory and attempting to migrate to a legitimate process or overflowing itself after use or cryptographically altering code.

#### Common AV Software

Antivirus Name	 | Service Name	 | Process Name
--- | --- | ---
Microsoft Defender | 	WinDefend	 | MSMpEng.exe
Trend Micro | 	TMBMSRV	 | TMBMSRV.exe
Avira	 | AntivirService, Avira.ServiceHost | 	avguard.exe, Avira.ServiceHost.exe
Bitdefender | 	VSSERV	 | bdagent.exe, vsserv.exe
Kaspersky | 	AVP<Version #> | 	avp.exe, ksde.exe
AVG	 | AVG Antivirus | 	AVGSvc.exe
Norton	 | Norton Security	 | NortonSecurity.exe
McAfee | 	McAPExe, Mfemms | 	MCAPExe.exe, mfemms.exe
Panda	 | PavPrSvr	 | PavPrSvr.exe
Avast | Avast Antivirus | 	afwServ.exe, AvastSvc.ex


## Bypassing Antivirus Detection

AV evasion has a dichotomy of evasion technique occuring either on-disk or in-memory. Generally modern malware is in-memory as it is less tracable and static in the sense that the disk state is saved, logged and monitored. If inside memory can it can migrate to processes like one of [[Meterpreter-Commands]] or have permission to remove itself, by overwritting itself in-memory by existing in it already without privilege or another privilege mechanicism to bypass to do so. 

#### Recon and Research

Firstly identify AV presence, type, versioning deployed before attempting to bypass ensures greater chances of success. Replication of the configuration of the AV deploy and its context can be used to test a bypass technique. If the bypass method for the type of AV configured won't detect it as it is not up-to-date 

#### On-Disk Evasion 
A list of obfuscate techniques used on malicious files stored on a physical disk.

Technique Name | Description | Subtechniques 
--- | --- | ---
[Packers](https://en.wikipedia.org/wiki/Executable_compression) | Reducing the size of the executable | Golang  [UPX](https://upx.github.io/) 
Obfuscators | Reorganize and mutation of code | Adding D43DC0D3, [[Refactoring]] like reordering, splitting function, variable, struct name changes
Crypters | Cryptographically altering executable code, decrypted in memory | 
Software Protectors | [Opposite of their legitmate use](https://www.enigmaprotector.com/en/home.html) | Anti-Reversing, Anti-Debugging, Sandbox Detection

#### In-Memory Techniques

![PE](Process-Injection-Info-Graphic-CCbyKarstenHahn-struppigelDOTblogspoDOTcomSLASH2017SLASH07SLASHprocess-injection-info-graphicDOThtml.png) 
[CC]:http://struppigel.blogspot.com/2017/07/process-injection-info-graphic.html)

[Used in Medium Ozan Unal](https://medium.com/@ozan.unal/process-injection-techniques-bc6396929740)

[In-Memory Process Injection](https://attack.mitre.org/techniques/T1055/) *"Adversaries may inject code into processes in order to evade process-based defenses as well as possibly elevate privileges. Process injection is a method of executing arbitrary code in the address space of a separate live process. Running code in the context of another process may allow access to the process's memory, system/network resources, and possibly elevated privileges. Execution via process injection may also evade detection from security products since the execution is masked under a legitimate process."* This technique is characters by is manipulation of [[Windows-Processes]] and their memory, without writing to physical disk.

Technique Name | Description | Links 
--- | --- | ---
Remote Process Memory Injection | Injecting a payload in to system legitimised process | [[Remote-Process-Memory-Injection]]
Reflective DLL Injection | Custom DLL loading a DLL from memory | [[Reflective-DLL-Injection]]
Process Hollowing | Bootstrapping a non-malicious executable image in a suspended state, removing image and replcaing with a malicious executable image | [[Windows-Process-Hollowing]]   
Inline Hooking | Introducing control flow redirection | [[Inline-Hooking]]
Process Doppelgänging |TxF transaction using legitimate executable then overwirting file with payload, create shared section of memory to load into, rollback changes to oringal exe, and then create process from malicious section of share memory | [[Process-Doppelganging]]

## Functions list

Function | Library | Documentation Link | Description
--- | --- | --
`LoadLibrary`| | [Documentation](https://docs.microsoft.com/en-us/windows/win32/api/libloaderapi/nf-libloaderapi-loadlibrarya) | Loads a DLL from disk.
`VirtualAllocEx` | kernel32.dll | [Documentation](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualallocex) | Reserves, commits, or changes the state of a region of memory within the virtual address space of a specified process, initializes the memory it allocates to zero.
`CreateThread` | kernel32.dll | [Documnetation](https://docs.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createthread) | Creates a thread to execute within the virtual address space of the calling process.  
`WriteProcessMemory` | | [Documentation](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-writeprocessmemory) | Writes data to an area of memory in a specified process.
`memset` | msvcrt.dll | [Documentation](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/memset-wmemset?view=msvc-170) | Sets a buffer to a specified character.


## AV Evasion Tooling

#### Whatever SOCs and Blue/Purple teams use

If its there to detect malware or a [database like VirusTotal](https://www.virustotal.com/gui/home/upload), obviously the more paranoid less and less open handling and handing over of your malware you may have to be depending on your occupation. Probably simulations and ports of these tools are used to test the testing frameworks without negilegently exposing any wonder malware. Inversily openly sharing YARA rules or safely sharing strains to make the majority of the global population safer is hard fought standard that may be fought back by all sides. Also the symbiosis of better detection mechanisms and practices means more interesting Malware to detect and greater requirement to write better Malware, which would be more interesting to write. 

#### Powershell

Although PowerShell Execution Policy settings can be dictated by one or more Active Directory GPOs, executed Scripts are more difficult for some AV to detirmine if it the script is malicious or not. This is due to scripts being interpreted not executable code. Key indicators such as variable names, comments, and logic can easily be changed and mutated. See [[Powershell-WinAPI-Interaction]] for more.

#### Golang

Golang is the *goto* language of malware development 
1. Runnable everywhere, google wants to be everywhere and so do malware writers.
2. Binaries can be minimized with default compiler -  github repository extend this, even then they can be staged if size restriction are in place.
3. Very fast compiler
4. Standard library is already very powerfully
	1. String handling
	2. Cryptography
	3. No flabby C++ feature creep
	4. Good documentation
	5. Used in Backend code with APIs and networking
5. Simple and fun to write making easier to be more creative with evasion techniques and the complexity of exploitation anyway.

#### Shellter 
[Shellter](https://www.shellterproject.com/) *"Shellter is a dynamic shellcode injection tool, and the first truly dynamic PE infector ever created. It can be used in order to inject shellcode into native Windows applications (currently 32-bit applications only). The shellcode can be something yours or something generated through a framework, such as Metasploit."* For more see the [[Shellter-Helpsheet]]

[AV Evasion Tooling from InfosecInstitute](https://resources.infosecinstitute.com/topic/antivirus-evasion-tools/)

#### Viel-Evasion

[Viel-evasion Framework](https://www.veil-framework.com/framework/veil-evasion/)

#### peCloak
peCloak from [Mike Czumak](http://www.securitysift.com/pecloak-py-an-experiment-in-av-evasion/) is python script that automates multiple tricks to evade AVs.

## References 
[AV Wiki](https://en.wikipedia.org/wiki/Antivirus_software)
[Heuristic-Based Detection Wiki](https://en.wikipedia.org/wiki/Heuristic_analysis
[Malware Wiki](https://en.wikipedia.org/wiki/Malware)
[VirusTotal](https://www.virustotal.com/gui/home/upload)
[Mitre ATT&CK](https://attack.mitre.org/)
[Mitre ATT&CK: In-Memory Process Injection](https://attack.mitre.org/techniques/T1055/)
[THM History of Malware Room](https://tryhackme.com/room/historyofmalware)
[THM MAL: Malware Introductory Room](https://tryhackme.com/room/malmalintroductory)
[THM MAL: Strings Room](https://tryhackme.com/room/malstrings)
[THM MAL: REMnux - The Redux](https://tryhackme.com/room/malremnuxv2)
[Microsoft Research on Behavior-based Malware Detection](https://www.microsoft.com/en-us/research/video/behavior-based-malware-detection/)
[AV Evasion Tooling from InfosecInstitute](https://resources.infosecinstitute.com/topic/antivirus-evasion-tools/)
[Packers and Executable Compression Wiki](https://en.wikipedia.org/wiki/Executable_compression)
[UPX](https://upx.github.io/)
[EnigmaProtector Free tool for AV evasion](https://www.enigmaprotector.com/en/home.html)
[Shellter](https://www.shellterproject.com/)
[Common Techniques Article](https://www.socinvestigation.com/most-common-antivirus-evasion-and-bypass-techniques/)
[Medium Dhanishtha Awasthi](https://offs3cg33k.medium.com/antivirus-evasion-bypass-techniques-b547cc51c371)
[Redfox Security Article](https://infosecwriteups.com/antivirus-evasion-26a30f072f76)
[Medium Ozan Unal](https://medium.com/@ozan.unal/process-injection-techniques-bc6396929740)
[THM Room Intro to AV](https://tryhackme.com/room/introtoav)