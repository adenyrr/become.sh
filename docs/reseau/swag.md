## Introduction

### Avant-propos

Si certains services comme [Ollama](https://wiki.become.sh/services/ollama/) peuvent rester uniquement en local, d'autres comme [Home Assistant](https://wiki.become.sh/services/hassio/) gagnent à être exposés, afin de pouvoir etre utilisés en déplacement.
Plusieurs solutions sont possibles comme l’utilisation d'un VPN ou le recours à un reverse-proxy. C'est cette dernière que l'on abordera ici.

### C'est quoi ?

[SWAG](https://docs.linuxserver.io/general/swag/) pour "Secure Web Application Gateway", est une application de reverse-proxy basée sur nginx, incluant certbot pour la création de certificats SSL et fail2ban comme protection primaire. Moddable, le conteneur est édité par [linuxserver.io](https://linuxserver.io) et propose de facilement associer un nom de domaine à un service, en toute sécurité.

!!! quote "Elle se défini elle-même par :"

    SWAG - Secure Web Application Gateway (anciennement connu sous le nom de letsencrypt, sans rapport avec Let's Encrypt™) met en place un serveur web Nginx et un reverse proxy avec un support php et un client certbot intégré qui automatise les processus gratuits de génération et de renouvellement des certificats de serveur SSL (Let's Encrypt et ZeroSSL). Il contient également fail2ban pour la prévention des intrusions.

### Avantages & inconvénients

=== "Avantages"

    * SWAG est une application plutôt clef en main, incluant plusieurs outils fondamentaux.
    * nginx est l'un des reverse-proxy les plus efficace en terme de rapidité et de consommation de ressources
    * Le renouvellement du certificat SSL (permettant de faire du https) est automatique
    * On peut assez facilement renforcer la sécurité

=== "Inconvénients"

    * Malgré son aspect clef-en-main, il demande une configuration manuelle dans tous les cas.
    * Il restera toujours moins sécurisé qu'un accès par VPN uniquement
    * Il est nécessaire d'ouvrir au moins un port sur son routeur (:443)

### Alternatives

Les alternatives les plus connues sont :

- [Traefik](hhttps://doc.traefik.io/traefik/) : open-source
- [HAProxy](https://www.haproxy.org/) : open-source
- [Caddy](https://caddyserver.com/) : open-source
  

## Installation

### Prérequis

Un certain nombre de prérequis sont non-négociables :

- CPU avec 1 coeurs
- 512 Mo de DDR
- 4 GB de HDD
- Accès SSH
- Docker
- Un NDD, Nom de Domaine

### Docker

C'est la *seule* méthode prise en charge. Linuxserver est une communauté basée autour de la publication de conteneurs, il est donc normal de retrouver ceux-ci dans les méthodes recommandées.

!!! warning "Bonnes pratiques :"

    Personnellement, mon serveur proxy est une machine virtuelle dédiée, très légère. Je ne peux que recommander d'utiliser une machine dédiée au serveur proxy, celui-ci étant un point d'entrée pour de potentiels pirates. Ainsi, si le port 443 du routeur est ouvert et redirigé sur l'adresse du serveur proxy, il constitue une surface d'attaque non négligeable. Surface que l'on diminue en ne laissant qu'un seul service accessible.

!!! note "Certificat OVH"

    Le docker & le compose fournis ici sont configurés pour un domaine loué chez OVH. Le   provider peut évidemment changer. Plus d'infos sur la [page dédiée](https://docs.linuxserver.io/general/swag/#authorization-method). La configuration montre donc la demande automatisée d'un certificat SSL permettant de passer au *HTTPS* à partir d'un domaine chez OVH, pour le domaine lui-meme et l'ensemble des sous-domaines. (www.domain.com,photos.domain.com,cloud.domaine.com,...)

Comme d'habitude, le docker-compose est compatible Portainer et Komodo.

=== "Docker"
    
    ``` bash
        docker run -d \
        --name swag \
        --cap-add NET_ADMIN \
        -e PUID=1000 \
        -e PGID=1000 \
        -e TZ=Etc/UTC \
        -e URL=domaine.com \
        -e VALIDATION=dns \
        -e SUBDOMAINS=wildcard \
        -e DNSPLUGIN=ovh \
        -e DOCKER_MODS=linuxserver/mods:swag-dashboard
        -v ./config:/config \
        -p 443:443 \
        --restart unless-stopped \
        lscr.io/linuxserver/swag:latest
    ```

=== "Docker Compose"

    ``` yaml
    services:
        swag:
            image: lscr.io/linuxserver/swag:latest
            container_name: swag
            cap_add:
            - NET_ADMIN
            environment:
            - PUID=1000
            - PGID=1000
            - TZ=Etc/UTC
            - URL=domaine.com
            - VALIDATION=dns
            - SUBDOMAINS=wildcard
            - DNSPLUGIN=ovh
            - DOCKER_MODS=linuxserver/mods:swag-dashboard
            volumes:
            - ./config:/config
            ports:
            - 443:443
            restart: unless-stopped
    ```
    On lance ensuite la commande `docker compose up -d` qui installe ça.

Et là, ça échoue. Pas de panique, c'est normal.