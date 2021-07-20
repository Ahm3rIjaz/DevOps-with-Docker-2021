# Exercises for Part 1
## 1.1
```shell
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
b01ede84ae32   nginx     "/docker-entrypoint.…"   35 seconds ago   Exited (0) 14 seconds ago             nostalgic_heisenberg
db4a9424d8e8   nginx     "/docker-entrypoint.…"   39 seconds ago   Up 36 seconds               80/tcp    relaxed_moser
fd551890d302   nginx     "/docker-entrypoint.…"   42 seconds ago   Exited (0) 5 seconds ago              epic_yonath
```

## 1.2
```shell
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

## 1.3: Secret Message
```shell
$ docker run -d -it --name secret devopsdockeruh/simple-web-service:ubuntu
dc97167360a2cd30fc900af59f11d46439bc5fcb0d1ca9f16eec206a5ebb3ebe
$ docker exec -it secret bash
root@dc97167360a2:/usr/src/app# tail -f ./text.log
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2021-07-18 17:48:31 +0000 UTC
```

## 1.4: Missing Dependencies
```shell
$ docker run -it ubuntu sh -c 'apt update;apt install curl;echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;'
```
###### OR
```shell
$ docker run -d -it --name missing-dep ubuntu
$ docker exec -it missing-dep bash
root@7fc0cda4598c:/# apt update
root@7fc0cda4598c:/# apt install curl
root@7fc0cda4598c:/# exit
exit
$ docker exec -it missing-dep sh -c 'echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;'
```
#### Output
Input website:
helsinki.fi
Searching..
```
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.helsinki.fi/">here</a>.</p>
</body></html>
```

## 1.5: Sizes of images
```shell
$ docker images
REPOSITORY                          TAG       IMAGE ID       CREATED        SIZE
devopsdockeruh/simple-web-service   ubuntu    4e3362e907d5   4 months ago   83MB
devopsdockeruh/simple-web-service   alpine    fd312adc88e0   4 months ago   15.7MB
$ docker run -d -it --name alp devopsdockeruh/simple-web-service:alpine
$ docker exec -it alp sh
/usr/src/app # tail -f ./text.log
2021-07-19 07:55:23 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
```

## 1.6: Hello Docker Hub
get overview of the repository from https://hub.docker.com/r/devopsdockeruh/pull_exercise
```shell
$ docker run -it devopsdockeruh/pull_exercise
Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"
```

## 1.7: Two line Dockerfile
##### Dockerfile
```dockerfile
# Start from the alpine image
FROM devopsdockeruh/simple-web-service:alpine

# When running docker run the command will be server
CMD server
```
##### Bash
```shell
$ docker build . -t web-server
$ docker run web-server
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /*path                    --> server.Start.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on :8080
```
## 1.8: Image for script
##### Dockerfile
```dockerfile
# Start from the alpine image
FROM ubuntu:18.04

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the hello.sh file from this location to /usr/src/app/ creating /usr/src/app/hello.sh
COPY hello.sh .

# When running docker run the command will be server
CMD ./hello.sh
```
##### Bash
```shell
$ docker build . -t curler
$ docker run -it curler
Input website:
helsinki.fi
Searching..
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.helsinki.fi/">here</a>.</p>
</body></html>
```
## 1.9: Volumes
```shell
$ docker run -v "$(pwd)/log.txt:/usr/src/app/text.log" devopsdockeruh/simple-web-service
Starting log output
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
Wrote text to /usr/src/app/text.log
$ cat ./log.txt 
2021-07-20 10:11:23 +0000 UTC
2021-07-20 10:11:25 +0000 UTC
2021-07-20 10:11:27 +0000 UTC
2021-07-20 10:11:29 +0000 UTC
```
## 1.10: Ports open
```shell
$ docker run -p 3000:8080 web-server
```
## 1.11: Spring
##### Dockerfile
```dockerfile
FROM openjdk:8

EXPOSE 8080

WORKDIR /usr/src/app

COPY . .

RUN ./mvnw package

CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
```
##### Bash
```shell
$ docker build . -t spring-example
$ docker run -p 8080:8080 spring-exapmle
```
## 1.12: Hello, frontend!
##### Dockerfile
```dockerfile
FROM ubuntu:latest

WORKDIR /usr/src/app

RUN apt-get update && apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash
RUN apt install -y nodejs

COPY . .

RUN npm install

RUN npm run build

RUN npm install -g serve

CMD ["serve", "-s", "-l", "5000", "build"]

EXPOSE 5000
```
##### Bash
```shell
$ docker build . -t example-frontend
$ docker run -p 5000:5000 example-frontend
```
## 1.13: Hello, backend!
##### Dockerfile
```Dockerfile
FROM golang:1.16

WORKDIR /usr/src/app

COPY . .

RUN go build

RUN go test ./... .

EXPOSE 8080

CMD ["./server"]
```
```shell
$ docker build . -t example-backend
$ docker run -p 8080:8080 example-backend
```