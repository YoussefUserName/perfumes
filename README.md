# "A fragrance for YOU"

***Technique de programmation - Projet de groupe***

*Al Bakaoui Chayma, El Yaakoubi Youssef, He Chunhua*

# Introduction

On a toutes et tous été confrontés à la difficulté de trouver le parfum qui correspondrait à nos envies. Quelle note choisir ? Pour quel budget ? Ceux sont pleins de facteurs qui entrent en jeu lors du processus d’achat. 

Aujourd’hui, la grande majorité des sites web nous permettent d’affiner de plus en plus notre recherche pour obtenir le résultat qui correspondrait au mieux à nos préférences. 
C’est surtout dans une logique de compréhension du processus qui se cache derrière ces sites web, que nous avons décidé de créer un outil qui automatise la tâche de recherche du parfum parfait ; mais aussi un outil de simplification. 

Notre outil vous invite à choisir le nombre de pages que vous souhaitez parcourir, à définir un ordre de tri, que ce soit par recommandation, par ordre de prix croissant ou décroissant etc... Et puis, la partie la plus importante : le note de parfum recherchée. 
Qu'est-ce qu'une note de parfum ?  Un parfum est constitué d’une structure appelée pyramide olfactive du fait que l’on distingue la note de tête tout en haut de la pyramide, la note de cœur au centre, et enfin la note de fond à la base. Notre outil vous offre une multitude de choix de note de tête comme une note orientale, musquée ou encore fruitée par exemple et en fonction de votre choix, il vous recommande le parfum qui possède cette note de tête. 

---
# Objectif

Par le Web Scrapping, nous avons automatiser notre outil, afin qu'il puisse vous fournir une expérience d'achat des plus simple et efficace.

Le choix du site à scrapper repose essentiellement sur la qualité des notes des parfums. Nous avons également choisi arbitrairement de cibler une clientèle féminine bien que la logique du code puisse être appliquée également à partir des parfums pour homme. Dans ce cas, l'URL à scrapper sera différent ; il correspondra au lien renvoyant à la page de parfums pour homme.

Nous avons choisi Notino.fr, l'un des plus grand site de parfum, rassemblant pas moins de 10 000 références. 

---
# Librairies utilisées

les libraires utilisées sont les suivantes : 
- Requests : il s'agit du package qui permet d'envoyer Python faire une requête sur une page HTTP
- Pandas : c'est la librairie qui va nous permettre de manipuler facilement les données à analyser
- BeautifulSoup : ce package va nous permettre l'analyse des documents HTML et XML et une extraction de données par web scrapping 

---
# Utilisation

1. Quelles sont vos envies ? 

Après avoir préalablement installé les packages nécessaires, vient l'étape de l'identification des envies de l'utilisateur. La première partie de notre code est consacrée à la mise en place d'une commande input qui demande à l'utilisateur de rentrer ses préférences pendant que le code tourne, pour lui éviter de rentrer dans le .py. L’utilisateur est invité à choisir le nombre de pages de résultat souhaité, de définir un ordre de tri (c’est-à-dire s’il souhaite que les résultats soient affichés par recommandation, par ordre de prix croissant etc.) ; et puis, le type de parfum qu’il recherche (en termes de note).

Pour ce programme nous allons nous baser sur des fonctions qui vont appeler différentes fonctionnalités.

Concentrons-nous sur la première requête "Entrez le nombre de pages souhaité" ; le code sur .py est le suivant : 

```

def saisie_nombre_de_pages_resultat():
    nb_pages = 0
    while True:
        try:
            nb_pages = int(input("Entrez le nombre de pages de résultat souhaité (minimum 1): "))
        except ValueError:
            print("Veuillez insérer un nombre valide")
            continue
        else:
            if nb_pages <= 0:
                print("Veuillez insérer un nombre supérieur à 0")
                continue
            else:
                break
    return nb_pages + 1

```

Cette boucle prend également en compte la possibilité que l'utilisateur saisisse un nombre invalide ou inférieur à 1. Dans ces deux cas de figures, un message d'erreur est affiché invitant l'utilisateur à retaper un nombre valide et supérieur à 1. La logique reste approximativement la même pour les autres 'fonctions de requêtes' préalables. 

---
2. Création du fichier CSV 

Un fichier CSV est tout simplement un fichier informatique de type tableur. Dans cette troisième partie du code, on récupère les données sur les parfums et on procède à une retranscription sous forme tabulaire dans un fichier CSV. Les données sont : la marque, la collection, le type de produit, le prix pour 100mL et la note. C'est à cette étape où la librairie BeautifulSoup entre en jeu : le code est tel qu'il procède à une récupération des informations associées à chaque donnée prédéfinie dans notre fichier CSV. 

---
# Le Web Scraping

   1. Parfums pour femme

Après avoir défini la page du site à scrapper, dans notre cas l’URL renvoyant aux parfums pour femme, on inspecte la page et on sélectionne les informations que l’on veut extraire : la marque, la collection, le type de produit, le prix pour 100mL et la note. 
Vient ensuite l’étape de l’élaboration du code : les librairies essentielles sont Pandas et BeautifulSoup, on configure le programme sur le lien qu’il doit suivre donc ici celui renvoyant aux parfums pour femme. 

Pour pouvoir 'scrapper' toutes les pages, nous rajoutons les numéros de pages ```str(page)``` qui vont être générés par nos fonctions. Ainsi que le type de tri des parfums ```str(type_tri)```, et enfin, leurs caractéristiques olfactives ```str(type_plist[type_parfum])```.

Les données que l’on souhaite extraire sont imbriquée dans ```<li>``` ; donc on va chercher les mentions ```<li>``` et leur nom de classes respectifs ```class```, extraire les données ```item``` et les stocker dans une variable ```url```. 

Cette partie du code est dédiée à orienter notre programme à récupérer uniquement les données concernant les parfums pour femme. 

```
def get_page(num_page, s, type_parfum, type_plist, type_tri):
    url = "https://www.notino.fr/parfums-femme/?f=" + \
      str(num_page) + '-' + str(type_tri) + "-55544-55545" + str(type_plist[type_parfum])

    res = s.post(url)
    if res.url not in urls:
        urls.append(url)
        soup = bs4(res.text, "lxml")
        return soup.find_all("li", {"class": "item"})

return None
```
---
2. Filtrer selon les caractérisitques

Dans une seconde partie, une fonction est programmée de sorte à récupérer les informations correspondantes aux caractéristiques des parfums, à partir du code html du site à scrapper. 

```
def traitement_parfums(parfums, produits_parfum_csv):
    for parfum in parfums:
        marque = parfum.find("span", class_="brand").text
        type_produit = verification_champ_type_produit(parfum.find("span", class_="subname"))
        prix = verification_champ_prix(parfum.find("span", class_="unit-price"))
        collection = verification_champ_collection(parfum.find("span", class_="name"))
        note = verification_champ_note(str(parfum.find("span", class_="product-rating")))
        ajout_parfum_csv(collection, marque, note, produits_parfum_csv, type_produit, prix)
```
---    
3. Alimentation du fichier CSV 

Pour chaque information extraite, le fichier est alimenté : 
```
def ajout_code_produits_parfum_csv(produits_parfum_csv):
    df = pd.read_csv(produits_parfum_csv)
    df.insert(0, "Code", [i for i in range(0, df.index.stop)])
    df.to_csv(produits_parfum_csv, mode='w+', index=False, header=True)



def programme_principal():
    saisie = saisie_utilisateur()
    nb_pages = saisie['nb_pages']
    type_tri = saisie['type_tri']
    type_parfum = saisie['type_parfum']
    type_plist = [
        -34693, -34696, -57258, -34691, -57259,
        -34694, -57257, -34700, -34699, -34702,
        -34697, -34698, ""
    ]
    produits_parfum_csv = "produits_parfum.csv"
    creation_fichier_csv(produits_parfum_csv)
    s = requests.session()

    for num_page in range(1, nb_pages):
        parfums_page_num_page = get_page(num_page, s, type_parfum, type_plist, type_tri)
        if parfums_page_num_page is not None:
            traitement_parfums(parfums_page_num_page, produits_parfum_csv)
            
    ajout_code_produits_parfum_csv(produits_parfum_csv)
    afficher_resutat(produits_parfum_csv)
    
 def afficher_resutat(produits_parfum_csv):
    df = pd.read_csv(produits_parfum_csv)
    print(df)
```

Pour pouvoir 'scrapper' toutes les pages, nous rajoutons les numéros de pages ```nb_pages``` qui vont être générés par nos fonctions. Ainsi que le type de tri des parfums ```type_tri```, et enfin, leurs caractéristiques olfactives ```type_list```.

Ce dernier code est une fonction ```def programme_principal()``` qui relie les fonctions du départ qui apportent les commandes input à notre programme. Y sont ajoutés, les caractérstiques des parfums ```type_plist``` qui correspondent aux codes URL du filtre qui est mis aux parfums sur le site selon leur note de tête.

---
# Lancement du programme et résultats obtenus
```
if __name__ == '__main__':
    programme_principal()
```
La commande ```if __name__ == '__main__'``` permet de contourner la distinction que va faire Python entre le module principal ```__main__``` et le module importé ```__name__```. Ici nous avons des fonctions créées (donc des modules importés) qui doivent fusionner entre elles, il faut alors toutes les rassembler dans le même module principal ```__main__```.
Puis nous exécutons notre programme.

Lors de l'éxécution du programme, vous aurez sûrement un nombre conséquent de résultats. Selon la plateforme sur laquelle vous lancer le programme, ceux-ci seront plus ou moins visibles dans leur totalité. N'hésitez pas à jeter un oeil dans vos documents, un fichier csv ```produits_parfum.csv``` vous y attend ;)

# Difficultés et Critiques

1. Difficultés 

Lors de la création de ce programme d'automatisation nous avons rencontrés plusieurs difficultés. L'une d'elle a été le fait de voir que la première page n'avait pas le même ```url``` que les autres pages, il nous a fallu un peu de temps pour contourner le problème. Ensuite, nous nous sommes confrontés à la difficulté de scrapper les prix et les notes des consommateurs. Dans le code ```html``` du site web, les prix sont précédés de chaînes de caractères, pour contourner ce problème il nous a fallu un certain temps. De même pour les notes des consommateurs où une autre difficulté s'est ajoutée : les étoiles de 1 à 5 sont (en nombre entier) sur 70, ce qui n'est pas facilement détéctable à première vue sur le code du site web. 
    
Aussi, nous avions au départ un code qui formait une grosse boucle. Grâce à vos conseils que nous avons appliqués, nous avons créé plusieurs fonctions qui se répondent au fil du code, réadapter le programme a aussi été une difficulté que nous avons rencontré. 

Enfin, après plusieurs recherches, nous avons découvert qu'il était possible que les développeurs d'un site web mettent souvent à jour le code de celui-ci. Nos codes ont souvent fonctionné un jour, puis plus tout un autre jour. Cela s'est limité à un changement de ```<li>``` par des ```<div>``` et vis-verça, heureusement. 

2. Critique

Nous avions comme idée au départ, de créer un programme qui traiterait de toutes les notes d'un parfum (de tête mais aussi de coeur et de fond). Ainsi, l'expérience aurait été totale, avec plusieurs milliers de parfums filtrés en seulement une petite dixaine. Nous nous sommes vite rendu compte que pour réaliser un tel projet, il aurait fallu récupérer toutes les ```url``` de tous les parfums individuellement, et par la suite récupérer toutes les notes en référence dans les liens de ces parfums. Nous n'avons pas trouvé de méthode, par manque de temps et d'expérience, mais nous avons pour objectif de réaliser ce projet à l'avenir. Ce travail nous a donné pleins d'idées. 

**Merci !**
