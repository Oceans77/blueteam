# MITRE-T1197 Threat Hunting Writeup
<div align="center">

| APT   | Government   | Hacking Group                                              |
| ------|:------------:|:----------------------------------------------------------:|
| APT39 | Iranian Gov  | Chafer, REMIX KITTEN, COBALT HICKMAN, G0087, Radio Serpens |
| APT41 | Chineese MSS | Double Dragon                                              |

| INFO                                                 |
| ---------------------------------------------------- |
| ID: T1197                                            |
| Sub-techniques:  No sub-techniques                   |
| Tactics: Defense Evasion, Persistence                |
| Platforms: Windows                                   |
| Defense Bypassed: Firewall, Host forensic analysis   |

| Name     | Malware type             |
| ---------|:------------------------:|
| Egregor  | Ransomeware-as-a-Service |
| JPIN     | Custom-built backdoor    |
| MarkiRAT | Remote Access Tool       |
| ProLock  | Ransomware               |
| UBoatRAT | Remote Access Tool       |

</div>

---

## Event Viewer Hunting
After getting my hands on some log files of an infected windows machine I was able to dive into:
> Microsoft-Windows-Windows-Defender

From here I immediately noticed a log showing that windows defender had detected an incident
</br>
![Defender Find](https://i.ibb.co/mRmZ0wq/2023-08-09-19h44-51.png)

This `Trojan:Win32/Meterpreter.O!MTB` is a clear sign that the `metasploit framework` was involved in this attack

Going further into our logs we can also see the malware attempting to spin up a Scheduled task to most likely gain persistence on the machine.
</br>
![Persistence](https://i.ibb.co/7WhSDGQ/2023-08-09-19h50-08.png)

A big thing I wanted to learn though was how did the malware get on the machine and who was the attacker?
After digging around in different logs I came accross the `Microsoft-Windows-Bits-Client Operations` log and was able to see exactly what had happened
</br>
![Attacker](https://i.ibb.co/smHNQ9N/2023-08-09-19h54-09.png)

Here we see the attackers IP
> 192.168.190.136
And the file that was being uploaded & executed
> test.exe

The attacker was utilizing the BITS service to transfer and execute the malware on the users computer. As stated on MITRE's website [here](https://attack.mitre.org/techniques/T1197/):
>Adversaries may abuse BITS jobs to persistently execute code and perform various background tasks.
>Windows Background Intelligent Transfer Service (BITS) is a low-bandwidth, asynchronous file transfer mechanism exposed through Component Object Model (COM).
>BITS is commonly used by updaters, messengers, and other applications preferred to operate in the background (using available idle bandwidth) without interrupting other networked applications.
>File transfer tasks are implemented as BITS jobs, which contain a queue of one or more file operations.

