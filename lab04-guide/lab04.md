Docker Installation
Step 1 - Let's Upgrade and Install 
```
apt update && apt upgrade -y
```
Step 2 - Install Docker

```
apt install docker.io
```
Step 3 - Enabling the Service

```
systemctl enable docke
```
Step 4 - Start the Service
```
systemctl start docker
```
Step 5 - Verify the Status
```
systemctl status docker
```
Xplico Installation and Setup
Step 1 - Go to /opt directory
Let's go to /opt

cd /opt
git clone https://github.com/geekscrapy/docker-xplico.git
Step 3 - Access the folder
We access the cloned folder, with the following command:

cd docker-xplico
Step 4 - Build
Now let's build with the following command:

docker build -t xplico .

Step 5 - Build a docker-compose.yaml
Now let's create or build a 
```
docker-compose.yaml file.
```
nano docker-compose.yaml
This YAML file will make it easier for us to create containers, connect them, enable ports, volumes, etc.
```
version: &#39;3.3&#39;
services:
xplico:
build: .
image: xplico

hostname: xplico
container_name: xplico
ports:
- 9876:9876
restart: always/
```