# Docker Container

## Pull image
`docker pull <image>:<tag>`.<br>
`docker pull docker.io/node:latest` or `docker pull node:latest` or `docker pull node`.<br>
`docker pull <image>:alpine` recommended for smaller image size.<br>
`docker images` see list images.<br>
![docker-1](./images/docker-1.PNG)

## Build single container web server
`nano Dockerfile`<br>
```
FROM node

MAINTAINER Arenda Bernhard Yoas <aby@arendabernhardyoas.com>

RUN git clone --single-branch --branch nodejs https://github.com/arendabernhardyoas/monitoring-laboratorium.git

WORKDIR /monitoring-laboratorium

CMD node app
```
![docker-2](./images/docker-2.PNG)
`docker build --tag monitoring-labs:1.0.0 ./`<br>
Reference build container from dockerfile [here](https://docs.docker.com/engine/reference/builder/).<br>
`docker run --detach --name monitoring-labs --publish 8080:8080 monitoring-labs:1.0.0`<br>
![docker-3](./images/docker-3.PNG)
![docker-4](./images/docker-4.PNG)
![docker-5](./images/docker-5.PNG)

## Build multi container web server and database
`docker volume create pgdata-monitoring-labs`<br>
`docker run --detach --name pg-monitoring-labs --publish 5432:5432 --volume pgdata-monitoring-labs:/var/lib/postgresql/data --env POSTGRES_PASSWORD='' postgres:latest`<br>
![docker-6](./images/docker-6.PNG)
`docker exec --interactive --tty pg-monitoring-labs bash -c "psql -U postgres"`<br>
`docker exec --interactive --tty pg-monitoring-labs bash -c "psql -U aby monitoring-laboratorium"`<br>
![docker-7](./images/docker-7.PNG)
![docker-8](./images/docker-8.PNG)
`nano Dockerfile`<br>
```
FROM node

MAINTAINER Arenda Bernhard Yoas <aby@arendabernhardyoas.com>

WORKDIR /monitoring-laboratorium

COPY . .

CMD node app
```
![docker-9](./images/docker-9.PNG)
Define environment variable for database host NodeJS.<br>
`process.env.DB_HOST`<br>
![docker-10](./images/docker-10.PNG)
`docker build --tag monitoring-labs:1.0.0 ./`<br>
`docker run --detach --name node-monitoring-labs --publish 8080:8080 --env DB_HOST=pg-monitoring-labs monitoring-labs:1.0.0`<br>
![docker-11](./images/docker-11.PNG)
`docker network create net-monitoring-labs`<br>
`docker stop <container> <container...>` stop all running containers.<br>
`docker network connect net-monitoring-labs pg-monitoring-labs`<br>
`docker network connect net-monitoring-labs node-monitoring-labs`<br>
`docker start pg-monitoring-labs node-monitoring-labs`<br>
![docker-12](./images/docker-12.PNG)
![docker-13](./images/docker-13.PNG)
![docker-14](./images/docker-14.PNG)

## Remove containers and images
`docker stop <container> <container...>` stop one or more running containers. Recommended to do first time if the containers are running.<br>
`docker rm <container> <container...>` remove one or more containers.<br>
`docker rmi <image> <image...>` remove one or more images.<br>
![docker-15](./images/docker-15.PNG)

** **

**NOTE:**<br>
Host<br>
Ubuntu Server 20.04.X AMD64<br>
VirtualBox 6.1.2X on Desktop Windows 8.1 Pro
