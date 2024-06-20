1. Configuration Initiale:
  ● Importation des bibliothèques nécessaires.
    ○ import requests
    ○ import pdb
    ○ import re
    ○ import os
    ○ import random
    ○ from bs4 import BeautifulSoup
    ○ import numpy as np
    ○ import matplotlib.pyplot as plt
    ○ import pandas as pd
  ● Configuration des paramètres de base (en-têtes HTTP, URL de base, etc.).
    ○ headers = {"User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:109.0)
    Gecko/20100101 Firefox/115.0"}
    ○ base_url = 'https://reelgood.com/'
2. Fonctions de Support:
  ● fetch_url: Fonction pour récupérer les URLs avec une gestion des tentatives.
    ○ j’ai mis une fonction fetch_url pour que chaque fois je veux
    connecter sur le lien je ne utilise pas le try.
  ● extract_categories: Fonction pour extraire les URLs des catégories à partir de
    la page d'accueil.
    ○ A chaque fois je connecte sur les liens je utilise la pattern_catégories pour
    récupérer les trois type dans reelgood
3. Traitement des Catégories:
  ● process_category: A chaque fois je récupère le nom de type dans site je crée un
  nouveau dossier qui porte son nom.
4. Traitement des Sous-catégories et Sources:
  ● process_subcategories_and_sources: Pour traiter les sous-catégories et les
  sources spécifiques à chaque catégorie. je utilise une nouvelle pattern qui s’appelle
  pattern_subcategories mais cela fonctionne pour les liens films et séries dans
  chaqu’un il ya trois type source, List et Genre Mais pour new non car quand j’entre
  dans new je trouve trois type Coming, new et leaving alors quand je connecte direct
  je trouve que je dans new/new pour ça j’ai crée une subcatgories ['new', 'coming',
  'leaving'] pour category/name =’new’
5. Extraction d'Informations:
  ○ je me connecte sur les liens dans chaque sous categorie par exemple le
  doctor-who-2005 dans sous catégorie action et aventure dans la type source dans la
  categorie film.
    i. # Extraire les informations avec BeautifulSoup et regex
    ii. title = soup.find('h1', class_='css-hiz1q1 e3s42hj0').text if soup.find('h1',
    class_='css-hiz1q1 e3s42hj0') else None
    iii. year = re.search(r'<div class="e1dskkhw7 css-1i5l3f2
    e83cah30">((.*?))</div>', content)
    iv. reelgood_rating = re.search(r'<div><span class="css-1ycvxny
    ey9061w1">(.{1,2})</span>', content)
    v. imdb_rating = re.search(r'</style><span class="css-1ycvxny
    ey9061w1">(.*?)</span>', content)
    vi. duration = re.search(r'<span duration=".*?" class="css-1r35rcf
    e1dskkhw11">(.*?)</span>', content)
    vii. genres = re.findall(r'href="/movies/genre/(.*?)"', content)
    viii. équipages = re.findall(r'<p class="css-si6acb
    eytn0nr3">(.{1,20})</p></a></div><div spacing="16px" class="css-zmol8d
    eytn0nr5"></div>', content)
    ix. origin = re.search(r'href="/origin/(.*?)"', content)
    x. availability = re.findall(r'<span title="(.*?)" class=', content)
  ○ après j’ai mois les variables dans un dictionnaire avec les données extraites
6. Extraire les donnée à partir des fichiers HTML:
  ○ j’ai utiliser la fonction precedente pour mettre tout les données dans une dataframe
7. Analyse et Visualisation:
  ○ analyser les données manquantes
  ○ nettoyages de données
  ○ analyser les données
Configuration de l'Environnement
Structure des Dossiers
Pour garantir un fonctionnement correct, l'environnement de travail doit être organisé
comme suit :
├── Pages/
│ ├── tv/
│ │ ├── Source/
│ │ │ ├── apple-tv
│ │ │ ├── netflix
│ │ │ └── ...
│ │ ├── Genre/
│ │ │ ├── action-and-adventure
│ │ │ ├── animation
│ │ │ ├── anime
│ │ │ ├── biography
│ │ │ ├── children
│ │ │ └── ...
│ │ ├── List/
│ │ │ ├── baseball
│ │ │ ├── college
│ │ │ ├── fashing
│ │ │ ├── games
│ │ │ ├── feel-good
│ │ │ └── ...
│ ├── movies/
│ │ ├── Source/
│ │ │ ├── apple-tv
│ │ │ ├── netflix
│ │ │ └── ...
│ │ ├── Genre/
│ │ │ ├── action-and-adventure
│ │ │ ├── animation
│ │ │ ├── anime
│ │ │ ├── biography
│ │ │ ├── children
│ │ │ └── ...
│ │ ├── List/
│ │ │ ├── africa
│ │ │ ├── animal
│ │ │ ├── car
│ │ │ ├── boxing
│ │ │ └── ...
│ ├── new/
│ │ ├── New/
│ │ │ ├── amazon
│ │ │ ├── Disney_plus
│ │ │ ├── Peacock
│ │ │ └── ...
│ │ ├── Coming/
│ │ │ ├── Netflix
│ │ │ ├── Hbo
│ │ │ ├── Hulu
│ │ │ └── ...
│ │ ├── Leaving/
│ │ │ ├── Amazon
│ │ │ ├── Apple_tv_plus
│ │ │ ├── Hbo
│ │ │ └── ...
├── projet_web_scraping.ipynb
├── scraping.log
└── projet web scarping.pdf
