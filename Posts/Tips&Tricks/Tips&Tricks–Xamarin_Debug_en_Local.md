Lancement du projet ASP.Net Core et de l’application Android. Combien de temps j’ai perdu pour essayer de faire fonctionner l’un avec l’autre ! J’ai essayé sur mon téléphone, rien. Mettre un point d’arrêt, et attendre que mon appli vienne appeler mon API…   

Après plusieurs recherches, j’ai compris qu’il n’est pas possible de venir requêter le IIS Express de « l’extérieur ». D’une autre machine sur le réseau, d’un téléphone, et de l’émulateur de téléphone. Il est considéré comme une autre machine. Je vais montrer l’outils qu’il faut avoir. Il est utile pour tester avec un émulateur, son téléphone, et aussi dans une équipe pour faire une démo depuis son poste.  

## Conveyor by Keyoti

Le nom de l’extension à installer est "[Conveyor by Keyoti](https://marketplace.visualstudio.com/items?itemName=vs-publisher-1448185.ConveyorbyKeyoti)".  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Xamarin/Conveyor01.png)  
  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Xamarin/Conveyor02-1024x358.png)

Il va demander s’il peut ouvrir les ports.  
![](hhttps://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Xamarin/Conveyor03.png)

Ensuite vous aurez cette fenêtre. Pour les tests, il faut prendre Remote URL, et le tour est joué.   
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Xamarin/07_Conveyor-1024x391.png)

Pas besoin de faire de modification de fichier de config, comme j’ai pu trouver, une grosse galère, et en plus rien ne fonctionnait.  

En debug sur Xamarin Android, il n’est pas possible d’utiliser le localhost comme adresse. Il faut utiliser l’IP « 10.0.2.2 ». Cette IP est l’alias spécial de l’interface de boucle hôte (alias 127.0.0.1,  le localhost du téléphone).  
