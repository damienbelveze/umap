---
title: Umap, l'éditeur de cartes interactives
subtitle: Atelier du stretching numérique
author: Damien Belvèze
date: 16-03-2021
---

![](images/logo_stretching_num.png)

## Trouver des données sur Wikidata : les requêtes sparql

Le SPARQL est un langage de requêtes pour chercher des entités
liées, ce langage d'interrogation a été mis au point par le consortium
World Wide Web et sert de langage d'interrogation à Wikidata

### Le format RDF

Le SPARQL permet de faire des requêtes sur des données présentées selon un **schéma RDF**
Ce schéma est basé sur des triplets. Un triplet est constitué d'un *sujet*, d'un *prédicat* (ou d'une propriété) et d'un *objet*.

Par exemple

Alice(=sujet) connaît(=prédicat) Bob(=objet) \

Bob(=sujet) joue(=prédicat) au tennis(=objet) \


![Les triplets dans RDF](images/triple.png)

### Analyse d'une requête en Sparql sur Wikidata

Pour obtenir des données présentes dans Wikidata, on peut utiliser l'éditeur de requête mis à disposition sur [ce portail](https://query.wikidata.org)

Nous allons utiliser comme exemple de requête simple une recherche sur
les cimetières de guerre allemands opérés par une association pour le souvenir des soldats tombés et répertoriés dans Wikidata :

![](images/sparql_query.png)

`````
#cemeteries
SELECT ?item ?itemLabel
WHERE
{
  ?item wdt:P31 wd:Q1241568 .
  ?item wdt:P137 wd:Q708567 .

   SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
`````

première ligne : commentaire (titre de la requête)

SELECT : indique les informations issus des résultats à faire figurer en
colonne, en l'occurrence le numéro de l'élément wikidata, son titre, les coordonnées géographiques de l'élément et l'image associée à cet élément.

WHERE {} permet de donner les critères de recherche.

````
?item wdt:P31 wd:Q1241568
````

on retrouve à cet endroit le triplet *item*,
*propriété*, *valeur* : on chercher tous les **items**(=sujet) qui **sont des instances de**(=prédicat) **cimetière militaire**(=objet)

Même chose à la ligne suivante :
````
?item wdt:P137 wd:Q708567 .
````
On cherche à obtenir tous les items qui observent la condition précédente et ont en outre pour propriété d'être opérés par) l'association "Volksbund Deutsche Kriegsgräberfürsorge"

On obtient **9 résultats** sous la forme d'un tableau qui comporte en première colonne l'identifiant de l'élément et en deuxième colonne le label (=nom de l'élément)

### obtenir les coordonnées et les images des éléments obtenus

Si on veut ajouter des colonnes supplémentaires pour obtenir les coordonnées géographiques et les images (liens vers les imagges de ces éléments dans Wikimedia Commons), il faut ajouter **?coord** et **?image** après *SELECT* et deux conditions supplémentaires :

````
?item wdt:P625 ?coord.
?item wdt:P18 ?image.
````
Requête prise dans sa globalité :

````
#cemeteries
SELECT ?item ?itemLabel ?coord ?image
WHERE
{
  ?item wdt:P31 wd:Q1241568 .
  ?item wdt:P137 wd:Q708567 .
  ?item wdt:P625 ?coord.
  ?item wdt:P18 ?image.

   SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
````
On n'obtient plus que **8 résultats** parce que l'élément [Q17353494](https://www.wikidata.org/wiki/Q17353494) ne dispose pas d'image.
On peut néanmoins trouver une image de ce cimetière [sur la Wikipédia allemande](https://de.wikipedia.org/wiki/Deutsche_Kriegsgr%C3%A4berst%C3%A4tte_auf_dem_Georgenberg#/media/Datei:Spremberg_Deutsche_Kriegsgr%C3%A4berst%C3%A4tte_auf_dem_Georgenberg_Zentrale_Sichtachse.JPG)
et compléter le tableau extrait de Wikidata à la main.

![](images/sparql_results.png)

Télécharger les résultats sous la forme d'un fichier CSV et l'ouvrir avec LibreOffice Calc (encodage en UTF8)


# formater le fichier téléchargé de Wikidata

Les actions de formatage qui suivent sont propres au logiciel Calc de la suite [LibreOffice](https://fr.libreoffice.org/) \

- clic droit sur la colonne C  "insérer une colonne après" \

- Sélectionner la colonne C et avec Ctrl+H remplacer *Point(* et *)* par rien pour supprimer ces caractères. \

- sélectionner la colonne C, dans *Données*, sélectionner *textes en colonnes* et utiliser l'espace comme séparateur. On obtient désormais deux colonnes, l'une avec la longitude
(colonne C), l'autre avec la latitude (colonne D). Umap repère les
colonnes dont les entêtes commencent par *lon* et *lat* et les interprête
comme des entête de colonnes dans lesquelles se trouvent les coordonnées
géographiques décimales (48.5678 est correct mais pas 48,5678 ni
48°5678, veiller à ce que les valeurs présentes dans les colonnes soient
bien formatées) \

- afficher comme entête à la colonne C (à la place de coord) *lon* et à la colonne D *lat* \

- renommer la colonne "itemlabel" avec l'entête "nom" (laisser en colonne E l'entête "image") \

## Importer les données dans umap

### Un mot sur Umap et OpenStreetMap

[Umap](umap.openstreetmap.fr/) est un éditeur de cartes qui fonctionne avec les cartes d'[OpenStreetMap](https://www.openstreetmap.org).
IL s'agit d'un éditeur libre en cohérence avec le projet libre qui est derrière OpenStreetMap (OSM).
Lorsqu'on dispose d'un compte sur OSM (si ce n'est pas le cas, [il faudra s'en créer un](https://www.openstreetmap.org/user/new)), on peut l'utiliser pour accéder à Umap et conserver ses propres cartes.
Les associations en faveur du libre utilisent depuis longtemps OpenStreetMap, les institutions s'y sont mises quand en 2018, Google a commencé à faire payer les sites qui ont beaucoup de visiteurs pour leur utilisation de Googlemaps (cf. [article du Monde](https://www.lemonde.fr/chronique-des-communs/article/2018/06/01/hausse-des-tarifs-de-google-maps-on-a-plus-que-jamais-besoin-d-alternatives-libres_5307968_5049504.html?utm_term=Autofeed&utm_campaign=Echobox&utm_medium=Social&utm_source=Twitter#link_time=1527839164))

OpenStreetMap est soutenue par une communauté d'utilisateurs très active, ce qui lui a donné dans bien des cas un avantage sur Google Map ([exemple de la cartographie du Cameroun](https://twitter.com/OSMCameroun/status/1085939637770702848))

### Importer le fichier de données

![importer les données dans Umap](images/import_umap.png)

Ouvrir à droite l'icone "flèche vers le haut", cliquer sur parcourir et charger le fichier de données.

Si l'import du fichier ne se fait pas bien à partir du module d'import, copier les valeurs avec leurs entêtes dans le module d'import ("collez vos données ici").  
Si c'est demandé, spécifiez qu'il s'agit d'un fichier en format CSV.

Des marqueurs devraient apparaître pour indiquer les lieux figurant dans
le jeu de données.

### paramétrer la vignette liée au marqueur

Créer un popup avec une description du marqueur :

Dans le menu de droite de Umap, cliquer sur l'icone "Editer les paramètres" (roue crantée), puis sélectionner "Option d'interaction par défaut", puis "gabarit du contenu de la popup "(suivi d'une infobulle)
A cet endroit, remplacer # {name} par # {nom} et {description} par {image}

Le # indique simplement que la valeur nom va s'afficher sous la forme d'un titre. S'il y avait une légende dans les données ou un titre secondaire on aurait pu ajouter un \#2 pour faire un titre 2
Pour faire en sorte que l'image s'affiche il faut rajouter des accolades autour de images (trois pour ouvrir et trois pour fermer).
Les valeurs attendues sont donc celles-ci :

````
# {nom}
{{{image}}}
````

Pour visualiser les changements, enregistrer les modifications et
désactiver le mode édition

![carte interactive](images/umap1.png)

## exercices d'extraction de données depuis Wikidata.

Réaliser un calque (calque 2) supplémentaire avec l'ensemble des musées maritimes français

Solution :
````
SELECT DISTINCT ?item ?itemLabel ?place ?coord
WHERE {
      ?item wdt:P17 wd:Q142.
      ?item wdt:P31 wd:Q1863818.
      ?item wdt:P625 ?coord.

  SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}
````
Réaliser un calque supplémentaire (calque 3) avec l'ensemble des centrales nucléaires ukrainiennes

Solution :

````

#nuclear plants in Ukraine
SELECT ?item ?itemLabel ?place ?coord
WHERE
{
  ?item wdt:P31 wd:Q134447 .
  ?item wdt:P17 wd:Q212 .
  ?item wdt:P625 ?coord.

   SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
}

````
## Remerciements

Merci à Nicolas Vigneron (\@Belett) qui m'a inité à l'interrogation de Wikipédia au moyen du langage de requêtes SPARQL
