
# JSON avec .Net

En lisant le post « [Try the new System.Text.Json source generator](https://devblogs.microsoft.com/dotnet/try-the-new-system-text-json-source-generator/)« , je me suis rendu compte que j’utilisais que très peu l’espace de noms `System.Text.Json` du .Net. Des années a utiliser [Newtonsoft.Json](https://github.com/JamesNK/Newtonsoft.Json), que je ne pensais plus à tester ou utiliser autre chose. Aaahhh la routine, l’ennemi du changement.  
Microsoft a livré cet espace de noms `System.Text.Json` avec le .Net Core 3.0 en 2019. Le pourquoi cet espace de noms était la performance et la sécurité. Microsoft a essayé d’empaqueter `Newtonsoft.Json` dans .Net, ils ont eu quelques difficultés. Microsoft souhaitait également supprimer la dépendance `Newtonsoft.Json` d’ASP.Net Core 3.0. Voilà pour la petite histoire.  
J’ai fait un projet de test pour montrer cet espace de noms. Le projet se trouve sur [GitHub](https://github.com/AnthonyRyck/CodesPourDevTo), dans le répertoire `dotNet6`, projet `TutoJson`.   
## Lire un JSON

Que la source soit via une requête Web ou d’un fichier JSON, il faut « `Deserializer` » le JSON en objet. Pour l’exemple j’ai généré un fichier JSON avec le site : [JSON Generator](https://www.json-generator.com/#). J’ai crée des « Personnes ». En voici un exemple de personne.  
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

Voilà un bout de code, pour passer d’une forme « string » en objet.  
```csharp
"######## Début de l'application Démo ########".ToConsoleInfo();
"######## pour JSON en .Net 6         ########".ToConsoleInfo();
"#:> Appuyer sur une touche pour commencer.".ToConsoleInfo();
ReadKey();

"######## LECTURE d'un JSON à partir d'un fichier ###########".ToConsoleInfo();
"#:> Appuyer sur une touche pour commencer.".ToConsoleInfo();
ReadKey();
WriteLine();

string pathFile = Path.Combine(AppContext.BaseDirectory, "Files", "personnes.json");
// En utilisant un stream.
using(var stream = File.OpenRead(pathFile))
{
	List<Personne> personnes = await JsonSerializer.DeserializeAsync< /><Personne>>(stream);
	$"Il y a {personnes.Count} personnes dans le fichier.".ToConsoleResult();
}
```

Il faut utiliser :   
<code>await JsonSerializer.DeserializeAsync<List<Personne>>(stream);</code>  
Doc Microsoft pour [JsonSerializer.DeserializeAsync](https://docs.microsoft.com/fr-fr/dotnet/api/system.text.json.jsonserializer.deserializeasync?view=net-6.0#:~:text=DeserializeAsync%20%28Stream%2C%20Type%2C%20JsonSerializerOptions%2C%20CancellationToken%29%20Asynchronously%20reads%20the,type.%20The%20stream%20will%20be%20read%20to%20completion.).  
A partir de là, nous pouvons travailler avec des objets `Personne`, mais moi je veux faire de la « manipulation » sur le JSON, sans passer pour nos objets « métiers ».  
## JsonObject / JsonNode

Chargement en mémoire.  
```csharp
JsonNode jsonNodePerson;
using (var stream = File.OpenRead(pathFile))
{
	jsonNodePerson = JsonObject.Parse(stream);
}
//NOTE : Possible de passer par un string.
//string contentString = File.ReadAllText(pathFile, Encoding.UTF8);
```

Faisons quelques requêtes. Trouver le nom d’une personne :  
```csharp
"Pour avoir le nom de la 2eme personne dans le fichier".ToConsoleInfo();
string nomPersonne = jsonNodePerson[1]["name"].GetValue<string>();
$"Nom de la 2eme personne {nomPersonne}".ToConsoleResult();

// avoir la valeur de la latitude.
double latitude = jsonNodePerson[1]["latitude"].GetValue<double>();
$"Latitude renseigné : {latitude}".ToConsoleResult();
```

Il est possible de « naviguer » dans l’arbre du JSON. Et s’il y a un `Array` dans une propriété, pareil on peut naviguer dedans.  
La propriété `friends` est un `Array`.  
```csharp
"Parcours dans les amis :".ToConsoleInfo();
var jsonFriend = jsonNodePerson[1]["friends"][2].ToJsonString();
$"Affiche un amis de la liste en JSON : {jsonFriend}".ToConsoleResult();

"Parcours dans un ami particulier :".ToConsoleInfo();
string nomAmi = jsonNodePerson[1]["friends"][2]["name"].GetValue<string>();
$"Nom de l'amis : {nomAmi}".ToConsoleResult();
```

J’ai utiliser la méthode `ToJsonString()` qui : (je cite la [doc](https://docs.microsoft.com/fr-fr/dotnet/api/system.text.json.nodes.jsonnode.tojsonstring?view=net-6.0)) *« Convertit l’instance actuelle en chaîne au format JSON »*.  

Bon maintenant je veux ajouter une nouvelle propriété à toutes les personnes, mais toujours sans passer par un objet `Personne`.   
Se sera la propriété `preference`, et ils auront tous la même valeur : « .Net 6 le meilleur ».  
```csharp
"Ajout d'une nouvelle propriété pour tout le monde : preference, avec sa valeur".ToConsoleInfo();
"#:> Appuyer sur une touche pour commencer.".ToConsoleInfo();
ReadKey();
JsonArray jsonArray = jsonNodePerson.AsArray();
$"Combien d'élément dans JsonArray : {jsonArray.Count}".ToConsoleResult();
WriteLine();
"Nous allons ajouter une nouvelle propriété dans le JSON".ToConsoleInfo();
for (int i = 0; i < jsonArray.Count; i++)
{
	jsonArray[i]["preference"] = ".Net 6 le meilleur";
}

"Vérification sur une personnne au hasard :".ToConsoleInfo();
Random random = new Random();
jsonArray[random.Next(0, jsonArray.Count)].ToString().ToConsoleResult();
```

J’indique que `jsonNodePerson` est un type `JsonArray`, ce qui me permet de boucler dessus :  
`JsonArray jsonArray = jsonNodePerson.AsArray();`  
Et pour chaque élément, j’ajoute la nouvelle propriété :  
`jsonArray[i]["preference"] = ".Net 6 le meilleur";`  
<img src="https://media.giphy.com/media/zcCGBRQshGdt6/giphy.gif" alt="" />

  
On peut dire que c’est très facile d’ajouter une propriété.   
Même chose dans le sens inverse, je veux enlever la propriété `gender`.  
```csharp
"Maintenant il faut enlever le genre, propriété gender".ToConsoleInfo();
"#:> Appuyer sur une touche pour commencer.".ToConsoleInfo();
ReadKey();
for (int i = 0; i < jsonArray.Count; i++)
{
	jsonArray[i].AsObject().Remove("gender");
}
"Vérification sur une personnne au hasard :".ToConsoleInfo();
jsonArray[random.Next(0, jsonArray.Count)].ToString().ToConsoleResult();
```
## JsonDocument

Il est possible de charger un JSON dans un [JsonDocument](https://docs.microsoft.com/fr-fr/dotnet/api/system.text.json.jsondocument?view=net-6.0).   
2 points d’attention sur JsonDocument :  
>Fournit un mécanisme permettant d’examiner le contenu structurel d’une valeur JSON sans instancier automatiquement des valeurs de données.Microsoft
  
*Source : Microsoft*  

Ce qui veut dire un gain sur l’espace mémoire.  
Et l’autre point, qui n’est pas des moindres :  
>Cette classe utilise les ressources de la mémoire regroupée pour réduire l’impact du garbage collector (GC) dans les scénarios à forte utilisation. Si vous ne parvenez pas à supprimer correctement cet objet, la mémoire ne sera pas retournée au pool, ce qui augmentera l’impact GC sur les différentes parties de l’infrastructure.Microsoft
  
*Source : Microsoft*  

Il faut utiliser les `using(JsonDocument ....)`, et ne pas faire le fifou.  
Avec `JsonDocument` ça nous permet de faire des requêtes LinQ.  
```csharp
"#### Utilisation avec JsonDocument ####".ToConsoleInfo();
"#:> Appuyer sur une touche pour commencer.".ToConsoleInfo();
ReadKey();
"Récupération de tous les noms".ToConsoleInfo();

using (var stream = File.OpenRead(pathFile))
using (JsonDocument document = JsonDocument.Parse(stream))
{
    JsonElement root = document.RootElement;
    var namesPersonne = root.EnumerateArray()
							.Select(x => x.GetProperty("name")
										.GetString())
							.ToList();

	$"Il y a {namesPersonne.Count} noms".ToConsoleResult();

	foreach (var name in namesPersonne.Take(100 .. 110))
    {
		$"- Nom : {name}".ToConsoleResult();
    }

	var personnesSup35ans = root.EnumerateArray()
								.Where(x => x.GetProperty("age").GetInt32() > 35)
								.Select(x => new
								{
									Nom = x.GetProperty("name").GetString(),
									Age = x.GetProperty("age").GetInt32()
								}).ToList();

	$"Il y a {personnesSup35ans.Count} personnes qui ont plus de 35 ans".ToConsoleResult();
	foreach (var person in personnesSup35ans)
	{
		$"- Nom : {person.Nom} - Age : {person.Age}.".ToConsoleResult();
	}
}
```

**Note** : J’ai utilisé `namesPersonne.Take(100..110)`, c’est une des nouveautés du C# 10. Permet de prendre les éléments entre l’index 100 et 110. Doc Microsoft sur le [Take](https://docs.microsoft.com/fr-fr/dotnet/api/system.linq.enumerable.take?view=net-6.0#System_Linq_Enumerable_Take__1_System_Collections_Generic_IEnumerable___0__System_Range_) et le [Range](https://docs.microsoft.com/fr-fr/dotnet/api/system.range?view=net-6.0).   

C# 10 est plein de petites nouveautés, que je vous invite à lire sur [Announcing .Net 6](https://devblogs.microsoft.com/dotnet/announcing-net-6/#system-linq-enumerable-support-for-index-and-range-parameters).  
## Serialization

Maintenant nous allons « Serializer » une liste d’objet `Incident`. Il n’y a que 2 propriétés dans cet objet, `Id` et `Titre`.  
```csharp
List<Incident> incidentsObject = new List<Incident>();
for (int i = 0; i <= 10; i++)
{
	incidentsObject.Add(new Incident() { Id = i, Titre = $"Problème num {i}" });
}

string contentInString = JsonSerializer.Serialize(incidentsObject);

contentInString.ToConsoleResult();
WriteLine();

"Raahhh beurk, ce n'est pas indenté".ToConsoleInfo();</Incident></Incident>
```

Voilà la sortie.  
```json
[{"Id":0,"Titre":"Probl\u00E8me num 0"},{"Id":1,"Titre":"Probl\u00E8me num 1"},{"Id":2,"Titre":"Probl\u00E8me num 2"},{"Id":3,"Titre":"Probl\u00E8me num 3"},{"Id":4,"Titre":"Probl\u00E8me num 4"},{"Id":5,"Titre":"Probl\u00E8me num 5"},{"Id":6,"Titre":"Probl\u00E8me num 6"},{"Id":7,"Titre":"Probl\u00E8me num 7"},{"Id":8,"Titre":"Probl\u00E8me num 8"},{"Id":9,"Titre":"Probl\u00E8me num 9"},{"Id":10,"Titre":"Probl\u00E8me num 10"}]
```
<img src="https://media.giphy.com/media/eGguteXjiil6LcvdQ6/giphy.gif" alt="" />

Quand vous exécuterez le code, le JSON est tout en ligne par défaut, ce qui est bien pour le stockage, envoie sur le Web, mais incompréhensible quand il faut l’ouvrir. Il est possible d’indiquer au moment de la sérialisation d’indenté le JSON avec `JsonSerializerOptions`.  
```csharp
JsonSerializerOptions options = new() { WriteIndented = true };
contentInString = JsonSerializer.Serialize(incidentsObject, options);
```
```json
{
  "description": "Liste des incidents pour un probl\u00E8me",
  "incidents": [
    {
      "id": 1,
      "titre": "Probl\u00E8me num\u00E9ro 1."
    },
    {
      "id": 2,
      "titre": "Probl\u00E8me num\u00E9ro 2."
    },
    {
      "id": 3,
      "titre": "Probl\u00E8me num\u00E9ro 3."
    },
    {
      "id": 4,
      "titre": "Probl\u00E8me num\u00E9ro 4."
    },
    {
      "id": 5,
      "titre": "Probl\u00E8me num\u00E9ro 5."
    },
    {
      "id": 6,
      "titre": "Probl\u00E8me num\u00E9ro 6."
    },
    {
      "id": 7,
      "titre": "Probl\u00E8me num\u00E9ro 7."
    },
    {
      "id": 8,
      "titre": "Probl\u00E8me num\u00E9ro 8."
    },
    {
      "id": 9,
      "titre": "Probl\u00E8me num\u00E9ro 9."
    },
    {
      "id": 10,
      "titre": "Probl\u00E8me num\u00E9ro 10."
    }
  ]
}
```

Bon, le JSON est indenté, mais les caractères spéciaux ne sont pas reconnu. Pour remédier à ça il faut encore ajouter une option.  
```csharp
JsonSerializerOptions optionSpecial = new()
{
	Encoder = JavaScriptEncoder.Create(UnicodeRanges.All),
	WriteIndented = true
};

contentInString = JsonSerializer.Serialize(incidentsObject, optionSpecial);
```

Avec  `UnicodeRanges`, il est possible de choisir l’encodage des caractères. –> Doc [UnicodeRanges](https://docs.microsoft.com/fr-fr/dotnet/api/system.text.unicode.unicoderanges?view=net-6.0).   

Je fais court sur cette partie, beaucoup d’explications pour la sérialisation sont sur le post :  
 « [Try the new System.Text.Json source generator](https://devblogs.microsoft.com/dotnet/try-the-new-system-text-json-source-generator/) » que j’ai parlé au début.  
## Création d’un JSON à la « mano »

Bon maintenant, je veux pouvoir créer un JSON de toute pièce, sans objet !   
Création du JSON Incident.  
```csharp
JsonArray incidents = new JsonArray();
for (int id = 1; id <= 10; id++)
{
	JsonObject nouveauIncident = new JsonObject
	{ 
		["id"] = id, 
		["titre"] = $"Problème numéro {id}." 
	};
	incidents.Add(nouveauIncident);
}

// Ajout de tous les incidents dans 
JsonObject jsonIncidents = new JsonObject();
jsonIncidents.Add("description", "Liste des incidents pour un problème");
jsonIncidents.Add("incidents", incidents);
```

Dans cette exemple, j’ai voulu montrer les 2 manières de créer une structure JSON :  
* en utilisant les `[ ]` dans l’initialisation d’un nouveau `JsonObject`   
* ou en passant pour la méthode `.Add("propertyName", Value)`  
```json
{
  "description": "Liste des incidents pour un problème",
  "incidents": [
    {
      "id": 1,
      "titre": "Problème numéro 1."
    },
    {
      "id": 2,
      "titre": "Problème numéro 2."
    },
    {
      "id": 3,
      "titre": "Problème numéro 3."
    }
  ]
}
```

Comme nous travaillons avec des objets `JsonObject`, `JsonNode`, `JsonArray`, nous pouvons faire tout les manipulations du début du post.  
Pour ce qui est de la performance, il existe plusieurs posts sur le Web mais sans trop vous « spoiler », c’est le meilleur !  

