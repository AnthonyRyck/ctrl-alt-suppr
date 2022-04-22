Il est facile de pouvoir partager une librairie avec le reste du monde via [Nuget.](https://www.nuget.org/) Tout le monde l’utilise pour récupérer des packages, mais en créer un ? Depuis Visual Studio, il est possible de les créer sans ligne de commande.  
## Création du package

Faire un clic droit sur le projet qui doit être exporté et Properties.  
Dans l’onglet Package, il faut remplir les informations.   
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Nuget/03-NugetInfo02.png)
* **ID de package** : c’est l’ID unique pour le retrouver dans la recherche de package.  
* **Version de package** / **Version Assembly** / **Version Assembly File** : ne pas oublier de mettre à jour, car le site nuget utilise ces informations pour proposer la nouvelle version.  
* **Auteurs** : pour savoir qui ça provenance. Les packages MS sont [Microsoft](https://www.nuget.org/profiles/Microsoft). Il peut y avoir plusieurs auteurs.  
* **Description** : explique brièvement ce que fait le package.  
* **Licensing** : Expression ou fichier, il faut une licence sinon ça nuget.org rejette le package. Pour l’expression, moi j’ai mis MIT.  

Toutes ces infos se retrouvent ensuite dans le résultat de recherche.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Nuget/01-NugetInfo.png)
## Déploiement sur nuget.org

Il faut avoir un compte Microsoft pour se connecter sur nuget.org. Sur la barre du haut, il faut clic sur upload.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Nuget/04-Upload-1024x561.png)

Un récap des informations est affiché une fois validée.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Nuget/05-EnValidation-1024x986.png)

Il faut attendre pour recevoir un mail confirmant que le package est disponible pour tous. De quelques instants à quelques heures…  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Nuget/06-Valide-1024x204.png)

Ensuite, il ne reste plus qu’à l’utiliser. Lien sur nuget pour ce package [Fanlib](https://www.nuget.org/packages/FanLib/). Ce package est fait par rapport au post [Créer une bibliothèque Blazor](https://www.ctrl-alt-suppr.dev/2021/03/31/creer-une-bibliotheque-blazor/).  
