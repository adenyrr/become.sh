# Introduction
## C'est quoi ?

[Jellyfin](https://jellyfin.org/) est une application de streaming multimédia. Elle permet de scanner une bibliothèque de fichiers vidéos, l'organiser dans une interface propre et soignée, reconnaitre et classer le contenu via les métadonnées et compléter celles-ci par la recherche sur des bases de donnes telles que imDB. En bref, elle transforme n'importe quelle bibliothèque de films et séries en netflix personnel.

!!! quote "Elle se défini lui-même par :"

    Jellyfin est la solution média communautaire, qui vous permet de contrôler vos médias. Diffusez sur n'importe quel appareil à partir de votre propre serveur, sans aucune contrainte. Votre média, votre serveur, votre façon de faire.

!!! question "Pourquoi Jellyfin et pas Plex ?"

    Contrairement à Jellyfin, Plex est propriétaire. Le code n'est pas ouvert, personne ne peut en vérifier le contenu. De plus, Plex demande une clef d'API, payante, pour effectuer du transcodage. Jellyfin est complet et entièrement contrôlé par l'utilisateur.

## Avantages & inconvénients

=== "Avantages"

    * Jellyfin est *vraiment* pratique lorsque l'on a des enfants. Il permet de configurer des heures d'accessibilité au service facilement, ainsi que l'accès aux classifications PEGI.
    * Contrairement à Netflix ou Disney+, lorsque le film ou la série est terminé.e, aucune bande annonce ne se lance.
    * Jellyfin indique l'heure de fin du média regardé : ça conscientise à la modération
    * Il dispose de clients libres sur quasiment toutes les plateformes (Android, Web, Windows, Mac OS ...)

=== "Inconvénients"

    * Plex est vraiment clef-en-main, parfois plus simple que Jellyfin
    * Plex dispose de plus d'outils communautaires
    * Plex dispose de nombreux clients

## Alternatives

Les alternatives les plus connues sont :

- [Plex](https://plex.tv) : propriétaire
- [Kodi](https://kodi.tv) : open-source
- [Emby](https://emby.media) : propriétaire

# Installation

## Prérequis

Un certain nombre de prérequis sont non-négociables :

- CPU avec 2 coeurs
- 4 GB de DDR
- 8 GB de HDD
- un dossier, disque, volume ou partage où se trouve le contenu

Cela vaut pour un serveur léger, sans transcodage. Un Raspberry Pi peut également suffire. Pour bénéficier du transcodage, il faut au moins :

- CPU avec 4 coeurs
- 4 GB de DDR
- 64 à 128 GB de HDD (soit 8 GB pour jellyfin, *et le reste pour les transcodages*)
- Un NAS qui partage un volume où sont les médias
- Une carte graphique

!! tip "Ma solution"

    J'utilise une machine virtuelle dédié pour le multimédia, avec 4 coeurs, 4 GB de DDR4 et 96 GB de disque. Le transcodage se fait sur une nVidia 1060. J'utilise une instance OMV qui partage plusieurs téras de fichiers sur cette machine. 

## Docker && compose

Une fois docker installé, autant l'utiliser. Jellyfin publie un conteneur, de même que [linuxserver.io](https://docs.linuxserver.io/images/docker-jellyfin/). Le `docker compose` fourni ici est compatible avec [Portainer](https://portainer.io).

!!! info

    On change évidemment les repertoires `(/autre)/chemin/des/medias` par le chemin absolu du repertoire où est le contenu. On peut aussi ajouter ou enlever autant de repertoires qu'on veut, en respectant le même schéma.
    Il faut évidemment que les repertoires existent, et s'ils proviennent d'un partage, qu'ils soient bien montés.

=== "Docker"
    
    ``` bash
    docker run -d \
     --name jellyfin \
     --user 1000:1000 \
     --net=host \
     --volume jellyfin-config:/config
     --volume jellyfin-cache:/cache
     --mount type=bind,source=/chemin/des/medias,target=/media \
     --restart=unless-stopped \
     jellyfin/jellyfin
    ```

=== "Docker Compose"

    ``` yaml
    services:
      jellyfin:
        image: jellyfin/jellyfin
        container_name: jellyfin
        user: 1000:1000
        network_mode: 'host'
        volumes:
          - ./config:/config
          - ./cache:/cache
          - type: bind
            source: /chemin/des/medias
            target: /media
          - type: bind
            source: /autre/chemin/des/medias
            target: /media2
        restart: 'unless-stopped'
        extra_hosts:
          - 'host.docker.internal:host-gateway'
    ```
    On lance ensuite la commande `docker compose up -d`, on attend que l'appli s'installe et on peut se rendre sur `http://IP:8096` en remplaçant `IP` par l'adresse IP du serveur jellyfin. (Pour trouver l'adresse IP, on peut utiliser `ip -a` sous linux).

## Exécutable

!!! warning "Bonnes pratiques !"

    La solution recommmandée est d'utiliser `docker`. Cependant, sur un serveur dédié au multimédia, on peut installer directement les exécutables fournis par l'équipe de jellyfin. C'est une solution qui *peut* convenir, par exemple sur une machine virtuelle.

Pour installer directement Jellyfin sous Linux, le projet propose un script tout fait :

    ``` bash
    curl https://repo.jellyfin.org/install-debuntu.sh | sudo bash
    ```

## Home Assistant

Il est également possible d'installer Jellyfin directement depuis Home Asistant, s'il est exécuté en mode `système d'exploitation`. Il suffit de se rendre sur le menu des add-ons, puis d'ajouter le dépôt suivant : `https://github.com/alexbelgium/hassio-addons` ou de cliquer [ici](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Falexbelgium%2Fhassio-addons).

# Configuration 

Il suffit de se rendre sur http://IP:8096 our commencer la configuration.
