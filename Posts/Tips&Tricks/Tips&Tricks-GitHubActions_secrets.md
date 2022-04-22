Dans ce post, je vais montrer comment sauvegarder des secrets (*mot de passe, clé d’API,…*) dans GitHub pour une utilisation dans GitHub Actions, et surtout que tout ça ne soit pas écrit en clair et aux yeux de tous. De toute façon, il y a la vigilance de [GitGuard](https://www.gitguardian.com/) pour vous envoyer un mail de rappel. La doc de [GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) pour sauvegarder vos secrets.  

Les secrets sont ajoutés **PAR** référentiel, ce qui veut dire que si vous avez 5 référentiels qui ont besoin de connaitre votre clé d’API Nuget par exemple, il faudra créer un secret dans chaque référentiel.  

## Création d’un secret

Il faut sélectionner un repository, et aller sur **Settings**.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Github/GithubSecret-01-Settings.png)

Ensuite aller sur **Secrets**.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Github/GithubSecret-02-SettingsSecrets-1024x632.png)

En haut à droite, il faut cliquer sur le bouton **New repository secret**.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Github/GithubSecret-03-NewSecrets.png)

GitHub demande un nom de secret et sa valeur.  
![](https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/Github/GithubSecret-04-Secrets.png)

## Utilisation des secrets

Le nom mit à la création, est celui qu’il faut utiliser dans le (les) fichier d’actions. Prenons par exemple le fichier workflow pour l’intégration continue de mon post sur [GitHub Actions – Créer une image Docker](https://www.ctrl-alt-suppr.dev/2021/05/09/github-actions-creer-une-image-docker/).  

```yaml
name: CI

on:
  push:
    paths:
     - 'src/BlazorRackManager/**'
     - '!.github/workflows/**'
     - '!README.md'

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Login to DockerHub
        run: echo ${{ secrets.DOCKERHUB_TOKEN }} | docker login --username ${{ secrets.DOCKERHUB_USERNAME }} --password-stdin
      - name: Build image
        run: docker build --file ./src/BlazorRackManager/dockerfileci --tag anthonyryck/rackmanager:ci .
      - name: Push to DockerHub
        run: docker push anthonyryck/rackmanager:ci
```

Pour appeler un secret dans une Action, il faut utiliser :   
`${{ secrets.DOCKERHUB_TOKEN }}`  
`secrets` est un mot clé qui permet d’accéder au contexte des secrets. Ensuite il faut mettre le nom du secret qui correspond à celui créé précédemment.  

Dans les logs d’exécution, tous les secrets sont remplacés par des *******, comme on peut le voir sur la phase de login sur Docker, mon mot de passe n’est pas écrit en clair.  
![](https://www.ctrl-alt-suppr.dev/wp-content/uploads/2021/05/GitActionDocker-02-LoginDocker.png)

Voilà, un petit post rapide pour une explication, j’espère simple.  
