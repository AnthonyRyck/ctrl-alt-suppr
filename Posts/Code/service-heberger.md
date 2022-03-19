
# Service Héberger

Il est possible d’héberger des services qui tourne en arrière-plan, de long traitement, où des traitements répétitifs. Ces tâches d’arrière-plan peuvent être implémentées en tant que services hébergés. Un service hébergé est une classe avec une logique de tâche en arrière-plan qui implémente l’interface `IHostedService`.  
Dans mon exemple (FanApp), j’utilise le service avec un compte à rebours pour mettre à jour ma fausse base de données à zéro.   
## Création du service

Créons une classe `ResetHostedService`  
```csharp
public class ResetHostedService : IHostedService, IDisposable
{
	private Timer _timerReset;

	private readonly FakeAccessDatabase FakeAccess;

	#region Constructeur

	public ResetHostedService(FakeAccessDatabase fakeAccess)
	{
		FakeAccess = fakeAccess;
	}

	#endregion

	#region Implement Interfaces

	/// <summary>
	/// Lancement du service
	/// </summary>
	/// <param name="cancellationToken" />
	/// <returns></returns>
	public Task StartAsync(CancellationToken cancellationToken)
	{
		// 10 jours en millisecondes
		var tempsEnMillisecond = Convert.ToInt32(TimeSpan.FromHours(240).TotalMilliseconds);
		_timerReset = new Timer(ResetCounter, null, 0, tempsEnMillisecond);
		
		return Task.CompletedTask;
	}

	/// <summary>
	/// Arrêt du service
	/// </summary>
	/// <param name="cancellationToken" />
	/// <returns></returns>
	public Task StopAsync(CancellationToken cancellationToken)
	{
		_timerReset?.Change(Timeout.Infinite, 0);
		return Task.CompletedTask;
	}

	public void Dispose()
	{
		_timerReset?.Dispose();
	}

	#endregion

	#region Private methods


	private void ResetCounter(object state)
	{
		FakeAccess.InitCollection();
	}

	#endregion
}
```

Pour que le service soit hébergé par Asp.Net, la classe doit implémenter `IHostedService`.   
```csharp
public interface IHostedService
{
    Task StartAsync(CancellationToken cancellationToken);
    Task StopAsync(CancellationToken cancellationToken);
}
```

  
Dans la méthode « Start » de mon exemple, je paramètre le `Timer` sur 10 jours avant de lever la méthode de réinitialisation `ResetCounter`. Cette méthode permet de remettre à zéro la liste des membres des fans.  

Dans le constructeur de notre service, nous avons besoin du service de base de données. C’est le moteur d’injection de dépendance qui fournira l’instance que le service à besoin.  
```csharp
public ResetHostedService(FakeAccessDatabase fakeAccess) { .... }
```
## Hébergement du service

Pour indiquer au serveur Asp.Net qu’il faut héberger ce service, il faut le déclarer dans le `Startup.cs` comme ci-dessous. Il est possible d’héberger plusieurs services.  
```csharp
public void ConfigureServices(IServiceCollection services)
{
     [....]
	// Service pour remettre à zéro
	services.AddHostedService<ResetHostedService>();
        
        services.AddHostedService<AutreService>();
        services.AddHostedService<EncoreUnAutreService>();
}</EncoreUnAutreService></AutreService></ResetHostedService>
```