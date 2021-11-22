# TD2 - MySQL
### 1- Récupération et déploiement de la VM
> Fait sur machine

### 2- Désactivation du pare-feu

On vérifie premièrement que le pare-feu est actif avec la commande :
```bash
firewall-cmd --state #running
```
On déscative ensuite le pare-feu avec :
```bash
systemctl stop firewalld
```
Et on vérifie la bonne exécution avec : 
```bash
firewall-cmd --state #not running
```

***Ne pas oublier que cette modification est temporaire***


### 3- Installation

#### Question 3A

On installe le package avec la commande :
```bash
dnf install mariadb-server
```
On utilise ensuite : 
```bash
rpm -ql mariadb-server
```
Ce package contient toutes les dépendances nécessaires à l'installation d'un serveur MariaDB.

Question 3B]_
MariaDB est ce qu'on appelle un "fork". C'est donc un dérivé logiciel de MySQL. Il a été créé pour répondre à un besoin de logiciel libre.

### 4- Prise en main

#### Question 4A

Le fichier de configuration principal de mariadb est situé à :
> /etc/my.cnf

#### Question 4B

Ce fichier est au format CNF. Ce format fut créé par MySQL et correspond à un fichier de paramétrage.

#### Question 4C

Ce fichier contient quelques caractéristiques principales telles que : 
* l'encodage des caractères par défaut
* les délais d'expiration
* les paramètres de cache par défaut

#### Question 4D

Le répertoire de stockage des bases de données par défaut est : 
> /var/lib/mysql

#### Question 4E

#### Question 4F


### 5- Démarrer le service mariadb
 
### 6- Connexion au service

### 7- Sécurisation

### 8- Création d'une base de données via script SQL

### 9- Gestion des comptes et accès

### 10- Sauvegarde et restauration
 
### 11- Outils graphiques

### 12- Réplication comme solution de sauvegarde ?

### 13- Pour aller plus loin

