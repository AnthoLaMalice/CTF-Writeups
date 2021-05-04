# BABA

## Énoncé 

#### harware \| radio

Votre ami Walrus s'initie à la radio et a découvert une nouvelle façon de communiquer. Il vous a donné un signal échantillonné à 200kHz que vous devez décoder.

Le flag est au format FCSC{}.

## Analyse 

On commence par ouvrir le fichier sur Audacity, le format du fichier n'étant pas reconnu directement, on l'ouvre via File &gt; Import &gt; Raw Data, on modifie le sample rate pour correspondre à l'énoncé -&gt; 200000. Après une première écoute du son, rien ne saute aux yeux. On réfléchit alors aux techniques basiques dans ce genre de challenge. En ralentissant le son on commence à entendre des bruits audibles qui ressemblent fortement à du Morse. On zoom alors un bon coup, surprise, on peut observer des petites barres et des barres un peu plus longues, toutes de même taille, c'est donc bel et bien du Morse.

![](.gitbook/assets/image%20%2812%29.png)

### Résolution

On prend notre tableau de conversion. 

![](.gitbook/assets/image%20%287%29.png)

Après avoir décodé les premiers caractères, nous trouvons ceci :

LeCodeMorseInternational

Ce qui correspond probablement à du texte pour meubler. \(Après coup, on se rend compte que tout le meublage correspondait à la page Wikipedia du Morse\) En réfléchissant au format du flag on essaye de retrouver la suite de caractère FCSC, en vain. On réfléchit donc une deuxième fois et on se rappel que les flags sont en sha256 et donc, composés de caractères hexadécimaux.

Nous allons donc chercher une suite de caractères morses composée, entre autre, de chiffres. Les chiffres sont facilement reconnaissables étant donné qu'ils sont composés de 5 symboles morses au lieu de 4 pour les lettres.

On tombe alors sur cette fameuse suite de caractères composée de chiffres et de lettres hexadécimales qui pourraient correspondre et en décodant un petit peu avant celle-ci, on tombe sur ceci :

### Flag

FLAG EST E6891C8BB8CCFE95A767457F3A1B73861928A002C5094762D580CD7BC4A15C64

Il ne reste donc plus qu'à rentrer le flag FCSC{e6891c8bb8ccfe95a767457f3a1b73861928a002c5094762d580cd7bc4a15c64}

