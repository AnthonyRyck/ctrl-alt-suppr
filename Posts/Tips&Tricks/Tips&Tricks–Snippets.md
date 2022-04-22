Pour gagner du temps sur l’écriture de code, il existe les « Snippets ». Je vais montrer comment je fais les miens. Il existe plusieurs extensions pour en faire, à vous de les tester, et de trouver votre solution.  

## Snippet Designer
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/ManageExtension.png)

Aller dans Extensions — Manage Extensions.  
Il faut chercher dans « Online — Visual Studio Marketplace » l’extension « Snippet Designer ».  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/01_snippet-768x527.png)

Une fois téléchargé, il faut redémarrer Visual Studio pour que l’installation soit faite.  

## Créer un Snippet

Pour cet exemple, je vais créer le snippet pour les tests unitaires en AAA. J’aime bien mettre des régions pour chacun des ‘A’ : Arrange, Act, Assert. Créer une class avec n’importe quel nom, et ajouter le bout de code que vous voulez avoir dans votre snippet.  
```csharp
[Fact]
public void NomMethodeTest()
{
	#region ARRANGE

	#endregion

	#region ACT

	#endregion

	#region ASSERT

	#endregion
}
```

Sélectionnez votre le code et faites un clique droit –> Export as Snippet.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/02_Export_snippet.png)

Il ouvre une nouvelle fenêtre d’édition de votre futur Snippet.   
Dans le bandeau du haut :  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/BandeauSnippet.png)

**Snippet** : SnippetFile1 –> Nom du fichier pour le Snippet. Conseil, mettre le même nom que votre Shortcut. Dans Snippet Explorer, la recherche se fait sur le nom du fichier, pas sur le raccourci.  
**Language** : Choix du langage. Visual studio vous proposera le snippet que si vous êtes dans une class C# dans notre cas.  
**Shortcut** : raccourci pour « invoquer » le snippet. Un peu comme « prop » – « ctor ».  

Bon maintenant que j’ai indiqué ces 3 champs, j’aimerai qu’il me propose de changer le nom de la méthode quand j’invoque le snippet.  
Sélectionnez le nom de la méthode (NomMethodeTest), et clique droit — Make Replacement.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/03_Mark_snippet.png)
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/04_Mark_snippet.png)

Une fois marqué, dans le tableau en bas, on peut voir cette valeur apparaître.   
On peut le définir comme :  
– Kind=Literal : un champs text comme un nom de méthode  
– Kind=Object : attend un type (string, object,…)  
## Fonctions

Dans le tableau du bas, il y a une colonne Function. Il existe quelques function pour nous faire gagner du temps :  
– `ClassName()` : Renvoie le nom de la classe qui contient l’extrait de code inséré.  
– `SimpleTypeName(TypeName)` : Réduit le paramètre TypeName à sa forme la plus simple dans le contexte dans lequel l’extrait de code a été appelé.  
– `GenerateSwitchCases(EnumerationLiteral)` : Génère une instruction `switch` et un ensemble d’instructions `case` pour les membres de l’énumération spécifiée par le paramètre `EnumerationLiteral`. Le paramètre `EnumerationLiteral` doit être une référence à un littéral d’énumération ou à un type d’énumération.  
## Mots clés

Enfin il y a des mots clés.  
– `$end$` : marque l’emplacement où placer le curseur après l’insertion de l’extrait de code.  
– `$selected$` : représente le texte sélectionné dans le document qui doit être inséré dans l’extrait de code lorsqu’il est appelé.  
## Pour conclure

Voilà à quoi ressemble mon snippet pour une méthode de test avec Fact :  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/factSnippet.png)

Et juste pour un autre exemple, mon snippet pour mes propriétés qui doivent notifier leur changement  – `NotifyPropertyChanged()`  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/Snippet/propchangedSnippet.png)

Faites des snippets pour des bouts de code que vous écrivez souvent, ça va vous faire gagner du temps, et même harmoniser l’écriture pour tous les développeurs d’une équipe.
