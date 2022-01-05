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

### [Question - 2B]

### [Question - 2C]
### [Question - 2D]
### [Question - 2E]
### [Question - 2F]
### [Question - 2G]


## III - Test de permission d'accès à un fichier

## IV - Verrous et opérations sur les fichiers

## V - Buffers (mémoire tampon)

## VI - Parcours d'un répertoire

## VII - Descripteurs de fichiers et fichiers binaires

## VIII - Fichiers séquentiels et fichiers à accès direct 

## IX - Sauvegarde d'une structure
