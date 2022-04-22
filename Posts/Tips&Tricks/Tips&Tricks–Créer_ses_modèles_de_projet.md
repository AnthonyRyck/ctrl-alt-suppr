Lorsque nous commençons un nouveau projet, Visual Studio nous propose plusieurs modèles pour démarrer. Il faut très souvent modifier certaines parties du projet, pour prendre en compte nos besoins. Que ce soit sur le type de base de données, ajouter le logo de la société, enlever ce qui est inutile, … Il est possible de gagner du temps, et d’avoir déjà un projet prêt à commencer, par rapport à nos besoins « quotidien ». Je vais expliquer comment faire. Je n’invente rien, tout est sur la [doc Microsoft](https://docs.microsoft.com/fr-fr/visualstudio/ide/how-to-create-project-templates?view=vs-2019).  
J’ai créé un repo sur [Github](https://github.com/AnthonyRyck/ProjectTemplatesVisualStudio) avec mes templates que j’utilise : SQLite et MySQL.  
## Créer son projet

D’abord il faut créer son projet. Modifier tout ce qui à besoin, traduction, ajout de logo, mettre des class que vous utilisez tout le temps, mettre les packages nuget avec la version voulue. Dans mon exemple, je vais créer un template de projet Blazor Server avec une base de données en SQLite. Je veux que le type d’authentification soit sur un compte local, et qu’au démarrage de l’application, il y a une injection d’un utilisateur « root ». Le login ne soit pas l’Email, mais le Username. Les logs se feront avec [Serilog](https://serilog.net/).  
## Création du modèle

Une fois que vous avez tout défini, dans Visual Studio, il faut aller dans le menu « Project » — « Export Template ».  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/VisualStudioTemplate/01-TemplateProject-Export.png)

Sur la nouvelle fenêtre, il faut choisir « Project template ».  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/VisualStudioTemplate/02-TemplateProject-Export.png)

Remplir les informations demandées. Elles seront affichées lors de création d’un nouveau projet. Tout le modèle est sauvegardé dans un fichier zip.   
En cochant la case « *Automatically import the template into Visual Studio*« , VS va copier le zip dans :  
`%userprofile%\Application Data\Microsoft\VisualStudio\16.0_5b06e7f4\ProjectTemplatesCache`  
Pour 16.0_5b06e7f4, je ne pense pas que ce soit universelle. Je pense que vous savez ou chercher.  

![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/VisualStudioTemplate/02a-TemplateProject-Info.png)  

## Création d’un nouveau projet

Le nouveau modèle est maintenant accessible dans la recherche.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/VisualStudioTemplate/03-TemplateProject-CreerProjet-966x1024.png)  

Une fois le projet créer, il faut modifier une chose. Avec le nouveau nom de projet que vous avez donné, VS modifie bien les namespaces des class, mais pas dans les fichiers razor. Il suffit de faire un CTRL+H (remplacement rapide), chercher le nom du template utilisé, dans mon exemple : BlazorSqliteTemplate, et le remplacer par le nom du nouveau projet. Il y a 4 occurrences qui seront trouvées, dans `_Imports.razor`, `NavMenu.razor`, et dans le csproj.  

A vos Templates!  
