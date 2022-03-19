
# Traefik et Docker
## Qu’est-ce que Traefik et pourquoi lui ?

L’image vient du site [Traefik](https://doc.traefik.io/traefik/), et illustre totalement ce super outil !  
Traefik est un reverse proxy et un Load Balancer qui facilite le déploiement de microservices. Traefik s’intègre à vos composants d’infrastructure existants et se configure automatiquement et dynamiquement.   

Pour cet article je vais prendre mon serveur comme exemple. Toutes les applications qui tournent sur mon serveur sont sous Docker.   
>**Ma règle**  
>Pas d’image docker ?   
>Pas sur mon serveur !  
>*Ryck*  
  
Au début pour accéder à mes applications, je tapais le chemin avec l’adresse IP du serveur, et je changeais le numéro du port en fonction de l’application voulu. *Mais ça, c’était avant*.   
Maintenant je me suis pris un nom de domaine, et pour faire le lien entre les sous domaines et les applications, il y a plusieurs options. J’ai trouvé des infos sur NGinx et autres. Pendant mes recherches, je trouvais de plus en plus d’articles sur Traefik, et quand j’ai vu que c’était français, je n’ai plus hésité ! Un peu de patriotisme 😉  
<span style="text-decoration: underline;">Pour info</span> : Traefik est développée par la société de logiciels Containous. Il est sorti en 2016 sous les termes de la licence MIT libre et open-source.  
En mai 2020, Traefik avait plus d’un milliard de téléchargements sur Docker Hub et plus de 28 000 étoiles sur Github.  

  
Pour mon exemple, il faut avoir [Docker](https://docs.docker.com/engine/install/) et docker-compose d’installés (voir pour l’[installation](https://docs.docker.com/compose/install/)). Il y a plusieurs manières de faire le fichier du docker-compose, certains utilisent des fichiers de configuration externe, mais pour ma part j’ai tout mis dans le docker-compose.yml.  

Voici mon fichier  
docker-compose.yml   
```yaml
version: "3.7"
services:

  traefik:
     image: traefik:chevrotin
     command:
       --entrypoints.http.address=:80
       --entrypoints.https.address=:443
       --providers.docker=true
       --api=true
       --certificatesresolvers.letsencrypt.acme.httpchallenge=true
       --certificatesresolvers.letsencrypt.acme.httpchallenge.entrypoint=http
       --certificatesresolvers.letsencrypt.acme.email=votremail@email.com
       --certificatesresolvers.letsencrypt.acme.storage=/letsencrypt/acme.json
     labels:
       - traefik.enable=true
       - traefik.http.routers.to-https.rule=HostRegexp(`traefik.votre-domaine.fr`)
       - traefik.http.routers.to-https.entrypoints=http
       - traefik.http.routers.to-https.middlewares=to-https
       - traefik.http.routers.traefik.rule=Host(`traefik.votre-domaine.fr`)
       - traefik.http.routers.traefik.entrypoints=http
       - traefik.http.routers.traefik.middlewares=auth
       - traefik.http.routers.traefik.service=api@internal
       - traefik.http.routers.traefik.tls=true
       - traefik.http.routers.traefik-https.tls.certresolver=letsencrypt
       - traefik.http.middlewares.to-https.redirectscheme.scheme=https
       - traefik.http.middlewares.auth.basicauth.users=admin:VotreMotDePasse
     ports:
       - 80:80
       - 443:443
     volumes:
       - /var/run/docker.sock:/var/run/docker.sock:ro
       - /docker/letsencrypt:/letsencrypt
  
  wordpresscompose:
       image: wordpress:latest
       container_name: wordpresscompose
       hostname: wordpresscompose
       expose :
         - 80
       environment:
         - WORDPRESS_DB_HOST=123.123.123.123:3306
         - WORDPRESS_DB_USER=YourUserLogin 
         - WORDPRESS_DB_PASSWORD=YourPassword 
         - WORDPRESS_DB_NAME=YourDbName

       labels:
         - traefik.enable=true
         - traefik.http.routers.blog.rule=Host(`sousdomaine.votre-domaine.fr`)
         - traefik.http.routers.blog.entrypoints=https
         - traefik.http.routers.blog.tls=true
         - traefik.http.routers.blog.tls.certresolver=letsencrypt
         
  demofansapp:
       image: anthonyryck/demofansapp:latest
       container_name: demofans
       hostname: demofansapp
       expose :
         - 80
       labels:
         - traefik.enable=true
         - traefik.http.routers.fandemo.rule=Host(`fandemo.votre-domaine.fr`)
         - traefik.http.routers.fandemo.entrypoints=https
         - traefik.http.routers.fandemo.tls=true
         - traefik.http.routers.fandemo.tls.certresolver=letsencrypt
```

Pour l’exécuter : `docker-compose up -d`  
docker-compose va créer/exécuter Traefik, WordPress et DemoFanApp (*c’est une application que j’ai fait et que j’utilise pour faire la demo de mes articles*). Il n’y a pas la base de données MySQL, comme je l’avais monté avant tout ça, et que je ne voulais pas tout refaire par rapport à mes autres projets, peut-être un jour.  
## Examinons chaque élément
## Service Traefik
```yaml
  traefik:
     image: traefik:chevrotin
     command:
       --entrypoints.http.address=:80
       --entrypoints.https.address=:443
       --providers.docker=true
       --api=true
       --certificatesresolvers.letsencrypt.acme.httpchallenge=true
       --certificatesresolvers.letsencrypt.acme.httpchallenge.entrypoint=http
       --certificatesresolvers.letsencrypt.acme.email=votremail@email.com
       --certificatesresolvers.letsencrypt.acme.storage=/letsencrypt/acme.json
     labels:
       - traefik.enable=true
       - traefik.http.routers.to-https.rule=Host(`traefik.votre-domaine.fr`)
       - traefik.http.routers.to-https.entrypoints=http
       - traefik.http.routers.to-https.middlewares=to-https
       - traefik.http.routers.traefik.rule=Host(`traefik.votre-domaine.fr`)
       - traefik.http.routers.traefik.entrypoints=http
       - traefik.http.routers.traefik.middlewares=auth
       - traefik.http.routers.traefik.service=api@internal
       - traefik.http.routers.traefik.tls=true
       - traefik.http.routers.traefik-https.tls.certresolver=letsencrypt
       - traefik.http.middlewares.to-https.redirectscheme.scheme=https
       - traefik.http.middlewares.auth.basicauth.users=admin:VotreMotDePasseEncoded
     ports:
       - 80:80
       - 443:443
     volumes:
       - /var/run/docker.sock:/var/run/docker.sock:ro
       - /docker/letsencrypt:/letsencrypt
```

Chose importante avant de commencer. L’indentation est très importante dans le fichier.  
* `traefik:` c’est le nom du service que vous donnez. Ca peut-être traefikSvc, plouf,…  
* `image: traefik:chevrotin` —> indique quelle image il faut utiliser.  
* `command:` —> ce sont les commandes que nous aurions saisies en l’exécutant en ligne de commande, *traefik –providers.docker=true –api=true…*  
* `ports:` —–> exemple avec le 80:80. Vous exposez le port 80 pour une redirection sur le 80.  
* `volumes:` —-> `/var/run/docker.sock:/var/run/docker.sock:ro` permet au container Traefik de voir toute modification sur les containers (ajout, suppression, arrêt, …)  
`/docker/letsencrypt:/letsencrypt` pour stocker les certificats générés et ne pas les perdre au cas d’une recréation du container.  
* `labels:` —-> pour les labels c’est là où ça devient intéressant. C’est le paramétrage de comment Traefik va le voir, ou se voir.  
## Détaille pour labels
* `- traefik.enable=true`   
Vous pouvez demander à Traefik de prendre en compte (ou non) le conteneur en définissant traefik.enable sur true ou false.  
* `- traefik.http.routers.to-https.rule=Host('traefik.votre-domaine.fr')`  
Vérifie si le domaine de la requête correspond à cette adresse, avec la route « to-https »  
* `- traefik.http.routers.to-https.entrypoints=http`  
Ecoute le point d’entré en http  
* `- traefik.http.routers.to-https.middlewares=to-https`  
  
* `- traefik.http.routers.traefik.rule=Host(traefik.votre-domaine.fr)`  
Vérifie si le domaine de la requête correspond à cette adresse, avec la route « traefik ».  
* `- traefik.http.routers.traefik.entrypoints=http`  
Indique le point d’entré de type http.  
* `- traefik.http.routers.traefik.middlewares=auth`  
Crée que le middlewares auth, utilisé pour le « basicauth »   
* `- traefik.http.routers.traefik.service=api@internal`  
* `- traefik.http.routers.traefik.tls=true`  
Activation du [TLS](https://fr.wikipedia.org/wiki/Transport_Layer_Security) (*Sécurité de la couche de transport*).  
* `- traefik.http.routers.traefik-https.tls.certresolver=letsencrypt`  
Indique-le resolver pour les certificats.  
* `- traefik.http.middlewares.to-https.redirectscheme.scheme=https`  
Redirige toutes les requêtes vers le https  
* `- traefik.http.routers.traefik.middlewares=auth`  
Crée que le middlewares auth, utilisé pour le « basicauth »  
* `- traefik.http.middlewares.auth.basicauth.users=admin:VotreMotDePasseEncoded`  
C’est le login (admin) et le mot de passe pour encodé pour accéder au tableau de bord de Traefik.  
J’ai utilisé ce [site ](https://www.web2generators.com/apache-tools/htpasswd-generator)pour faire l’encodage du mot de passe. Il doit être encodé en MD5, SHA1, ou BCrypt.  
NOTE : s’il y a des $ dans la chaine de caractère il faut les doubler $$ pour  faire un échappement.  
## Service demofanapp
```yaml
demofansapp:
       image: anthonyryck/demofansapp:latest
       container_name: demofans
       hostname: demofansapp
       expose :
         - 80
       labels:
         - traefik.enable=true
         - traefik.http.routers.fandemo.rule=Host(`fandemo.votre-domaine.fr`)
         - traefik.http.routers.fandemo.entrypoints=https
         - traefik.http.routers.fandemo.tls=true
         - traefik.http.routers.fandemo.tls.certresolver=letsencrypt
```

Beaucoup plus simple.  
Expose le port 80, mais pas besoin de redirection, car c’est Traefik qui va communiquer directement avec ce container, donc il faut juste que le container expose son port.  
Pour les labels :  
* `- traefik.enable=true`  
Vous pouvez demander à Traefik de prendre en compte (ou non) le conteneur en définissant traefik.enable sur true ou false.  
* – `traefik.http.routers.fandemo.rule=Host(fandemo.votre-domaine.fr)`  
Règle quand il reçoit cette requête. Il faut que ce soit une requête avec ce sous domaine, pour l’envoyer au service voulu.  
* – `traefik.http.routers.fandemo.entrypoints=https`  
Point d’entré en https  
* ``- traefik.http.routers.fandemo.tls=true``  
Activation du [TLS](https://fr.wikipedia.org/wiki/Transport_Layer_Security) (*Sécurité de la couche de transport*)  
* ``- traefik.http.routers.fandemo.tls.certresolver=letsencrypt``  
activation du resolver pour les certificats avec Let’s Encrypt.  

Note : dans `traefik.http.routers.fandemo.entrypoints=https` le mot clé « **fandemo** » est le nom de la route, c’est un nom arbitraire, ça peut être « bloublou », faut juste garder ce nom de route pour le service.  

Bon j’avoue, il y a des choses que je ne maîtrise pas encore, mais je n’ai qu’un serveur pour faire mes tests, du coup quand tout fonctionne, on touche plus rien !  
