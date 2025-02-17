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

Dans ce cas, ma carte graphique, une `nvidia 1080 Ti` dont le code GPU est bien `GP102`, est bien détectée. Ensuite, on installe les drivers.

=== "nvidia"

    Les drivers nvidia étant propriétaires, il faut activer les dépots contrib et non-free *avant* d'installer `nvidia-smi`.
    ~~~ bash
    sudo apt install nvidia-smi
    ~~~

=== "AMD"

    Les pilotes sont inclus dans le noyau linux par défaut, rien à faire :)


### Docker && compose
### Exécutable
### Home Assistant
## Configuration 
