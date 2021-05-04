# Disque Nuagique 2

### Énoncé 

#### forensics \| réaliste \| dfir

Pour cette étape de l'investigation, vous devez retrouver un élément pouvant être lié au nom ou au pseudo de l'administrateur. L'élément à rechercher a été remplacé par un flag au format `FCSC{xxx}`.

La copie du disque est identique à l'épreuve `Disque nuagique 1`.

### Analyse 

Après avoir extrait les deux fichiers du zip nous obtenons deux fichiers de type EWF. Après quelques recherches, on se rend compte que ces deux fichiers sont complémentaires et seront donc traités ensemble une fois montés. 

La commande ewfinfo nous permet d'afficher les metadata des fichiers EWF. Nous allons donc tenter de l'utiliser afin de voir si un indice ne s'y cacherait pas. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/disquenuagique2.1.png)

Pas grand chose de bien intéressant. On monte alors les fichiers. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/disquenuagique2.2.png)

On peut maintenant accéder au contenu de la partition. Après des heures et des heures de recherches via grep et Autopsy on se rend compte qu'il n'y a pas grand chose d'intéressant contenu directement dans le disque. En recherchant "FCSC" le seul output est celui qui semble être contenu dans les metadata de la partition. 

Je suis donc parti sur une autre piste et j'ai décidé de monter la partition sur ma machine. 

Pour ce faire, j'utilise l'outil kpartx. 

Solution un peu brute de décoffrage, elle permet néanmoins de gagner du temps et de ne pas s'embêter avec les permissions. Je vais copier ma partition ewf1 à un autre endroit permettant ainsi de la monter plus facilement en bypassant les erreurs rencontrées. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/disquenuagique2.3.png)

Une fois la partition montée sur la machine, on peut maintenant accéder au contenu de cette dernière.

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/disquenuagique2.4.png)

Je me suis d'abord intéressé au deuxième volume qui est volume chiffré de type LUKS2. N'ayant pas de mot de passe pour accéder au contenu de celui-ci j'ai tenté de le bruteforce en vain. Il semblerait que la version 1 de LUKS est simple à bruteforce mais que ce n'est pas le cas pour la 2. Après, encore une fois, quelques heures perdues, je me suis cette fois intéressé au contenu du premier volume. 

En observant le type des fichiers contenus dans ce volume, on se rend compte qu'il y a quelque chose de fort intéressant. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/disquenuagique2.5.png)

On s'empresse alors d'aller unzip ce fichier. La commande unzip me renvoyant une erreur ne me permettant pas donc pas de dézipper ce fichier, j'ai utilisé l'utilitaire de base "Engrampa Archive Manager"

J'ai encore une fois du copier le fichier à un autre endroit tout en modifiant les permissions pour pouvoir le dézipper.

Une fois le fichier dézippé, on tombe sur un autre zip que l'on va à nouveau extraire.

On se retrouve maintenant avec pas mal de fichiers intéressants : 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/disquenuagique2.6.png)

Un petit grep de FCSC et le tour est joué. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/disquenuagique2.7.png)

On peut par la suite observer que le contenu de ce fichier se trouvait à cet emplacement `unzip/root-LfekH8/.ssh/authorized_keys`

### Flag 

FCSC{0fb01eb22d4f812dcbdfcb}

### Conclusion 

Une solution un peu brute de décoffrage pour un challenge qui valait le nombre maximal de points,  n'étant au final pas bien compliqué, mais qui m'en aurait fait baver pendant pas mal de temps. On aurait également pu envisager de récupérer le contenu du zip via quelques commandes Sleuthkit par exemple



