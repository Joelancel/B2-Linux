# TP4 : Hardening Script

Le but de ce TP va être de **proposer un script qui permet de "durcir" une machine Linux.**

> On va aller un peu plus loin et l'utiliser pour un setup classique reverse proxy -> serveur web.

Le but c'est donc que vous me proposiez un script qui contient vos recommandations de sécurité pour Rocky Linux.

![God scripting](./img/god_script.png)

## Sommaire

- [TP4 : Hardening Script](#tp4--hardening-script)
  - [Sommaire](#sommaire)
- [0. Setup](#0-setup)
- [I. Setup initial](#i-setup-initial)
- [II. Hardening script](#ii-hardening-script)

# 0. Setup

➜ **Machines Rocky Linux**

- on aura un serveur web et un reverse proxy (deux machines donc)

# I. Setup initial

| Machine      | IP          | Rôle                       |
| ------------ | ----------- | -------------------------- |
| `rp.tp5.b2`  | `10.4.1.11` | reverse proxy (NGINX)      |
| `web.tp5.b2` | `10.4.1.12` | serveur Web (NGINX oci) |

🌞 **Setup `web.tp5.b2`**
```
[user@web ~]$ sudo dnf install nginx
[user@web ~]$ sudo mkdir /var/www/
[user@web ~]$ sudo mkdir /var/www/app_nulle
[user@web ~]$ sudo vim /var/www/app_nulle/index.html

[user@localhost app_nulle]$ sudo cat /var/www/app_nulle/index.html 
<h1> WOOOOOOW <h1>

[user@web ~]$ sudo cat /etc/nginx/conf.d/site.conf 
server {
    listen         80 default_server;
    listen         [::]:80 default_server;
    server_name    _;
    root           /var/www/app_nulle;
    index          index.html;
    try_files $uri /index.html;
}
```
🌞 **Setup `rp.tp5.b2`**

```
[joel@rp conf.d]$ cat site.conf 
server {
    listen    80;
    server_name   app.tp4.b2;

    location / {
        proxy_pass http://10.4.1.12;
    }
}
```


🌞 **HTTPS `rp.tp5.b2`**

- mettez en place du HTTPS avec le reverse proxy afin de proposer une connexion sécurisée aux clients
- un certificat auto-signé ça fait très bien l'affaire, vous pouvez générer une clé et un certificat avec RSA et des clés de 1024 bits avec :

```bash
openssl req -new -newkey rsa:1024 -days 365 -nodes -x509 -keyout server.key -out server.crt
```

- un exemple de configuration NGINX ressemble à :

```nginx
server {
    listen    443 ssl;
    server_name   app.tp5.b2;

    ssl_certificate     /path/to/cert;
    ssl_certificate_key /path/to/key;

    location / {
        proxy_pass http://10.5.1.12;
    }
}
```

> Je rappelle qu'il existe un endroit standard pour stocker les clés et les certificats d'une machine Rocky Linux (commun à tous les OS RedHat) : `/etc/pki/tls/private` pour les clés et `/etc/pki/tls/certs` pour les certificats.

# II. Hardening script

[lien du script](scriptsi.sh)

![Feels good](./img/feels_good.png)