# INSA 2SU 2020 - Thuy Dung PHAM

https://bitbucket.org/whitewand/2su_2020/src

## Questions

- Quels sont les chemins d'attaque possibles sur la signature d'un système embarqué?


- A quoi sert la chaine de confiance? Pourquoi est-elle nécessaire?
- Décrire la méthode pour aborder la sécurité sur un produit embarqué. Pourquoi établir un modèle d'attaquant est-il important?
- Trouver un moyen rapide de faire du debug embarqué (par exemple sur cible ARM)? Expliquer les avantages
- Lister les catégories de bug possibles et comment les exploiter et les défendre
- Quelles idées pour améliorer la sécurité en embarqué? (IA, Anti-debug, Obfuscation, Crypto ...) Choisissez une idée, chercher si elle existe et développer en quelques phrases quel avantage elle apporte et ses limites

## TD1: \[Reverse engineering\]

### Finir crack emily (version avec soluce)

#### Problème initial
Nous avons un fichier gentle.c, qui permet de rentrer une chaîne de caractères et de vérifier si celle-ci correspond à la chaîne donnée dans le programe (en gros, on cherche un mot de passe).

#### Utilisation de Ghidra

A partir de Ghidra, on va utiliser l'outil d'analyse afin de trouver rapidement le fonctionnement du programme et la méthode afin d'éditer le binaire.

En fouillant rapidement les résultats d'analyse, on observe les chaînes de caractères correspondant aux résultats possibles du programme.

![alt text](img/Capture&#32;du&#32;2020-01-28&#32;15-36-45.png)

On voit à ce niveau là des sauts qui vont sur l'un ou l'autre des messages, ce qui veut dire que cette partie correspond à la comparaison de notre chaîne de caractères avec le mot de passe stocké dans le programme. 

Pour nous aider toujours plus, Ghidra nous montre une version plus lisible de cette partie : 

![alt text](img/Capture&#32;du&#32;2020-01-28&#32;15-57-33.png)

Là dessus, on suppose que *iVar1* est la clé de notre problème, car c'est cette valeur que l'on compare afin de savoir si l'on a le bon mot de passe, on va donc observer le comportement de la fonction associée *FUN_00000840*.

![alt text](img/Capture&#32;du&#32;2020-01-28&#32;16-06-19.png)

La fonction en question est toute simple, elle compare la chaîne rentrée en paramètre avec la variable *DAT_00000984* (qui est égal à "*poop*", donc au final on a trouvé le mot de passe, mais on veut faire mieux que ça), et renvoie 1 ou 0 en fonction du résultat de la comparaison. Nous voulons que quelque soit la réponse, le mot de passe soit correct, on va donc modifier la valeur de retour à *0x1* à chaque fois. On remarquera que la fonction donnée par Ghidra a bien compris ce qu'on voulait faire !

![alt text](img/Capture&#32;du&#32;2020-01-28&#32;16-32-51.png)

Pour finir, on enregistre le nouveau binaire (*gentle.bin*) et on observe le résultat !

![alt text](img/Capture&#32;du&#32;2020-01-28&#32;16-36-46.png)
