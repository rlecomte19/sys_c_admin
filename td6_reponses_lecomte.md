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
### [Question - 7A]
Le paramètre "flags" permet de définir le mode d'ouverture du fichier souhaité. 
Le site https://www.gladir.com/CODER/CLINUX/open.htm fournit une très bonne explication de chacune des valeurs possibles :

![flags_open()](https://user-images.githubusercontent.com/72377954/148653880-67bb8799-5efc-433f-bb2f-bb4727b9f78a.png)

### [Question - 7B]
Le paramètre "mode" sert à indiquer les permissions que possède l'utilisateur sur le fichier (correspondant à des permissions Linux).

### [Question - 7C]
```c
int main(){
	// Opening test.bin file with write only (and creating it if not existing already)
	int file_descriptor = open("test.bin", O_WRONLY | O_CREAT);
	
	// Initializing the bytes to write and retrieving the size occupated
	char *bytesToWrite = "19496893802562113L";
	int written_bytes = (int) sizeof(char) * strlen(bytesToWrite);

	// Writing caracters into the file according to the size retrieved
	write(file_descriptor, bytesToWrite, written_bytes);

	// Closing file descriptor
	close(file_descriptor);
	
	return 0;
}
```

Le fichier binaire ainsi créé contient bien la chaîne de caractères donnée comme buffer.

### [Question - 7D]
```c
int main(){
	// Opening and creating file if it does not exist
	int file_descriptor = open("testhexa.bin", O_WRONLY | O_CREAT);
	
	// Initializing buffer for writing
	char *writeBuffer = "0x4142434451525354L";

	// Retrieving size of bytes to write
	int written_bytes = (int) sizeof(char) * strlen(writeBuffer);
	
	// Declaring reading buffer
	char *readBuffer = malloc(written_bytes);

	// Writing bytes into file thanks to writing buffer
	write(file_descriptor, writeBuffer, written_bytes);
	
	// Retrieving bytes from file into reading buffer
	read(file_descriptor, readBuffer, written_bytes);
	
	// Displaying buffer as an Integer
	printf("Entier : %d \n", readBuffer);
	
	// Displaying buffer as an Hexadecimal
	printf("Hexadécimal : %0x \n", readBuffer);
	
	close(file_descriptor);
}
```
***Figure 1*** : Sortie du programme
 
![7D](https://user-images.githubusercontent.com/72377954/148656203-241f04e0-a66a-4ef2-9eab-61dcd08a25a7.PNG)

***Figure 2 *** : Contenance du fichier testhexa.bin créé : 

![testhexax](https://user-images.githubusercontent.com/72377954/148656206-1423d357-35ac-459e-a479-a6563f5e12be.PNG)

Ici le fichier contient donc l'adresse vers la chaîne de caractères écrite. Il est bon de noter que cette dernière est contenue dans un buffer. 

## VIII - Fichiers séquentiels et fichiers à accès direct 
> à faire

```c
int main(){
    int file_descriptor = open("position.bin", O_WRONLY | O_CREAT);
    int firstNb = 10;
    for(int i=0;i<20;i++){
        write(file_descriptor, &firstNb, sizeof(int));
        firstNb++;
    }

    close(file_descriptor);

    int read_descriptor = open("position.bin", O_RDONLY);
    int toRead[30];
    lseek(read_descriptor, 0L, SEEK_SET);
    int ctr = 0;
    while(read(file_descriptor, toRead, sizeof(int))){
        printf("\nLu : [%d]\n", toRead[ctr]);
        lseek(read_descriptor, 3, SEEK_CUR);
        ctr++;
    }


    close(read_descriptor);
}
```
## IX - Sauvegarde d'une structure
On comprendra pour cet exercice un fichier exercice9.h qui contiendra : 
```c
#include <stdlib.h>
#include <stdio.h>
#include <sys/types.h>
#include <string.h>
#include <unistd.h>
typedef struct{
    char nom[20];
    int age;
    float taille;
} Personne;

void ex9();
void fillPersonnes(Personne *personnes);
void displayPersonnes(const Personne *personnes);
void writePersonnes(const Personne *buffer);
void readPersonnes(Personne *buffer);

```
### [Question - 9A]
On crée une fonction qui créera les différentes personnes : 
```c
void fillPersonnes(Personne *personnes){
	Personne p1 = {"Pierre", 20, 172.5f};
	Personne p2 = {"Paul", 24, 176.7f};
	Personne p3 = {"Jacques", 21, 168.4f};
	Personne p4= {"Georges", 27, 192.2f};

	personnes[0] = p1;
	personnes[1] = p2;
	personnes[2] = p3;
	personnes[3] = p4;
}
```
Le main contient donc : 
```c
int main(){
	Personne personnes[4];
    	fillPersonnes(personnes);
}
```
### [Question - 9B]
Ici, il suffit de boucler autant de fois qu'il y a d'éléments dans le tableau de Personne et d'afficher les différents éléments : 
```c
void displayPersonnes(const Personne *personnes){
	for (int i = 0; i < 4; i++)
	{
		printf("[Personne %d] - %s | Âge : %d | Taille : %0.1f \n", i, personnes[i].nom, personnes[i].age, personnes[i].taille);
	}
}
```
Le main contient à ce point : 
```c
int main(){
    Personne personnes[4];
    // Fill in Personne array
    fillPersonnes(personnes);
    printf("Personnes enregistrées : \n");
    // Display every element of the array
    displayPersonnes(personnes);
}
```
### [Question - 9C]
On ouvre pour cela un fichier dans lequel on écrira le tableau de structure Personne :
```c
void writePersonnes(const Personne *personnes){
	// Opening file
	FILE *file = fopen("structures.bin", "w");

	if(file == NULL){
		printf("ERREUR OUVERTURE FICHIER\n\n");
	}else{
		printf("\nFichier ouvert.....\n");
		// Writing data in file
		fwrite(personnes, sizeof(Personne), 4, file);
	}
	// Closing file
	fclose(file);
}
```
Le main contient à ce point : 
```c
int main(){
    Personne personnes[4];
    // Fill in Personne array
    fillPersonnes(personnes);
    printf("Personnes enregistrées : \n");
    // Display every element of the array
    displayPersonnes(personnes);
    // Writing every Personne in a binary file
    writePersonnes(personnes);
}
```
### [Question - 9D & 9E]
Lorsque l'on lit dans le fichier précédemment créé on enregistre les données dans le buffer et on les affiche au fur et à mesure : 
```c
void readPersonnes(Personne *buffer){
   	FILE *file = fopen("structures.bin", "r");

    	if(file == NULL){ printf("ERREUR OUVERTURE FICHIER LECTURE \n"); }

    	int nbPersonnes = 0;
    	printf("\nRécupération des personnes via fichier binaire. . . . \n");
    
    	// Reading one Personne by one and registering it into the buffer (here it is our array of Personne)
	while(fread(&buffer[nbPersonnes], sizeof(Personne), 1, file)){
		// Displaying every data from the Personne read
        	printf("\n[Personne %d] - %s | Âge : %d | Taille %0.1f\n",
			nbPersonnes,
			buffer[nbPersonnes].nom,
			buffer[nbPersonnes].age,
			buffer[nbPersonnes].taille
                );
    	    	nbPersonnes++;
	}
	// Closing file
	fclose(file);
}
```
Le main final contient donc : 
```c
int main(){
    Personne personnes[4];
    fillPersonnes(personnes);
    printf("Personnes enregistrées : \n");
    displayPersonnes(personnes);
    writePersonnes(personnes);

    Personne bufferPersonnes[4];

    readPersonnes(bufferPersonnes);
    return 0;
}
```


