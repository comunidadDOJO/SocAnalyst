### Guía de Instalación de OpenVAS en Kali Linux

OpenVAS es un Open source Vulnerability scanner muy útil que permite encontrar fallas de seguridad e información detallada de vulnerabilidades que pueden ser explotadas para poner en peligro la confidencialidad, la disponibilidad y la integridad de los datos almacenados y procesados en nuestros equipos. Abajo encontrarás los pasos de instalación requeridos.

### Paso 1: Actualiza el sistema operativo
Ejecutando en una terminal el siguiente comando :

```
sudo-apt-get update
```

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/60082995d953874d8648e4dc_1%20IMAGEN.jpg)

```
sudo-apt-get upgrade
```

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/600829ad1bf9579a0f830556_2%20IMAGEN.jpg)

### Paso 2. Instala GVM

```
sudo apt-get install gvm*
```

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/600829c1c699139cca51809b_3%20IMAGEN.jpg)

__Nota: De aparecer un mensaje de configuración de paquetes, colocar "yes" ya que es un proceso para reiniciar servicios de ciertas librerías__

### Paso 3: Inicia la configuración de openvas

```
sudo gvm-setup
```

Se iniciará la descarga de todas las firmas que utiliza Openvas para detectar vulnerabilidades



![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/600829d3e1a49b0ec39c46e0_4%20IMAGEN.jpg)

El siguiente comando mostrará la contraseña del admin al final:
```
sudo gvm-setup
```

_Si se olvidó de escribirla o guardarla, puede restablecer la contraseña del admin con el comando gvmd explicado al correr el comando anterior_

Para crear otro usuario y contraseña puedes utilizar el siguiente comando y ejemplo:
```
sudo runuser -u _gvm -- gvmd --create-user=kali --new-password=12345 
```
Este comando generara una contraseña la cual debes copiar y utilizar

**Esta tarea puede tomar un aproximado de 15 a 30 minutos, dependiendo de las especificaciones de la máquina virtual y la red** 

### Paso 4 Instala UFW

```
sudo apt-get install ufw
```

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/600829eb76d3fa54749fb239_5%20IMAGEN.jpg)

### Paso 5 Habilita UFW y permite el acceso al servidor de OpenVAS a traves de los puertos 80 y 9392

```
sudo ufw enable
```
```
sudo ufw allow 80
```
```
sudo ufw allow 9392
```

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/60082a0ad13897bd5975ef50_6%20IMAGEN.jpg)

### Paso 6 Instala el asistente de greenbone
```
sudo apt-get install -y greenbone-security-assistant
```

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/60082a1fa43b8c5c5e7a272f_7%20IMAGEN.jpg)

### Paso 7: Confirma que OpenVas estés instalado correctamente y listo para ser usado
```
sudo gvm-check-setup
```

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/60082a311c522be77f6e80a2_8%20IMAGEN.jpg)

### Paso 8 inicia Open y haz login

En una terminal ejecuta 
```
sudo gvm-start
```
 y luego abre un browser.

https://localhost:9392/

![Image for post](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/60082a3e0a929d9013e65135_1*i7OcH3rEYxbBPyjYZ83Rlg.png)

![img](https://uploads-ssl.webflow.com/5e6befb8c88b0e98c69b1333/60082a74a911e45fed592f8b_9%20IMAGEN.jpg)

Guía de Instalación de OpenVAS en Kali Linux

__Ultima actualización 10/22/21__