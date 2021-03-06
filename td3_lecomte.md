
11371 HTTK : Port fermé ==> ? désactiver la signature 
option pour enlever les signatures ?
# TD3 - LXC - DOCKER
### 1- Récupérer et configurer une VM
> Fait sur machine

### 2- Désactiver SELinux
On désactive de façon temporaire SELinux
```bash
setenforce 0
```

### 3- Désactiver le firewall
Également de façon temporaire, on désactive le firewall :
```bash
systemctl stop firewalld
```

### 4- Origine des conteneurs
#### \[Question 4A\]
Traduit de l'anglais, une jail est une prison. En effet, elle sert à compartimenter des processus et leurs enfants. On imagine plusieurs services ayant besoin de fonctionner sans avoir de contact avec les autres afin d'éviter des conflits.

#### \[Question 4B\]
Les jails sont utilisés dans deux cadres principaux :
1) Restreindre les accès de l'exécution d'une application sensible. Certaines application peuvent être exécutées avec de haut privilèges. Ainsi, en cas de menaces ou bien même de test, cela permet de garder le serveur sain.
2) Créer une image virtuelle du système. Cela permet l'exécution de plusieurs applications basées sur la même image. 

#### \[Question 4C\]
La conteneurisation LXC sous Linux correspond aux "Jails" de BSD.

#### \[Question 4D\]
Les Control Group (cgroup) sous Linux sont des ajouts faits par Google en 2006 au noyau Linux. Ces derniers permettent d'organiser des processus de façon hiérarchiques en fixant des limites pour chaque groupe/sous-groupe de services. Chaque accès à ces derniers est contrôlé et la mémoire définie leur est allouée. 

### 5- Les conteneurs 
#### \[Question 5A\]
Le principe de fonctionnement de la conteneurisation est l'exécution d'une application dans une seule zone du système. Cette zone, appelée conteneur contient seulement l'environnement virtuel de l'application (CPU, réseau, système de fichiers..). Le conteneur se connecte par la suite au noyau qui, lui, gère les ressources et communication inter-composants. Le principe de conteneurisation consiste donc au final en un gain de mémoire et de portabilité entre les différents systèmes d'exploitation.

#### \[Question 5B\]
La différence essentielle entre un conteneur et une VM est que les conteneurs se partagent un système d'exploitation (celui de l'hôte). Cela réduit donc les coûts de :
* stockage mémoire
* mémoire vive liées aux systèmes d'exploitation présents sur les VM
* temps de mise à jour

#### \[Question 5C\]
Les conteneurs existants les plus populaires sont :
* Docker
* Podman
* LXC (ou de façon alternative LXD)
* OpenVZ
* Linux-VServer

### 6- Installation et prise en main de LXC
On installe d'abord tous les outils nécessaires : 
```bash
dnf install wget epel-release lxc lxc-templates libvirt lxc-extra
```
On vérifie ensuite le statut de la configuration LXC
```bash
lxc-checkconfig # les différents points : control groups, misc, checkpoint, restore sont bien actifs 
```

#### \[Question 6A\]
```bash
systemctl start libvirtd
systemctl status libvirtd # le service est bien actif
```
Le service ***libvirtd*** est un "daemon" côté serveur. Il est utile pour la gestion et la création de platerformes de virtualisation.

#### \[Question 6B\]
```bash
systemctl start lxc
systemctl status lxc # le service est bien actif
```
Le service ***lxc*** quant à lui est utilisé pour la gestion et création des conteneurs qui ont étés détaillés _Question 5_.

### 7- Création d'un conteneur LXC
#### \[Question 7A\]
Les deux façons de créer un conteneur localement sous LXC sont :
* avec privilèges
* sans privilèges

#### \[Question 7B\]
Lors de la création d'un conteneur sur une image, cette erreur arrive lorsque l'on arrive pas à se connecter au serveur de clé. En effet, la signature du conteneur n'est pas trouvée en général.

#### \[Question 7C\]
Un serveur de clé permet la communication entre un serveur et un client souhaitants partager des messages chiffrés. Le serveur reçoit et stocke des clés de chiffrement par leurs propriétaires. Il les distribue par la suite aux programmes les nécessitants. En général, ces clés sont accompagnées d'un certificat.
Les requêtes effectuées sur ce type de serveur sont faites via HTTP. En revanche, cependant, les serveurs de clé sont à l'écoute sur le port 11371 qui travaille, lui, avec le protocole HKP.

#### \[Question 7D\]
La première solution serait de vérifier que le proxy côté client n'est pas bloquant quant-au port 11371. On pourrait donc temporairement désactiver le pare-feu. Une autre solution serait de désactiver la vérification la signature lors du téléchargement de la template. 

#### \[Question 7E\]
```bash
lxc-create -t download -n Debian -- --no-validate
#=========================#
|   Distribution          |
|   Name : debian         |
|   Release : 9           |
|   Architecture : arm64  |
#=========================#
# Non créé sur la machine

```

#### \[Question 7F\]
```bash
lxc-create -t download -n centos -- --no-validate
#=========================#
|   Distribution          |
|   Name : centos         |
|   Release : 7           |
|   Architecture : i386  |
#=========================#
# Créé sur la machine
```

L'option -n permet de spécifier le nom de l'hôte.

#### \[Question 7G\]
On lance le conteneur via :
```bash
lxc-start centos -d
```
Par la suite, on peut vérifier le bon déroulement des opérations via :
```bash
lxc-info centos
```
![Capture](https://user-images.githubusercontent.com/72377954/143845310-3837a40d-0595-4ff5-9fc9-a128cb31aa85.PNG)

/!\ L'option ***-d*** permet de lancer le conteneur en arrière plan.

#### \[Question 7H\]
Voici le rôle de chacune des commandes :
* lxc-info \[nomMachine\] = donne les informations sur un conteneur donné (nom,statut,pid, ip, usage mémoire et processeur...)
* lxc-execute = exécuter une commande dans un conteneur spécifié par son nom. Permet souvent le lancement rapide d'applications.
* lxc-attach = effectue la même chose mais sur un conteneur lancé. ***Execute*** doit définir la configuration à utiliser pour la virtualisation contrairement à ***Attach***. 
* lxc-top = affiche les statistiques du conteneur (usage cpu, mémoire...)

![Capture](https://user-images.githubusercontent.com/72377954/143847027-5baaf654-470a-4a6c-ad13-2c44ac28cb6e.PNG)

#### \[Question 7I\]
L'option ***-B*** permet de définir le "backingstore". Cela correspond au chemin de sauvegarde du conteneur.

#### \[Question 7J\]
Le fichier _/etc/lxc/default.conf_ sert à définir la localisation de la virtualisation par défaut. 

#### \[Question 7K\]
Le conteneur déployé est situé dans /var/lib/lxc/centos.

#### \[Question 7L\]
Le répertoire ***config*** contient la configuration par défaut du conteneur concernant : virtualisation, nom, chemins des dépendances.

#### \[Question 7M\]
Le répertoire ***rootfs*** contient, quant-à-lui, les fichiers du conteneur en lui-même. On y retrouve un répertoire personnel, les dossiers connus /etc /lib /sbin /tmp /usr /var, et bien d'autres. C'est en soit ici que le système virtuel est stocké.

#### \[Question 7N\]
L'interface ***virbr0*** est comme un commutateur virtuel installé par libvirt. C'est à lui que vont se connecter tous les conteneurs.

#### \[Question 7O\]
Les interfaces veth sont des périphériques Ethernet virtuels. En effet, ils agissent comme une sorte de pont entre la VM et les conteneurs.

#### \[Question 7P\]
La commande lxc-attach permet de lancer une commande à partir d'un conteneur. On peut donc lui demande de lancer la commande passwd. 
Soit : 
```bash
lxc-attach centos passwd

New password : test123
Retype password : test123
```

#### \[Question 7Q\]
La première façon de se connecter est d'utiliser la commande prévue par lxc, à savoir :
```bash
lxc-attach centos
```
Cela n'est pourtant pas un moyen efficace car on ne se connecte pas avec des droits complets.

On peut aussi utiliser :
```bash
chroot /var/lib/lxc/centos/rootfs/ /bin/bash
```
Ceci change le répertoire par défaut du Shell utilisé. Ainsi, on travail dans le conteneur en faisant abstraction de tout le reste.

#### \[Question 7R\]
```bash
lxc-attach -n centos
sh-4.2# ip a #192.168.122.24
sh-4.2# exit
lxc-attach -n centos yum install openssh
lxc-attach -n centos systemctl start sshd # on démarre le service
lxc-attach -n centos systemctl enable sshd # on active définitivement le service
lxc-attach -n centos systemctl status sshd # on vérifie le statut
ssh root@192.168.122.24 # fonctionnement attendu
```

#### \[Question 7S\]
Cela est impossible. En effet, la carte réseau virtuelle qui gère la connexion entre le conteneur et la machine CentOS ne le fait pas avec le reste du réseau. Il serait intéressant de créer un serveur possédant par exemple de l'IP forwarding.

#### \[Question 7T\]
Si l'on doit gérer de nombreux conteneurs, le problème principal posé serait la configuration. En effet, on ne souhaite pas passer des heures à taper les mêmes commandes et les mêmes installations sur chaque conteneur. IL serait donc intéressant de faire 2 script : 
* create_container_mysql.sh
* create_container_apache.sh
Ces derniers contiendraient des instructions communes telles que la mise à jour des paquets, l'installation des services ssh et/ou d'autres services nécessaires.
Ils contiendraient aussi des commandes relatives à la configuration basique des différents types de serveur.
Les problèmes réseau liés à des IP virtuelles fixes qui ne communiquent qu'avec la VM seraient aussi des configurations utiles à inclure.

### 8- Création d'un conteneur Docker
#### Installation de Docker
Mise à jour des paquets connus du gestionnaire de paquets yum
```bash
yum check-update
```
Ajout du dépôt des paquets docker dans la configuration de yum
```bash
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
Installation de Docker en lui-même et démarrage du service
```bash
yum install docker-ce docker-ce-cli containerd.io
systemctl start docker # démarrage de docker
systemctl status docker # vérification du bon fonctionnement
```
#### \[Question 8A\]
Les deux méthodes de création d'un conteneur avec Docker sont
* en le lançant directement à sa création
```bash
docker run –it \[image\] \[SHELL\]
```
* sans le lancer directement à sa création
```bash
docker container create --name appserver python
```
#### \[Question 8B\]
Cette interface, à l'instar de virbr0 créée par libvirt précédemment, est un pont qui fait la connexion entre les conteneurs et la VM. 
![Capture](https://user-images.githubusercontent.com/72377954/144140503-7baab3fa-3eec-4c3b-933f-05b9f1cf760e.PNG)

### 9- Utilisation d'un Dockerfile
#### \[Question 9A\]
J'ai commencé par créer un conteneur contenant un service apache configuré de bonne façon avec l'image httpd
```bash
docker pull httpd # récupération de l'image
dnf install httpd -y # téléchargement des services apache
systemctl start httpd # démarrage du service apache
systemctl status httpd # vérification
docker build -t myapachecontainer . # fabrication du conteneur à partir du Dockerfile
docker run -dit --name myContainer -p 8080:80 myapachecontainer # lancement du conteneur sur la base de sa fabrication précédente
```
Le Dockerfile contient :
> FROM httpd:2.4
> COPY ./public-html/ /usr/local/apache2/htdocs/public-html
#### \[Question 9B\]
La directive FROM sert à indiquer l'image à utilise pour le conteneur venant à être créé.
#### \[Question 9C\]
La directive COPY permet d'indiquer au DockerFile que l'on souhaite utiliser le contenu d'un fichier de l'hôte et le copier dans un des dossiers du conteneur.
#### \[Question 9D\]
Le fichier /public-html/ correspond au point d'entrée pour apache lié à notre conteneur.
#### \[Question 9E\]
_/usr/local/apache2/htdocs/_ correspond au répertoire par défaut d'Apache. C'est via ce dernier que nous verrons affiché la page d'accueil du serveur apache du conteneur.
Dans le dossier _/public-html/_ on crée le fichier index.php. On y inscrit par la suite :
```php
<?php phpinfo(); ?>
```
On lance ensuite le conteneur : 
```bash
docker exec -it myContainer bash
```
On installe ensuite les dépendances
```bash
apt-get update
apt install php libapache2-mod-php php-info
```
Finalement on redémarre le service apache :
```bash
apachectl restart # lancement
apachectl status # vérification
```

Il y a un problème avec le paquet libapache avec l'image httpd. Je n'ai pas réussi à le solutionner dans le temps impartit du TP.

#### \[Question 9F\]
Pour créer une image avec un tag (un nom) on utilise : 
```bash
docker commit myContainer apachy # docker commit [conteneurCile] [tag]
```
#### \[Question 9G\]
La commande permettant de lister les images du dépôt local est : 
```bash
docker image ls
```
#### \[Question 9H\]
Mon dépôt local contient : 
* httpd
* hello-world
* apachy

#### \[Question 9I\]
Afin de supprimer une image on utilise la commande : 
```bash
docker rmi [image]
```
#### \[Question 9J\]
Afin de démarrer le conteneur on utilise  :
```bash
docker run apachy
```
Ci-dessous la liste des différentes options : 
* -it : lance une console interactive du conteneur
* --name : indique le nom que le souhaite donner au conteneur
* -p (publish) : détermine un port d'écoute pour le conteneur
* -d : lance le conteneur en arrière plan et affiche son PID

#### \[Question 9K\]
On peut vérifier que le conteneur est lancé avec la commande 
```bash
docker ps
```
Cette commande affiche les différents conteneurs lancés avec leur PID.

#### \[Question 9L\]
Le périphérique d'écoute du conteneur est veth100aw4. Ce dernier est, à l'instar de celui que nous avions pour lxc, un pont correspondant à une interface virtuelle pour le conteneur.
#### \[Question 9M\]
La commande permettant de connaître l'ip du conteneur est :
```bash
docker inspect -f \[id_conteneur\]
```
#### \[Question 9N\]
Ayant mis mon conteneur à l'écoute sur le port 8080, en utilisant l'url ***10.30.111.65:8080*** j'ai bien accès à ce dernier. Cependant, selon l'énoncé du TP, cela ne devrait pas marcher car aucune écoute n'est mise en place.

#### \[Question 9O\]
Les deux fichiers sont les mêmes. Nous avons en effet mis en place une sorte de redondance entre la VM et le conteneur dans le fichier public-html.

### 10- Création d'une image personnalisée avec un Dockerfile
La commande RUN dans un dockerfile permet de lancer une commande dans le conteneur avant qu'il ne soit lancé (en général des M.A.J..). 
#### \[Question 10A\]
Tout d'abord on crée l'image à partir d'un Dockerfile complet : 
![dockerfile](https://user-images.githubusercontent.com/72377954/144480532-0ae4efee-0340-431c-bd0e-c8799f39af23.PNG)

Ce dernier contient les directives pour créer un fichier d'accès au dossier apache, la mise à jour des paquets dnf, l'installation des paquets apache et php ainsi que la mise en écoute du port 80. 

On crée par la suite l'image : 
```bash
docker build -i web /var/www/html/public-html
```
On peut désormais créer et lancer le conteneur avec :
```bash
docker run -itd -p 8080:80 --name web --privileged web:latest /usr/sbin/init
```
On lance ici le conteneur en tant que démon (avec une console virtuelle cachée par ailleurs). Aussi, on le nomme "web". On lui indique qu'il doit fonctionner en tant que conteneur privilégié afin d'avoir accès au systemd (je n'ai pas compris pourquoi...). 

On crée par la suite un fichier _index.php_ dans le conteneur avec la commande ***phpinfo()*** dans le dossier _/var/www/html_.

```bash
docker exec -it web bash # on lance le conteneur avec le SHELL Bash
systemctl start httpd
cd /var/www/html
touch index.php
```
***Fichier index.php***
```php
<?php phpinfo(); ?>
```

Afin de vérifier que l'image est lancée on utilise : 
```bash
docker ps
```
Afin de vérifier que le serveur apache marche bien 
```bash
systemctl status httpd # active
```
On peut désormais vérifier à nouveau le bon fonctionnement d'apache et du moteur php en allant sur le port 8080 de l'ip de la VM (10.30.111.65). 
La page construite avec phpinfo() s'affiche bien. Tout est donc fonctionnel.
[2021-12-02 19_30_04-PHP 8 0 13 - phpinfo()](https://user-images.githubusercontent.com/72377954/144482198-0873ae57-f2d7-4236-b3bd-cdc30300109e.png)
