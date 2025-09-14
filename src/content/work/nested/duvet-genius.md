---
title: Vision Artificielle et Reconnaissance {python}
publishDate: 2024-07-15 00:00:00
img: /assets/stock-3.jpg
img_alt: Processus d'extraction de caractéristiques avec des graphiques vibrants
description: |
  Ce projet implique le développement d'une application d'extraction de caractéristiques et de calcul de distances utilisant Python et Flask.
tags:
  - Python
  - Anaconda
  - OpenCV
  - Streamlit
---

## Aperçu du Projet

Ce projet démontre le développement d'une application d'extraction de caractéristiques et de calcul de distances utilisant Python et Flask. Il inclut la mise en œuvre d'une API RESTful pour extraire les caractéristiques d'images et calculer les distances entre celles-ci. Le projet met en avant divers aspects du développement back-end, notamment le traitement d'images, l'extraction de caractéristiques et la création d'API.  
[dépôt GitHub](https://github.com/RedaBenzakour/ia-2).

### Technologies Utilisées

- **Back-end** : Python, Flask  
- **Traitement d'Images** : OpenCV  
- **Extraction de Caractéristiques** : SIFT (Scale-Invariant Feature Transform)  
- **Calcul de Distances** : Distance euclidienne  
- **Stockage des Données** : NumPy  

### Structure du Projet

Le projet est structuré comme suit :  

#### Scripts Principaux

1. **app.py** : Application Flask principale pour gérer les requêtes API.  
2. **descriptor.py** : Script pour l'extraction de caractéristiques utilisant SIFT.  
3. **distances.py** : Script pour calculer les distances entre ensembles de caractéristiques.  

#### Fichiers de Données

1. **iris.jpg** : Image d'exemple pour tester l'extraction de caractéristiques.  
2. **signatures.npy** : Fichier NumPy contenant des signatures pré-calculées.  
3. **temp_image.png** : Fichier image temporaire pour le traitement.  

### Fonctionnalités Clés

- **API d'Extraction de Caractéristiques** : Extrait les caractéristiques SIFT des images téléchargées.  
- **API de Calcul de Distances** : Calcule la distance euclidienne entre deux ensembles de caractéristiques.  
- **Traitement d'Images** : Utilise OpenCV pour les tâches de traitement d'images.  
- **API RESTful** : Créée en utilisant Flask pour gérer les requêtes et réponses HTTP.  

#### Lien GitHub

Pour accéder au code source complet de ce projet, visitez le [dépôt GitHub](https://github.com/RedaBenzakour/ia).

### Exemple de Code

Voici un exemple du code de l'application principale (**app.py**) démontrant la fonctionnalité d'extraction de caractéristiques et de calcul de distances :

#### app.py

```python
from flask import Flask, request, jsonify
import numpy as np
import cv2
import descriptor
import distances

app = Flask(__name__)

# Route pour extraire les caractéristiques d'une image
@app.route('/extract_features', methods=['POST'])
def extract_features():
    file = request.files['image']
    image = cv2.imdecode(np.frombuffer(file.read(), np.uint8), cv2.IMREAD_COLOR)
    
    features = descriptor.describe(image)
    return jsonify(features.tolist())

# Route pour calculer la distance entre deux jeux de caractéristiques
@app.route('/compute_distance', methods=['POST'])
def compute_distance():
    data = request.json
    features1 = np.array(data['features1'])
    features2 = np.array(data['features2'])
    
    distance = distances.euclidean(features1, features2)
    return jsonify({'distance': distance})

if __name__ == '__main__':
    app.run(debug=True)
