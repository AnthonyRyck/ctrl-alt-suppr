
# Mutation Testing

Tester son code est obligatoire, mais ça veut dire que ces tests c’est aussi du code, et qui test ce code ? Pas dans le sens algorithmique mais dans leurs véracités.  
En décembre 2021, j’ai suivi une conf à l’Agile Tour de Rennes, sur le Mutation Testing, et j’avoue avoir adoré.   
Dans ce post je vais montrer comment mettre en place un outil de mutation.  
## Définition

L’idée du Mutation Testing est de modifier le code « métier » pour voir si les tests échouent. Par exemple, dans une méthode ou il y aurait :   
`var result = x + y`   
serait transformé en :   
`var result = x - y`.  
En gros l’outils inverses les opérateurs :   
`+` devient `-`  
`*` devient `/`  
`==` devient `!=`, … Vous avez compris l’idée. (Doc sur les mutations [stryker-mutator.io](https://stryker-mutator.io/docs/stryker-net/mutations) et sur les [regex](https://stryker-mutator.io/docs/stryker-net/regex-mutations))  

Vous allez me dire c’est tout ? Les opérateurs `+`,`-`,`x`,`/`,`==`,`!=`,… ont une énorme importance dans une application. Ce sont eux qui définissent réellement le comportement d’une application. A cela vous ajoutez tous les traitements que nous pouvons faire sur les `string`.  

Modifier le code pour changer tous les opérateurs à la main, ça va prendre beaucoup de temps. Heureusement il existe un outil qui permet d’automatiser ces modifications, d’exécuter les tests et même de générer des rapports.  
C’est **Stryker.NET** ([stryker-mutator.io](https://stryker-mutator.io/) et [Github](https://github.com/stryker-mutator/stryker-net))  
## Projet de test/démonstration

Sur mon [Github](https://github.com/AnthonyRyck/CodesPourDevTo/tree/master/src/dotNet6/TutoMutationTesting) j’ai mis un projet pour tester Stryker. Il est composé d’une librairie `Business` et d’un projet de test `Business.Test` pour les tests unitaires. La librairie permet de créer des packs de produit via la `class Engine`. Un produit (une conserve par exemple) est défini par une taille et un poids, et un pack est juste un paquet de 6 produits. Voici les 2 entités :  
```csharp
namespace Business;

public class Produit
{
    public string IdentifiantProduit { get; private set; }
    
    public double Poids { get; private set; }
    
    public int Taille { get; private set; }

    /// <summary>
    /// Constructeur d'un produit.
    /// </summary>
    /// <param name="id" />
    /// <param name="poids" />
    /// <param name="taille" />
    public Produit(string id, double poids, int taille)
    {
        IdentifiantProduit = id;
        Poids = poids;
        Taille = taille;
    }
}


public class Pack
{
    /// <summary>Numéro lot </summary>
    public int NumLot { get; private set; }
    
    /// <summary>Produits contenus dans ce pack</summary>
    public List<Produit> Produits { get; private set; } = new List<Produit>();
    
    /// <summary>
    /// Constructeur d'un pack
    /// </summary>
    /// <param name="numeroLot" />
    public Pack(int numeroLot)
    {
        NumLot = numeroLot;
    }

    /// <summary>
    /// Ajoute un produit dans le Pack.
    /// </summary>
    /// <param name="produit" />
    public void AddProduit(Produit produit)
    {
        Produits.Add(produit);
    }
}</Produit></Produit>
```

Comme dit plus haut, la `class Engine` va traiter des produits pour savoir s’ils sont bon (respecte une taille et un poids), pour pouvoir être packagé. Voici le code :  
```csharp
public class Engine
{
	/// <summary>Liste des produits rejetés</summary>
	public List<Produit> RejetProduits { get; private set; }

	/// <summary>Liste des packs remplis</summary>
	public List<Pack> PacksRemplis { get; private set; }

	/// <summary>Pack en cours de remplissage</summary>
	public Pack PackEnCours { get; private set; }
	
	/// <summary>Compteur de Pack</summary>
	public int CompteurDuJour {get; private set; }

	/// <summary>Données d'acceptation</summary>
	private ConfigProduit _config;

	/// <summary>Poids minimal acceptable par rapport à la marge</summary>
	public double MargePoidsMin;

	/// <summary>Poids maximal acceptable par rapport à la marge</summary>
	public double MargePoidsMax;

	public Engine(ConfigProduit config, int compteurDepart)
	{
		RejetProduits = new List<Produit>();
		PacksRemplis = new List<Pack>();
		_config = config;
		CompteurDuJour = compteurDepart;

		double marge = config.PoidsIdeal * (config.MargeErreurPoids / 100);
		MargePoidsMin = config.PoidsIdeal - marge;
		MargePoidsMax = config.PoidsIdeal + marge;
	}

	#region Public methods

	/// <summary>
	/// Va traiter une liste de produit pour les "packager"
	/// </summary>
	/// <param name="nouveauProduits" />
	public void Process(IEnumerable<Produit> nouveauProduits)
	{
		foreach (var produit in nouveauProduits)
		{
			if(Validate(produit))
			{
				AddToPack(produit);
			}
			else
			{
				RejetProduits.Add(produit);
			}	
		}
	}

	#endregion

	#region Private methods

	/// <summary>
	/// Permet d'ajouter un produit dans un pack
	/// </summary>
	/// <param name="produit" />Produit acceptable
	private void AddToPack(Produit produit)
	{
		if(PackEnCours == null)
		{
			PackEnCours = new Pack(CompteurDuJour++);	
		}

		PackEnCours.AddProduit(produit);
		if(PackEnCours.Produits.Count == 6)
		{
			PacksRemplis.Add(PackEnCours);
			// Remise à zéro
			PackEnCours = null;
		}		
	}

	/// <summary>
	/// Permet de valider ou non un produit
	/// </summary>
	/// <param name="produit" />
	/// <returns></returns>
	private bool Validate(Produit produit)
	{
		if(produit.Taille != _config.TailleIdeale)
			return false;

		if(!ValidatePoids(produit.Poids))
			return false;
		
		return true;
	}

	/// <summary>
	/// Valide que le poids du produit est dans la marge d'erreur
	/// </summary>
	/// <param name="poids" />Poids du produit
	/// <returns>TRUE : est acceptable.</returns>
	private bool ValidatePoids(double poids)
	{
		return MargePoidsMin <= poids && poids <= MargePoidsMax;
	}

	#endregion
}</Produit></Pack></Produit></Pack></Produit>
```

Rien de fou fou, je reste dans un exemple simple, mais je ne voulais pas un exemple avec des méthodes `a + b`, `a > b`,….  
## Installation de Stryker.NET

Installons Stryker localement à un projet. Nous allons utiliser un « outils » dotnet (doc sur [les outils dotnet](https://docs.microsoft.com/fr-fr/dotnet/core/tools/global-tools)).   
Pour installer un outil pour un accès local uniquement (pour le répertoire courant et les sous-répertoires), nous devons ajouter un fichier manifeste. ([dotnet new tool-manifest](https://docs.microsoft.com/fr-fr/dotnet/core/tools/global-tools#install-a-local-tool)).  
Il faut se positionner dans le répertoire du projet voulu et faire les commandes suivantes.  
```generic
dotnet new tool-manifest
```

Ça ajoute un répertoire `.config`, avec un fichier `dotnet-tools.json`. Ensuite indiquer quel outil installer.  
```generic
dotnet tool install dotnet-stryker
```

Ce qui ajoute les informations dans le fichier json.  
```json
{
  "version": 1,
  "isRoot": true,
  "tools": {
    "dotnet-stryker": {
      "version": "1.2.1",
      "commands": [
        "dotnet-stryker"
      ]
    }
  }
}
```
```generic
dotnet tool restore
```

Notre projet est prêt pour subir quelques mutations.  
## Utilisation de Stryker

Dans le répertoire du projet de test unitaire (`Business.Test`), il faut exécuter la commande `dotnet stryker` pour que Stryker nous crée des mutants.  
<img src="https://media.giphy.com/media/I0Z7xEnYL3Fu0/giphy.gif" alt="" />

Informations que Stryker affiche.  
```generic
Version: 1.2.1

[05:05:50 INF] Identifying project to mutate.
[05:05:51 INF] The project C:\CodeSource\Github\AnthonyRyck\CodesPourDevTo\src\dotNet6\TutoMutationTesting\Business\Business.csproj will be mutated.
[05:05:53 INF] Analysis complete.
[05:05:53 INF] Building test project C:\CodeSource\Github\AnthonyRyck\CodesPourDevTo\src\dotNet6\TutoMutationTesting\Business.Test\Business.Test.csproj (1/1)
[05:05:58 INF] Total number of tests found: 3.
[05:05:58 INF] Initial testrun started.
[05:06:00 INF] 37 mutants created
[05:06:00 INF] Capture mutant coverage using 'CoverageBasedTest' mode.
[05:06:01 INF] 37    mutants will be tested because: not run
[05:06:01 INF] 37    total mutants will be tested
100,00% │ Testing mutant 37 / 37 │ K 34 │ S 3 │ T 0 │ ~0m 00s │                                                                                     00:00:19

Killed:   34
Survived:  3
Timeout:   0
Hint: by passing "--open-report or -o" the report will open automatically once Stryker is done.
Your html report has been generated at:
file:///C:/CodeSource/Github/AnthonyRyck/CodesPourDevTo/src/dotNet6/TutoMutationTesting/Business.Test/StrykerOutput/2022-01-05.05-05-50/reports/mutation-report.html
You can open it in your browser of choice.
[05:06:20 INF] Time Elapsed 00:00:30.1037369
[05:06:20 INF] The final mutation score is 91.89 %
```

Il a créé 37 mutants dans le code, 34 ont pu être tués mais il en reste 3. Il crée aussi un répertoire `StrykerOutput` dans le répertoire d’exécution de la commande, avec un rapport en html.  
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/MutationTesting/MutationTesting-04-Report.png" alt="" />

Dans le rapport, ouvrons `Engine.cs`, il y a 2 mutants, tentons de les tuer.  
#### Premier mutant
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/MutationTesting/MutationTesting-05-Mutants01.png" alt="" />

Donc là il nous montre que le fait de changer le compteur de sens, ça ne perturbe pas du tout les tests unitaires.  
Normal… aucun test ne couvre cette partie. J’ajoute un nouveau test unitaire.  
```csharp
[Fact]
public void TestOnNumeroLot()
{
	#region Arrange
	
	ConfigProduit configTest = new ConfigProduit(100, 5, 20);
	List<Produit> ProduitsTest = new List<Produit>()
	{
		new Produit("produitOk-01", 100, 20),
		new Produit("produitOk-02", 101, 20),
		new Produit("produitOk-03", 102, 20),
		new Produit("produitOk-04", 103, 20),
		new Produit("produitOk-05", 104, 20),
		new Produit("produitOk-06", 105, 20)
	};
		
	#endregion
	
	#region Act
	
	Engine engine = new Engine(configTest, 0);
	engine.Process(ProduitsTest);
	
	#endregion
		
	#region Assert
	
	// doit y avoir 6 produits OK (soit un pack complet), 
	Assert.True(engine.CompteurDuJour == 1, "Il y a qu'un pack complet (6 produits)");
	Assert.True(engine.PacksRemplis[0].NumLot == 0);
	#endregion
}</Produit></Produit>
```

Le fait de tester si la propriété `NumLot == 0` élimine le mutant. Stryker, en mettant `--` il sera détecté.  
#### Deuxième mutant
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/MutationTesting/MutationTesting-05-Mutants02.png" alt="" />

Là le poids minimal n’est pas testé s’il est sur la marge inférieure. J’ai beau avoir des tests avec des poids différents, mais je n’en ai pas un qui va tester sur les poids qui tendent vers le mini, je faisais que sur des poids qui aller sur la marge supérieure.   
Ajout d’un nouveau test unitaire :  
```csharp
[Fact]
public void TestOnPoidsMinimal()
{
	#region Arrange
	
	ConfigProduit configTest = new ConfigProduit(100, 5, 20);
	List<Produit> ProduitsTest = new List<Produit>()
	{
		new Produit("produitOk-01", 100, 20),
		new Produit("produitOk-02", 99, 20),
		new Produit("produitOk-03", 98, 20),
		new Produit("produitOk-04", 97, 20),
		new Produit("produitOk-05", 96, 20),
		new Produit("produitOk-06", 95, 20),
		new Produit("produitNOk-07", 94, 20)
	};
		
	#endregion
	
	#region Act
	
	Engine engine = new Engine(configTest, 0);
	engine.Process(ProduitsTest);
	
	#endregion
		
	#region Assert
	
	// doit y avoir 6 produits OK (soit un pack complet), 
	Assert.True(engine.CompteurDuJour == 1, "Il y a qu'un pack complet (6 produits)");
	Assert.True(engine.PacksRemplis[0].NumLot == 0);
	Assert.True(engine.RejetProduits.Count == 1, "Il y a un rejet, poids trop bas");
	
	#endregion
}</Produit></Produit>
```

Là j’ai le poids 94 grammes qui sera en rejet, normal, et c’est avec le produit avec un poids de 95 grammes, qui est sur la limite de la marge inférieure, que le mutant sera détecté.   

Je relance la commande `dotnet stryker`.  
```generic
Version: 1.2.1

[05:38:31 INF] Identifying project to mutate.
[05:38:33 INF] The project C:\CodeSource\Github\AnthonyRyck\CodesPourDevTo\src\dotNet6\TutoMutationTesting\Business\Business.csproj will be mutated.
[05:38:34 INF] Analysis complete.
[05:38:34 INF] Building test project C:\CodeSource\Github\AnthonyRyck\CodesPourDevTo\src\dotNet6\TutoMutationTesting\Business.Test\Business.Test.csproj (1/1)
[05:38:38 INF] Total number of tests found: 4.
[05:38:38 INF] Initial testrun started.
[05:38:40 INF] 37 mutants created
[05:38:40 INF] Capture mutant coverage using 'CoverageBasedTest' mode.
[05:38:41 INF] 37    mutants will be tested because: not run
[05:38:41 INF] 37    total mutants will be tested
100,00% │ Testing mutant 37 / 37 │ K 36 │ S 1 │ T 0 │ ~0m 00s │   
00:00:17

Killed:   36
Survived:  1
Timeout:   0
Hint: by passing "--open-report or -o" the report will open automatically once Stryker is done.
Your html report has been generated at:
file:///C:/CodeSource/Github/AnthonyRyck/CodesPourDevTo/src/dotNet6/TutoMutationTesting/Business.Test/StrykerOutput/2022-01-05.05-38-31/reports/mutation-report.html
You can open it in your browser of choice.
[05:38:58 INF] Time Elapsed 00:00:27.1282839
[05:38:58 INF] The final mutation score is 97.30 %
```

Et le rapport.  
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/MutationTesting/MutationTesting-06-Report-nouveau.png" alt="" />

Nous pouvons voir que nous avons tués les 2 mutants.  
## Pour conclure

Avec Stryker j’ai pu voir que certaines parties du code n’étaient pas testés et il pouvait nous ajouter des mutants sans que les tests unitaires ne voient rien. Ensuite, il nous a aussi montré que sur des méthodes qui ont tests, il pouvait aussi ajouter des mutants sans problème… C’est un outil qui vient en complément avec les tests unitaires et d’autres métriques. Evitons de nous retrouver avec une couverture de code à 97%, mais qu’au final ces tests ne sont là que pour gonfler une métrique sans réel apport pour le projet, et de nous retrouver seul face à innombrables potentiels bugs.   
<img src="https://media.giphy.com/media/h7jjtmZstLBTCkWSKQ/giphy.gif" alt="" />

