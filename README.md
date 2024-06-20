### README Amélioré

Voici une version améliorée et réorganisée de votre fichier README pour le projet de web scraping. J'ai clarifié certaines sections, corrigé la grammaire et ajouté des explications pour plus de clarté.

---

# Projet de Web Scraping

Ce projet consiste à extraire des données de la plateforme Reelgood en utilisant Python. Les données extraites sont ensuite analysées et visualisées.

## 1. Configuration Initiale

### Importation des bibliothèques nécessaires

```python
import requests
import pdb
import re
import os
import random
from bs4 import BeautifulSoup
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
```

### Configuration des paramètres de base

```python
headers = {
    "User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
}
base_url = 'https://reelgood.com/'
```

## 2. Fonctions de Support

### Fonction `fetch_url`

Cette fonction permet de récupérer les URLs avec une gestion des tentatives.

```python
def fetch_url(url, headers, max_attempts=10):
    for attempt in range(max_attempts):
        timeout = random.randint(1, 10)
        try:
            response = requests.get(url, headers=headers, timeout=timeout)
            response.raise_for_status()
            print(f'Successfully fetched {url}')
            return response
        except requests.RequestException as e:
            print(f'Attempt {attempt + 1} failed for {url} with timeout {timeout}: {e}')
    return None
```

### Fonction `extract_categories`

Cette fonction permet d'extraire les URLs des catégories à partir de la page d'accueil.

```python
response = fetch_url(base_url, headers)
if response:
  contenu = response.text
  pattern_categories = '<a class="css-7s3mks ewkc9rp0" aria-expanded="false" href="(.*?)">'
  categories_urls = re.findall(pattern_categories, contenu)
```

## 3. Traitement des Catégories



Cette fonction crée un dossier pour chaque catégorie et récupère son contenu.

```python
for category_url in categories_urls:
  category_url_full = f"https://reelgood.com{category_url}"
  category_name = category_url.split('/')[-1]
  folder = f"Pages/{category_name}"
  os.makedirs(folder, exist_ok=True)
  category_response = fetch_url(category_url_full, headers, max_attempts=10)
  if category_response:
    category_content = category_response.text
```

## 4. Traitement des Sous-catégories et Sources



Cette fonction traite les sous-catégories et les sources spécifiques à chaque catégorie.

```python
# Extraire les subcategories dynamiquement
pattern_subcategories = f'href="/{category_name}/(.*?)"'
subcategories = re.findall(pattern_subcategories, category_content)
for subcategory in subcategories:
    subcategory_folder = os.path.join(folder, subcategory.capitalize())
  os.makedirs(subcategory_folder, exist_ok=True)
  subcategory_url_full = f"{category_url_full}/{subcategory}"
  subcategory_response = fetch_url(subcategory_url_full, headers, max_attempts=10)
  if subcategory_response:
    subcategory_content = subcategory_response.text
    '''
    Extraire les liens dans chaque sous categorie par exemple le doctor-who-2005 dans sous catégorie dans la type source dans la categorie film
    '''
    pattern_links = r'"content-listing-link" href="(.*?)" class="css-0 ewkc9rp0"'
    links = re.findall(pattern_links, subcategory_content)
```

## 5. Extraction d'Informations

### Exemple d'extraction d'informations

```python
def extract_info_from_html(file_path):
  with open(file_path, 'r', encoding='utf8') as file:
  content = file.read()
  soup = BeautifulSoup(content, 'html.parser')
  # Extraire les informations avec BeautifulSoup et regex
  title = soup.find('h1', class_='css-hiz1q1 e3s42hj0').text if soup.find('h1', class_='css-hiz1q1 e3s42hj0') else
  year = re.search(r'<div class="e1dskkhw7 css-1i5l3f2 e83cah30">((.*?))</div>', content)
  reelgood_rating = re.search(r'<div><span class="css-1ycvxny ey9061w1">(.{1,2})</span>', content)
  imdb_rating = re.search(r'</style><span class="css-1ycvxny ey9061w1">(.*?)</span>', content)
  duration = re.search(r'<span duration=".*?" class="css-1r35rcf e1dskkhw11">(.*?)</span>', content)
  genres = re.findall(r'href="/movies/genre/(.*?)"', content)
  équipages = re.findall(r'<p class="css-si6acb eytn0nr3">(.{1,20})</p></a></div><div spacing="16px" class="css-zmol8d origin = re.search(r'href="/origin/(.*?)"', content)
  availability = re.findall(r'<span title="(.*?)" class=', content)
  # Créer un dictionnaire avec les données extraites
  data = {
  'Title': title,
  'Year': year.group(1) if year else None,
  'Reelgood Rating': reelgood_rating.group(1) if reelgood_rating else None,
  'IMDB Rating': imdb_rating.group(1) if imdb_rating else None,
  'Duration': duration.group(1) if duration else None,
  'Genres': ', '.join(genres),
  'Equipages': ', '.join(équipages),
  'Origin': origin.group(1) if origin else None,
  'Availability': list(set(availability)) # Utiliser set pour supprimer les duplications
  }
  return data
```

## 6. Extraction des données à partir des fichiers HTML

Utilisation de la fonction précédente pour mettre toutes les données dans une dataframe.

```python
data_list = []
for root, dirs, files in os.walk('Pages'):
  for file in files:
    if file.endswith('.html'):
      file_path = os.path.join(root, file)
      data = extract_info_from_html(file_path)
      data_list.append(data)
df = pd.DataFrame(data_list)
df.to_csv('web_scraping.csv')
```

## 7. Nettoyer et Analyser

### Nettoyer des données manquantes

```python
colonnes_vides = df.isna().sum()
# Calculer le pourcentage de colonnes vides par rapport aux colonnes non vides
pourcentage_colonnes_vides = (colonnes_vides / len(df)) * 100
pourcentage_colonnes_non_vides = 100 - pourcentage_colonnes_vides
# Créer un graphique à barres
plt.figure(figsize=(10, 6))
pourcentage_colonnes_vides.plot(kind='bar', color='red', alpha=0.7, label='Colonnes vides')
pourcentage_colonnes_non_vides.plot(kind='bar', color='blue', alpha=0.7, label='Colonnes non vides')
plt.title('Pourcentage de colonnes vides par rapport aux colonnes non vides')
plt.xlabel('Colonnes')
plt.ylabel('Pourcentage')
plt.legend()
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
# Créer un graphique circulaire
plt.figure(figsize=(8, 8))
plt.pie([pourcentage_colonnes_vides.sum(), pourcentage_colonnes_non_vides.sum()],labels=['Colonnes vides', 'Colonnes non vides'],colors=['red', 'blue'],autopct='%1.1f%%',startangle=140)
plt.title('Pourcentage de colonnes vides par rapport aux colonnes non vides')
plt.axis('equal')
plt.tight_layout()
```

### Analyser des données

```python
# Filtrer les lignes avec des valeurs non numériques dans la colonne 'IMDB Rating'
df_clean = df[pd.to_numeric(df['IMDB Rating'], errors='coerce').notna()]
# Convertir les données de la colonne 'IMDB Rating' en nombres (float)
df_clean.loc[:, 'IMDB Rating'] = df_clean['IMDB Rating'].astype(float)
# Définir les intervalles personnalisés pour l'évaluation IMDb
intervals_x = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
# Calculer l'histogramme avec les intervalles personnalisés
hist, bins = np.histogram(df_clean['IMDB Rating'], bins=intervals_x)
# Tracer l'histogramme
plt.bar(bins[:-1], hist, color='skyblue', edgecolor='black')
plt.xlabel('IMDB Rating')
plt.ylabel('Nombre de films')
plt.title('Distribution des évaluations IMDb')
plt.xticks(intervals_x) # Utiliser les intervalles comme marqueurs sur l'axe des x
plt.show()


```

## Configuration de l'Environnement

### Structure des Dossiers

Pour garantir un fonctionnement correct, l'environnement de travail doit être organisé comme suit :

```
├── Pages/
│   ├── tv/
│   │   ├── Source/
│   │   │   ├── apple-tv
│   │   │   ├── netflix
│   │   │   └── ...
│   │   ├── Genre/
│   │   │   ├── action-and-adventure
│   │   │   ├── animation
│   │   │   ├── anime
│   │   │   ├── biography
│   │   │   ├── children
│   │   │   └── ...
│   │   ├── List/
│   │   │   ├── baseball
│   │   │   ├── college
│   │   │   ├── fishing
│   │   │   ├── games
│   │   │   ├── feel-good
│   │   │   └── ...
│   ├── movies/
│   │   ├── Source/
│   │   │   ├── apple-tv
│   │   │   ├── netflix
│   │   │   └── ...
│   │   ├── Genre/
│   │   │   ├── action-and-adventure
│   │   │   ├── animation
│   │   │   ├── anime
│   │   │   ├── biography
│   │   │   ├── children
│   │   │   └── ...
│   │   ├── List/
│   │   │   ├── africa
│   │   │   ├── animal
│   │   │   ├── car
│   │   │   ├── boxing
│   │   │   └── ...
│   ├── new/
│   │   ├── New/
│   │   │   ├── amazon
│   │   │   ├── Disney_plus
│   │   │   ├── Peacock
│   │   │   └── ...
│   │   ├── Coming/
│   │   │   ├── Netflix
│   │   │   ├── Hbo
│   │   │   ├── Hulu
│   │   │   └── ...
│   │   ├── Leaving/
│   │   │   ├── Amazon
│   │   │   ├── Apple_tv_plus
│   │   │   ├── Hbo
│   │   │   └── ...
├── projet_web_scraping.ipynb
├── scraping.log
└── projet_web_scraping.pdf
```

---

Cette structure garantit une organisation claire et facilite la gestion des différentes étapes
