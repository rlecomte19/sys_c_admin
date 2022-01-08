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
> Vous trouverez ci-dessous le code créé

On commence par déclarer :
* un tableau (considéré comme buffer) qui accueillera les différentes valeurs à enregistrer dans un fichier binaire
* une structure de type ***flock*** qui constituera notre vérou
* un descripteur de fichier pointant vers le fichier binaire souhaité en écriture seule

Par la suite, on initialise les différents attributs de notre vérou (notamment son type en écriture seule).

Ensuite, on génère les valeurs aléatoires que nous enregistrons dans le buffer. 

Finalement, on vérouille le fichier souhaité puis on y inscrit les valeurs du buffer. 

```c
#define NB_RANDOMS 10

int main()
{
	struct flock myLock;
	int randomData[NB_RANDOMS];
	int towrite_file_descriptor = 0;
	
	// Opening the desired binary file
	towrite_file_descriptor = open("/home/student/td6_sysadmin/test.bin", O_WRONLY); 
	
	// Initializes flock struct attributes
	myLock.l_type = F_WRLCK;
	myLock.l_whence = SEEK_SET; 
	myLock.l_len = 0;
	myLock.l_start = 0;

	// Generates random values between 0 & 100 into our array
	for(int i=0;i<NB_RANDOMS;i++){
		randomData[i] = (rand() % 101);
	}

	// Locking file
	fcntl(towrite_file_descriptor, F_SETLKW, &myLock);

	// Writes our array values into a file 
	write(towrite_file_descriptor, randomData, sizeof(int)*NB_RANDOMS);
	
	// Waiting for a return to ensure the program is not closing 
	printf("\n Appuyer sur la touche \"Entrée\" pour continuer...");
	getchar();
	return (0);
}
```
## V - Buffers (mémoire tampon)

## VI - Parcours d'un répertoire
> Le code correspondant se trouve en fin d'exercice

On commence ici par déclarer trois variables importantes : 
* \*dir : le répertoire que l'on veut parcourir 
* \*dirp : contiendra les fichiers de ce répertoire
* stat : les statistiques système de ce fichier

On ouvre ensuite le répertoire souhaité (dans cet exercice, le répertoire courant).

Par la suite, on boucle sur ce répertoire afin d'obtenir un par un tous les fichiers dans la variable dirp.

On récupère ensuite la taille du fichier courant que l'on ajoute à une variable qui contient la sommes des tailles. 

Finalement, on ferme le fichier puis on affiche le total des tailles des fichiers du répertoire courant.
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/dir.h>
#include <sys/types.h>
#include <sys/stat.h>D
#include <unistd.h>

int main() {
	DIR *dir; 
	struct stat fileStat; 
	struct dirent *dirp;
	int totalFileSize = 0; 
	
	// Verify file opening
	if((dir = opendir(".")) == NULL){
		printf("\nLe répertoire n'a pas pu être ouvert !");
		exit(2);
	}
	
	// Retrieve stats of each file contained in directory
	while((dirp = readdir(dir)) != NULL){
		stat(dirp->d_name, &fileStat);
		// Sum of every file size
		totalFileSize += (int) fileStat.st_blocks * 512;
	}

	// Verify directory closing
	if(closedir(dir) == -1){
		printf("\nAttention, le répertoire n'a pas pu être correctement fermé ! ");
	}

	printf("La taille totale des fichiers du répertoire courant est de %d octets", totalFileSize);

}
```
## VII - Descripteurs de fichiers et fichiers binaires

## VIII - Fichiers séquentiels et fichiers à accès direct 

## IX - Sauvegarde d'une structure
