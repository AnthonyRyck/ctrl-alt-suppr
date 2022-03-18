

Marre d’avoir des retours de votre API avec la moitié de la base de données, alors que vous vouliez juste connaitre un ID d’un champ en particulier ?   
Fatigué d’avoir à faire des *end points* en masse, pour avoir un type de retour, puis un autre, et encore un autre, pour au final dire, « Au puis merde, je vais tout ramener, et je ferais le tri des patates dans l’application… »  
<img src="https://media.giphy.com/media/l1KVaj5UcbHwrBMqI/giphy.gif" alt="" />

GraphQL est là pour vous simplifier la vie.  
## Qu’est-ce que GraphQL
>**GraphQL** (pour **Graph Query Language**) est un langage de requêtes et un environnement d’exécution, créé par Facebook en 2012, avant d’être publié comme >projet open-source en 2015. Inscrit dans le modèle Client-Serveur, il propose une alternative aux API REST. La requête du client définit une structure de données, dont >le stockage est éventuellement distribué, et le serveur suit cette structure pour retourner la réponse. Fortement typé, ce langage évite les problèmes de retour de >données insuffisants (*under-fetching*) ou surnuméraires (*over-fetching*).  
[GraphQL — Wikipédia (wikipedia.org)](https://fr.wikipedia.org/wiki/GraphQL)  

## Projet de test

Le code source du projet est sur [GitHub](https://github.com/AnthonyRyck/CodesPourDevTo/tree/master/src/dotNet6/TutoGraphQL), et j’ai mis un live de la demo sur : [graphql-tuto.ctrl-alt-suppr.dev](https://graphql-tuto.ctrl-alt-suppr.dev/).  
Dans ce post je vais utiliser le package [HotChocolate.AspNetCore](https://www.nuget.org/packages/HotChocolate.AspNetCore/) de [ChilliCream](https://chillicream.com/).   
Pour découvrir GraphQL, nous allons créer notre serveur web. Cet exemple sera une API permettant d’avoir des informations sur des personnes. Les données sont stockées dans un JSON, et voici un exemple de personne.  
```json
{
  "id": "70f05566-1386-425d-bec3-d20b9c8e88fa",
  "index": 0,
  "isActive": true,
  "age": 40,
  "name": "Florence Wade",
  "gender": "female",
  "company": "VICON",
  "email": "florencewade@vicon.com",
  "phone": "+1 (942) 437-2165",
  "address": "405 Times Placez, Motley, Nevada, 9643",
  "about": "Id reprehenderit aute laborum do eiusmod qui reprehenderit dolore. Proident reprehenderit duis do duis minim commodo aute id. Anim incididunt in irure fugiat ipsum ex. Aliqua officia sunt excepteur proident tempor ex.\r\n",
  "registered": "2021-08-18T04:16:38 -02:00",
  "latitude": 8.848137,
  "longitude": 73.653237,
  "friends": [
    {
      "id": 0,
      "name": "Johnston Zamora"
    },
    {
      "id": 1,
      "name": "Clarke Blair"
    },
    {
      "id": 2,
      "name": "Lindsey Chandler"
    }
  ],
  "greeting": "Hello, Florence Wade! You have 2 unread messages.",
  "favoriteFruit": "apple"
}
```
### Création du projet de test

Toute la création du projet se fera en ligne de commande, parce que nous adorons ça !  
<img loading="lazy" src="https://media.giphy.com/media/lCbSAbRrFEfkY/giphy.gif" alt="" width="429" height="333" />
```generic
# création d'un projet web vide.
dotnet new web --output TutoGraphQL
```

Il faut ajouter les packages suivant.  
```generic
dotnet add .\TutoGraphQL package HotChocolate.AspNetCore
dotnet add .\TutoGraphQL package HotChocolate.Data
```
* `HotChocolate.AspNetCore` contient le middleware GraphQL ASP.NET Core pour Hot Chocolate. De plus, ce package comprend le middleware [Banana Cake Pop](https://chillicream.com/docs/bananacakepop/getting-started), qui fournit un IDE GraphQL (c’est l’IDE sur l’exemple que j’ai mis en live). Cela permet de tester des requêtes.  
* `HotChocolate.Data` contient des extensions prêtes à l’emploi pour la gestion des données dans HotChocolate (filtrage, projections et tri).  
### Ajout du schéma GraphQL

Le schéma GraphQL est défini par des entités. Dans notre cas, se seront `Personne` et `Friend`.  
```csharp
namespace TutoGraphQl.Models
{
    public class Personne
    {
        public Guid id { get; set; }
        public int index { get; set; }
        public bool isActive { get; set; }
        public int age { get; set; }
        public string name { get; set; }
        public string gender { get; set; }
        public string company { get; set; }
        public string email { get; set; }
        public string phone { get; set; }
        public string address { get; set; }
        public string about { get; set; }
        public string registered { get; set; }
        public float latitude { get; set; }
        public float longitude { get; set; }
        public Friend[] friends { get; set; }
        public string greeting { get; set; }
        public string favoriteFruit { get; set; }
    }
}
```
```csharp
namespace TutoGraphQl.Models
{
    public class Friend
    {
        public int id { get; set; }
        public string name { get; set; }
    }
}
```
### Ajout du service de donnée

Il faut ajouter un répertoire Data, et ajouter l’interface et son implémentation pour « gérer » les personnes.  
```csharp
namespace WebApiGraphQl.Data
{
    public interface IDataAccess
    {
	/// <summary>
	/// Retourne toutes personnes
	/// </summary>
	/// <returns></returns>
	IEnumerable<Personne> GetAll();

	/// <summary>
	/// Retourne la personne par rapport à son ID
	/// </summary>
	/// <param name="id" />
	/// <returns></returns>
	Personne GetPersonne(Guid id);
    }
}
```
```csharp
using System.Text.Json;

namespace WebApiGraphQl.Data
{
	public class DataAccess : IDataAccess
	{
		private IEnumerable<Personne> AllPeople;

		public DataAccess()
		{
			// Chargement des données JSON.
			string pathJson = System.IO.Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "JsonFile", "personnes.json");

			StreamReader reader = new StreamReader(pathJson);
			AllPeople = JsonSerializer.Deserialize< /><Personne>>(reader.ReadToEnd());
		}

		/// <inheritdoc cref="IDataAccess.GetAll"></inheritdoc>
		public IEnumerable<Personne> GetAll()
		{
			return AllPeople;
		}

		/// <inheritdoc cref="IDataAccess.GetPersone(Guid)"></inheritdoc>
		public Personne GetPersonne(Guid id)
		{
			return AllPeople.FirstOrDefault(x => x.id == id);
		}	
	}
}
```

Déclaration de ce nouveau service dans `Program.cs`  
```csharp
var builder = WebApplication.CreateBuilder(args);

// Service pour "gerer" les donnees
builder.Services.AddSingleton<IDataAccess, DataAccess="">();
// [...] reste du code.</IDataAccess,>
```
## Définir notre résolveur

Un résolveur agit comme un gestionnaire de requêtes GraphQL. Dans notre exemple, nous allons créer une méthode racine `ElRequetor` qui expose les requêtes possibles qu’un utilisateur peut explorer. Créons un répertoire `RequetesGraph`, et ajoutons la nouvelle `class ElRequetor`.  
```csharp
namespace WebApiGraphQl.RequetesGraph
{
	public class ElRequetor
	{
		private IDataAccess _access;

		public ElRequetor(IDataAccess dataAccess)
		{
			_access = dataAccess;
		}

		[UseSorting]
		[UseFiltering]
		public IEnumerable<Personne> GetPersonnesAsync()
		{
			return _access.GetAll();
		}
		
		public Personne GetPersonneById(Guid id)
		{
			return _access.GetPersonne(id);
		}

		[UseSorting]
		public Personne GetFriendsById(Guid id)
		{
			return _access.GetPersonne(id);
		}
	}
}
```

J’exposse 2 méthodes pour interroger les données.  
* `GetPersonnesAsync()` retourne l’ensemble des personnes, et il a les attributs `[UseSorting]` qui permet d’indique un classement ([doc Sorting](https://chillicream.com/docs/hotchocolate/fetching-data/sorting)), et `[UseFiltering]` qui permet de filtrer ([doc Filtering](https://chillicream.com/docs/hotchocolate/fetching-data/filtering)).  
* La 2eme méthode donne une personne en fonction de l’id passé en paramètre.  
### Configuration du serveur

Il faut ajouter dans Program.cs les middlewares à notre serveur. Voici le code complet de `Program.cs`.  
```csharp
using WebApiGraphQl.RequetesGraph;

var builder = WebApplication.CreateBuilder(args);

// Service pour "gerer" les donnees
builder.Services.AddSingleton<IDataAccess, DataAccess="">();

builder.Services.AddGraphQLServer()
				.AddQueryType<ElRequetor>()
				.AddFiltering()
				.AddSorting();

var app = builder.Build();

app.MapGraphQL("/");

app.Run();
```
## Testons l’application

En exécutant le code, ou en allant sur le live, vous aurez l’IDE Banana Cake Pop. Cet IDE permet de tester les requêtes GraphQL. Il y a un système d’intellisense (comme le montre l’image).  
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Decouvertes/GraphQL/01_GraphQl_BananaHelpRequete.png" alt="" />

Notre première requête (ci-dessous) récupère sur l’ensemble des personnes, leur id, nom et email. Je prends que ce dont j’ai besoin !  
```json
query{
  personnesAsync
  {
    id
    name
    email
  }
}
```

`personnesAsync`… Tient tient, mais c’est le nom de ma méthode dans mon resolver. Ok il manque le Get.   
Nous avons comme résultat.  
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Decouvertes/GraphQL/02_GraphQl_Result.png" alt="" />

La 2eme requête est sur la même méthode, je veux que l’ID et le nom, et surtout utiliser un classement différent. Je veux que le classement soit faire sur le nom et de Z à A.   
```json
query{
  personnesAsync (order: {name:DESC}) {
    id
    name  
  }
}
```

Comme cette méthode porte l’attribut `UseSorting`, cela me permet d’indiquer un ordre différent, et sur quelle propriété.  
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Decouvertes/GraphQL/03_GraphQl_ResultSorting.png" alt="" />

Maintenant, je veux avoir la liste des amis d’une seule personne en particulier.  
```json
query{
  personneById(id: "feecf00e-609d-4eae-ba49-6328c0b84248") {
    friends
    {
      id
      name
    }
  }
}
```

`personneById` ! Quand je vous disais que ce sont les mêmes noms que le resolver ! Vous voyez comment passer un paramètre à la requête. Maintenant, j’indique que je veux que la propriété `friends`, et que l’id et le nom des amis.  
Pas d’image pour celle-là, je vous laisse l’essayer.  
Comme cette méthode n’a pas d’attribut `Sorting`, l’intellisense ne propose pas `order`, comme avec la méthode `friendsById.`  
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Decouvertes/GraphQL/04_GraphQl_Intellisense.png" alt="" />

J’ai fait une méthode qui prend en paramètre l’id d’une personne, mais j’aurais pu utiliser un filtre. La méthode personneAsync a comme autre attribut `UseFiltering`, ce qui permet d’indiquer à la requête un filtre. Voici la requête qui donnera le même résultat. (Voir la doc sur le [filtering de ChilliCream](https://chillicream.com/docs/hotchocolate/fetching-data/filtering))  
```json
query {
  personnesAsync(where: {id:{eq:"feecf00e-609d-4eae-ba49-6328c0b84248"}})
  {
    id
    name
  }
}
```

C’est le petit mot clé `eq` qui fait tout 😉   

D’autres packages nuget permettent d’intégrer GraphQL avec MongoDb, EntityFramework, Neo4J, … ([Integration](https://chillicream.com/docs/hotchocolate/integrations)).   
Voilà fini pour cette découverte, il reste encore tellement de chose à voir, (les mutations, ajout/suppression/update des données), mais je vous laisse découvrir ce merveilleux outil.  

