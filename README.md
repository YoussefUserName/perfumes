# A desire and its perfume
Technique de programmation - Projet de groupe

Al Bakaoui Chayma, El Yaakoubi Youssef, He Chunhua

***Introduction***

On a toute et tous été confrontés à la difficulté de trouver LE parfum qui correspondrait à nos envies même les plus précises. Quelle note choisir ? Pour quel budget ? Ce sont pleins de facteurs qui entrent en jeu lors du processus d’achat. 

Aujourd’hui, la grande majorité des sites web nous permettent d’affiner de plus en plus notre recherche pour obtenir le résultat qui correspond au mieux à nos préférences. 
C’est surtout dans une logique de compréhension du processus qui se cache derrière ces sites web que nous avons décidé de créer un outil qui automatise la tâche de recherche du parfum parfait ; mais aussi de simplification. 

Pour être plus précis, notre outil vous invite à choisir le nombre de page de résultat que vous souhaitez obtenir, à définir un ordre de tri, que ce soit par recommandation, par ordre de prix croissant ou décroissant etc. et puis, la partie la plus importante : le note de parfum recherchée. 
Mais qu’est-ce que c’est ?  Un parfum est constitué d’une structure appelée pyramide olfactive du fait que l’on distingue la note de tête tout en haut de la pyramide, la note de cœur au centre, et enfin la note de fond à la base. Notre outil vous offre une multitude de choix de note de tête comme une note orientale, musquée ou encore fruitée par exemple et en fonction de votre choix, il vous recommande le parfum qui possède cette note de tête. 


***Objectif***

Par une technique nommée le Web Scraping, notre outil vous fournit, de la façon la plus simple et la plus rapide, le parfum qui correspond à vos envies, même les plus précises !

En effet, le Web Scraping nous permet de collecter une quantité importante d’information à partir de sites internet. C’est une technique permise via un programme mais aussi un logiciel automatique ou un autre site. 

Le choix du site à scrapper repose essentiellement sur la qualité des notes des parfums. Nous avons également choisi arbitrairement de cibler une clientèle féminine bien que la logique du code puisse être appliquée également à partir des parfums pour homme. Dans ce cas, l'URL à scrapper sera différent ; il correspondra au lien renvoyant à la page de parfums pour homme.

***Librairies utilisées ***

les libraires utilisées sont les suivantes : 
- Requests : il s'agit du package qui permet d'envoyer Python faire une requête sur une page HTTP
- Pandas : c'est la librairie qui va nous permettre de manipuler facilement les données à analyser
- BeautifulSoup : ce package va nous permettre l'analyse des documents HTML et XML et une extraction de données

***Utilisation* **

1. Quelles sont vos envies ? 

Après avoir préalablement installé les packages essentiels, vient l'étape de l'identification des envies de l'utilisateur. La première partie de notre code est consacrée à la mise en place d'une commande input qui demande à l'utilisateur de rentrer ses préférences pendant que le code tourne, pour lui éviter de rentrer dans le .py. L’utilisateur est invité à choisir le nombre de pages de résultat souhaité, de définir un ordre de tri (c’est-à-dire s’il souhaite que les résultats soient affichés par recommandation, par ordre de prix croissant etc.) ; et puis, le type de parfum qu’il recherche. En d’autres termes, les notes ou encore les caractéristiques recherchées dans le parfum (oriental, musqué, fruité etc.). 

Concentrons-nous sur la première requête "Entrez le nombre de page souhaité" ; le code sur .py est le suivant : 

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

Cette boucle prend également en compte la possibilité que l'utilisateur saisisse un nombre invalide ou inférieur à 1. Dans ces deux cas de figures, un message d'erreur est affichée invitant l'utilisateur à retaper un nombre valide et supérieur à 1. La logique reste approximativement la même pour les autres questions préalables. 

2. Création du fichier CSV 

Un fichier CSV est tout simplement un fichier informatique de type tableur. Dans cette troisième partie du code, on récupère les données sur les parfums et on procède à une retranscription sous forme tabulaire dans un fichier CSV. Les données sont en particulier la marque, la collection, le type de produit, le prix pour 100mL et la note. C'est à cette étape où la librairie BeautifulSoup entre en jeu : le code est tel qu'il procède à une récupération des informations associées à chaque donnée prédéfinie dans notre fichier CSV. 

***Le Web Scraping***

1. Parfums pour femme

Après avoir défini la page du site à scrapper, dans notre cas l’URL renvoyant aux parfums pour femme, on inspecte la page et on sélectionne les informations que l’on veut extraire : la marque, la collection, le type de produit, le prix pour 100mL et la note. 
Viens ensuite l’étape de l’élaboration du code : les librairies essentielles sont Pandas et BeautifulSoup sentrent en jeu, on configure le programme sur le lien qu’il doit suivre donc ici celui renvoyant aux parfums pour femme. 
Les données que l’on souhaite extraire sont imbriquée dans <li> ; donc on va chercher les mentions <li> et leur nom de classes respectifs, extraire les données et les stocker dans une variable. 

Cette partie du code est dédiée à orienter notre programme à récupérer uniquement les données concernant les parfums pour femme. 

def get_page(num_page, s, type_parfum, type_plist, type_tri):
    url = "https://www.notino.fr/parfums-femme/?f=" + \
          str(num_page) + '-' + str(type_tri) + "-55544-55545" + str(type_plist[type_parfum])

    res = s.post(url)
    if res.url not in urls:
        urls.append(url)
        soup = bs4(res.text, "lxml")
        return soup.find_all("li", {"class": "item"})

    return None

2. Ces envies dans les parfums pour femme

Dans une seconde partie, le code est programmé de sorte à récupérer les informations correspondantes aux envies de l'utilisateur à partir et uniquement à partir de lien URL de parfums pour femme. 

def traitement_parfums(parfums, produits_parfum_csv):
    for parfum in parfums:
        marque = parfum.find("span", class_="brand").text
        type_produit = verification_champ_type_produit(parfum.find("span", class_="subname"))
        prix = verification_champ_prix(parfum.find("span", class_="unit-price"))
        collection = verification_champ_collection(parfum.find("span", class_="name"))
        note = verification_champ_note(str(parfum.find("span", class_="product-rating")))
        ajout_parfum_csv(collection, marque, note, produits_parfum_csv, type_produit, prix)

3. Alimentation du fichier CSV 

Pour chaque information extraite, le fichier est en quelque sorte alimenté ; cela grâce au code suivant : 

def ajout_code_produits_parfum_csv(produits_parfum_csv):
    df = pd.read_csv(produits_parfum_csv)
    df.insert(0, "Code", [i for i in range(0, df.index.stop)])
    df.to_csv(produits_parfum_csv, mode='w+', index=False, header=True)



def programme_principal():
    saisie = saisie_utilisateur()
    nb_pages = saisie['nb_pages']
    type_tri = saisie['type_tri']
    type_parfum = saisie['type_parfum']

    #Variables à decommentez pour tester facilement l'application sans avoir les questions..
    #nb_pages = 40
    #type_tri = 9
    #type_parfum = 0
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

Fichier à partir duquel les informations vont être extraites pour donner réponse au programme. 

***Lancement du programme et résultats obtenus* * 

def afficher_resutat(produits_parfum_csv):
    df = pd.read_csv(produits_parfum_csv)
    print(df)

if __name__ == '__main__':
    programme_principal()

