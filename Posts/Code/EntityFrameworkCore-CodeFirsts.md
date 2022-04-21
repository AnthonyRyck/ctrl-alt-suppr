Dans mon premier post sur EF Core, j’ai montré comment installer EF Core en ligne de commande et faire du [Database First](https://www.ctrl-alt-suppr.dev/2022/01/21/entity-framework-core-database-first/) (*générer du code à partir d’une base de données*). Dans ce post, je vais montrer comment générer une base de données à partir du code, c’est le **Code First**. Le code source du projet d’exemple est sur [GitHub](https://github.com/AnthonyRyck/CodesPourDevTo/tree/master/src/dotNet6/TutoEfCoreCodeFirst).  

## Préparation

Je vais prendre un exemple simple de clients qui ont passés des commandes. Voilà à quoi la base doit ressembler :  
![Image créée à partir de MySQL, ou les tables sont créées par EF Core… vous allez voir plus tard.](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/EfCoreCodeFirst/01-EfCoreCodeFirst-Schema.png)

Création du projet.  
```shell
mkdir TutoEfCoreCodeFirst
cd  TutoEfCoreCodeFirst
dotnet new sln   # Ajout du fichier sln
mkdir WebApiCodeFirst  # répertoire pour le projet
cd WebApiCodeFirst  
dotnet new webapi --no-https --use-minimal-apis
cd ..
dotnet sln add .\WebApiCodeFirst\ # ajout du projet dans le sln
```

La commande nous crée notre projet Web API avec le minimum vital.  
```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

var summaries = new[]
{
    "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
};

app.MapGet("/weatherforecast", () =>
{
    var forecast =  Enumerable.Range(1, 5).Select(index =>
        new WeatherForecast
        (
            DateTime.Now.AddDays(index),
            Random.Shared.Next(-20, 55),
            summaries[Random.Shared.Next(summaries.Length)]
        ))
        .ToArray();
    return forecast;
})
.WithName("GetWeatherForecast");

app.Run();

record WeatherForecast(DateTime Date, int TemperatureC, string? Summary)
{
    public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
}
```

J’enlève tout ce qui est inutile pour le post et il nous reste :  
```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.Run();
```

Pour que le code crée les tables dans une base de données, il faut une base. Pour l’exemple je vais prendre SQLite. Ajoutons le nécessaire au projet.  
```generic
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```
```generic
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```
### Création des models

Maintenant il faut créer nos modèles. Avant de les créer je vais ajouter un fichier `GlobalUsings.cs` (au cas vous ne connaissez pas, un bon post pour expliquer [global using](https://www.thomasclaudiushuber.com/2021/09/30/c-10-global-using-directives/))  
J’ajouterai d’autres using.  
```csharp
global using System.ComponentModel.DataAnnotations;
global using System.ComponentModel.DataAnnotations.Schema;
```

Les modèles liés au « Client ».  
```csharp
namespace WebApiCodeFirst.Models
{
    public class Client
    {
        [Key]
        public string Id { get; set; }

        [Required(ErrorMessage = "Un prénom est obligatoire")]
        public string Prenom { get; set; }

        [Required(ErrorMessage = "Un nom est obligatoire")]
        public string Nom { get; set; }

        [Required(ErrorMessage = "Un age est obligatoire")]
        public int Age { get; set; }

        public Adresse Adresse { get; set; }

        public ICollection<Telephone> Telephones { get; set; }

        public ICollection<Commande> Commandes { get; set; }
    }
}
```
```csharp
namespace WebApiCodeFirst.Models
{
    public class Adresse
    {
        [Key]
        public string Id { get; set; }

        public string Numero { get; set; }
        public string Rue { get; set; }
        public string Ville { get; set; }

        [ForeignKey("ClientId")]
        public string ClientId { get; set; }
    }
}
```
```csharp
namespace WebApiCodeFirst.Models
{
    public class Telephone
    {
        [Key]
        public string Number { get; set; }
        
        public string Type { get; set; }

        [ForeignKey("ClientId")]
        public string ClientId { get; set; }
    }
}
```

Et les modèles liés à une « Commande ».  
```csharp
namespace WebApiCodeFirst.Models
{
    public class Commande
    {
        [Key]
        public string IdCmd { get; set; }

        [ForeignKey("ClientId")]
        public string ClientId { get; set; }

        public ICollection<Panier> Panier { get; set; }
    }
}
```
```csharp
namespace WebApiCodeFirst.Models
{
    public class Panier
    {
        [Key]
        public Guid IdPanier { get; set; }

        public int IdItem { get; set; }
        public int Quantite { get; set; }

        [ForeignKey("CommandeIdCmd")]
        public string CommandeIdCmd { get; set; }
    }
}
```
### Comment EF Core gère les colonnes et fait les relations

Nous pouvons voir qu’il y a des attributs qui sont mis sur certaines propriétés :  
`Key` ([doc sur MS](https://docs.microsoft.com/fr-fr/dotnet/api/system.componentmodel.dataannotations.keyattribute?view=net-6.0)) : Indique la clé primaire, « *une entité de manière unique*« .  
`ForeignKey` ([doc sur MS](https://docs.microsoft.com/fr-fr/dotnet/api/system.componentmodel.dataannotations.schema.foreignkeyattribute?view=net-6.0)) : Indique la clé étrangère.  
`Required` : Indique que ce sera une valeur NON NULL en base.  
J’aurai pu ajouter aussi l’attribut :   
`[StringLength(50, ErrorMessage = "Le nom est trop long, 50 caractères max")]`  
pour spécifier à la base une longueur max de caractère pour cette colonne.  
Pour avoir une liste des attributs possible sur [entityframeworktutorial.net](https://www.entityframeworktutorial.net/code-first/dataannotation-in-code-first.aspx), [entityframework.net](https://entityframework.net/data-annotations) et bien sûr la référence [docs.microsoft.com](https://docs.microsoft.com/fr-fr/ef/core/modeling/entity-properties?tabs=data-annotations%2Cwithout-nrt).  

Avec les attributs, nous pouvons indiquer comment les colonnes seront gérées (primary key, not null, …), mais comment indiquer les relations entre les tables. Prenons l’exemple entre l’entité Client et Telephone.  
Telephone indique qu’il a une propriété de clé étrangère qui s’appelle `ClientId`.  
```csharp
[ForeignKey("ClientId")]
public string ClientId { get; set; }
```

En mettant juste ça, il n’y aura pas de lien entre les tables, il faut aussi indiquer à l’entité `Client` une propriété :  
```csharp
public ICollection<Telephone> Telephones { get; set; }</Telephone>
```

Attention : il y a une convention d’écriture entre les propriétés pour que EF puisse faire la bonne liaison entre les tables, sinon il peut soit prendre la mauvaise propriété, soit carrément créer une nouvelle colonne pour faire la FK. Le mieux est de mettre les mêmes noms de propriété dans chaque entité.  
Nous avons tous les modèles, maintenant il faut indiquer à l’application comment construire la base de données.  
## Création du Context

Il n’y a pas besoin d’installer le package `Microsoft.EntityFrameworkCore`, car `Microsoft.EntityFrameworkCore.Sqlite` a comme dépendance :  
`Microsoft.EntityFrameworkCore.Sqlite.Core` qui lui a comme dépendance :  
`Microsoft.EntityFrameworkCore.Relational` qui dépend de :  
`Microsoft.EntityFrameworkCore`.  
![](https://media.giphy.com/media/nthoYgQ91Up2u7qmcE/giphy.gif)

Ajouter un répertoire `Data` et la `class ApplicationDbContext`  
```csharp
using Microsoft.EntityFrameworkCore;

namespace WebApiCodeFirst.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions options) 
            : base(options)
        {
        }
        
        public DbSet<Telephone> Telephones { get; set; }
        public DbSet<Adresse> Adresses { get; set; }
        public DbSet<Client> Clients { get; set; }
        public DbSet<Panier> Paniers { get; set; }
        public DbSet<Commande> Commandes { get; set; }
    }
}
```

Définition de `DbContext` sur la [doc MS](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.entityframeworkcore.dbcontext?view=efcore-6.0), juste en prenant les premiers mots : « *Une instance DbContext représente une session avec la base de données et peut être utilisée pour interroger et enregistrer des instances de vos entités*«   
Définition du type `DbSet` sur la [doc MS](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.entityframeworkcore.dbset-1?view=efcore-6.0). En gros ça indique que l’entité passé dans le DbSet sera une table en base de données, et que les requêtes LinQ faites seront traduites en SQL.  

Il ne reste plus qu’à ajouter dans `Program.cs`, le code pour faire la connexion, ligne très importante, je reviens un peu plus bas sur cette « *connexion*« .  
```csharp
// Add Application Db Context options
builder.Services.AddDbContext<ApplicationDbContext>(options =>
			options.UseSqlite(@"Data Source=TutoCodeFirst.db"));
```
## Génération de la base

Pour que la base de données soit généré par le code, il faut ajouter un autre package : `Microsoft.EntityFrameworkCore.Tools`  
```generic
dotnet add package Microsoft.EntityFrameworkCore.Tools
```
```generic
Install-Package Microsoft.EntityFrameworkCore.Tools
```

C’est avec ce package que nous pourrons générer le code pour créer notre base de données.  
Commande pour générer le code.  
```generic
dotnet ef migrations add InitialCreate
```
```generic
Add-Migration InitialCreate
```

Une fois la compilation terminée, il y a dans le projet un nouveau répertoire : `Migrations`.   
A l’intérieur se trouve une `class InitialCreate`, *tient j’ai déjà vu ça quelque part,* qui hérite de [Migration](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.entityframeworkcore.migrations.migration?view=efcore-6.0). Elle contient 2 méthodes :  
`protected override void Up(MigrationBuilder migrationBuilder) {...}`  voir la doc [Migration.Up](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.entityframeworkcore.migrations.migration.up?view=efcore-6.0#microsoft-entityframeworkcore-migrations-migration-up(microsoft-entityframeworkcore-migrations-migrationbuilder))  
`protected override void Down(MigrationBuilder migrationBuilder) {...}` voir la doc [Migration.Down](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.entityframeworkcore.migrations.migration.down?view=efcore-6.0#microsoft-entityframeworkcore-migrations-migration-down(microsoft-entityframeworkcore-migrations-migrationbuilder))  
Il y a aussi la class ApplicationDbContextModelSnapshot, qui hérite de ModelSnapshot (par [ici la doc](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.entityframeworkcore.infrastructure.modelsnapshot?view=efcore-6.0))  

**Remarque**  
Le code qui est généré est fait en fonction de la « *connexion*« . Quand je parle de connexion, c’est `options.UseSqlite(...)` dans `Program.cs`. Si j’utilise une autre base de données, par exemple MariaDb/MySQL, j’utiliserai le package : `Pomelo.EntityFrameworkCore.MySql` au lieu de  `Microsoft.EntityFrameworkCore.Sqlite`, et je mettrai `options.UseMySql(...)`.  
Voici un exemple de code généré pour SQLite et pour MySQL, juste sur la table Clients.  
```csharp
 migrationBuilder.CreateTable(
				name: "Clients",
				columns: table => new
				{
					Id = table.Column<string>(type: "TEXT", nullable: false),
					Prenom = table.Column<string>(type: "TEXT", nullable: false),
					Nom = table.Column<string>(type: "TEXT", nullable: false),
					Age = table.Column<int>(type: "INTEGER", nullable: false)
				},
				constraints: table =>
				{
					table.PrimaryKey("PK_Clients", x => x.Id);
				});
```
```csharp
migrationBuilder.CreateTable(
		name: "Clients",
		columns: table => new
		{
			Id = table.Column<string>(type: "varchar(255)", nullable: false)
				.Annotation("MySql:CharSet", "utf8mb4"),
			Prenom = table.Column<string>(type: "longtext", nullable: false)
				.Annotation("MySql:CharSet", "utf8mb4"),
			Nom = table.Column<string>(type: "longtext", nullable: false)
				.Annotation("MySql:CharSet", "utf8mb4"),
			Age = table.Column<int>(type: "int", nullable: false)
		},
		constraints: table =>
		{
			table.PrimaryKey("PK_Clients", x => x.Id);
		})
		.Annotation("MySql:CharSet", "utf8mb4");
```

C’est ce qui est génial avec EF Core CodeFirst, sans changer code, nous ne dépendons plus d’une base.  
![](https://media.giphy.com/media/5bpTIc6QTsJXzcjX7o/giphy.gif)

Pour créer la base à partir du code il faut exécuter une des commandes.  
```generic
dotnet ef database update InitialCreate
# en mettant le nom donné de la migration.
```
```generic
Update-Database
```

En voici les logs.  
```generic
Build started...
Build succeeded.
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 6.0.1 initialized 'ApplicationDbContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite:6.0.1' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (73ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      PRAGMA journal_mode = 'wal';
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (38ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "__EFMigrationsHistory" (
          "MigrationId" TEXT NOT NULL CONSTRAINT "PK___EFMigrationsHistory" PRIMARY KEY,
          "ProductVersion" TEXT NOT NULL
      );
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (2ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT COUNT(*) FROM "sqlite_master" WHERE "name" = '__EFMigrationsHistory' AND "type" = 'table';
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT "MigrationId", "ProductVersion"
      FROM "__EFMigrationsHistory"
      ORDER BY "MigrationId";
info: Microsoft.EntityFrameworkCore.Migrations[20402]
      Applying migration '20220129111929_InitialCreate'.
Applying migration '20220129111929_InitialCreate'.
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Clients" (
          "Id" TEXT NOT NULL CONSTRAINT "PK_Clients" PRIMARY KEY,
          "Prenom" TEXT NOT NULL,
          "Nom" TEXT NOT NULL,
          "Age" INTEGER NOT NULL
      );
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Commandes" (
          "IdCmd" TEXT NOT NULL CONSTRAINT "PK_Commandes" PRIMARY KEY,
          "ClientId" TEXT NOT NULL
      );
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Adresses" (
          "Id" TEXT NOT NULL CONSTRAINT "PK_Adresses" PRIMARY KEY,
          "Numero" TEXT NOT NULL,
          "Rue" TEXT NOT NULL,
          "Ville" TEXT NOT NULL,
          "ClientId" TEXT NOT NULL,
          CONSTRAINT "FK_Adresses_Clients_ClientId" FOREIGN KEY ("ClientId") REFERENCES "Clients" ("Id") ON DELETE CASCADE
      );
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Telephones" (
          "Number" TEXT NOT NULL CONSTRAINT "PK_Telephones" PRIMARY KEY,
          "Type" TEXT NOT NULL,
          "ClientId" TEXT NOT NULL,
          CONSTRAINT "FK_Telephones_Clients_ClientId" FOREIGN KEY ("ClientId") REFERENCES "Clients" ("Id") ON DELETE CASCADE
      );
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Paniers" (
          "IdPanier" TEXT NOT NULL CONSTRAINT "PK_Paniers" PRIMARY KEY,
          "IdItem" INTEGER NOT NULL,
          "Quantite" INTEGER NOT NULL,
          "CommandeIdCmd" TEXT NOT NULL,
          CONSTRAINT "FK_Paniers_Commandes_CommandeIdCmd" FOREIGN KEY ("CommandeIdCmd") REFERENCES "Commandes" ("IdCmd") ON DELETE CASCADE
      );
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE UNIQUE INDEX "IX_Adresses_ClientId" ON "Adresses" ("ClientId");
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE INDEX "IX_Paniers_CommandeIdCmd" ON "Paniers" ("CommandeIdCmd");
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE INDEX "IX_Telephones_ClientId" ON "Telephones" ("ClientId");
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO "__EFMigrationsHistory" ("MigrationId", "ProductVersion")
      VALUES ('20220129111929_InitialCreate', '6.0.1');
Done.
```

La création de la base SQLite est faite à la racine du projet et se nomme : `TutoCodeFirst.db`.  
Voilà c’était pour démystifier Entity Framework Core : Code First.
