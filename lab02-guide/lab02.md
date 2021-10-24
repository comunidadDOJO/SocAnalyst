Instalación de Splunk 
===============
Este laboratorio es para fines educativos y su contenido no debe ser utilizado para fines ilícitos  

**Objetivos**
* Instalar la versión gratuita de Splunk 
* Familiarizar al participantes con el entorno de splunk 
* Cargar datos  

**Requisitos**
* Desde VirtuaBox, inciar las máquinas virtuales de Kali Linux y Ubuntu Server 20.04
* Identificar dirección IP del host - Ubuntu Server 20.04. Desde la termimal de este servidor escriba ese comando: 
``` 
ifconfig
``` 
Ahora anote la dirección, debe algo parecido a 10.0.2.15

* Conexion Remota al servidor 
Desde Kali. 

Debe utilizar el IP de su servidor ubuntu 20.04 y utilizar las credenciales compartidad en el canal en Discord.
 
**_NOTE:_**  Reemplase usuario y dirección IP por los asociados sus servidor ubuntu


``` 
ssh usuario@direcciónIP
``` 

**Actualizar eñ repositorio de paquetes del sistema operativo**
```
apt-get update && apt-get upgrade -y
```
A continuación, descargaremos los paquetes Dockers y los actualizaremos a la versión más reciente con los siguientes comandos:

```
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release -y
```
```
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
 ```
 ```
 echo \ "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu/ $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
 ```
 ```
apt-get update && apt-get upgrade -y
 ```
 ```
  apt-get install docker-ce docker-ce-cli containerd.io -y
````
Y por último, habilitaremos dockers para que se inicie al arrancar el ordenador y luego iniciaremos el servicio nosotros mismos de forma manual. Utiliza el siguiente comando:
```
systemctl enable docker && systemctl start docker
```
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
```
sudo chmod +x /usr/local/bin/docker-compose
```
```
docker-compose version
```
```
nano docker-compose.yml 
```
```
version: "2"

networks:
  splunknet:
    driver: bridge

volumes:
  splunk-data: {}

services:
  splunk:
    networks:
      splunknet:
        aliases:
          - splunk
    image: splunk/splunk
    restart: always
    container_name: splunk
    environment:
      - SPLUNK_START_ARGS=--accept-license
      - SPLUNK_PASSWORD=d0j02020
      - DEBUG=true
    ports:
      - 8083:8000
      - 8084:8089
    volumes:
      - splunk-data:/opt/splunk
```

Ahora descargamos e instalamos la imagen de Splunk desde el hub Docker usando los datos del archivo .yml que acabamos de crear usando el siguiente código docker-compose up. Este comando también nos permitirá ejecutar splunk para que podamos acceder a la interfaz web y empezar a utilizarla.

```
docker-compose up
```

Ahora confirmamos que splunk está en funcionamiento abriendo una nueva terminal y utilizando el comando
```
sudo docker ps
```
Y por último, vamos a nuestro navegador, escribimos la IP de nuestro servidor y el puerto 8083. (x.x.x.x:8083) 
![alt text](./lab02-images/lab02-fig1-splunk.png "Metasploit framework")
* usuario : admin
* contraseña: d0j02020

Felicidades ya lograste instalar Splunk


Para mayor información
[Sitio oficial de Splunk] (https://www.splunk.com/)
[Curso gratuito oficial de Splunk] (https://www.splunk.com/en_us/training/free-courses/)(splunk-fundamentals-1.html)