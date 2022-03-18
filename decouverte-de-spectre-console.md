
## Qu’est-ce que Spectre.Console ?

C’est une librairie qui permet de faire des projets `Console` bien plus… Bling Bling.   
<img src="https://spectreconsole.net/assets/images/example.png" alt="" />
  
*Image de spectreconsole.net*  
**[LINK TO VIDEO](https://spectreconsole.net/assets/images/table.webm)**

Le projet est OpenSource sur [GitHub : Spectre.Console](https://www.nuget.org/packages/Spectre.Console/0.43.1-preview.0.43) et toute la documentation est sur [spectreconsole.net](https://spectreconsole.net/). Pour cette découverte, nous allons faire un projet console pour ce blog (ctrl-alt-suppr).  
## Création du projet

Il faut créer un projet console ou il faut ajouter 2 librairies : [Spectre.Console](https://www.nuget.org/packages/Spectre.Console/0.43.1-preview.0.43) et [Spectre.Console.ImageSharp](https://www.nuget.org/packages/Spectre.Console.ImageSharp/0.43.1-preview.0.43). La 2eme est une librairie qui étend les fonctionnalités de Spectre sur les images, vous verrez, c’est bien sympa. Voici les commandes pour créer le projet :  
```generic
dotnet new console -o ConsoleBlingBling
dotnet add .\ConsoleBlingBling package Spectre.Console
dotnet add .\ConsoleBlingBling package Spectre.Console.ImageSharp
```
#### Le logo

Au lancement de l’application, j’aimerai afficher le « logo » du blog, ou du moins ce qui s’en rapproche le plus. J’ai fait cette image :  
<img src="https://raw.githubusercontent.com/AnthonyRyck/CodesPourDevTo/master/src/dotNet6/ConsoleBlingBling/logo.jpg" alt="" />

Vous allez me dire :   
*« Mais dans une application console, on ne peut pas afficher d’image »*  
Spectre va jouer sur les couleurs de background et faire une sorte de « ACSII art » de l’image, et c’est avec le package `Spectre.Console.ImageSharp` que nous pourrons faire ça.   
Ajoutons le code :  
```csharp
using Spectre.Console;

// Image pour le logo
var logo = new CanvasImage("logo.jpg");
AnsiConsole.Write(logo);
AnsiConsole.WriteLine(); // pour faire un espace
```

C’est `AnsiConsole` qui faut utiliser à la place de la `class Console` ([doc Console](https://docs.microsoft.com/fr-fr/dotnet/api/system.console?view=net-6.0)). Comme l’image est à la racine du projet et copié à chaque fois à la compilation, il faut juste mettre le nom. Le `csproj` :   
```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net6.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>

  <ItemGroup>
    <None Include="logo.jpg">
      <CopyToOutputDirectory>Always</CopyToOutputDirectory>
    </None>
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Spectre.Console" Version="0.43.0"></PackageReference>
    <PackageReference Include="Spectre.Console.ImageSharp" Version="0.43.0"></PackageReference>
  </ItemGroup>
</Project>
```

Il est possible de compiler l’image dans l’`exe` en utilisant `EmbeddedResource`, dans le `csproj` :   
```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net6.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>

  <ItemGroup>
    <EmbeddedResource Include="logo.jpg"></EmbeddedResource>
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Spectre.Console" Version="0.43.0"></PackageReference>
    <PackageReference Include="Spectre.Console.ImageSharp" Version="0.43.0"></PackageReference>
  </ItemGroup>
</Project>
```

En revanche pour charger l’image, il faut utiliser la `Reflection` :  
```csharp
using System.Reflection;

Stream logoStream = Assembly.GetExecutingAssembly().GetManifestResourceStream("ConsoleBlingBling.logo.jpg");
CanvasImage logo = new CanvasImage(logoStream);
AnsiConsole.Write(logo);
```

Dans la méthode `GetManifestResourceStream()`, il faut indiquer le **namespace complet** (*donc attention si l’image est dans un répertoire*) et le nom du fichier.  
#### Le slogan

Maintenant je veux ajouter le slogan du blog.  
```csharp
// Slogan
var slogan= new FigletText("Partager du code")
			.RightAligned()
			.Color(Color.White);
AnsiConsole.Write(slogan);
```

`FigleText` ([doc spectre](https://spectreconsole.net/widgets/figlet)) permet de convertir le string passé en paramètre en `FIGlet` ([définition](https://fr.wikipedia.org/wiki/FIGlet)).   
Voilà ce que ça donne :  
<img src="https://raw.githubusercontent.com/AnthonyRyck/CodesPourDevTo/master/src/ImgBlog/Spectre/01-Spectre_LogoSousTitre.png" alt="" />

Le fond avec les étoiles est mon background de mon Windows Terminal. Dans les [best-practices](https://spectreconsole.net/best-practices) de Spectre, il indique que le rendu peut être « gâché » (*c’est ma traduction 🙂*) en fonction de comment est configuré la sortie de l’utilisateur. Si vous mettez une grande image, et que l’utilisateur a une fenêtre de console toute petite, ça va sortir un résultat très différent de ce que vous vous attendiez.   
Maintenant que j’ai mis le logo et le slogan, je veux que cette application récupère les 10 derniers articles pour les afficher dans un menu.  
#### Récupération des articles

Pendant que la console récupère les articles, je veux qu’elle affiche un « waiting », une animation pour faire patienter l’utilisateur. Je vais utiliser `AnsiConsole.Status()` ([doc spectre](https://spectreconsole.net/live/status)).  
Voici le code :  
```csharp
// Affichage des 10 derniers articles du blog.
List<PostWordPress> posts = new List<PostWordPress>();

await AnsiConsole.Status()
    		.StartAsync("Récupération des 10 derniers post...", async ctx => 
    		{
				// Change le "waiting".
				ctx.Spinner(Spinner.Known.Aesthetic);

				using(var client = new HttpClient())
				{
					string url = @"https://www.ctrl-alt-suppr.dev/wp-json/wp/v2/posts?per_page=10";
					var streamPosts = await client.GetStreamAsync(url);
					posts = await JsonSerializer.DeserializeAsync< /><PostWordPress>>(streamPosts);
				}
				// Pour avoir le temps de voir le "waiting".
				await Task.Delay(5000);
				AnsiConsole.MarkupLine(":check_mark: Terminé " + Emoji.Known.CheckMark);
    		});</PostWordPress></PostWordPress></PostWordPress>
```

L’animation sera affichée jusqu’à la fin du `StartAsync()`. Il est possible de choisir le type d’animation, avec l’option [Spinner](https://spectreconsole.net/appendix/spinners) (`ctx.Spinner(Spinner.Known.Aesthetic);`). Dans mon cas j’ai mis : `Aesthetic`, mais vous trouverez tous les [exemples ici](https://jsfiddle.net/sindresorhus/2eLtsbey/embedded/result/).  
**Note** : J’ai mis un `await Task.Delay(5000)`, car à l’air de la fibre vous ne verrez quasiment pas le « waiting ».  

Sur la dernière ligne :   
`AnsiConsole.MarkupLine(":check_mark: Terminé " + Emoji.Known.CheckMark);`   
J’ai fait exprès de mettre les 2 manières de mettre des [Emojis](https://spectreconsole.net/appendix/emojis), soit c’est une chaine de caractère (`:check_mark:`) ou de passer par l’énumération `Emoji.Know`.  
#### Menu pour les articles

Je vais faire un menu rapide qui donnera le choix à l’utilisateur entre les 10 articles, de réafficher le menu ou de quitter l’application.  
```csharp
string cmdQuit = "quit";
string selection = string.Empty;

ShowPosts();

while(selection != cmdQuit)
{
	AnsiConsole.WriteLine();
	selection = AnsiConsole.Ask<string>("Quel est votre choix ?");

	switch (selection)
	{
		case "quit":
			AnsiConsole.WriteLine();
			AnsiConsole.MarkupLine("See you soon " + Emoji.Known.OkHand);
			AnsiConsole.WriteLine();
			Environment.Exit(0);
			break;
		case "posts":
			ShowPosts();
			break;
		case "1":
		case "2":
		case "3":
		case "4":
		case "5":
		case "6":
		case "7":
		case "8":
		case "9":
		case "10":
			int index = int.Parse(selection);
			string urlPost = posts[index - 1].link;
			try
			{
				ProcessStartInfo startInfo = new ProcessStartInfo(@"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe");
            	startInfo.WindowStyle = ProcessWindowStyle.Maximized;
            	startInfo.Arguments = urlPost;
            	Process.Start(startInfo);
				AnsiConsole.MarkupLine("[green]Bonne lecture[/] :grinning_face_with_big_eyes:");
			}
			catch (System.Exception ex)
			{
				AnsiConsole.MarkupLine("[red]ERREUR [/] :angry_face:");
				AnsiConsole.MarkupLine("Edge n'a pas pu être ouvert, du coup voici l'URL de l'article choisi :");
				AnsiConsole.MarkupLine(urlPost);
			}
			break;
		default:
			AnsiConsole.MarkupLine("[red]Euuhhh le choix n'est pas compliqué...[/] :angry_face:");
			AnsiConsole.MarkupLine(string.Empty);
			break;
	}
}

void ShowPosts()
{
	// Affichage des posts.
	for (var i = 0; i < posts.Count; i++)
	{
		AnsiConsole.MarkupLine($"[purple]{i + 1}[/] - {posts[i].date.ToString("d")} - {posts[i].title.rendered}");
	}
	AnsiConsole.MarkupLine("[green]posts[/] : pour réafficher la liste des articles.");
	AnsiConsole.MarkupLine("[red]quit[/] : pour quitter.");
}</string>
```

Qu’est ce qui est important.  
* <code data-enlighter-language="csharp" class="EnlighterJSRAW">selection = AnsiConsole.Ask<string>("Quel est votre choix ?");</code>  
Il remplace `Console.ReadLine();` ou `Console.ReadKey();`, et la sortie peut être directement typé.  /r/n* `AnsiConsole.MarkupLine("See you soon " + Emoji.Known.OkHand);`  
[MarkupLine](https://spectreconsole.net/markup) équivaut à `AnsiConsole.Write(new Markup("....."));` Je vous laisse lire la doc pour lui, car il permet de changer de couleur le texte, souligner, mettre des [emojis](https://spectreconsole.net/appendix/emojis),… enfin c’est lui qui rend la vie plus be  /r/n
Pour changer la couleur : `AnsiConsole.MarkupLine("[red]quit[/] : pour quitter.");`   
Spectre va *parser* la chaine de string. Par exemple, pour avoir du rouge il faut écrire : `[red]` et ne **PAS OUBLIER** le `[/]` pour indiquer la fin, sinon **Exception**. Comme c’est dans une chaine de string, votre IDE ne va pas vous aider à trouver l’erreur.  

**Attention**  
Pour la découverte, quand l’utilisateur choisira un article ça lui ouvre l’application Edge, donc si vous utilisez un Mac/Linux ça ne fonctionnera pas (*testé sous Win10 et Win11*).  
## Et on peut faire du Scripting ?

Nous venons de créer un projet console, et si on le scriptait ? Pour voir comment faire/utiliser un script en C#, aller voir le post : [Tips & Tricks – Du script en C#](https://www.ctrl-alt-suppr.dev/2022/02/28/tips-tricks-du-script-en-c/).   
Dans ce script, je reprends à 99% le code du projet console. Vous retrouvez le script sur [GitHub](https://raw.githubusercontent.com/AnthonyRyck/CodesPourDevTo/master/src/dotNetScript/SpectreScript/main.csx), et pour l’exécuter en direct voici la commande :  
```generic
dotnet script https://raw.githubusercontent.com/AnthonyRyck/CodesPourDevTo/master/src/dotNetScript/SpectreScript/main.csx
```

Voilà, maintenant je suis un adepte de ces librairies, je pense même refaire certains de mes projets avec 😉 Il y a encore tellement à découvrir (les tableaux, les commandes,…), mais je vous laisse faire vos propres tests.  

