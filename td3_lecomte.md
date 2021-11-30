
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
#### \[Question 8A\]
#### \[Question 8B\]

### 9- Utilisation d'un Dockerfile
#### \[Question 9A\]
#### \[Question 9B\]
#### \[Question 9C\]
#### \[Question 9D\]
#### \[Question 9E\]
#### \[Question 9F\]
#### \[Question 9G\]
#### \[Question 9H\]
#### \[Question 9I\]
#### \[Question 9J\]
#### \[Question 9K\]
#### \[Question 9M\]


### 10- Création d'une image personnalisée avec un Dockerfile
#### \[Question 10A\]


### 11- Pour aller plus loin
### 12- Liens utiles

