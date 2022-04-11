
# Utiliser des raccourcis clavier avec Blazor

Dans une application, il est toujours bon de pouvoir utiliser des raccourcis clavier pour faire une action, comme « CTRL + S » pour sauvegarder son travail, même dans une application web. Dans ce post je vais montrer la librairie [Toolbelt.Blazor.HotKeys](https://github.com/jsakamoto/Toolbelt.Blazor.HotKeys), qui est un projet Open Source sur GitHub. Je vais prendre mon projet favori de démo sur Blazor : [FanApps](https://github.com/AnthonyRyck/CodesPourDevTo/tree/master/src/dotNet6/FansApp) ([en live ici](https://fandemo.ctrl-alt-suppr.dev/)). L’idée est d’ajouter sur la page d’Index, la possibilité de faire « SHIFT +  » un numéro (de 1 à 6) pour naviguer entre les différentes pages.  
## Installation

Il faut d’abord installer le package nuget à notre projet Blazor ([nuget package](https://www.nuget.org/packages/Toolbelt.Blazor.HotKeys/)).  
```generic
dotnet add package Toolbelt.Blazor.HotKeys --version 12.1.1
```

Dans `Program.cs` (*dans la version .NET 6, sinon voir le README sur Github pour le .NET 5*), il faut ajouter le service HotKeys.  
```csharp
WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
[...]
// Service pour "Toolbelt HotKeys"
builder.Services.AddHotKeys();
[...]
```
## Utilisation

Dans la doc sur GitHub, il montre comment mettre en place HotKeys directement dans la vue, dans la partie `@code{...}`. Ce que je vous propose c’est de mettre tout ça dans un ViewModel. J’aime bien séparer les choses, la vue ne fait que de l’affichage, et notre ViewModel lui dit comment faire !  
Je mets tout le code du ViewModel, et je vous explique chaque étape :  
```csharp
using Microsoft.AspNetCore.Components;
using Toolbelt.Blazor.HotKeys;

namespace FansApp.ViewModel
{
	public class IndexViewModel : IIndexViewModel, IDisposable
	{
		private NavigationManager navigationManager;
		private HotKeys HotKeysContext;

		public IndexViewModel(HotKeys hotKeys, NavigationManager navigation)
		{
			navigationManager = navigation;
			HotKeysContext = hotKeys;

			HotKeysContext.CreateContext()
				.Add(ModKeys.Shift, Keys.Num1, OpenFanclubPage, "Ouvrir Fanclub Page.")
				.Add(ModKeys.Shift, Keys.Num2, OpenTitanicPage, "Ouvrir Titanic ML Page.")
				.Add(ModKeys.Shift, Keys.Num3, OpenPage, "Ouvrir la page sur Scss Isolation.")				
				.Add(ModKeys.Shift, Keys.Num4, OpenPage, "Ouvrir la page Middlewares.")
				.Add(ModKeys.Shift, Keys.Num5, OpenPage, "Ouvrir la page FAQ.")
				.Add(ModKeys.Shift, Keys.Num6, OpenPage, "Ouvrir la page de ChatBot.");
		}

		private Task OpenFanclubPage(HotKeyEntry arg)
		{
			navigationManager.NavigateTo("/FanclubPage", true);
			return Task.CompletedTask;
		}

		private Task OpenTitanicPage(HotKeyEntry arg)
		{
			navigationManager.NavigateTo("/titanic", true);
			return Task.CompletedTask;
		}

		private Task OpenPage(HotKeyEntry arg)
		{
			switch (arg.Key)
			{
				case Keys.Num3:
					navigationManager.NavigateTo("/drawcss", true);				
					break;
				case Keys.Num4:
					navigationManager.NavigateTo("/intergiciel-Errrk-disgusting", true);
					break;
				case Keys.Num5:
					navigationManager.NavigateTo("/faq", true);
					break;
				case Keys.Num6:
					navigationManager.NavigateTo("/webchat", true);
					break;
				default:
					navigationManager.NavigateTo("/", true);
					break;
			}
			return Task.CompletedTask;
		}

		public async void Dispose()
		{
			await HotKeysContext.DisposeAsync();
		}
	}
}
```

Examinons cela d’un peu plus près.  
<img loading="lazy" src="https://media.giphy.com/media/Gpf8A8aX2uWAg/giphy-downsized.gif" alt="" width="596" height="334" />
### Le constructeur
```csharp
public IndexViewModel(HotKeys hotKeys, NavigationManager navigation)
{
	navigationManager = navigation;
	HotKeysContext = hotKeys;

	HotKeysContext.CreateContext()
		.Add(ModKeys.Shift, Keys.Num1, OpenFanclubPage, "Ouvrir Fanclub Page.")
		.Add(ModKeys.Shift, Keys.Num2, OpenTitanicPage, "Ouvrir Titanic ML Page.")
		.Add(ModKeys.Shift, Keys.Num3, OpenPage, "Ouvrir la page sur Scss Isolation.")	
		.Add(ModKeys.Shift, Keys.Num4, OpenPage, "Ouvrir la page Middlewares.")
		.Add(ModKeys.Shift, Keys.Num5, OpenPage, "Ouvrir la page FAQ.")
		.Add(ModKeys.Shift, Keys.Num6, OpenPage, "Ouvrir la page de ChatBot.");
}
```

Je récupère le service `HotKeys` via l’injection de dépendance de ASP.Net, ainsi que `NavigationManager` (*rien à voir avec HotKeys*), et je configure les raccourcis que CETTE page capturera. C’est sur cette page (via ce ViewModel) que je configure les raccourcis, donc ils ne seront utilisables que sur la page Index.  
Je vais juste expliquer cette signature de la méthode `.Add` que j’ai utilisé pour l’exemple :  
* 1er paramètre – `ModKeys` : la touche de modification (CTRL, SHIFT, ALT, META ou rien (`ModKeys.None`)). [Liste des ModKeys](https://github.com/jsakamoto/Toolbelt.Blazor.HotKeys/blob/master/Toolbelt.Blazor.HotKeys/ModKeys.cs).  
Pour info la touche « META » pour Windows : c’est la touche Windows, et pour Apple : c’est la touche Command.  
* 2eme paramètre – `Keys` : toutes les autres touches du clavier, A à Z, 0 à 9,..   
Elle prend en charge des touches spéciales comme *« Audio Volume Down/Up, Launch Application, … »*. [Liste des Keys](https://github.com/jsakamoto/Toolbelt.Blazor.HotKeys/blob/master/Toolbelt.Blazor.HotKeys/Keys.cs).  
* 3eme paramètre – l’Action : C’est la méthode qui sera appelée quand la combinaison de touche sera faite.  
* 4eme paramètre – la description : c’est un paramètre non obligatoire, cependant peut être utile quand vous avez beaucoup de raccourcis.  
```csharp
// Exemples de "commandes" :
// Pour le raccourci SHIFT + 1
.Add(ModKeys.Shift, Keys.Num1, OpenFanclubPage, "Ouvrir Fanclub Page.")

// Pour le raccourci CTRL + SHIFT + 1
.Add(ModKeys.Ctrl | ModKeys.Shift, Keys.Num1, OpenFanclubPage, "Ouvrir Fanclub Page.")
```

Maintenant si vous avez remarqué, j’ai mis pour `Keys.Num1` et `Keys.Num2`, 2 méthodes distingues, et pour les autres j’ai mis la même méthode.   
Les méthodes qui sont appelées, doivent avoir comme paramètre un <code>[HotKeyEntry](https://github.com/jsakamoto/Toolbelt.Blazor.HotKeys/blob/master/Toolbelt.Blazor.HotKeys/HotKeyEntry.cs)</code>. Avec lui, ça me permet de faire un `switch` sur la propriété `Key`. Valable pour mon cas, car j’utilise toujours le modificateur SHIFT.  
Note : `HotKeyEntry` fournit aussi la propriété `ModKeys`.  
```csharp
private Task OpenPage(HotKeyEntry arg)
{
	switch (arg.Key)
	{
		case Keys.Num3:
			navigationManager.NavigateTo("/drawcss", true);				
			break;
		case Keys.Num4:
			navigationManager.NavigateTo("/intergiciel-Errrk-disgusting", true);
			break;
		case Keys.Num5:
			navigationManager.NavigateTo("/faq", true);
			break;
		case Keys.Num6:
			navigationManager.NavigateTo("/webchat", true);
			break;
		default:
			navigationManager.NavigateTo("/", true);
			break;
	}
	return Task.CompletedTask;
}
```
### IDisposable

Enfin il faut que le composant Razor ou le ViewModel implémente `IDisposable`, pour pouvoir libérer les ressources (inscrit dans la doc – *Step 4 de How to install and use?*). En regardant un peu plus la `class HotKeysContext` (*[code](https://github.com/jsakamoto/Toolbelt.Blazor.HotKeys/blob/master/Toolbelt.Blazor.HotKeys/HotKeysContext.cs)*), il y a les méthodes : `Register` et `Unregister` utiliser dans son `Dispose()`.  

Voilà, il est possible de faire des exclusions en fonction de quel élément à le focus, mais je vous laisse découvrir cette librairie.  
<img src="https://media.giphy.com/media/bOwOAey4MDO3ivBkgK/giphy-downsized.gif" alt="" />

