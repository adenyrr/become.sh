## Faire un onglet dédié à la météo

### Introduction

### Mise en page

J'utilise le mode "sections" avec 4 colonnes et l'en-tête. Dans les faits, la troisième colonne est en fait double : il s'agit de cartes météo qui gagnent à prendre un peu plus d'espace.

### L'en-tête

L'en-tête, ou *header* est la portion située en haut de l'onglet. Elle est limitée en terme de fonctionnalités, mais c'est la première chose qu'on voit : idéale donc pour les alertes météo ou les allergies. Afin d'éviter un encombrement, on créée des cartes conditionnelles qui ne s'affichent que lorsque l'entité choisie est active.

!!! tip "Source ?"

    J'utilise l'intégration de l'[IRM](https://github.com/jdejaegh/irm-kmi-ha), puisque je suis belge. Pour les autres, [AccuWeather](https://www.home-assistant.io/integrations/accuweather/) fourni les concentrations précises et le niveau de danger associé.

<figure markdown="span">
  ![Header](https://raw.githubusercontent.com/adenyrr/become.sh/3ebd2f09bc567be2149ef95f435b3b3f7363f264/docs/assets/hassio/header.png)
  <figcaption>En-tête de l'onglet météo : Alertes et allergies</figcaption>
</figure>

Chacun des pollens mesurés possède son propre badge, dont voici le code :

``` yaml
type: entity # Simple badge entité
show_name: true # On affiche le nom du pollen incriminé
show_state: false # Le nom suffit, pas besoin de la valeur précise
show_icon: true # Parce que c'est plus sympa avec
entity: sensor.maison_grasses_level # Attention ici, si on utilise AccuWeather, le senseur aura une forme de type "sensor.maison_grass_pollen_day_0" (*maison* étant le nom de l'intégration Accuweather dans mon cas, *0* étant le jour d'aujourd'hui)
name: Graminées # On précise le nom français
show_entity_picture: false # Pas besoin
color: pink # Plus contrasté que le rouge, je le préfère pour les dangers.
visibility: # On modifie la visibilité ici pour qu'il ne s'affiche que si le capteur est différent de *none* (aucun), unavalaible (indisponible) ou unknown (inconnu). En gros : s'il est actif.
  - condition: state
    entity: sensor.maison_grasses_level
    state_not: none
  - condition: state
    entity: sensor.maison_grasses_level
    state_not: unavailable
  - condition: state
    entity: sensor.maison_grasses_level
    state_not: unknown
```