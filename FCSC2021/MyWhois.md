# My Whois

### Énoncé 

#### misc \| web

Nous avons mis en place un service en ligne faisant de la recherche de nom de domaine. Pouvez-vous accéder à son code source ?

### Analyse

Une fois arrivé sur le site web, on comprend vite que l'objectif de celui-ci est d'exécuter la commande Linux "whois" sur l'input que nous envoyons. 

Petit rappel, "whois" permet d'interroger des base de données à propos du nom de domaine \(ou IP\) renseigné afin d'obtenir différentes informations sur ce dernier. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/My%20whois.PNG)

On remarque alors très vite que l'input que nous envoyons n'est pas vérifié permettant ainsi de l'exploiter à notre guise. 

On pense alors tout de suite au "  ;  " qui, sur un système Linux, permet d'exécuter deux ou plusieurs commandes les unes après les autres, peu importe le résultat de la dernière. 

### Résolution

Avec un input tel que :  

```text
a ; ls
```

Nous parvenons à lister le contenu du répertoire courant. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/mywhois2.PNG)

On voit alors tout de suite que le fichier index.php qui contient le code source de la page est présent dans le répertoire courant. Il ne reste alors plus qu'à afficher le contenu de celui-ci. 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/mywhois4.PNG)
En observant le code source de la page on trouve directement le flag. 

### Flag 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/mywhois5.PNG)

### Sources & Aides

[https://www.howtogeek.com/680086/how-to-use-the-whois-command-on-linux/](https://www.howtogeek.com/680086/how-to-use-the-whois-command-on-linux/)





