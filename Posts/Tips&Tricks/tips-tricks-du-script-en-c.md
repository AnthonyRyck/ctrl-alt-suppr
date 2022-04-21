# Tips & Tricks – Du script en C#

Le script en C# ne date pas de hier (*en 2011, pour [les nostalgiques](https://docs.microsoft.com/fr-fr/archive/blogs/cdndevs/adding-c-scripting-to-your-development-arsenal-part-1)*), et dans ce post je vais présenter l’outil : `dotnet-script` ([site du projet](https://github.com/filipw/dotnet-script)).   

Certains diront : *« Ah mais non, il y a d’autres langages/technos pour faire du script, comme Powershell, Python »*.   
C’est vrai, mais il faut maitriser chaque techno/langage. Ensuite quand on maitrise une librairie (*par exemple Newtonsoft.Json, **LA** librairie que tout développeur .NET a utilisé*), est-ce que je vais trouver la même chose en Python par exemple, et en combien de temps je vais pouvoir la maîtriser.   
Bref, il y a du pour et du contre.  
## Installation

Pour le post, je suis en .NET 6, mais ça fonctionne aussi en .NET 5. Il faut utiliser la commande `dotnet tool install`, comme ci-dessous :  
```generic
dotnet tool install -g dotnet-script
```

La dernière version à ce jour de l’article est la 1.3.1. Si vous êtes en .NET 5 c’est la version 1.0, pour le .NET 6 la version commence à 1.3.  
## Utilisation

L’outil permet de créer un script et un « environnement » dans le cas d’une utilisation avec Visual Studio Code et omnisharp (l’extension C#).   
Faire la commande dans un répertoire :  
```generic
dotnet-script init
```
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/dotNetScript/01-dotnetscript.png)

Ça va générer 3 fichiers : `launch.json`, `main.csx` et `omnisharp.json`. L’extension des fichiers de script C# est le `csx`, afin de bien montrer la différence. Le fichier `omnisharp.json` contient des paramètres tels que le framework cible et autorise ou non les références de nuget.  
```json
{
  "script": {
    "enableScriptNuGetReferences": true,
    "defaultTargetFramework": "net6.0"
  }
}
```

Dans un script il n’y a pas de méthode `Main`. Commençons avec un simple `Console.WriteLine`.  
```csharp
Console.WriteLine("Le fameux Hello World !");
Console.WriteLine(">Appuyer sur une touche pour continuer.");
Console.ReadKey();
```

Pour exécuter un script, il faut faire la commande :  
```generic
#dotnet-script nomDuFichier.csx // ou dotnet script (sans le "-")
dotnet-script main.csx
```
#### Référence à d’autres scripts

Tout mettre dans un script, ça peut faire beaucoup, et aussi en tant que dév nous aimons écrire une fois les choses, et surtout les réutiliser. Il est possible de charger un script, dans un script.   
Il faut utiliser le mot clé : `#load "nomdufichier.csx".`   
Prenons un exemple simple. Ajoutons dans le répertoire un nouveau script : `ConsoleExtension.csx`. En voici le code :  
```csharp
public static void Info(string message)
{
	ForegroundColor = ConsoleColor.White;
	Console.WriteLine(message);
}

public static void Result(string message)
{
	ForegroundColor = ConsoleColor.Green;
	Console.WriteLine(message);
}

public static void Title(string titre)
{
	ForegroundColor = ConsoleColor.White;
	Console.WriteLine("######## " + titre + " ########");
}

public static void Pause()
{
	Info(">Appuyer sur une touche pour continuer.");
	Console.ReadKey();
}

public static void SautDeLigne()
{
	Info(string.Empty);
}
```

J’ajouter la référence :  
```generic
#load "ConsoleExtension.csx"
```

Ajoutons ça au script `main.csx`.  
```csharp
#load "ConsoleExtension.csx"

Console.WriteLine("Le fameux Hello World !");
Console.WriteLine(">Appuyer sur une touche pour continuer.");
Console.ReadKey();

// A partir d'ici, l'appel à Console se fait avec un autre script
Title("Utilisation d'un autre script");
Pause();
Info("Il est possible d'utiliser un autre fichier script en utilisant");
Result("#load \"nomdufichier.csx\"");
SautDeLigne();
```

Là j’ai utilisé des méthodes `static`, mais ça peut être des `class` pour définir des objets. Tout le champ des possibilités du C# est utilisable.  
![](https://media.giphy.com/media/3o84sq21TxDH6PyYms/giphy.gif)
#### Utilisation de Nuget

C’est bien beau d’ajouter du code dans son script, mais bon je ne vais pas tout réinventer, je veux pouvoir utiliser des packages sur nuget… ou… de nuget ! (***sur** ou **de** ?*)   
Et c’est possible ! C’est là que le script devient une machine de guerre. Pour utiliser un package il faut mettre dans le script :  
```generic
#r "nuget: nomDuPackage, version"
```

On retrouve la commande sur le site de nuget pour un package.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/dotNetScript/05-dotnetscript-nuget.png)

**Note** : A chaque ajout d’un package avec la commande, il faut redémarrer l’extension Omnisharp (C#), pour que l’intellisense soit prise en charge pour l’ajout. (CTRL+SHIFT+P –> omnisharp)  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/dotNetScript/04-dotnetscript-Omnisharp-Reset.png)

Je vais prendre un exemple de package : [AlienFruit.FluentConsole.AsciiArt](https://www.nuget.org/packages/AlienFruit.FluentConsole.AsciiArt/). Il permet de faire du ACSII Art. Ajoutons tout ça dans le script `main.csx`, et je mets le script au complet.  
```csharp
#load "ConsoleExtension.csx"
#r "nuget: AlienFruit.FluentConsole.AsciiArt, 1.0.10"
using AlienFruit.FluentConsole.AsciiArt;
using AlienFruit.FluentConsole;

Console.WriteLine("Le fameux Hello World !");
Console.WriteLine(">Appuyer sur une touche pour continuer.");
Console.ReadKey();

// A partir d'ici, l'appel à Console se fait avec un autre script
Title("Utilisation d'un autre script");
Pause();
Info("Il est possible d'utiliser un autre fichier script en utilisant");
Result("#load \"nomdufichier.csx\"");
SautDeLigne();

Title("Utilisation d'un package Nuget");
Pause();

Info("Il faut utiliser la commande :");
Info("#r \"nuget: nomDuPackage, version\"");
SautDeLigne();
Pause();

Info("Exemple :");
FConsole.GetInstance().DrawDemo(DemoPicture.RainbowPukeSkull);
SautDeLigne();

Info("Voilà, à vos Script !");
```

Il faut que les instructions `#load` et `#r` soient **TOUJOURS** en haut du script.  
![](https://media.giphy.com/media/YAlhwn67KT76E/giphy.gif)

**Attention** : Si dans Visual Studio Code après avoir ajouté un package nuget, VS vous met tout en erreur, j’ai eu la même chose. C’est un problème de version entre `dotnet-script` et `Omnisharp` ([issue sur Github](https://github.com/OmniSharp/omnisharp-roslyn/issues/2020)).   
Pour cela il faut modifier le fichier de config de l’extension.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/dotNetScript/02-dotnetscript-Omnisharp.png)
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/dotNetScript/03-dotnetscript-Omnisharp.png)

Il faut ajouter à la fin du fichier de config :  
```json
"omnisharp.path":"latest"
```

Omnisharp va se remettre à jour pour utiliser la dernière version du serveur. Faire un redémarrage de VS Code.  
#### Petit bonus

Le petit bonus que je trouve cool, c’est qu’il est possible d’exécuter un script sans l’avoir en local ([remote-scripts](https://github.com/filipw/dotnet-script#remote-scripts)). Comme avec mon exemple :  
```generic
dotnet script --no-cache https://raw.githubusercontent.com/AnthonyRyck/CodesPourDevTo/master/src/dotNetScript/ScriptViaWeb.csx
```

Voici à quoi ressemble le script  
```csharp
#load "nuget:ConsoleExtensionScript, 1.0.0"
#r "nuget: AlienFruit.FluentConsole.AsciiArt, 1.0.10"
using AlienFruit.FluentConsole.AsciiArt;
using AlienFruit.FluentConsole;

Info("Utilisation d'un script en \"Remote\" !");
Pause();

Info("Exemple :");
FConsole.GetInstance().DrawDemo(DemoPicture.Punisher);
SautDeLigne();

Info("Voilà, à vos Script !");
Pause();
```

Pour les scripts en remote, je conseille d’utiliser l’option `--no-cache`, ça force l’outil à reprendre le script à chaque fois, sinon il va rester bloqué sur son cache (*oui ça sent le vécu…*). S’il y a des modifications, comme il reste sur le cache, vous ne les verrez pas.  
Ensuite avec un script qui n’est pas en local, vous ne pouvez pas faire de référence à d’autres scripts à distance (`#load "nomFichier.csx"`), SAUF quand ils sont exportés sur nuget.org. Il est possible de créer [nos packages de script](https://github.com/filipw/dotnet-script#script-packages), et du coup il faut utiliser :  
```generic
#load "nuget:packageName, version"
#load "nuget:ConsoleExtensionScript, 1.0.0"
```
#### Création d’un package nuget avec un csx

Pour l’exemple j’ai créé un package script [ConsoleExtensionScript](https://www.nuget.org/packages/ConsoleExtensionScript/1.0.0).  
Comment procéder. Créer un fichier nuspec à la « racine ».  
```generic
#nuget spec NomDuPackageVoulu
nuget spec ConsoleExtensionScript
```

Le fichier nuspec pour mon package ConsoleExtensionScript ressemble à ça.  
```xml
<?xml version="1.0" encoding="utf-8"?>
<package>
  <metadata>
    <id>ConsoleExtensionScript</id>
    <version>1.0.0</version>
    <authors>Anthony Ryck</authors>
    <requireLicenseAcceptance>false</requireLicenseAcceptance>
    <license type="expression">MIT</license>
    <projectUrl>https://github.com/AnthonyRyck/CodesPourDevTo/tree/master/src/dotNetScript</projectUrl>
    <description>Pour montrer comment utiliser un package dans un script C#</description>
    <releaseNotes></releaseNotes>
    <copyright>Copyright Anthony Ryck 2022</copyright>
    <tags>csx</tags>
  </metadata>
  
  <files>
    <file src="contentFiles/csx/netstandard2.0/main.csx" target="contentFiles/csx/netstandard2.0/main.csx"></file>
  </files>
</package>
```

Ensuite le ou les fichiers `csx` doivent être mis en `target` dans un ordre de répertoire : `/contentFiles/csx/netstandard2.0` et notre*/nos* fichier « main.csx ».   
Je vous invite à lire le post : [NuGet ContentFiles Demytified](https://devblogs.microsoft.com/nuget/nuget-contentfiles-demystified/) et la [doc MS sur contentFiles](https://docs.microsoft.com/fr-fr/nuget/reference/nuspec#including-content-files), ils expliquent le pourquoi du nommage des répertoires.  
## Conclure

Depuis le .NET Core, il est possible de faire du scripting sur toutes les plateformes, pourquoi s’en privé ? C’est utilisé sur Azure : [Azure Function](https://docs.microsoft.com/fr-fr/azure/azure-functions/functions-reference-csharp).  
Donc à vos scripts 😉  
