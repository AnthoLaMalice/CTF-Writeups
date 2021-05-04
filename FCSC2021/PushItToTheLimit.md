# Push it to the limit

### Énoncé

#### web \| sql

Exploitez une injection SQL afin de vous connecter sur l'application web.

### Résolution

Notre site web contient un simple formulaire d'authentification. En sachant que nous avons pour objectif d'exploiter une injection SQL on va alors commencer par un simple test.

```text
test ' OR 1=1 -- -
```

Cette injection nous permettra de nous connecter peu importe notre input \(OR 1=1 -&gt; toujours vrai\) tout en mettant en commentaire le champs mot de passe qui sera donc ignoré. 

Le serveur nous retourne que nos identifiants sont incorrects, il y a donc un soucis avec notre injection. 

En ouvrant le code source de la page, on se rend compte que la requête envoyée au serveur est écrite en commentaire. 

On se rend alors rapidement compte que le champs username est entouré de **"** et non pas de **'**

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/pushit1.PNG)

On adapte alors notre requête :

```text
test " OR 1=1 -- -
```

Notre requête fonctionne ! Cependant, le serveur refuse d'afficher autant de données. On va alors adapter nos requêtes afin de faire un peu de tri. 

                    ![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/pushit2.PNG)

Afin de faire un peu de tri, nous allons utiliser la commande UNION. 

Pour rappel : La commande UNION de SQL permet de mettre bout-à-bout les résultats de plusieurs requêtes utilisant elles-même la commande SELECT. C’est donc une commande qui permet de concaténer les résultats de 2 requêtes ou plus. Pour l’utiliser il est nécessaire que chacune des requêtes à concaténer retournes le même nombre de colonnes, avec les mêmes types de données et dans le même ordre.

Il nous sera donc impératif de connaitre le nombre de colonnes que nous allons afficher.

Pour ce faire, nous allons utiliser la commande ORDER BY. Dans ce cas, le serveur nous retournera une erreur si nous utilisons cette commande sur un trop grand nombre de colonnes. Cela nous permettra donc indirectement de savoir le nombre de colonnes qui nous intéressent. 

```text
test " ORDER BY 5 -- -
```

Même pas besoin de chercher plus loin, le serveur nous retourne directement qu'il n'y a que 2 colonnes à afficher. Il ne reste alors plus qu'à afficher ces dernières ! 

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/pushit3.PNG)

```text
test " UNION SELECT 1,2 -- -
```

### Flag

Et le flag s'affiche directement.

![alt text](https://github.com/AnthoLaMalice/CTF-Writeups/blob/main/FCSC2021/pictures/pushit4.PNG)

### Sources & Aides :

[https://tryhackme.com/room/sqlilab](https://tryhackme.com/room/sqlilab)

[https://sql.sh/cours/union](https://sql.sh/cours/union)

