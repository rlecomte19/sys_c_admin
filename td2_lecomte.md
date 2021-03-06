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

Le répertoire /var/lib existe pour stocker des données qui peuvent être modifiées lors de l'exécution d'un programme. Normalement, chaque programme possèdes ses données. Il est donc légitime de placer les bases de données à cet endroit dans un dossier spécifique _mysql_. Cependant, lorsque l'on est dans le cadre de gestion de grandes bases de données, la taille de ce dossier ne devient plus suffisante.

#### Question 4F

Une utilisation plus conforme dans le cadre d'un serveur de production serait de gérer les bases dans un disque dur ou une baie à part. Ainsi, il n'y aurait pas ou du moins plus difficilement de problème de place.


### 5- Démarrer le service mariadb
#### Question 5A
On peut démarrer le service mariadb avec la commande :
```bash
systemctl start mariadb
systemctl status mariadb # On vérifie si tout s'est bien passé
```
Pour connaître les services à l'écoute sur le réseau on peut utiliser la commande netstat et rechercher par état "LISTEN" : 
```bash
netstat -tulpn | grep "LISTEN"
```

On trouve ainsi que le port d'écoute de ce service est **3306**.

#### Question 5B
Par défaut,  le service mariadb écoute sur l'interface de loopback, à savoir le localhost. Il écoute donc sur **127.0.0.1**.

### 6- Connexion au service
#### Question 6A
On peut se connecter au service mariadb par la commande : 
```bash
mysql
```

#### Question 6B
Cette option permet de définir un protocole de transport pour se connecter au serveur. Il existe :
* TCP/IP
* SOCKET
* PIPE
* MEMORY

#### Question 6C
L'autre protocole pour joindre mariadb est le socket Unix. 

#### Question 6D
Le protocole utilisé par défaut est le protocole SOCKET. En effet, _netstat_ nous indique l'utilisation du fichier : 
> /var/lib/mysql/mysql.sock

### 7- Sécurisation
#### Question 7A
Le compte administrateur utilisé par mariadb est le compte root. On peut le vérifier dans plusieurs des tables de la base de données "mysql".

#### Question 7B
On se connecte avec l'ip du serveur : 
```bash
ip a #192.168.100.183
mysql --host=192.168.100.183
```
Cela nous apprend que cette dernière n'est pas autorisée à se connecter au serveur MariaDB. 

#### Question 7C

On se connecte grâce à la commande : 
``` bash
mysql -h 127.0.0.1
```
Une fois dans la console mysql on peut utiliser _"\s"_ pour vérifier le statut de la connection au serveur. Ainsi on voit bien l'utilisation du protocole TCP/IP
> Connection:       127.0.0.1 via TCP/IP

#### Question 7D
De même que pour la connection précédente, j'ai utilisé la commande :
``` bash
mysql -h localhost
```
Une fois dans la console mysql on peut utiliser _"\s"_ pour vérifier le statut de la connection au serveur. Ainsi on voit bien l'utilisation du protocole SOCKET UNIX.
> Connection:     Localhost via UNIX socket

#### Question 7E
La seule chose sur laquelle repose la sécurité actuelle est que seul localhost en tant que root a accès au service mariadb. Cependant, l'accès se fait sans mot de passe. On peut donc dire que la sécurité ne repose sur rien.

Les risques de la non sécurisation de ce peut conduire : 
* à des fuites de données
* à la corruption de mots de passe
* à des authentifications non désirées
* etc...

Sécurisation du service mariadb avec le script mysql_secure_installation : 
```bash
mysql_secure_installation
```
Les instructions du TP ont été suivies en mettant comme mot de passe pour le compte root : **rootMysql**


### 8- Création d'une base de données via script SQL
#### Question 8A
On crée la base de données avec la suite de commandes suivante : 
```bash
mysql -prootMysql
```
```sql
CREATE DATABASE worlddb;
```

#### Question 8B
Un exemple de méthode d'affichage des bases de données présentes :
```bash
echo "SHOW DATABASES;" | mysql -prootMysql
```
On peut aussi directement utiliser la console mysql pour ce faire.

#### Question 8C
Tout d'abord on importe le script de création de la base de données :
```bash
wget 10.30.111.43/~aurelio/WORLDDB-FINAL-UTF8.sql
```
On lance ensuite le service en lui envoyant ce script : 
```bash
mysql -prootMysql < WORLDDB-FINAL-UTF8.sql
```

#### Question 8D

Pour lister les tables présentes on tape simplement dans la console mysql : 
```sql
USE worlddb;
SHOW TABLES;
```
![Capture](https://user-images.githubusercontent.com/72377954/142840317-3a8c69a9-6ef2-42b8-9f94-a44fe51ca24a.PNG)

#### Question 8E
Il y a 239 pays dans la table country. 
```sql
SELECT COUNT(*) FROM coutry;
```

#### Question 8F
J'ai récupéré le schéma de la base de données grâce au logiciel Mysql Workbench permettant de faire de la rétro-conception via un script sql. Cela crée toutes les tables et attributs définis dans le script sous forme de modèle.

#### Question 8G
Premièrement il m'a fallu télécharger et déziper le répertoire conséquent.
```bash
dnf install epel-release
dnf install p7zip
wget 10.30.111.43/~aurelio/sakila-db.7z
7za x sakila-db.7z
```
Par la suite, en une ligne de commande, j'ai créé la base et lui ai donné son schéma. 
```bash
echo "CREATE DATABASE sakila;" | mysql -prootMysql < sakila-schema.sql
```
Finalement, j'ai remplis la base de données avec le fichier corrélé.
```bash
mysql -prootMysql < sakila-data.sql
```

#### Question 8H
J'ai utilisé plusieurs méthodes dans la console SQL pour ce faire :
```sql
SELECT first_name, last_name FROM sakila.actor WHERE actor_id < 6;
SELECT first_name, last_name FROM sakila.actor LIMIT 5;
```
![Capture](https://user-images.githubusercontent.com/72377954/142844630-28c0f3e8-787c-4aa3-991a-d3e8d0350c7b.PNG)

#### Question 8I
Les bases de données gérées par le serveur sont :
* information_schema
* mysql
* performance_schema
* sakila
* worlddb

#### Question 8J
On supprime premièrement l'entièreté de la base de données :
```sql
DROP DATABASE sakila;
```
Par la suite on vérifie que sakila n'existe plus :
```sql
SHOW DATABASES;
```
![Capture](https://user-images.githubusercontent.com/72377954/142845273-4cb9a10f-cf94-4fcd-a129-71421bc827ae.PNG)


### 9- Gestion des comptes et accès
#### Question 9A
S'il y a des failles de sécurité dans l'application web, un attaquant pourrait se connecter de n'importe où en prenant l'apparence de root sans mot de passe. Ainsi, toutes les données lui seraient accessibles :
* mots de passe
* logins
* données personnelles ou sensibles

En bref, toutes les informations de tous les utilisateurs de l'application.

#### Question 9B
L'injection SQL est la plus courante et la plus facile des attaques possibles sur un système.
Elle consiste en la modification d'une requête déjà présente via une zone de texte dans une application web par exemple. 
En ajoutant des guillements sur une requête mal formée, on peut changer son comportement en la continuant dans la zone de texte.

#### Question 9C
```sql
CREATE USER worlddbuser@'localhost';
```
**NOTA BENE** : On peut définir un mot de passe à cet utilisateur en ajoutant la directive IDENTIFIED BY !

#### Question 9D
```sql
GRANT ALL PRIVILEGES ON worlddb.* TO worlddbuser@'localhost';
```
**NOTA BENE** : On peut définir un mot de passe lors de la connexion en ajoutant la directive IDENTIFIED BY !

#### Question 9E
```sql
SHOW GRANTS FOR 'worlddbuser'@'localhost';
```
![Capture](https://user-images.githubusercontent.com/72377954/142847595-71c6aa61-a6dc-43f3-b4ea-68bf20cac729.PNG)

#### Question 9F
La commande ***FLUSH PRIVILEGES*** permet de recharger la table des droits dans mysql. Cela permet une affectation définitive des droits que nous venons de mettre en place.

#### Question 9G
Nous pouvons prouver que les accès ont bien marché en se connectant à mysql via cet utilistateur et en affichant les bases de données auxquelles il a accès.
```bash
echo "SHOW DATABASES;" | mysql -u worlddbuser
```
On obtient bien seulement les bases : 
* information_schema
* worlddb

#### Question 9H
Les tables accessibles sont... toutes ! En effet, nous avons donné tous les droits à notre utilisateur.
```sql
SHOW TABLES FROM worlddb;
```
Si l'utilisateur n'avait pas les droits sur ces tables, elles n'appraîtraient même pas.

#### Question 9I
La commande REVOKE permet de révoquer des droits.
On aurait pu par exemple enlever tous les droits précédemment donnés : 
```sql
REVOKE ALL PRIVILEGES ON worlddb.* FROM worlddbuser@'localhost';
```

#### Question 9J
Ce script est un script PERL interractif permettant de gérer les permissions dans les tables grant de mysql.

#### Question 9K
Le script mysqlaccess est un outil permettant de vérifier les privilèges donnés à un nom d'hôte, à un nom d'utilisateur ou à une base de données.

### 10- Sauvegarde et restauration
#### Question 10A
Les principaux dangers liés à une mauvaise sécurité dans la gestion des bases de données sont : 
* l'accès à des données qui ne sont pas censées être divulguées (personnelles ou sensibles) par excès, abus ou élévation de privilèges
* la modification de données menant à une intégrité nulle de ces dernières
* l'accès à des zones du systèmes sensibles
* l'accès à des sauvegardes critiques

#### Question 10B
Afin de modifier la population de chaque pays on utilise : 
```sql
UPDATE country SET population = 10;
```
On peut vérifier le bon fonctionnement en utilisant :

```sql
SELECT population FROM country;
```
Cette requête nous renvoie, en toute logique, seulement des 10.

#### Question 10C
Afin de créer une sauvegarde de la base de données ***worlddb*** j'ai créé un dossier spécifique pour toutes les sauvegardes _ /var/lib/mysql/backup/dump_scripts _.
Par la suite, il suffit d'utiliser la commande mysqldump : 
```bash
cd /var/lib/mysql/backup/dump_scripts
mysqldump -prootMysql worlddb > dump-worlddb.sql
```

#### Question 10D
La commande permettant la restauration d'une base de données est la suivante :
```bash
mysql -prootMysql < /var/lib/mysql/backup/dump_script/dump-worlddb.sql
```

#### Question 10E
Une sauvegarde logique consiste en copier les données présentes dans la base en question. Cela se fait par des requêtes SQL contenues généralement dans un script (CREATE, INSERT...).

#### Question 10F
Les sauvegardes physiques principales sont les sauvegardes :
* complètes
* incrémentales
* différentielles
* miroirs

#### Question 10G
La commande _mariabackup_ permet aussi de restaurer des bases de données.

#### Question 10H
La sauvegarde incrémentale est celle qui est proposée dans l'exemple de ce lien.

#### Question 10I
Les avantages d'une sauvegarde de ce type sont :
* le gain de temps
* le gain d'espace

#### Question 10J
Le problème avec ce type de sauvegardes est que la restauration prend plus de temps du fait qu'il y ait un besoin de restaurer la sauvegarde complète précédente avant l'incrémentale.

### 11- Outils graphiques 
#### Question 11A
L'intérêt d'une connexion par tunnel SSH à un service mariadb est le gain de flexibilité. En effet, il est ainsi possible de travailler avec des outils plus "user-friendly", de partout, en tout temps. Ainsi, on pourrait utiliser des interfaces graphiques pour son serveur et la gestion de ses bases sous Windows par exemple. Le tunnel SSH est par ailleurs bien plus sécurisé que d'autres services. L'intégrité des données est donc en jeu.

### 12- Réplication comme solution de sauvegarde ?
#### Question 12A
Les limites de la réplication sont : 
* le  fait que la réplication seule n'est pas suffisante pour gérer les sauvegardes
* le fait que la réplication ne protège pas de la perte de donnée car tout est repliquée depuis la machine primaire (DELETE,DROP...)
* le fait qu'il soit nécessaire de mettre en place des protocoles sécurisés quant à la synchronisation des données afin d'éviter une perte de ces dernières

#### Question 12B
La réplication est utilisée dans la plupart des cas pour :
* protéger les données en cas de panne physique ou de données compromises : assurer un service constant (haute disponibilité)
* accélérer l'accès aux données (plusieurs mêmes données dans plusieurs pays différents)
* améliorer les performances des serveurs (rediriger les requêtes sur les différentes répliques pour soulager le maître)

#### Création d'une nouvelle VM dite "esclave" et paramétrages
Une fois la machine virtuelle créée et lancée sur le bon VLAN, j'ai tout d'abord installé mariadb.
```bash
dnf install mariadb-server
systemctl start mariadb # on lance le service
systemctl status mariadb # on vérifie l'état du service : ACTIVE
```
Une fois le service installé, comme le demande le sujet du TP, j'ai ping ma machine "maître" et testé la connexion à son service : 
```bash
#IP MACHINE ESCLAVE : 192.168.100.184
ping 192.168.100.183 # les paquets sont bien échangés
mysql --host 192.168.100.183 -prootMysql # n'a pas les accès de connexion car il n'y a pas encore de compte distant autorisé via GRANT dans les tables.
```
Par la suite, j'ai créé le fichier de paramétrage _ /etc/my.cnf.d/server.cnf _ (cf. ci-dessous).

![Capture](https://user-images.githubusercontent.com/72377954/143235615-ce0ea6bf-c06a-4f8f-804c-081aab85caff.PNG)

Puis redémarrer le service mariadb et créé des droits pour l'esclave
```bash
systemctl restart mariadb
mysql -prootMysql
```
```sql 
GRANT ALL PRIVILEGES ON *.* TO root@192.168.100.184 IDENTIFIED BY 'rootMysql' WITH GRANT OPTION;
FLUSH PRIVILEGES;
SHOW MASTER STATUS;
```
![Capture](https://user-images.githubusercontent.com/72377954/143237973-ceb88149-f867-446a-bf96-8622eaea1f3c.PNG)

Finalement, j'ai configuré ma machine esclave : 
```bash
vim /etc/my.cnf.d/server.cnf
============================
[mariadb]
server-id=101
read_only=on
#replicate-do-db=worlddb
============================

systemctl restart mariadb
```
On se connecte ensuite à la console mysql pour activer l'esclave :
```sql
CHANGE MASTER TO MASTER_HOST = 'IPMASTER', 
MASTER_USER = 'root',
MASTER_PASSWORD = 'rootMysql',
MASTER_LOG_FILE = 'mariadb-bin.000001',
MASTER_LOG_POS = 333;

START SLAVE; #Lance la synchronisation de l'esclave
SHOW SLAVE STATUS\G; #Affiche le statut propre de l'esclave
```
