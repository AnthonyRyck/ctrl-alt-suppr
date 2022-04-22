Si vous devez stocker des fichiers sur le serveur, et qu’ils doivent être accessible pour les clients, l’exemple le plus simple : des images, mais que vous ne voulez pas TOUT stocker dans `/www/root`, il est possible d’indiquer à l’application d’autres répertoires. Pour cela il faut aller dans `Startup.cs` dans la méthode `public void Configure(...)`   
```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env, IServiceProvider serviceProvider)
{
	// Reste du code...
	
	string pathMyStaticFiles = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "MyStaticFiles");
	if (!Directory.Exists(pathMyStaticFiles))
		Directory.CreateDirectory(pathMyStaticFiles);

	app.UseStaticFiles(new StaticFileOptions
	{
		FileProvider = new PhysicalFileProvider(pathMyStaticFiles),
		RequestPath = "/MyStaticFiles"
	});

	string pathCache = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "Cache");
	if (!Directory.Exists(pathCache))
		Directory.CreateDirectory(pathCache);

	app.UseStaticFiles(new StaticFileOptions
	{
		FileProvider = new PhysicalFileProvider(pathCache),
		RequestPath = "/Cache"
	});

	// Reste du code...
}
```

Dans ce bout de code, je crée 2 répertoires : `MyStaticFiles` et `Cache` et active l’utilisation de fichiers statiques avec les options spécifiées avec `app.UseStaticFiles`. Les options sont données avec [StaticFileOptions](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions?view=aspnetcore-5.0).  
Avec [PhysicalFileProvider](https://docs.microsoft.com/fr-fr/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-5.0), je donne le chemin ou se trouve le répertoire.   
Pour `RequestPath`, cela correspond à qu’elle requête il faut faire pour accéder à un fichier, par exemple pour `MyStaticFiles` il faut faire :   
`http://votredomaine.fr/MyStaticFiles/nomDuFichier.png`
