L’application est sur le [Windows Store](https://www.microsoft.com/fr-fr/p/windows-terminal/9n0dx20hk701). Je ne pourrais pas mieux expliquer ce que fait Windows Terminal que la doc Microsoft – [Windows Terminal Doc MS](https://docs.microsoft.com/fr-fr/windows/terminal/).  
Personnellement je l’utilisais de façon « simple ». J’ose faire ce post à la suite de l’article de Monsieur Scott Hanselman :  
[My Ultimate PowerShell prompt with Oh My Posh and the Windows Terminal](https://www.hanselman.com/blog/my-ultimate-powershell-prompt-with-oh-my-posh-and-the-windows-terminal)  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-08.png)
Mr Scott Hanselman  
![](https://media.giphy.com/media/3o7TKqFZJpwL8Giz2E/giphy.gif)

Je vais reprendre ce qu’il indique en ajoutant des explications pour avoir un Windows Terminal qui soit un peu plus « bling bling ».  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-01.png)
## Installer PowerShell Core

L’installation n’est pas obligatoire, mais super utile car tout ce qui sera fait ici sera « visible » dans Visual Studio Code.   
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-09.png)  

En plus cette version est installable aussi sur Linux.  
```csharp
winget install Microsoft.PowerShell
```

Une fois installé, il faut redémarrer Windows Terminal. Dans les paramètres, il y aura « PowerShell ».  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-02.png)  
Nous voyons bien la différence entre « Windows PowerShell » et « PowerShell » Core.  
## Installation de OH-MY-POSH

C’est avec l’application « [Oh My Posh](https://ohmyposh.dev/) » qui rend Windows Terminal plus sympa.   
```powershell
# Avec l'invite de commande PowerShell
winget install JanDeDobbeleer.OhMyPosh
```

Une fois que s’est installé, il faut indiquer à PowerShell qu’à chaque démarrage il importe ce module. Pour cela il faut créer/modifier le `$PROFILE`. Si vous avez Visual Studio Code, il faut faire :  
```powershell
 code $PROFILE

#sinon sans VSCode
notepad $PROFILE
```

`$PROFILE` indique le chemin d’un fichier powershell qui est chargé au lancement de PowerShell. C’est dans ce fichier que nous pouvons ajouter des fonctionnalités.   
**Attention** : le fichier est lié au profil de l’utilisateur **ET** à la version Powershell utilisé (Windows ou Core).  

Soit ça va modifier votre fichier `Microsoft.PowerShell_profile.ps1` existant ou vous le créer.  
Il faut ajouter ces lignes pour [importer le module](https://docs.microsoft.com/fr-fr/powershell/scripting/developer/module/importing-a-powershell-module?view=powershell-7.2) `oh-my-posh` :  
```powershell
Import-Module oh-my-posh
oh-my-posh --init --shell pwsh --config ~/jandedobbeleer.omp.json | Invoke-Expression
```

Redémarrer Windows Terminal. Vous risquez comme moi d’avoir une erreur :   
`PowerShell ps1 is not digitally signed. You cannot run this script`  
Voir ce post [PowerShell ps1 is not digitally signed.](http://mvsourcecode.com/powershell-ps1-is-not-digitally-signed-you-cannot-run-this-script/) pour avoir l’explication.  
Perso j’ai mis en `Unrestricted` pour voir si tout fonctionne.  
```powershell
# A lancer en tant qu'administrateur
Set-ExecutionPolicy -Scope:CurrentUser -ExecutionPolicy:Unrestricted
```
## Changer de thème

Dans OH MY POSH il y a plusieurs thèmes proposés ([liste des thèmes](https://ohmyposh.dev/docs/themes)).   
En exécutant la commande `Get-PoshThemes`, OMP (*Oh My Posh*) va afficher tous les thèmes par rapport à l’endroit ou vous êtes dans l’arborescence.  
Je vais prendre l’exemple du thème [Craver](https://github.com/JanDeDobbeleer/oh-my-posh/blob/main/themes/craver.omp.json). Le lien ouvre un GitHub du thème. Il faut copier le json et le mettre dans un fichier en local. Comme expliqué par Mr Hanselmann, le mieux est de mettre se fichier dans OneDrive/DropBox, pour que ce soit synchronisé entre vos différentes machines.  
Il faut modifier le fichier de profile.   
`code $PROFILE`   
Indiquer ou se trouve le json de configuration du thème. Pour mon cas, voici ma ligne :  
```powershell
oh-my-posh.exe --init --shell pwsh --config C:\Users\antho\OneDrive\Documents\OhMyPoshTheme\ThemeCraver.omp.json | Invoke-Expression
```
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-04.png)  
C’est moche…  

Vous devriez avoir un truc comme ça… il ne reconnait pas les caractères spéciaux. Il faut installer une Font : [CasKaydiaCode Nerd](https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/CascadiaCode.zip?WT.mc_id=-blog-scottha), et l’installer sur votre poste.  
Une fois installer, redémarrer Windows Terminal, aller dans « Paramètres », sélectionner Profils « PowerShell » et dans l’onglet Apparence, sélectionner `CaskaydiaCove Nerd Font`, **ET** ne pas oublier de faire « enregistrer » en bas à droit.   
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-05.png)  
Tout de suite, l’affichage est mieux.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-06.png)  
Bling Bling !  

## Ajouter des icônes « cool »
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-03-1024x764.png)  

Pour avoir les icônes devant les répertoires, fichiers, il faut utiliser `Terminal-Icons` qui est un projet sur [GitHub](http://Terminal-Icons). Voici la commande pour installer le module (toujours sous PowerShell).  
```powershell
Install-Module -Name Terminal-Icons -Repository PSGallery
```

Il faut l’ajouter dans le `$PROFILE`.  
```powershell
Import-Module -Name Terminal-Icons
```
## Ajouter un autocomplete predictive

Je vous laisse aller voir le post du *Maitre* pour ajouter `PSReadLine`.   
[You should be customizing your PowerShell Prompt with PSReadLine](https://www.hanselman.com/blog/you-should-be-customizing-your-powershell-prompt-with-psreadline)  
![](https://media.giphy.com/media/aLTFHi2aowjJe/giphy.gif)

Voici mon fichier complet de mon profil powershell.  
```powershell
# Pour charger Oh-My-Posh avec Thème particulier.
oh-my-posh.exe --init --shell pwsh --config C:\Users\antho\OneDrive\Documents\OhMyPoshTheme\ThemeCraver.omp.json | Invoke-Expression
# -- Theme par défaut.
#oh-my-posh.exe --init --shell pwsh --config ~/jandedobbeleer.omp.json | Invoke-Expression
## -- Pour montrer comment avoir d'autre Theme.
#oh-my-posh.exe --init --shell pwsh --config C:\Users\antho\OneDrive\Documents\OhMyPoshTheme\OhMyPoshTheme.omp.json | Invoke-Expression

# Pour avoir des icônes plus stylés.
Import-Module -Name Terminal-Icons

# Permet de charger PSReadLine, c'est pour "l'IntelliSense"
if($host.Name -eq 'ConsoleHost')
{
    Import-Module PSReadLine
    Set-PSReadLineOption -PredictionSource History
    # C'est dans une version  PREVIEW...
    #Set-PSReadLineOption -PredictionViewStyle ListView
    Set-PSReadLineOption -EditMode Windows

    Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward
    Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward
}
```

Et voici le fichier powershell du profil de Monsieur Hanselman sur [Gist](https://gist.github.com/shanselman/25f5550ad186189e0e68916c6d7f44c3?WT.mc_id=-blog-scottha) … quasiment 700 lignes….  
![](https://media.giphy.com/media/iiS84hOJXh1Pq/giphy.gif)

## Windows Terminal et WLS

Il est possible d’installer OhMyGosh sur les distributions Linux WSL. Pour l’exemple j’ai pris l’image Debian sur le [Windows Store](https://www.microsoft.com/fr-fr/p/debian/9msvkqc78pk6). Une fois installé, redémarré Windows Terminal, et ooohh magic.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Tips%26Tricks/WindowsTerminal/WinTerminal-07.png" alt=""/></figure>
Installons OMP sur notre Debian WSL.  
```generic
# Met à jour le référenciel
sudo apt-get update

# Installation wget si pas encore fait
sudo apt-get install wget

# Installation de OhMyPosh
sudo wget https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-linux-amd64 -O /usr/local/bin/oh-my-posh

# Ajout du droit d'exécution
sudo chmod +x /usr/local/bin/oh-my-posh
```

Une fois que tout est installé, il faut modifier le fichier de configuration de notre bash, pour qu’il prenne en compte OMP.  
```generic
sudo nano ~/.bashrc
```

Ajouter cette ligne. Je met en exemple le chemin de mon fichier de thème.  
```generic
eval "$(oh-my-posh --init --shell bash --config /mnt/c/Users/antho/OneDrive/Documents/OhMyPoshTheme/OhMyPoshTheme.omp.json)"
```

Recharger le bash pour prise d’effet.  
```generic
# Pour recharger le bash
. ~/.bashrc
```

C’est tout moche, il faut faire comme dit plus haut, aller dans les paramètres du profil de la « console » Debian, dans Apparence, et changer la police de caractère pour `CaskaydiaCove Nerd Font`.  

Pour info : j’ai fait cette installation sur mes serveurs PHYSIQUES Debian, et ça fonctionne ! Ce n’est pas juste réservé à des machines virtuelles. Bon tuning à vous.
