Pour donner suite au post : [Notebooks avec .Net Interactive](https://www.ctrl-alt-suppr.dev/2021/08/16/notebooks-avec-net-interactive/), je vais montrer comment intégrer des graphiques. Un notebook d’exemple se trouve sur [Github](https://github.com/AnthonyRyck/CodesPourDevTo/blob/master/src/Notebooks/03-Afficher-graphique.ipynb). Github prend en charge les fichiers `.ipynb`, il permet d’afficher le contenu de façon lisible, sans devoir ouvrir Visual Studio Code. Pour l’article, jee reprend ce que j’ai écris dans le notebook.  
Il est possible d’utiliser le javascript pour afficher des graphiques, mais je veux montrer une alternative plus « .net ».  

## ScottPlot

[ScottPlot](https://swharden.com/scottplot/) est une librairie qui est prévu pour :   
* Windows Forms  
* WPF  
* Avalonia  
* Console  

Dans notre cas, je prend le « côté » `Console`. La sortie du graphique se fait sous forme de fichier d’image qui est à sauvegarder sur le disque. Pour l’afficher dans le notebook, je passe par du `html`.  
```html
// Affichage du résultat de l'image dans un "bout HTML"
#!html
<div>
    <img src="quickstart_scatter.png" />
</div>
```

Il faut apprendre comment construire les graphiques. Pour avoir des exemples de graphiques possible avec ScottPlot, il faut aller sur le [Cookbook](https://swharden.com/scottplot/cookbooks/4.1.18/) du site (version 4.1.18 au 15/09/21).  

## Plotly.net

Aahhh en voilà une librairie qu’elle est bien : [Plotly.net](https://plotly.net/).  
La documentation est vraiment très complète et avec beaucoup d’exemples. Il est possible d’utiliser le C# pour faire des graphiques, mais elle est quand même très orientée vers le F#. Je ne suis pas super à l’aise avec le F#, mais en regardant les exemples, il n’y a rien d’insurmontable.  
Contrairement à `ScottPlot`, l’affichage de l’image se fait directement dans le Notebook.  
Avec Plotly, nous avons un grand nombre de graphique possible, 2D, 3D, avec des cartes, …  

## Javascript

Bon, je vais quand même parlé un peu de Javascript, enfin… montré une vidéo Youtube de la chaîne Visual Studio Code. Il montre l’utilisation de [D3.js](https://d3js.org/). La vidéo commence sur le moment ou il montre l’utilisation de D3.js.   
ATTENTION : il n’explique pas comment l’utiliser, il montre qu’il est possible de l’utiliser…  
[Vidéo sur Youtube](https://www.youtube.com/embed/DMYtIJT1OeU?start=760&feature=oembed)  
