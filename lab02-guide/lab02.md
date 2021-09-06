Instalación de Splunk y carga de Datos
===============
Este laboratorio es para fines educativos y su contenido no debe ser utilizado para fines ilícitos  

**Objetivos**
* Instalar la versión gratuita de Splunk 
* Familiarizar al participantes con el entorno de splunk 
* Cargar datos  

**Requisitos**
* Acceso a DOJO Cloud Lab por VPN ( openVPN)
* Conexion Remota al servidor (Putty)
* Identificar dirección IP del host - Ubuntu Server 20.04  

**Pasos**
**Actualizar eñ repositorio de paques del sistema operativo**
```
apt-get update && apt-get upgrade -y
```
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release -y