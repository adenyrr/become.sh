## Introduction
### C'est quoi ?

[Ollama](https://ollama.com/) est une application qui permet de faire tourner et de manipuler une IA (en fait, des *modèles de langage*) sur son propre matériel. [Open-WebUI](https://github.com/open-webui/open-webui) est l'application fournissant une interface web pour intéragir avec Ollama, ainsi qu'avec les LLM.

!!! quote "Elle se défini elle-même par :"

    Ollama est un *outil* léger et extensible pour la construction et l'exécution de modèles de langage fournis localement. Il expose une API simple pour créer, exécuter et gérer des modèles, ainsi qu'une bibliothèque de modèles préconstruits qui peuvent être facilement utilisés dans une variété d'applications.

!!! question "Pourquoi Ollama et OpenWebUI ?"

    Ollama s'est imposé comme standard dans la communauté. Il est très facile d'utilisation et fourni des IA "clef en main" facilement. OpenWebUI en est directement dérivé comme interface utilisable, Ollama ne fournissant qu'une console locale.

### Avantages & inconvénients

L'IA étant un domaine des plus complexes, je n'aborderai pas ici les avantages ou inconvénients d'ollama *versus une autre application* mais les pour et contre d'utiliser un LLM local.

=== "Avantages"

    * Les données ne sortent pas du serveur
    * On peut utiliser n'importe quel modèle disponible, et ils sont nombreux
    * C'est une bonne manière d'apprendre à utiliser l'outil "IA"
    * Connecté à Home Assistant, ça fait une IA *à la Jarvis* qui déchire

=== "Inconvénients"

    * Les LLM consomment énormément de ressources, qui ont un coût certain (vram, gpu, ...)
    * ... Et d'électricité !
    * Seuls les modèles ayant des licences permissives sont disponibles

### Alternatives

Les alternatives les plus connues sont :

- [ChatGPT](https://chatgpt.com) : propriétaire, hébergé
- [Le Chat](https://chat.mistral.ai/chat) : modèle open-source, mais service hébergé
- [LocalAI](https://emby.media) : open-source, hébergé
  

## Installation


### Prérequis

- Une machine (virtuelle ou non) sous debian avec *au moins* 4 coeurs et 10240 MB de DDR
- Docker
- Une ou des carte.s graphique.s (du même fabricant !)

Avant d'installer quoi que ce soit, on attribue si possible une adresse IP fixe. Ensuite, on vérifie que la carte graphique est bien détectée avec

``` bash
lspci
```
Qui liste l'ensemble des périphériques liés aux bus PCI(-e) et devrait renvoyer quelque chose du genre :

``` bash
...
00:10.0 VGA compatible controller: NVIDIA Corporation GP102 [GeForce GTX 1080 Ti] (rev a1)
00:10.1 Audio device: NVIDIA Corporation GP102 HDMI Audio Controller (rev a1)
...
```

Dans ce cas, ma carte graphique, une `nvidia 1080 Ti` dont le code GPU est `GP102`, est bien détectée. Ensuite, on installe les drivers.

=== "nvidia"

    Les drivers nvidia étant propriétaires, il faut activer les dépots contrib et non-free *avant* d'installer `nvidia-smi`.
    ~~~ bash
    sudo apt install nvidia-smi
    ~~~

=== "AMD"

    Les pilotes sont inclus dans le noyau linux par défaut, rien à faire :)


### Docker && compose

La méthode recommandée, comme bien souvent, est de passer via docker. Par chance, OpenWebUI fourni un conteneur *complet, incluant Ollama*. On a donc pas besoin d'installer les deux séparément, on a juste à lancer la commande :

``` bash
docker run -d -p 3000:8080 --gpus=all -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:ollama
```
Personnellement, je préfère utiliser un docker compose, qui sera sous la forme :

``` yaml
services:
    open-webui:
        container_name: open-webui
        restart: always
        image: ghcr.io/open-webui/open-webui:ollama
        ports:
            - 3000:8080
        volumes:
            - ollama:/root/.ollama
            - open-webui:/app/backend/data
        deploy:
            resources:
                reservations:
                    devices:
                        - driver: nvidia
                          count: all
                          capabilities:
                              - gpu
volumes:
    ollama:
        external: true
        name: ollama
    open-webui:
        external: true
        name: open-webui
```
Comme toujours après un docker compose, on doit initialiser le fichier avec
``` bash
docker compose up -d
```

### Exécutable

!!! warning "Bonnes pratiques"

    L'installation par le script automatique n'est recommandé que dans le cas d'une utilisation sur une machine (virtuelle ou non) dédiée.
    
L'équipe derrière Ollama fourni un script qui se charge de tout :
``` bash
curl -fsSL https://ollama.com/install.sh | sh
```
Ollama est installé, mais n'est utile que sous forme d'API ou de console. Pour simplifier son utilisation, on va donc installer OpenWebUI. Pour ça, il faut d'abord installer `python3-full` qui se chargera d'installer les scripts `python` (Python est le langage le plus utilisé dans le monde de l'IA). Puis, on demande à `pip` (le gestionnaire d'installation de *python*) d'installer le bon paquet.
``` bash
sudo apt install python3-pip -y
pip install open-webui --break-system-packages
#Lorsque l'installation est terminée, on lance OpenWebUI avec
open-webui serve
```

## Configuration 
