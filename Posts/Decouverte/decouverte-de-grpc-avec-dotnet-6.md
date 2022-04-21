Dans cet article je vais montrer comment mettre en place le framework gRPC dans une application (serveur et client).  
Avec Visual Studio 2022 ou par la commande dotnet, il est possible de créer un projet gRPC.  

![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-01-CreationProjet.png)  

Mais pour cet article je vais partir d’une application [from scratch](https://www.from-scratch.fr/definition/). Je veux aussi revenir à la base, faire toute la création du projet en ligne de commande. Nous sommes souvent très/trop aidés (*moi le premier*) avec des menus contextuels pour ajouter un package nuget, une référence à un projet, mais sait-on encore le faire sans ?  
Tout le code source « final » se trouve sur [GitHub](https://github.com/AnthonyRyck/CodesPourDevTo/tree/master/src/dotNet6/TutoGrpc).  

## Qu’est-ce que gRPC ?

gRPC est un framework initialement développé par Google en 2015. Je ne vais pas vous vendre du rêve en recopiant le site de [grpc.io](https://www.grpc.io/docs/what-is-grpc/introduction/) pour vous donner une bonne explication, je vous laisse aller la voir…  
#### Les avantages

Je reprends les mots de la doc de Microsoft.  

*Les principaux avantages de gRPC sont :*  
* *Framework RPC léger, moderne et à haut niveau de performance.*  
* *Développement d’API « Contract-first », à l’aide de mémoires tampons de protocole par défaut, permettant des implémentations indépendantes du langage.*  
* *Outils disponibles pour de nombreux langages afin de générer des clients et serveurs fortement typés.*  
* *Prise en charge d’appels de streaming client, serveur et bidirectionnels.*  
* *Utilisation réduite du réseau avec sérialisation binaire Protobuf.*  

*Ces avantages font de gRPC la solution idéale pour :*  
* *Les microservices légers où l’efficacité est essentielle.*  
* *Les systèmes polyglottes où plusieurs langages sont nécessaires au développement.*  
* *Les services en temps réel de point à point qui doivent gérer des demandes ou réponses de streaming.*  

## Le projet

Ce que nous allons créer est un serveur Asp.Net Core qui sera possible d’interroger pour avoir la liste de tous les utilisateurs ou avoir un utilisateur par son id. Pour ne pas monter de base de données et rester dans quelque chose de simple, j’ai mis un fichier json contenant plus de 5000 fiches utilisateurs. Voici un exemple d’utilisateur :  

```json
{
    "_id": "61b6c9ceb0b039f7477bedef",
    "index": 0,
    "guid": "f7c10fe0-f887-4627-ac57-d332121664b9",
    "isActive": true,
    "balance": "$2,348.47",
    "picture": "http://placehold.it/32x32",
    "age": 27,
    "eyeColor": "green",
    "name": "Stephens Ferguson",
    "gender": "male",
    "company": "MOLTONIC",
    "email": "stephensferguson@moltonic.com",
    "phone": "+1 (976) 536-2883",
    "address": "294 Stillwell Avenue, Lowell, Oregon, 6643",
    "about": "Cupidatat veniam consectetur tempor est deserunt sint enim ullamco. Anim voluptate amet nulla commodo adipisicing ipsum minim aliqua dolore cillum aliqua adipisicing. Incididunt fugiat veniam officia velit cillum.\r\n",
    "registered": "2019-11-01T06:42:36 -01:00",
    "latitude": -71.063895,
    "longitude": 166.435462,
    "tags": [
      "fugiat",
      "esse",
      "velit",
      "duis",
      "magna",
      "ut",
      "voluptate"
    ],
    "friends": [
      {
        "id": 0,
        "name": "Polly Hull"
      },
      {
        "id": 1,
        "name": "Molina Livingston"
      },
      {
        "id": 2,
        "name": "Nikki Wong"
      }
    ],
    "greeting": "Hello, Stephens Ferguson! You have 5 unread messages.",
    "favoriteFruit": "apple"
  }
```

## Création du serveur gRPC

Première partie, nous allons créer notre serveur.  
Créer un répertoire pour accueillir le code source.  
`mkdir TutoGrpc  `
`cd TutoGrpc`  
Je l’ai dit, on part de la base !  
Une fois dans le répertoire, il faut créer notre fichier sln.  
`dotnet new sln`  
Le fichier sln prendra le nom du répertoire ou il se trouve (*si pas d’option donnée*).  
Création de notre serveur Web.  
`dotnet new web -o ServerGrpc`  – doc [dotnet new](https://docs.microsoft.com/fr-fr/dotnet/core/tools/dotnet-new)  
Crée un nouveau projet avec le template **web** et avec l’argument **-o** (*output*) ServerGrpc.   
**web** désigne un projet ASP.Net Core vide.  Pour l’output cela donne le nom du projet et crée un répertoire du même nom.  
Ajoutons ce projet dans notre fichier de solution.  
`dotnet sln add .\ServerGrpc\`   – doc [dotnet sln add](https://docs.microsoft.com/fr-fr/dotnet/core/tools/dotnet-sln#add)  
Nous allons avoir besoin des packages nuget pour gRPC, ajoutons-les à notre projet ServerGrpc.  
`cd ServerGrpc`  
dotnet add package Grpc.AspNetCore</code>    – doc [dotnet add package](https://docs.microsoft.com/fr-fr/dotnet/core/tools/dotnet-add-package)  
Je ne mets pas de nom de projet car je suis dans le répertoire du projet ou je veux ajouter les packages.  
[Grpc.AspNet.Core](https://www.nuget.org/packages/Grpc.AspNetCore/) est un meta package, ça veut qu’il contienne plusieurs packages (*pour la dernière version*) :  
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) (>= 3.18.0)  
* [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory/) (>= 2.41.0)  
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) (>= 2.41.0)  

Revenir en arrière `cd..` pour être dans le répertoire où se trouve le fichier `sln`, et faire :  
`dotnet build`. C’est pour être sûr que toutes les dépendances soient bien chargées.  
Maintenant on peut ouvrir notre éditeur de code 😉  

## Du Code !
### Partie Serveur

Nous avons notre projet Web tout vierge avec ces références à `Grpc.AspNet.Core`. Chargeons le fichier json en mémoire. Je crée un service `IDataService` qui va charger le fichier et faire la « gestion » sur les utilisateurs.  
```csharp
using System.Text.Json;

public class DataService : IDataService
{

    private List<User> AllUsers;


    public DataService()
    {
        AllUsers = LoadData()
                    .GetAwaiter()
                    .GetResult();
    }


    private async Task< /><User>> LoadData()
    {
        string pathFile = Path.Combine(AppContext.BaseDirectory, "Data", "Users.json");
        List<User> result = new List<User>();

        using (var stream = File.OpenRead(pathFile))
        {
            result = await JsonSerializer.DeserializeAsync< /><User>>(stream);
        }

        return result;
    }


    #region IDataService Implementation

    /// <see cref="IDataService.GetAllUser"></see>
    public User[] GetAllUser()
    {
        return AllUsers.ToArray();
    }

    /// <see cref="IDataService.GetCountUser"></see>
    public int GetCountUser()
    {
        return AllUsers.Count;
    }

    /// <see cref="IDataService.GetUser(string)"></see>
    public User GetUser(string id)
    {
        return AllUsers.FirstOrDefault(x => x._id == id);
    }

    #endregion
}
```

Juste pour info, voici l’entité `User` et `Friend`.  
```csharp
public class User
{
    public string _id { get; set; }
    public int index { get; set; }
    public string guid { get; set; }
    public bool isActive { get; set; }
    public string balance { get; set; }
    public string picture { get; set; }
    public int age { get; set; }
    public string eyeColor { get; set; }
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
    public string[] tags { get; set; }
    public Friend[] friends { get; set; }
    public string greeting { get; set; }
    public string favoriteFruit { get; set; }
}

public class Friend
{
    public int id { get; set; }
    public string name { get; set; }
}

```

Il faut déclarer le service dans l’application (`Program.cs`) :  
```csharp
builder.Services.AddSingleton<IDataService, DataService="">();</IDataService,>
```

Maintenant il faut créer le services gRPC. Pour ce faire il faut créer un fichier Protobuf (`.proto`), de l’abréviation de Protocol Buffers. Protobuf a son propre langage, mais rien de compliqué. C’est avec ce fichier que les outils C# (*Grpc.Tools*) vont générer le code.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-02-Workflow.png)

Créer un répertoire `Protos` dans le projet, et créer un fichier `collectionusers.proto`. Voici le code :  

```generic
// déclare la version de la syntax
syntax = "proto3";

package collectionusers;
option csharp_namespace = "ServerGrpc";

// déclaration des services/ contrats.
service Utilisateurs {
	rpc GetUserById (UserIdRequest) returns (UserResponse);
	rpc GetCountUser(Empty) returns (CountUser);

	// prend un argument Empty
	rpc GetAll (Empty) returns (AllUsersResponse);
}

message Empty {};

message CountUser{
    int32 nombreUser = 1;
}

// Le message de requête contient l'ID de l'utilisateur.
message UserIdRequest {
	string idUser = 1;
}

message UserResponse {
    string id = 1; 
    int32 index = 2;
    string guid = 3;
    bool isActive = 4;
    string balance = 5;
    string picture = 6;
    int32 age = 7;
    string eyeColor = 8; 
    string name = 9;
    string gender = 10;
    string company = 11;
    string email = 12;
    string phone = 13;
    string address = 14;
    string about = 15;
    string registered = 16;
    float latitude = 17;
    float longitude = 18;
    repeated string tags = 19;
    repeated Friends friends = 20;
    string greeting = 21;
    string favoriteFruit = 22;
}

message Friends {
    int32 id = 1;
    string name = 2;
}

message AllUsersResponse {
    repeated UserResponse AllUsers = 1;
}
```
![](https://media.giphy.com/media/YAlhwn67KT76E/giphy.gif)

**Attention** : Pour que les outils puissent générer le code il faut ajouter dans le `csproj` l’emplacement du fichier `proto`.   
```generic
<ItemGroup>
    <Protobuf Include="Protos\collectionusers.proto" GrpcServices="Server"></Protobuf>
</ItemGroup>
```

Il faut indiquer `GrpcServices="Server"` pour la partie serveur, et quand c’est la partie cliente il faut mettre ???? … Oui : Client. Non je ne me moque pas. Le code généré n’est pas le même c’est tout.  

Maintenant examinons ça de plus près !  
![](https://media.giphy.com/media/Gpf8A8aX2uWAg/giphy-downsized-large.gif)
* `syntax = "proto3";`  
Indique la version de la syntax du fichier proto. La version actuelle est la 3. [Voir la doc](https://developers.google.com/protocol-buffers/docs/proto3).  
* `package collectionusers;`  
On peut ajouter un spécificateur de package, c’est facultatif à un fichier .proto. C’est pour éviter les conflits de noms entre les types de messages de protocole. [Voir la doc](https://developers.google.com/protocol-buffers/docs/proto3#packages).  
* `option csharp_namespace = "ServerGrpc";`  
Comme indiqué c’est une option, ça permet de donner un nom de namespace choisi pour la génération du code des messages, services,… je vais y revenir. Là j’*override* ce qui est mis dans *package*.  
* `service Utilisateurs`  
Déclaration d’un service qui va s’appeler Utilisateurs. Il contiendra les différentes méthodes que le service offrira via les **rpc**.  
Voir [la doc](https://developers.google.com/protocol-buffers/docs/proto3#services) pour les services.  

### rpc

Là ça devient intéressant. Comme dit plus haut, dans le service il faut déclarer les méthodes, et c’est avec le mot clé `rpc`. Je vais prendre un exemple :  
`rpc GetUserById (UserIdRequest) returns (UserResponse);`  
`GetUserById` : Nom de la méthode et il prend en paramètre un **message** `UserIdRequest`, et cette méthode retourne (`returns`) un autre **message** `UserResponse`.  

### message

Pour le C#, les messages sont l’objet principal de transfert de données dans Protobuf. Elles sont conceptuellement similaires aux classes .NET. Je vais prendre l’exemple avec :  
`message UserResponse {`  
`     string id = 1;`  
`     int32 index = 2;`  
`     ....`  
`    repeated string tags = 19;`  
`    repeated Friends friends = 20;`  
`     .... }`   

A l’intérieur du message, il faut déclarer les propriétés ET son « ordre de passage ». Comme vous pouvez le voir, chaque champ de la définition du message a un numéro unique. Ces numéros sont utilisés pour identifier les champs dans le format binaire du message et ne doivent pas être modifiés une fois que le type de message est utilisé.   
**Mode Advanced** : Notez que les numéros de champ compris entre 1 et 15 prennent un octet à coder, y compris le numéro de champ et le type de champ (vous pouvez en savoir plus à ce sujet dans Protocol Buffer Encoding). Les numéros de champ compris entre 16 et 2047 prennent deux octets. Vous devez donc réserver les numéros 1 à 15 pour les éléments de message très fréquents. N’oubliez pas de laisser de la place aux éléments fréquents qui pourraient être ajoutés à l’avenir. Voir la doc pour plus d’info sur [la structure du message](https://developers.google.com/protocol-buffers/docs/encoding#structure).  

**Attention** : Le code est généré à la compilation du projet ! Donc avoir un projet compilable, quand vous ajoutez un service, une méthode ou un message.  
![Non rien à voir, ça ne sent pas le vécu…](https://media.giphy.com/media/3o7btUg31OCi0NXdkY/giphy.gif)

#### Les types pour les propriétés

Comme protobuf a son « langage », il a sa manière de déclarer ses types. Dans l’exemple, j’ai fait exprès de choisir des types simples (string, int32,…) et des types plus complexes. Pour les types simples je vous laisse aller voir [la doc Microsoft](https://docs.microsoft.com/fr-fr/aspnet/core/grpc/protobuf?view=aspnetcore-6.0#scalar-value-types) pour avoir la correspondance entre les types protobuf et les types .Net. Il y a aussi la doc google pour [tous les langages](https://developers.google.com/protocol-buffers/docs/proto3#scalar).   
Quand je dis type « complexe », c’est par exemple un tableau de string comme pour la propriété Tags. Pour déclarer un tableau/List, il faut utiliser le mot clé `repeated`.   
Ensuite dans `User`, j’ai une propriété `Friend[]`. Pour les tableaux, il faut `repeated`, mais `Friend` n’est pas un type simple. Pas grave, il faut créer un nouveau **message**.  
```generic
message Friends {
    int32 id = 1;
    string name = 2;
}
```

Même chose, définir les propriétés du message et l’ordre.  
Pour infos : il est possible de déclarer un message dans un message, des types imbriqués. [doc Google Nested type](https://developers.google.com/protocol-buffers/docs/proto3#nested).  
Pour une énumération voir la [doc Google](https://developers.google.com/protocol-buffers/docs/proto3#enum).  
## Implémentation du service gRPC

Une fois les services **rpc** déclarés, tous les **message** (request et response) déclarés et définis, il ne faut surtout pas oublier de compiler le projet, pour que les outils (Grpc.Tools) puissent générer le code.   
Pour info: le code généré se trouve dans `...\ServerGrpc\obj\Debug\net6.0\Protos`  
Dans le répertoire Service, il faut ajouter une nouvelle `class` qui sera notre service qui va répondre aux différentes demandes. En voici le code.  

```csharp
using Grpc.Core;

namespace ServerGrpc.Services;

public class UtilisateursService : Utilisateurs.UtilisateursBase
{

    private IDataService dataSvc;

    public UtilisateursService(IDataService dataService)
    {
        dataSvc = dataService;
    }

    public override Task<CountUser> GetCountUser(Empty request, ServerCallContext context)
    {
        int nombre = dataSvc.GetCountUser();
        return Task.FromResult(new CountUser() { NombreUser = nombre});
    }

    public override Task<UserResponse> GetUserById(UserIdRequest request, ServerCallContext context)
    {
        // Récupération de l'utilisateur.
        User user = dataSvc.GetUser(request.IdUser);
        return Task.FromResult(CreateResponse(user));
    }

    public override Task<AllUsersResponse> GetAll(Empty request, ServerCallContext context)
    {
        var allUsers = dataSvc.GetAllUser();

        AllUsersResponse response = new AllUsersResponse();
        foreach (var user in allUsers)
        {
            response.AllUsers.Add(CreateResponse(user));
        }

        return Task.FromResult(response);
    }


    private UserResponse CreateResponse(User user)
    {
        // Création de UserSelected (correspondant à notre "message" gRPC)
        UserResponse userReponse = new UserResponse()
        {
            About = user.about,
            Address = user.address,
            Age = user.age,
            Balance = user.balance,
            Company = user.company,
            Email = user.email,
            EyeColor = user.eyeColor,
            FavoriteFruit = user.favoriteFruit,
            Gender = user.gender,
            Greeting = user.greeting,
            Guid = user.guid,
            Id = user._id,
            Index = user.index,
            IsActive = user.isActive,
            Latitude = user.latitude,
            Longitude = user.longitude,
            Name = user.name,
            Phone = user.phone,
            Picture = user.picture,
            Registered = user.registered,
        };

        // Pour les listes de string.
        foreach (var tag in user.tags)
        {
            userReponse.Tags.Add(tag);
        }

        // Pour les listes d'un autre objet.
        foreach (var amis in user.friends)
        {
            userReponse.Friends.Add(new Friends() { Id = amis.id, Name = amis.name });
        }

        return userReponse;
    }
}
```

Choses importantes :  
`public class UtilisateursService : Utilisateurs.UtilisateursBase`  
Il faut que notre class de service implémente la class généré du service proto. Le nom `Utilisateurs` correspond au nom du service proto : `service Utilisateurs`.  
Nous retrouvons nos 3 méthodes correspondant à nos 3 déclarations **rpc**.  
<code>public override Task GetCountUser(Empty request, ServerCallContext context)  
public override Task GetUserById(UserIdRequest request, ServerCallContext context)  
public override Task GetAll(Empty request, ServerCallContext context)</code>  
Et enfin nous retrouvons tous les types de nos **message**s.  
`UserResponse`, `AllUsersResponse`, `UserIdRequest` et `Empty`.  

Il reste maintenant à configurer le serveur pour qu’il accepte des requêtes gRPC.   
Dans Program.cs il faut ajouter le middleware de Grpc.   
```csharp
// Ajout du service Grpc
builder.Services.AddGrpc(options =>
{
    // Pour l'envoie de message sans limite de taille
    options.MaxSendMessageSize = null;
});
```

Il est possible de configurer le service gRPC. Pour cet exemple j’ai mis l’option `MaxSendMessageSize` à `null`.   
*Taille maximale du message, en octets, qui peut être envoyée à partir du serveur. Toute tentative d’envoi d’un message qui dépasse la taille de message maximale configurée entraîne une exception. Lorsque la valeur est `null`, la taille du message est illimitée*.  
Voir la [Doc Microsoft](https://docs.microsoft.com/fr-fr/aspnet/core/grpc/configuration?view=aspnetcore-6.0) pour avoir toutes les options.  
Ensuite je veux configurer le serveur pour qu’il utilise le Http3 ([HTTP/3 support in .NET 6](https://devblogs.microsoft.com/dotnet/http-3-support-in-dotnet-6/))  
```csharp
string pathCertifFile = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "Certif", "tutogrpc.pfx");
builder.WebHost.ConfigureKestrel((context, options) =>
{
    options.Listen(IPAddress.Any, 5001, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2AndHttp3;
        listenOptions.UseHttps(pathCertifFile, "PassCtrlAltSuppr");
    });
});

[...]

app.MapGrpcService<UtilisateursService>();
```

Je configure le serveur en « dur » pour qu’il écoute sur le port 5001, et qu’il utilise le protocole `Http1AndHttp2AndHttp3`.  
Pour utiliser le HTTP3, il faut que la connexion soit sécurisée. J’ai exporté le certificat de développement. (Voir la fin de l’article,  ***Note pour certificat***, pour ajouter le certificat à votre machine, sinon Exception… (⌣̩̩́_⌣̩̩̀)  ).  

Pour voir la différence entre gRPC et un appel « classique » à un `controller`, j’ai créé ce controller.  

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace ServerGrpc.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class TutoGrpcUsersController : ControllerBase
    {
        private IDataService DataService;

        public TutoGrpcUsersController(IDataService service)
        {
            DataService = service;
        }

        [HttpGet("count")]
        public int GetCountUser()
        {
            // requête :
            // https://localhost:5001/api/tutogrpcusers/count
            return DataService.GetCountUser();
        }

        [HttpGet("all")]
        public IEnumerable<User> GetUsers()
        {
            // requête :
            // https://localhost:5001/api/tutogrpcusers/all
            return DataService.GetAllUser();
        }

        [HttpGet("user")]
        public User GetUser(string id)
        {
            // requête :
            // https://localhost:5001/api/tutogrpcusers/user?id=61b6c9ceb0b039f7477bedef
            return DataService.GetUser(id);
        }
    }
}
```

Enfin je mets toute la configuration de `Program.cs` (gRPC + controller).  
```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
using ServerGrpc.Services;
using System.Net;

var builder = WebApplication.CreateBuilder(args);

// Ajout du service Grpc
builder.Services.AddGrpc(options =>
{
    // Pour l'envoie de message sans limite de taille
    options.MaxSendMessageSize = null;
});
string pathCertifFile = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "Certif", "tutogrpc.pfx");
builder.WebHost.ConfigureKestrel((context, options) =>
{
    options.Listen(IPAddress.Any, 5001, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2AndHttp3;
        listenOptions.UseHttps(pathCertifFile, "PassCtrlAltSuppr");
    });
});

builder.Services.AddSingleton<IDataService, DataService="">();

builder.Services.AddControllers();

var app = builder.Build();
app.UseRouting();

app.MapGrpcService<UtilisateursService>();
app.MapGet("/", () => "C'est pour l'article sur gRPC sur www.ctrl-alt-suppr.dev");

// Pour le controler
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});

app.Run();</UtilisateursService></IDataService,>
```

### Partie Client

Ajout d’une application cliente pour utiliser notre service gRPC.  
A partir du répertoire de base, là où se trouve le fichier `sln`, faire :  
`dotnet new console -o ConsoleClient`  
Crée un nouveau projet Console. L’ajouter dans le sln.  
`dotnet sln add .\ConsoleClient`  
Ajout des packages gRPC dans notre client.  
<code>dotnet add .\ConsoleClient package Google.Protobuf  
dotnet add .\ConsoleClient package Grpc.Net.Client  
dotnet add .\ConsoleClient package Grpc.Tools</code>  
Ce n’est pas obligé d’être dans le répertoire du projet pour ajouter des packages.  
Pour que le client puisse se connecter au service il faut qu’il connaisse aussi le fichier `proto`.   
**Note** : Soit faire une copie du fichier proto du projet serveur dans le projet client, ce qui peut engendrer des problèmes s’il y a une différence entre les 2 versions. Sinon faire un projet que les 2 projets auront en référence.   
Dans l’exemple j’ai copié le fichier proto.  
![](https://media.giphy.com/media/nGX0uxigecYr6/giphy.gif)

J’ai fait ça c’est pour changer une ligne : `option csharp_namespace = "ClientGrpc";` C’était pour montrer que le namespace est bien pris en compte lors de la génération de code.  
Une fois le fichier proto copié, il faut ajouter la ligne dans le `csproj` en le déclarant comme `GrpcServices="Client"`.  

```xml
<ItemGroup>
	<Protobuf Include="Protos\collectionusers.proto" GrpcServices="Client"></Protobuf>
</ItemGroup>
```

Compiler la solution, pour que le code client soit généré.  
Ajoutons le code pour se connecter au service.  
```csharp
using ClientGrpc;  // ---> namespace du fichier proto ;) 

var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Utilisateurs.UtilisateursClient(channel);
```

Là c’est l’utilisation « simple », mais il est possible de forcer notre channel pour utiliser une connexion en HTTP3. Il faut que le serveur et le client soit compatible. Voici le code.  
```csharp
var http3Client = new HttpClient();
http3Client.DefaultRequestVersion = HttpVersion.Version30;
http3Client.DefaultVersionPolicy = HttpVersionPolicy.RequestVersionExact;

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    MaxReceiveMessageSize = null,
    HttpClient = http3Client
});
var client = new Utilisateurs.UtilisateursClient(channel);
```

J’ai ajouté aussi une option pour que les messages reçus n’ont pas de limite de taille.  
`MaxReceiveMessageSize = null,`  
Il est possible d’ajouter des configurations côté client. [Doc Microsoft](https://docs.microsoft.com/en-us/aspnet/core/grpc/configuration?view=aspnetcore-6.0#configure-client-options).  
![Différence de temps entre gRPC et un controller](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-04-RecupHttp3.png)

**Note** : pour la différence entre les 2 temps, j’ai ajouté dans le temps de « récupération » via le controller le fait de convertir la chaine de string (json) en objet.  
```csharp
HttpClient httpClient = new HttpClient();
HttpResponseMessage resultController = await httpClient.GetAsync(BASE_ADDRESS + "/api/tutogrpcusers/all");
using (Stream reponse = await resultController.Content.ReadAsStreamAsync())
{
    var tousLesUtilisateurs = JsonSerializer.DeserializeAsync< /><User>>(reponse);
    $"Il y a {tousLesUtilisateurs.Result.Count} utilisateurs".ToConsoleResult();
}
```

Je l’ai ajouté car avec gGRPC une fois la réception terminée, nous travaillons déjà en objet.  
`AllUsersResponse allClients = await client.GetAllAsync(empty);`  

## Pour conclure

C’est un poste de découverte, c’était histoire de dégrossir/démystifier se framework.   
## Note pour certificat

Pour que la machine ne lève pas d’exception entre votre machine et le serveur, il faut importer le certificat que j’ai exporté ([Self-Signed Certificate](https://docs.microsoft.com/en-us/dotnet/core/additional-tools/self-signed-certificates-guide#with-dotnet-dev-certs)). C’est un certificat pour « localhost », et vous avez surement déjà vos certificats, du coup il y aura un « désaccord » entre celui fourni dans l’application et ceux de votre machine.  
Clic droit sur le **.pfx**.  
  
![ Installer PFX.](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-05-Certif01.png)  
  
![Choisir : Ordinateur local.](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-05-Certif02.png)  
  
![Récap du fichier](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-05-Certif03.png)  
  
![Indiquer le mot de passe, comme affiché.](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-05-Certif04.png)  
  
![Choisir cette option.](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-05-Certif05.png)  
  
![Placer dans « Autorités de certification racines de confiance »](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-05-Certif06.png)  
  
![Certificat ajouté.](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/gRPC/Grpc-05-Certif07.png)  
