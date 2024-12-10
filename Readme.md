# TD Docker

## Exercice 1 - Installation

1. Installer la dernière version du logiciel Docker
2. Vérifier que la version de docker est bien installée (docker --version)
```
root@PORT-43814:~# docker --version
Docker version 24.0.7, build 24.0.7-0ubuntu2~22.04.1
```
   
## Exercice 2 - Manipulation basique de conteneur
Le but de cet exercice est de comprendre comment fonctionne la création, la suppression et la
modification d’un conteneur docker.

1. Afficher tous les conteneurs actuellement lancés sur votre machine.
```
root@PORT-43814:~# docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

2. Créer un conteneur Ubuntu.
```
root@PORT-43814:~# docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        latest    66f8bdd3810c   8 days ago    192MB
ubuntu       latest    b1d9df8ab815   2 weeks ago   78.1MB

root@PORT-43814:~# docker run --name ubunutu_exercice2 -it ubuntu

root@cc7fb5c910b7:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

3. Créer un conteneur Nginx (détaché́ du terminal)
```
root@PORT-43814:~# docker run --name nginx_exercice2 -d nginx
f4975426199adba04142103c02eef9bf0aefa3d15ac4446ee8acac410e11288b

root@PORT-43814:~# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
f4975426199a   nginx     "/docker-entrypoint.…"   9 seconds ago   Up 8 seconds   80/tcp    nginx_exercice2
```

4. Afficher l’adresse IP du container Nginx.
```
root@PORT-43814:~# docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx_exercice2
172.17.0.2
```

5. Afficher le contenu de la page index.html dans votre navigateur.
```
Etant sur une WSL, j'ai exposé le port 80 du conteneur sur le port 8080 de ma WSL.

root@PORT-43814:~# docker run --name nginx_exercice2 -d -p 8080:80 nginx
root@PORT-43814:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.21.219.172  netmask 255.255.240.0  broadcast 172.21.223.255
        inet6 fe80::215:5dff:fe78:6d7d  prefixlen 64  scopeid 0x20<link>
        ether 00:15:5d:78:6d:7d  txqueuelen 1000  (Ethernet)
        RX packets 79812  bytes 118460744 (118.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2840  bytes 254813 (254.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```
Depuis mon navigateur, je renseigne l'adresse IP de ma WSL suivis du port 8080 :
![Screenshot Welcome to Nginx.](https://github.com/PimsX9/IMT-INTES/blob/main/Docker/TDs/WelcomeToNginx.png)

6. A l’aide de la ligne de commande exporter uniquement le nom de tous les conteneurs actifs dans un fichier appelé́
conteneur-actif.txt
```
root@PORT-43814:~# docker ps --format "{{.Names}}" > conteneur-actif.txt
root@PORT-43814:~# cat conteneur-actif.txt
nginx_exercice2
```
7. Supprimer votre conteneur Nginx et votre conteneur Ubuntu.
```
root@PORT-43814:~# docker stop nginx_exercice2
nginx_exercice2

root@PORT-43814:~# docker rm nginx_exercice2 ubunutu_exercice2
nginx_exercice2
ubunutu_exercice2
```
## Exercice 3 - Manipulation de volume
Le but de cet exercice est de comprendre comment fonctionne la création, la suppression et la modification d’un volume docker.

1. Lister tous les volumes présents sur votre machine.
```
root@PORT-43814:~# docker volume ls
DRIVER    VOLUME NAME
local     1d59d3403f0648ff7f34c87e7e45962f21bdc8ceae9a66e65483ef545e94a3ef
```
2. Stocker le nom de tous les volumes dans un fichier nommé volume.txt.
```
root@PORT-43814:~# docker volume ls --format {{.Name}} > volume.txt
root@PORT-43814:~# cat volume.txt
1d59d3403f0648ff7f34c87e7e45962f21bdc8ceae9a66e65483ef545e94a3ef
```
3. Créer un volume appelé́ html-nginx.
```
root@PORT-43814:~# docker volume create html-nginx
html-nginx

root@PORT-43814:~# docker volume ls
DRIVER    VOLUME NAME
local     1d59d3403f0648ff7f34c87e7e45962f21bdc8ceae9a66e65483ef545e94a3ef
local     html-nginx
```
4. Monter ce volume sur le path /usr/share/nginx/ d’un conteneur nginx.
```
root@PORT-43814:~# docker run --name nginx_exercice2 -d -v html-nginx:/usr/share/nginx/ -p 8080:80 nginx
bb2ef41040766443aaffd39c4b150e484f9a64a888aa93f4501182f7ac8132eb
```
5. Monter ce volume sur le path /usr/html/file d’un conteneur ubuntu.
```
root@PORT-43814:~# docker run --name ubuntu_exercice3 -ti -v html-nginx:/usr/html/file ubuntu
root@d1ba52d6d0e1:/#
```
6. Au sein du conteneur ubuntu modifier le fichier index.html ajouter dans le body <p>Hello world</p>
```
root@d1ba52d6d0e1:/usr/html/file/html# cat index.html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

root@d1ba52d6d0e1:/usr/html/file/html# cat index.html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<p>Hello world</p>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
7. Rendez-vous sur le conteneur nginx.
```
root@PORT-43814:~# docker exec -ti nginx_exercice3 /bin/bash
```
8. Installer curl.
```
root@b52317ec8edc:/# apt-get install curl
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
curl is already the newest version (7.88.1-10+deb12u8).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
9. Lancer curl localhost.
```
root@b52317ec8edc:/# curl localhost
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<p>Hello world</p>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
10. Qu’en concluez-vous ?
```
Le volume html-nginx est partagé par les deux conteneurs
```
## Exercice 4 - Création d'images
Objectif : Créer une image avec et sans dockerfile.

1. Clonage du projet : https://gitlab.com/Jimmy.Trackoen/docker-simple-api.git
```
Utilisateur@PORT-43814 MINGW64 ~/Documents/IMT/INTES_RBB/Docker/TDs/Exercice_4
$ git clone https://gitlab.com/Jimmy.Trackoen/docker-simple-api.git
Cloning into 'docker-simple-api'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (5/5), done.
```
2. Analyser le fichier app.js du projet pour en comprendre son fonctionnement.

3. Créer une image permettant de lancer l’application en utilisant la méthode “commit” à partir de l’image UBUNTU, nommer cette image simple-webapp:1.0.0
```
osadmin@PORT-43814:~$ docker run --name ubuntu_exercice4 -ti ubuntu /bin/bash
root@70a97b9a60f2:/# apt update
root@70a97b9a60f2:/# apt-get install nodejs
root@70a97b9a60f2:/# apt-get install git
root@70a97b9a60f2:/# apt-get install npm
root@70a97b9a60f2:/# cd /opt/app/

root@70a97b9a60f2:/opt/app# git clone https://gitlab.com/Jimmy.Trackoen/docker-simple-api.git
Cloning into 'docker-simple-api'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (5/5), done.
root@70a97b9a60f2:/opt/app# cd docker-simple-api/
root@70a97b9a60f2:/opt/app/docker-simple-api# npm install

Sur une autre CLI :
root@PORT-43814:~# docker commit ubuntu_exercice4 simple-webapp:1.0.0
sha256:6501d9cae189ab3ef95f923e651e94a510e05448980ea5a6a0080442a3be52cf
root@PORT-43814:~# docker images
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
simple-webapp   1.0.0     6501d9cae189   15 seconds ago   971MB
nginx           latest    66f8bdd3810c   12 days ago      192MB
ubuntu          latest    b1d9df8ab815   2 weeks ago      78.1MB
```
4. Lancer l’application dans le conteneur sur le port 8080
```
root@PORT-43814:~# docker run -ti -p 8888:8080 --name simple-webapp_exercice4 simple-webapp:1.0.0
root@326a4ddc5069:/# cd /opt/app/docker-simple-api/
root@326a4ddc5069:/opt/app/docker-simple-api# node app.js
Example app listening on port 8080
```
5. Accéder à l’application via votre navigateur sur le port 8888

![Screenshot Welcome to NodeJS.](https://github.com/PimsX9/IMT-INTES/blob/main/Docker/TDs/TD_Docker_exercice_4.png)

6. Créer une image permettant de lancer l’application en utilisant la méthode “dockerfile” à partir de l’image qui vous semble la plus pertinente, nommer cette image simple-webapp:2.0.0
```
root@PORT-43814:~# vim Dockerfile

root@PORT-43814:~# cat Dockerfile
FROM node:lts
WORKDIR /opt
#Installation des dépendences
RUN apt-get update && apt-get install -y  git
#Installation/Configuration applicatives
RUN cd /opt/ && git clone https://gitlab.com/Jimmy.Trackoen/docker-simple-api.git && cd docker-simple-api/ && npm install
CMD cd /opt/docker-simple-api && npm run start

root@PORT-43814:~# docker build -t simple-webapp:2.0.0 .

root@PORT-43814:~# docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
simple-webapp   2.0.0     001b5cc3b906   2 minutes ago   1.15GB
simple-webapp   1.0.0     6501d9cae189   5 hours ago     971MB
node            lts       5dc60a49c105   5 days ago      1.12GB
nginx           latest    66f8bdd3810c   12 days ago     192MB
ubuntu          latest    b1d9df8ab815   2 weeks ago     78.1MB
```
7. Lancer l’application dans le conteneur sur le port 8082 (sans modifier le code source de l’application)
```
root@PORT-43814:~# docker run -ti --env PORT=8082 -p 8899:8082 --name simple-webapp_exercice4_Q7 simple-webapp:2.0.0
root@2d570ea1207c:/opt# printenv | grep PORT
PORT=8082
```
8. Accéder à l’application via votre navigateur sur le port 8899

![Screenshot Welcome to NodeJS.](https://github.com/PimsX9/IMT-INTES/blob/main/Docker/TDs/TD_Docker_exercice_4_Q7.png)

## Exercice 5 - Troubleshooting et déploiement
### Cette exercice est volontairement plus difficile que les exercices du dessus ##
Objectif : Monter en compétences sur le “troubleshooting” (capacité à réparer un environnement/application non fonctionnel), s’autoformer sur le fonctionnement et lancement
d’une application NodeJs.

1. Cloner le projet : https://gitlab.com/Jimmy.Trackoen/docker-db-connection.git
```
Utilisateur@PORT-43814 MINGW64 ~/Documents/IMT/INTES_RBB/Docker/TDs/Exercice_4
$ git clone https://gitlab.com/Jimmy.Trackoen/docker-db-connection.git
Cloning into 'docker-db-connection'...
remote: Enumerating objects: 6, done.
remote: Total 6 (delta 0), reused 0 (delta 0), pack-reused 6 (from 1)
Receiving objects: 100% (6/6), done.
```
2. Analyser le fichier app.js.

3. Lancer un conteneur à partir de l’image mysql écoutant sur le port 3306
```
root@PORT-43814:~# docker run -d -p 3306:3306 --env MYSQL_ROOT_PASSWORD=paswrd --name mysqlDB_exercice5 mysql
Unable to find image 'mysql:latest' locally
latest: Pulling from library/mysql
2c0a233485c3: Pull complete
cb5a6a8519b2: Pull complete
570d30cf82c5: Pull complete
a841bff36f3c: Pull complete
80ba30c57782: Pull complete
5e49e1f26961: Pull complete
ced670fc7f1c: Pull complete
0b9dc7ad7f03: Pull complete
cd0d5df9937b: Pull complete
1f87d67b89c6: Pull complete
Digest: sha256:0255b469f0135a0236d672d60e3154ae2f4538b146744966d96440318cc822c6
Status: Downloaded newer image for mysql:latest
649bf1dddf2568964ed4ab5caa2ebe1da2191d55107cd6297fc0f7bfc9def658

root@PORT-43814:~# docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED             STATUS             PORTS
                       NAMES
649bf1dddf25   mysql                 "docker-entrypoint.s…"   6 seconds ago       Up 5 seconds       0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysqlDB_exercice5
2d570ea1207c   simple-webapp:2.0.0   "docker-entrypoint.s…"   About an hour ago   Up About an hour   0.0.0.0:8899->8082/tcp, :::8899->8082/tcp              simple-webapp_exercice4_Q7
```
4. Lancer un conteneur à partir du projet cloner à l’étape 1.
```
J'ai remarqué dans le Dockerfile l'absence de la commande 'npm install', je la rajoute :

root@PORT-43814:~/docker-db-connection# vim Dockerfile
root@PORT-43814:~/docker-db-connection# cat Dockerfile
FROM node:16.13.1
WORKDIR /app
COPY . .
RUN npm install
ENTRYPOINT ["npm", "start"]

root@PORT-43814:~/docker-db-connection# docker build -t webapp-db-connection:1.0.0 .
root@PORT-43814:~/docker-db-connection# docker images | grep webapp-db
webapp-db-connection   1.0.0     bda408360d96   57 seconds ago   916MB

root@PORT-43814:~/docker-db-connection# docker run -d -p 8787:8080 --name webapp-db-connection_exercice5 webapp-db-connection:1.0.0
10ea817ba09d1959048df0f5e6b127e1734350344520bd8bca6e021e5be6ca96
root@PORT-43814:~/docker-db-connection# docker ps
CONTAINER ID   IMAGE                        COMMAND       CREATED         STATUS         PORTS
                         NAMES
10ea817ba09d   webapp-db-connection:1.0.0   "npm start"   7 seconds ago   Up 6 seconds   0.0.0.0:8787->8080/tcp, :::8787->8080/tcp   webapp-db-connection_exercice5

```
5. Rendez-vous via votre navigateur sur la page localhost:8787 et assurez-vous que votre application vous affiche bien le message “Connection à la DB successful."
```
La connexion est KO avec le conteneur créé a la question 4.
En regardant dans les logs, je vois qu'il y a un souci sur l'adresse IP de connexion, le conteneur essai de se connecter sur le localhost "127.0.0.1" :
root@PORT-43814:~/docker-db-connection# docker logs webapp-db-connection_exercice5 | grep error
node:events:368
      throw er; // Unhandled 'error' event
      ^

Error: connect ECONNREFUSED 127.0.0.1:3306
    at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1161:16)
Emitted 'error' event on Connection instance at:
    at Connection._notifyError (/app/node_modules/mysql2/lib/connection.js:236:12)
    at Connection._handleFatalError (/app/node_modules/mysql2/lib/connection.js:167:10)
    at Connection._handleNetworkError (/app/node_modules/mysql2/lib/connection.js:180:10)
    at Socket.emit (node:events:390:28)
    at emitErrorNT (node:internal/streams/destroy:157:8)
    at emitErrorCloseNT (node:internal/streams/destroy:122:3)
    at processTicksAndRejections (node:internal/process/task_queues:83:21) {
  errno: -111,
  code: 'ECONNREFUSED',
  syscall: 'connect',
  address: '127.0.0.1',
  port: 3306,
  fatal: true
}

Je récupère donc l'adresse IP du conteneur mysqlDB_exercice5, pour l'ajouter par la suite en variable d'environnement :
root@PORT-43814:~/docker-db-connection# docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mysqlDB_exercice5
172.17.0.3

root@PORT-43814:~/docker-db-connection# docker run -d -p 8787:8080 --env HOST=172.17.0.3 --name webapp-db-co
nnection_exercice5 webapp-db-connection:1.0.0
78c8efcc60a46170939458f045a84f2ea6fe84d8b3c271cb470a3c060fccde6a

Accès a la page localhost:8787 OK
```
![Screenshot Welcome to NodeJS.](https://github.com/PimsX9/IMT-INTES/blob/main/Docker/TDs/TD_Docker_exercice_5_Q5.png)

6. Créer un compte sur https://hub.docker.com/

[Mon compte Docker Hub](https://hub.docker.com/u/pimsx9)

7. Nommer votre image docker-db-connection:1.0.0
```
root@PORT-43814:~/docker-db-connection# docker build -t pimsx9/docker-db-connection:1.0.0 .
root@PORT-43814:~/docker-db-connection# docker images | grep docker-db
pimsx9/docker-db-connection   1.0.0     bda408360d96   About an hour ago   916MB

```
8. Pousser votre image dans un repository public.
```
root@PORT-43814:~/docker-db-connection# docker login
root@PORT-43814:~/docker-db-connection# docker push pimsx9/docker-db-connection:1.0.0
The push refers to repository [docker.io/pimsx9/docker-db-connection]
5375b6290582: Pushed
b3fccc7a3d22: Pushed
0451e92fbb08: Pushed
2ad69b8bbc87: Mounted from library/node
3381de1613af: Mounted from library/node
2499085b8d55: Mounted from library/node
034b01b1f757: Mounted from library/node
6ca8686315b6: Mounted from library/node
9c2b7d0c8e89: Mounted from library/node
79a45871588c: Mounted from library/node
bdfff16e8653: Mounted from library/node
dc4f2875405c: Mounted from library/node
1.0.0: digest: sha256:d5433e4ca7d7ae28684fb41b15a61bdd35f528db3a844295c65e121a30c5283f size: 2840
```
9. Documenter votre repository afin que de futurs utilisateurs sachent quels paramètres à renseigner pour que l’application fonctionne convenablement.
A noter :
- Dans le Dockerfile du projet docker-db-connection, il manque quelques petites choses pour que notre image soit prête à lancer notre application convenablement.
- Souvenez-vous : Docker seul, n’est pas très bon pour la résolution DNS (retrouver une adresse IP à partir d’un nom de domaine), la commande docker inspect sera votre ami...

   [Lien Docker Hub](https://hub.docker.com/repository/docker/pimsx9/docker-db-connection/general)
