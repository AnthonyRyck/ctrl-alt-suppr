

Dans ce post je vais montrer comment créer une release sous GitHub, en utilisant GitHub Action. Je vais prendre une petite application que j’ai fait pour surveiller des machines sur mon réseau, c’est [Ping’O’Matic](https://github.com/AnthonyRyck/PingOMatic).   
Si vous ne connaissez pas GitHub Actions, je vous invite à lire mon [premier post sur le sujet](https://www.ctrl-alt-suppr.dev/2021/04/25/github-actions/). Pour résumer, un workflow sur Github Action est un fichier YAML (ou yml) qui est décomposé en plusieurs parties. Attention, l’indentation est **TRES IMPORTANTE** !  
Je vais découper chaque partie du fichier.  
#### Nommer le workflow

`name: ReleaseAction`  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/01-githubActionRelease.png" alt=""/></figure>#### Indiquer le trigger
<p id="indiquer-le-trigger">C’est avec le mot clé `on:` que nous indiquons sur quel évènement l’action sera lancé. Dans mon cas, je veux pouvoir l’exécuter à la demande, donc se sera avec le mot clé : `workflow_dispatch:`  
Il est possible de fournir des informations au workflow. Pour cela il faut utiliser `inputs:`.  
```yaml
>on:
  # Lancement manuel
  workflow_dispatch:
    inputs:
      tags:
        description: "Nom du tag pour cette release"</pre>
Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
```yaml
>jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
```yaml
>jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâch
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
```yaml
>jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâch
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
```yaml
>jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
```yaml
># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
```yaml
>`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâch
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
```yaml
>jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Ces étapes obtiennent la configuration de MSBuild et NuGet et sont ajoutées à la variable PATH
 - name: Setup MSBuild
   uses: microsoft/setup-msbuild@v1
 - name: Restore Packages
   run: nuget restore .\src\PingOMatic\PingOMatic.sln</pre>
 `- name:` permet de donner un nom à l’étape, afin de savoir où nous en sommes dans le workflow.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/03-githubActionRelease.png" alt=""/></figure>
C’est avec `run:` que nous indiquons la commande à exécuter sur l’environnement.  
**CHOSE A SAVOIR**  
Le « curseur  » se trouve à la racine du checkout du repo. Pour la commande nuget restore que je fais, j’indique le chemin par rapport à la position du « curseur », il faut suivre l’arborescence du repo (/src/PingOMatic).  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Compilation du code.

```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un
```

Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâch
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Ces étapes obtiennent la configuration de MSBuild et NuGet et sont ajoutées à la variable PATH
 - name: Setup MSBuild
   uses: microsoft/setup-msbuild@v1
 - name: Restore Packages
   run: nuget restore .\src\PingOMatic\PingOMatic.sln</pre>
 `- name:` permet de donner un nom à l’étape, afin de savoir où nous en sommes dans le workflow.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/03-githubActionRelease.png" alt=""/></figure>
C’est avec `run:` que nous indiquons la commande à exécuter sur l’environnement.  
**CHOSE A SAVOIR**  
Le « curseur  » se trouve à la racine du checkout du repo. Pour la commande nuget restore que je fais, j’indique le chemin par rapport à la position du « curseur », il faut suivre l’arborescence du repo (/src/PingOMatic).  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Compilation du code.

```

`job
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Ces étapes obtiennent la configuration de MSBuild et NuGet et sont ajoutées à la variable PATH
 - name: Setup MSBuild
   uses: microsoft/setup-msbuild@v1
 - name: Restore Packages
   run: nuget restore .\src\PingOMatic\PingOMatic.sln</pre>
 `- name:` permet de donner un nom à l’étape, afin de savoir où nous en sommes dans le workflow.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/03-githubActionRelease.png" alt=""/></figure>
C’est avec `run:` que nous indiquons la commande à exécuter sur l’environnement.  
**CHOSE A SAVOIR**  
Le « curseur  » se trouve à la racine du checkout du repo. Pour la commande nuget restore que je fais, j’indique le chemin par rapport à la position du « curseur », il faut suivre l’arborescence du repo (/src/PingOMatic).  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Compilation du code.

```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un
```

Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâch
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Ces étapes obtiennent la configuration de MSBuild et NuGet et sont ajoutées à la variable PATH
 - name: Setup MSBuild
   uses: microsoft/setup-msbuild@v1
 - name: Restore Packages
   run: nuget restore .\src\PingOMatic\PingOMatic.sln</pre>
 `- name:` permet de donner un nom à l’étape, afin de savoir où nous en sommes dans le workflow.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/03-githubActionRelease.png" alt=""/></figure>
C’est avec `run:` que nous indiquons la commande à exécuter sur l’environnement.  
**CHOSE A SAVOIR**  
Le « curseur  » se trouve à la racine du checkout du repo. Pour la commande nuget restore que je fais, j’indique le chemin par rapport à la position du « curseur », il faut suivre l’arborescence du repo (/src/PingOMatic).  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Compilation du code.

```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un
```

Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâch
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Ces étapes obtiennent la configuration de MSBuild et NuGet et sont ajoutées à la variable PATH
 - name: Setup MSBuild
   uses: microsoft/setup-msbuild@v1
 - name: Restore Packages
   run: nuget restore .\src\PingOMatic\PingOMatic.sln</pre>
 `- name:` permet de donner un nom à l’étape, afin de savoir où nous en sommes dans le workflow.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/03-githubActionRelease.png" alt=""/></figure>
C’est avec `run:` que nous indiquons la commande à exécuter sur l’environnement.  
**CHOSE A SAVOIR**  
Le « curseur  » se trouve à la racine du checkout du repo. Pour la commande nuget restore que je fais, j’indique le chemin par rapport à la position du « curseur », il faut suivre l’arborescence du repo (/src/PingOMatic).  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Compilation du code.

```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est u
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâch
```

`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de
```

Dans mon exemple, je veux fournir le nom du tag qui sera mis pour cette release. J’ajoute `tags:` et `description:`.   
« tags » n’est pas un mot clé de Github Action, j’aurai pu mettre n’importe quoi, c’est arbitraire, en revanche `description`, c’est un mot clé.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/02-githubActionRelease.png" alt=""/></figure>#### L’exécution

C’est dans cette partie que nous indiquons à GitHub toutes les étapes.  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest</pre>
`jobs` est un mot clé ([la doc jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)). C’est une liste des travaux qui s’exécutent dans le cadre du workflow. Chaque tâche s’exécutera indépendamment des autres et s’exécutera sur un environnement virtuel différent. Les travaux peuvent avoir un nom pour les rendre facilement identifiables dans l’interface utilisateur ou dans les journaux. Les travaux contiennent un ensemble d’étapes qui seront exécutées, dans l’ordre. Ce workflow comporte une seule tâche, la tâche de génération.   
`build:` est le nom que je lui ai donné.  
`runs-on: windows-latest`. Indique sur quel environnement il faudra exécuter les tâches. Pour mon cas, c’est une application WPF avec le framework 4.7.2, du coup, il me faut du Windows.  
Voici une liste des [environnements possible](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners).  
#### Les étapes

C’est avec le mot clé `steps:` que nous définissons les étapes. Chaque étape doit commencer par un **–** *(tiret)*.  
Première chose à faire, récupérer le code source. Pour ça il faut utiliser une action qui existe déjà dans Github. Comme c’est une action qui existe déjà, il faut utiliser `uses:` et le nom de l’action. ([code source de checkout](https://github.com/actions/checkout))  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Steps represent a sequence of tasks that will be executed as part of the job
steps:
# Checkout des sources
  - uses: actions/checkout@v2</pre>
Ensuite, indiquer à l’environnement que je veux utiliser MSBuild. Comme c’est une action réutilisable, il faut `uses:`  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Ces étapes obtiennent la configuration de MSBuild et NuGet et sont ajoutées à la variable PATH
 - name: Setup MSBuild
   uses: microsoft/setup-msbuild@v1
 - name: Restore Packages
   run: nuget restore .\src\PingOMatic\PingOMatic.sln</pre>
 `- name:` permet de donner un nom à l’étape, afin de savoir où nous en sommes dans le workflow.  
<figure class="wp-block-image size-large"><img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/03-githubActionRelease.png" alt=""/></figure>
C’est avec `run:` que nous indiquons la commande à exécuter sur l’environnement.  
**CHOSE A SAVOIR**  
Le « curseur  » se trouve à la racine du checkout du repo. Pour la commande nuget restore que je fais, j’indique le chemin par rapport à la position du « curseur », il faut suivre l’arborescence du repo (/src/PingOMatic).  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Compilation du code.
  - name: Build de la solution
    run: msbuild.exe .\src\PingOMatic\PingOMatic.sln /p:platform="Any CPU" /p:configuration="Release"
# Zip de la release
  - name: Zip release
    run: Compress-Archive -Path '.\src\PingOMatic\PingOMatic\bin\Release\*' PingOMatic-Release.zip</pre>
Compilation du code, et compression de la release dans un fichier zip. Comme je suis sur un environnement Windows, j’utilise [Compress-Archive](https://docs.microsoft.com/fr-fr/powershell/module/microsoft.powershell.archive/compress-archive?view=powershell-7.2) de Powershell.  
#### La release

La partie que vous attendez, comment créer une release sur Github.  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group=""># Création de la release
- name: Création de la release
  uses: actions/create-release@v1
  id: create_release
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  with:
    tag_name: ${{ github.event.inputs.tags }}
    release_name: ${{ github.event.inputs.tags }}
    body: |
      Changement dans cette version.
      - Ajout du menu pour "alléger" la fenêtre de test
      - Ajout la possibilité d'ajouter des items dans le menu contextuel sur les tests.
      - Ajout d'un export des résultats
    draft: false
    prerelease: false
- name: Upload artifact
  uses: actions/upload-release-asset@v1
  env:
    GITHUB_TOKEN: ${{ github.token }}
  with:
    upload_url: ${{ steps.create_release.outputs.upload_url }}
    asset_path: PingOMatic-Release.zip
    asset_name: PingOMatic-Release.zip
    asset_content_type: application/zip</pre>
`uses: actions/create-release@v1` : j’utilise l’action mis à disposition ([code de create-release](https://github.com/actions/create-release)).  
Cette action a besoin d’une variable `GITHUB_TOKEN` qu’on déclare avec `env:`, et la valeur est donné avec `${{ secrets.GITHUB_TOKEN }}`.  
Note : Le token est fourni par Actions, vous n’avez pas besoin de créer le token.  
Ensuite, il faut fournir les informations pour que la release soit bien remplie. Voir la doc sur [create-release, sur input](https://github.com/actions/create-release#inputs). Je vais juste m’attarder sur  
`tag_name: ${{ github.event.inputs.tags }}`  
C’est pour donner le nom du tag pour cette release. Je récupère l’information du début. J’utilise le contexte de github ([doc pour tous les contextes](https://docs.github.com/en/actions/learn-github-actions/contexts#github-context)), et faut suivre « l’arborescence » : `inputs` – `tags` *(ceux expliqués au début)*.  

La dernière étape est de publier dans la release le zip contenant l’application compilé.  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">- name: Upload artifact
  uses: actions/upload-release-asset@v1
  env:
    GITHUB_TOKEN: ${{ github.token }}
  with:
    upload_url: ${{ steps.create_release.outputs.upload_url }}
    asset_path: PingOMatic-Release.zip
    asset_name: PingOMatic-Release.zip
    asset_content_type: application/zip</pre>
Toujours pareil, j’utilise une action présente dans Actions ([code source](https://github.com/actions/upload-release-asset)) pour upload le zip. Chose importante :  
`upload_url: ${{ steps.create_release.outputs.upload_url }`  
`steps` est un mot clé permettant de « naviguer » dans notre workflow. Là, je veux naviguer dans l’étape ou j’ai créé la release. Je n’utilise pas le name, mais `id:` Je récupère l’URL de cette action (comme indiqué dans [la doc](https://github.com/actions/create-release#outputs) sur `upload_url`).  

Maintenant la release est créée.  
<img src="https://raw.githubusercontent.com/AnthonyRyck/ctrl-alt-suppr/main/ImgBlog/GithubAction/CreateRelease/04-githubActionRelease-Release.png" alt="" />

Je mets le fichier YML du workflow complet (qui se [trouve ici](https://github.com/AnthonyRyck/PingOMatic/blob/master/.github/workflows/releaseAction.yml)).  
<pre class="EnlighterJSRAW" data-enlighter-language="yaml" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">name: ReleaseAction

on:
  # Lancement manuel
  workflow_dispatch:
    inputs:
      tags:
        description: "Nom du tag pour cette release"

jobs:
  build:
    # The type of runner that the job will run on
    runs-on: windows-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    # Checkout des sources
      - uses: actions/checkout@v2
    # Ces étapes obtiennent la configuration de MSBuild et NuGet et sont ajoutées à la variable PATH
      - name: Setup MSBuild
        uses: microsoft/setup-msbuild@v1
      - name: Restore Packages
        run: nuget restore .\src\PingOMatic\PingOMatic.sln
    # Compilation du code.
      - name: Build de la solution
        run: msbuild.exe .\src\PingOMatic\PingOMatic.sln /p:platform="Any CPU" /p:configuration="Release"
    # Zip de la release
      - name: Zip release
        run: Compress-Archive -Path '.\src\PingOMatic\PingOMatic\bin\Release\*' PingOMatic-Release.zip
    # Création de la release
      - name: Création de la release
        uses: actions/create-release@v1
        id: create_release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.event.inputs.tags }}
          release_name: ${{ github.event.inputs.tags }}
          body: |
            Changement dans cette version.
            - Ajout du menu pour "alléger" la fenêtre de test
            - Ajout la possibilité d'ajouter des items dans le menu contextuel sur les tests.
            - Ajout d'un export des résultats
          draft: false
          prerelease: false
      - name: Upload artifact
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ github.token }}
        with:
          upload_url: ${{ steps.create_release.outputs.upload_url }}
          asset_path: PingOMatic-Release.zip
          asset_name: PingOMatic-Release.zip
          asset_content_type: application/zip</pre>
