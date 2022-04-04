
# Tips & Tricks – Instruction de compilation

Lors du développement d’une application, il arrive que les équipes aient besoin d’ajouter du code, **« non métier »**, pour sortir des stats, des logs, … mais qui ne faudra surtout pas livrer en production. Imaginer avant chaque livraison, faire le tour de toute l’application pour commenter/décommenter du code. Grosse source d’erreur. C’est pour ça qu’il existe la compilation conditionnelle/instruction de compilation. La compilation conditionnelle permet d’indiquer au compilateur s’il doit ou non compiler tel ou tel bout de code.  

Dans ce court exemple, je vais utiliser les instructions de compilation en fonction de la configuration du projet. Le projet sera exécuté de 3 façons possible. Soit en DEBUG, pour le développeur, en RELEASE pour un déploiement « classique » et en RELEASEDOCKER. J’ai ajouté cette configuration de compilation (*copié à partir de RELEASE*) car je dois faire un traitement supplémentaire sur la chaine de connexion.  
DEBUG : je suis sur ma machine, avec ma base de données en local, du coup, ma connexion sera sur `127.0.0.1` et un mot de passe bidon.  
RELEASE : là il faut utiliser le fichier `appsettings.json`.  
RELEASEDOCKER : dans cette configuration je passe des paramètres à la commande `docker run` pour la chaîne de connection (voir mon post : [ConnectionString et image Docker](https://www.ctrl-alt-suppr.dev/2021/02/01/connectionstring-et-image-docker/))  
## Utilisation

Je cite la doc MS :  
>Vous utilisez quatre directives de préprocesseur pour contrôler la compilation conditionnelle :#if: Ouvre une compilation conditionnelle, où le code est compilé uniquement si le symbole spécifié est défini.#elif: Ferme la compilation conditionnelle précédente et ouvre une nouvelle compilation conditionnelle basée sur si le symbole spécifié est défini.#else: Ferme la compilation conditionnelle précédente et ouvre une nouvelle compilation conditionnelle si le symbole spécifié précédemment n’est pas défini.#endif: Ferme la compilation conditionnelle précédente.Directives de préprocesseur C# | Microsoft Docs
  
*Source : Directives de préprocesseur C# | Microsoft Docs*  

Ce qui donne pour mon exemple :  
```csharp
// Compilation en DEBUG
#if DEBUG

    string connectionDb = "server=127.0.0.1;user id=root;password=MonSuperPass;database=basedetest";

// Compilation pour Docker
#elif RELEASEDOCKER

    string connectionDb = builder.Configuration.GetConnectionString("SqlConnection");

    // *** Dans le cas ou une utilisation avec DOCKER
    string databaseAddress = Environment.GetEnvironmentVariable("DB_HOST");
    string login = Environment.GetEnvironmentVariable("LOGIN_DB");
    string mdp = Environment.GetEnvironmentVariable("PASSWORD_DB");
    string dbName = Environment.GetEnvironmentVariable("DB_NAME");

    connectionDb = connectionDb.Replace("USERNAME", login)
                            .Replace("YOURPASSWORD", mdp)
                            .Replace("YOURDB", dbName)
                            .Replace("YOURDATABASE", databaseAddress);

// Pour toutes les autres configurations
#else 

    string connectionDb = builder.Configuration.GetConnectionString("SqlConnection");

#endif
```

Et voici un exemple de `dockerfile` utilisant la compilation RELEASEDOCKER.  
```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:6.0.1-bullseye-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

ENV DB_HOST="YourAddressdatabase"
ENV LOGIN_DB="YourLogin"
ENV PASSWORD_DB="YourPassword"
ENV DB_NAME="YourDbName"

FROM mcr.microsoft.com/dotnet/sdk:6.0.101-bullseye-slim AS build
WORKDIR /src
COPY ["./src/BankDataAccess/", "BankDataAccess/"]
COPY ["./src/BlazorMyBankAccount/", "BlazorMyBankAccount/"]

RUN dotnet restore "BlazorMyBankAccount/BlazorMyBankAccount.csproj"
RUN dotnet build "BlazorMyBankAccount/BlazorMyBankAccount.csproj" -c ReleaseDocker -o /app/build

FROM build AS publish
RUN dotnet publish "BlazorMyBankAccount/BlazorMyBankAccount.csproj" -c ReleaseDocker -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "BlazorMyBankAccount.dll"]
```
## Un autre « outil » similaire

Il est possible d’utiliser un autre outil pour tracer des informations dans l’application, sans passer par des instructions de compilation, avec la `class Debug`. ([voir la doc MS](https://docs.microsoft.com/fr-fr/dotnet/api/system.diagnostics.debug?view=net-6.0)).  
C’est une class static qui est compilé **UNIQUEMENT** dans une configuration Debug. Il y a aussi la [class Trace](https://docs.microsoft.com/fr-fr/dotnet/api/system.diagnostics.trace?view=net-6.0) qui utilise le symbole TRACE. Ce symbole est présent en DEBUG et RELEASE, donc à bien configurer.  

