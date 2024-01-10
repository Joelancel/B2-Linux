# I. Init

🌞 **Ajouter votre utilisateur au groupe `docker`**

```
[user@localhost ~]$ sudo usermod -aG docker $USER
[user@localhost ~]$ newgrp docker
```

## 4. Un premier conteneur en vif

🌞 **Lancer un conteneur NGINX**

- avec la commande suivante :

```bash
[user@localhost ~]$ docker run -d -p 9999:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
af107e978371: Pull complete 
336ba1f05c3e: Pull complete 
8c37d2ff6efa: Pull complete 
51d6357098de: Pull complete 
782f1ecce57d: Pull complete 
5e99d351b073: Pull complete 
7b73345df136: Pull complete 
Digest: sha256:bd30b8d47b230de52431cc71c5cce149b8d5d4c87c204902acf2504435d4b4c9
Status: Downloaded newer image for nginx:latest
1f3957826ae53edb66a311718ecfee206d374a03d7cbedfebcd2814f7366e443
```
🌞 **Visitons**


```[user@localhost ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                   NAMES
1f3957826ae5   nginx     "/docker-entrypoint.…"   19 seconds ago   Up 18 seconds   0.0.0.0:9999->80/tcp, :::9999->80/tcp   jolly_moore
```

```
[user@localhost ~]$ sudo ss -lnpt
[sudo] password for user: 
State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port     Process                                                                                                         
LISTEN      0           4096                   0.0.0.0:9999                0.0.0.0:*         users:(("docker-proxy",pid=23248,fd=4))                                                                                                       
LISTEN      0           4096                      [::]:9999                   [::]:*         users:(("docker-proxy",pid=23253,fd=4)) 
```
```
[user@localhost ~]$ docker logs 1f3957826ae5
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/12/21 09:36:46 [notice] 1#1: using the "epoll" event method
2023/12/21 09:36:46 [notice] 1#1: nginx/1.25.3
2023/12/21 09:36:46 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2023/12/21 09:36:46 [notice] 1#1: OS: Linux 5.14.0-362.8.1.el9_3.x86_64
2023/12/21 09:36:46 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1073741816:1073741816
2023/12/21 09:36:46 [notice] 1#1: start worker processes
2023/12/21 09:36:46 [notice] 1#1: start worker process 28
10.0.0.1 - - [21/Dec/2023:09:48:12 +0000] "GET / HTTP/1.1" 200 615 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:120.0) Gecko/20100101 Firefox/120.0" "-"
10.0.0.1 - - [21/Dec/2023:09:48:12 +0000] "GET /favicon.ico HTTP/1.1" 404 153 "http://10.0.0.3:9999/" "Mozilla/5.0 (X11; Linux x86_64; rv:120.0) Gecko/20100101 Firefox/120.0" "-"
```
```
[user@localhost ~]$ sudo firewall-cmd --add-port=9999/tcp
success
```



🌞 **On va ajouter un site Web au conteneur NGINX**
```
[user@localhost nginx]$ curl http://10.0.0.3:9999/
<h1>MEOOOW</h1>

```

🌞 **Visitons**

- [user@localhost nginx]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                               NAMES
4c7bc3bb8a05   nginx     "/docker-entrypoint.…"   11 minutes ago   Up 11 minutes   80/tcp, 0.0.0.0:9999->8080/tcp, :::9999->8080/tcp   nifty_wilbur
```
[user@localhost nginx]$ sudo ss -lnpt
State             Recv-Q            Send-Q                       Local Address:Port                        Peer Address:Port            fd=3))                    
LISTEN            0                 4096                               0.0.0.0:9999                             0.0.0.0:*                users:(("docker-proxy",pid=23973,fd=4))  
```
- ```
[user@localhost nginx]$ curl http://10.0.0.3:9999/
<h1>MEOOOW</h1>
```

## 5. Un deuxième conteneur en vif

🌞 **Lance un conteneur Python, avec un shell**

 ```
 [user@localhost nginx]$ docker run -it python bash
Unable to find image 'python:latest' locally
latest: Pulling from library/python
bc0734b949dc: Pull complete 
b5de22c0f5cd: Pull complete 
917ee5330e73: Pull complete 
b43bd898d5fb: Pull complete 
7fad4bffde24: Pull complete 
d685eb68699f: Pull complete 
107007f161d0: Pull complete 
02b85463d724: Pull complete 
Digest: sha256:3733015cdd1bd7d9a0b9fe21a925b608de82131aa4f3d397e465a1fcb545d36f
Status: Downloaded newer image for python:latest

 ```


🌞 **Installe des libs Python**

```
root@146a91da17f7:/# pip install aiohttp
root@146a91da17f7:/# pip install aioconsole

```