
11371 HTTK : Port fermé ==> ? désactiver la signature 
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


### 7- Création d'un conteneur LXC
#### \[Question 7A\]
#### \[Question 7B\]
#### \[Question 7C\]
#### \[Question 7D\]
#### \[Question 7E\]
#### \[Question 7F\]
#### \[Question 7G\]
#### \[Question 7H\]
#### \[Question 7I\]
#### \[Question 7J\]
#### \[Question 7K\]
#### \[Question 7M\]
#### \[Question 7N\]
#### \[Question 7O\]
#### \[Question 7P\]
#### \[Question 7Q\]
#### \[Question 7R\]
#### \[Question 7S\]
#### \[Question 7T\]

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

