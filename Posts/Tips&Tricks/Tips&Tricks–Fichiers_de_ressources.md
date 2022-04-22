Dans une application ou nous devons gérer plusieurs langues, il nous faut autant de fichier de ressource que de langue. Dans ce post je vais vous expliquer ce que j’utilise pour gagner du temps et éviter des erreurs.  
## Installation de ResX Manager

ResX Manager est une extension qui va nous permettre la gestion de nos fichiers de ressources. Pour l’installer il faut aller dans :   
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/FichiersRessources/01-installResXManager.png)

Une fois la fenêtre ouverte, chercher « ResX » dans la partie Online.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/FichiersRessources/02-installResXManager.png)

Faire un redémarrage de Visual Studio pour l’installation se fasse. Comme vous pouvez le voir, il existe plusieurs extensions pour gérer les fichiers de ressources, il faut les essayer et tenter de trouver celui avec qui vous avez un bon feeling.  

## Utilisation

Pour pouvoir utiliser ResXManager, il faut au moins un fichier de ressource (.resx) dans votre projet. Il suffit de faire un click droit sur le fichier, et ouvrir avec ResX Manager.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/FichiersRessources/03-OpenWith.png)

L’extension ouvre une nouvelle fenêtre, avec tous vos fichiers qu’il trouve dans le projet.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/FichiersRessources/04-VueResXManager.png)

Je vais prendre l’exemple de « Resources\Pages.Index ». Il trouve qu’il y a 2 langues, anglais et français. Il arrive à matcher grâce au nom des fichiers. Pour avoir 2 langues, il faut 2 fichiers :   
– Pages.Index.en.resx  
– Pages.Index.fr.resx  
Il expose les informations des 2 fichiers directement en ligne, et chaque langue en colonne. Donc pour créer une nouvelle valeur, il suffit de saisir la clé (Key) et les valeurs pour chaque langue, et c’est l’extension qui se charge de modifier les fichiers. Un gain de temps, surtout quand vous devez gérer 10 langues, soit 10 fichiers. Nous pouvons tout de suite voir un oubli d’une traduction pour une clé.  

## Les options

Ce n’est pas tout. Avec cette extension, il est possible de faire des traductions automatiques. Il est possible d’utiliser Google, Azure pour ne citer les plus connus.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/FichiersRessources/05-Traduction.png)

Il peut aussi vous aider à créer de nouveau fichier ressource, en choisissant une langue, et de façon très précise, par exemple le Cherokee (chr-Cher-US). Il y a une option qui permet de faire toutes les traductions au moment de la création du fichier.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/FichiersRessources/06-langues.png)

## Pour conclure

Cette extension est vraiment top quand il faut travailler avec plusieurs fichiers de ressources. Elle fonctionne avec les applications Blazor, UWP, WPF… en gros avec tout MS. Je vous laisse découvrir les autres fonctionnalités (export en Excel,…).  
En bas à droite, il y a un bouton « Donate ». Un petit Don, même 10 balles, n’est rien par rapport au temps qu’il va vous faire gagner.
