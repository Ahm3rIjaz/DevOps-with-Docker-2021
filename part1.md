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
```
$ docker run -it devopsdockeruh/pull_exercise
Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"
```