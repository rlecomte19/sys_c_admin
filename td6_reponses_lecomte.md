# TD6 - Gestion des fichiers

## I - Périphériques
### [Question - 1A]
Voici à quoi correspondent les périphériques suivants : 
* ***/dev/null*** : correspond en quelques sortes au vide. Tout flux envoyé sur ce périphérique disparaît sans pouvoir être récupéré. De la même manière, tout ce qui provient de ce périphérique est considéré le néant. 
* ***/dev/zero*** : est assez similaire au précédent. Cependant, ce dernier peut renvoyer des données (seulement la valeur nulle NUL en ASCII). On l'utilise principalement pour l'initialisation de fichiers.
* ***/dev/random*** : correspond à un générateur de nombres aléatoires. Il utilise des données de certains pilotes de périphériques traitées par des fonctions de hashage pour la génération. Par ailleurs, il est à noter que sa lecture est bloquée lorsque le système ne peut plus avoir accès à des variables sources pour le traitement. On dit que les capacités du système ne sont plus suffisantes (phénomène appelé entropie).
* ***/dev/urandom*** : ce périphérique fonctionne de la même manière que _/dev/random_. Cependant, le fichier n'est pas bloqué lors d'une entropie. Ainsi, l'intégrité et la  variabilité des nombres générés n'est pas toujours assurée.
* ***/dev/loop0*** : correspond à ce que l'on appelle un périphérique en boucle. Il est en lecture seule, généralement utilisé pour monter des images.  

> Toutes les réponses ont été majoritairement tirées de Wikipedia et complétée par d'autres sites annexes 

### [Question - 1B]
Voici le code effectué pour cette question : 
```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>

int main(){

	// Initializing file descriptor in read only over /dev/urandom file
    int file_descriptor = open("/dev/urandom", O_RDONLY);

	char random_hexa_data[50];

	// Reading data from our file descriptor
    read(file_descriptor, random_hexa_data, 8);
    
	// Closing file descriptor to avoid data leak
	close(file_descriptor);

    printf("%d", random_hexa_data);
    printf("\n");
    return 0;
}
```

## II - Le système de fichiers _/proc_
### [Question - 2A]
Les répertoires nommés par des numéros correspondent à des PDI, donc des identifiants de processus actuellement en train de tourner.
### [Question - 2B]
Le fichier _cmdline_ correspond à la commande effectuée dans un invite de commande permettant de lancer le processus ayant pour PID le numéro du répertoire dans ***/proc***. Si le processus est un zombie, _cmdline_ ne contiendra rien et retournera seulement un 0. Il est bon de noter que ce fichier est en lecture simple. 
### [Question - 2C]
***/proc/\[pid\]/cwd*** est un lien symbolique vers le répertoire courant où le processus est en exécution. 
### [Question - 2D]
***/proc/\[pid\]/exe*** est un lien symbolique vers le chemin de l'exécutable de la commande lançant ce processus.
### [Question - 2E]
***/proc/\[pid\]/root*** est un lien symoblique vers le répertoire racine d'exécution du processus. Il se comporte par la suite comme un exécutable cité en question ***2E***.
### [Question - 2F]
***/proc/devices*** correspond à la liste des groupes de périphériques et des numéros majeurs.
### [Question - 2G]
Proc est en soit un système de fichiers spécifique, assez unique. Le répertoire ***/proc/self*** correspond en fait au processus qui appelle ce système de fichiers.

## III - Test de permission d'accès à un fichier

## IV - Verrous et opérations sur les fichiers
### [Question - 4A]
Nous pouvons avoir besoin de vérouiller un fichier pour plusieurs raisons. La majeur partie de ces raisons concerne l'intégrité des données ou informations que contiennent ces fichiers. 
Nous pouvons notamment citer les raisons suivantes : 
* un fichier peut être endommagé si deux processus font des manipulations en même temps
* un fichier peut renvoyer des données endommagées à un processus A essayant de le lire tandis qu'un processus B le manipule
* éviter l'accès non autorisé à un fichier / gérer un fichier privé

### [Question - 4B]

## V - Buffers (mémoire tampon)

## VI - Parcours d'un répertoire

## VII - Descripteurs de fichiers et fichiers binaires

## VIII - Fichiers séquentiels et fichiers à accès direct 

## IX - Sauvegarde d'une structure
