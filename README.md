# INSA 2SU 2020 - Thuy Dung PHAM

https://bitbucket.org/whitewand/2su_2020/src

## Questions

>Quels sont les chemins d'attaque possibles sur la signature d'un système embarqué?

Les chemins les plus "rapides" pour attaquer la signature d'un système embarqué est de faire une intrusion physique sur le système afin de changer manuellement la signature stockée à l'intérieur. On peut ainsi par exemple remplacer la signature fiable par la signature de l'attaquant.

> A quoi sert la chaine de confiance? Pourquoi est-elle nécessaire?

La chaîne de confiance sert à savoir par où passe les informations. Elle est nécessaire pour connaître à quels endroits il faut placer des contrôles/sécurités.

> Décrire la méthode pour aborder la sécurité sur un produit embarqué. Pourquoi établir un modèle d'attaquant est-il important?

Pour aborder la sécurité d'un système embarqué, on va d'abord définir contre qui on se bat, puis les caractéristiques du produit mises en jeu et les méthodes de défense possibles.
Il faut savoir qui nous attaque pour proposer une défense adaptée en fonction (il ne faudrait pas placer une défense énorme au détriment des performances si cela n'est pas nécessaire).

> Trouver un moyen rapide de faire du debug embarqué (par exemple sur cible ARM)? Expliquer les avantages

Un moyen rapide de faire du debug embarqué est de définir au préalable un plan de validation avec des tests unitaires. De ce fait, les tests sont automatisés et la conception du plan avant l'implémentation du code fait qu'on ne se focalise pas sur celui-ci pour réaliser les tests.

> Lister les catégories de bug possibles et comment les exploiter et les défendre

Bugs : 
- Stack buffer overflow
- Return Oriented Programming
- Use after free
- Signedness error example
- Use uninitialized
- Format string

Exploitation :
- Remplissage de la pile pour aller sur des adresses qui ne sont pas accessibles normalement
- Utilisation d'un programme en parallèle pour modifier certaines valeurs après la vérification du programme ou si la valeur n'est pas initialisée

Défense :
- Niveau hardware
- Sur l'architecture
- Options de compilation

> Quelles idées pour améliorer la sécurité en embarqué? (IA, Anti-debug, Obfuscation, Crypto ...) Choisissez une idée, chercher si elle existe et développer en quelques phrases quel avantage elle apporte et ses limites

-> Utilisation de l'IA pour l'amélioration de la sécurité.

L'utilisation de l'IA est déjà un principe utilisé pour l'amélioration de la sécurité, par exemple dans les firewalls. Grâce à l'apprentissage de l'IA, on peut adapter un moyen de défense avec les attaques passées, on peut également "prévoir" de potentielles attaques. Le problème de cette méthode est qu'il faut une base de données d'attaques au départ, en plus du fait qu'il est difficile de porter ça directement sur de l'embarqué car il faut généralement des machines assez puissantes pour faire tourner l'IA. L'idée serait de mettre l'IA sur une machine distante et d'envoyer les mises à jour de sécurité via le réseau. 

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


## TD9 [Crypto]
On va signer notre fichier src/gentle.c

Passphrase de la clé privée : Hello

La signature peut protéger dans un contexte embarqué dans le sens où il est possible de comparer le hash d'un programme via la clé publique de la personne qui a signé le programme. Ainsi, on peut savoir si un programme a été modifié via la signature. 

Concernant l'update d'un firmware, on peut par exemple signer l'update afin de garantir que l'on n'installe pas une update malveillante lors de son téléchargement par exemple.