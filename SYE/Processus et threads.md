# Processus et threads 

## Définition d'un processus

On a d'un côté la stack qui descend, la heap qui monte. en-dessous le BSS, DATA et le Code. 

## processus

Ils sont ordonné en hiérarchie père-fils dont la racine est root. chaque processus est composé de contexte d'exécution. Un processus ne peut être démarrer qu'à partir d'un autre processus. 

## Thread 

c'est un processus léger 

## PCB

C'est une structure de donnée unique qui est initialisée à la création d'un nouveau processus. Elle contient toutes informations relatives à ses sources. Cela permet d'enregistrer son état. Elle est gérée uniquement par le noyau. 

- Etat du processus 

- Espace adressage 

- Pointeur vers le tas 

- Référence vers le processus fils 

- descripteur de fichier ouvert 

- contexte d’exécution

## Création de processus 
L'appel système fork() permet de créer un nouveau processus. On copie intégrale du processus parent dans le processus fils. **Par copie on entend une référence sur le processus parent et si on a un accès mémoire en write, il copie physiquement** 
Si on veut éxécuter une programme différent du parents, on doit  créer un autre contexte mémoire avec un fork() et après créer un environnement différent avec un fichier exec().  $

## Changement de contexte 
Une fois le processus prêt à être utilisé, il n'est pas lancé mais il est prêt à être ordonnancé. Le mode Zombie est utile pour garder la valeur de retour. 

Le changement de contexte entre un thread et un processus diffère dans le sens que le TCB est beaucoup plus léger. et donc c'est moins coûteux de changer. cela s'explique notamment par le fait que les threads partagent l'espace mémoire (sauf la pile), les sections data et bss. 

# exo

getppid() obtient le pid du parent. Une variable globale n'est pas changée dans le parent si elle est modifiée dans le fils. car lors du fork() le fils a une référence en lecture sur l'espace mémoire parent et lors de l'écriture une copie de l'espace mémoire est faite pour ne pas modifier celui du parent. 

Si un printf est effectué dans le parent **avant** le fork **sans \n** le fils va aussi copier ce qu'il y a dans le tampon de sortie du printf. Ainsi il va afficher deux fois ce qu'il y a dans le tampon malgré que le printf soit fait avant. 

```c
printf("message");
pid = fork();

//affichage deux fois message
```

pthread_join() est comme waitpid(). Il attend la fin du thread. 



# IPCs

Stdlib garantie qu'il y a au minimum 3 file descriptor ouvert au début du programme(stdin, stdout, stderr). 

dup2() donne la possibilité de dupliquer l'accès à un file descriptor. 

## Pipe

- unidirectionnelle 
- lecture destructive de type FIFO
- capacité limitée 
- deux files descriptor (un pour chaque extrémité write / read)

# Tubes 

read() et write retourne le nombre d'octets efectivement lu ou écris. Il se peut qu'on doive effectuer plusieurs read pour tout lire 

les tubes nommé sont fait avec mkfifo(). 

# Signaux

Ne tiens pas compte du nombre de fois qu'on envoie le signal. Pour envoyer un signal, n'importe lequel, il faut utiliser kill(). 

# fichiers mappés 

bidirectionnel, pas limité en taille, ne nécessite pas de synchronisation entre les processus. MAP_shared permet de partager le fichier avec un autre processus. 

#  Sockets 

htonl (host to network). le network est considéré comme big endian. pour ouvrir une connexion côté serveur il faut spécifié le protocole, l'adresse acceptée, et le port