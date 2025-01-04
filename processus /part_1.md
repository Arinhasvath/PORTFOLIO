# Guide de Base - Création d'un Site E-commerce FMP (For My Printer)

## Chapitre 1 : Les Fondamentaux

### 1.1 Qu'est-ce qu'un site e-commerce ?
Un site e-commerce est une plateforme en ligne qui permet de :
- Présenter des produits
- Permettre aux clients d'acheter en ligne
- Gérer les commandes et le stock
- Traiter les paiements

### 1.2 Les composants essentiels
Un site e-commerce comprend plusieurs parties fondamentales :

1. **Frontend** (ce que l'utilisateur voit)
   - Pages produits
   - Panier d'achat
   - Formulaires de commande
   - Interface utilisateur

2. **Backend** (la partie serveur)
   - Gestion des données
   - Traitement des commandes
   - Sécurité
   - API

3. **Base de données**
   - Stockage des produits
   - Informations clients
   - Historique des commandes

## Chapitre 2 : Préparation de l'Environnement

### 2.1 Les outils nécessaires

1. **Éditeur de code**
   - VS Code (recommandé)
   - Installation : [https://code.visualstudio.com/](https://code.visualstudio.com/)
   - Extensions utiles :
     - Python
     - Live Server
     - Git
     - HTML CSS Support

2. **Python**
   - Téléchargement : [https://www.python.org/downloads/](https://www.python.org/downloads/)
   - Version recommandée : 3.8 ou supérieure
   - Vérifier l'installation :
     ```bash
     python --version
     ```

3. **Git**
   - Système de versioning
   - Installation : [https://git-scm.com/downloads](https://git-scm.com/downloads)

### 2.2 Structure initiale du projet
```
FMP/
├── README.md           # Documentation du projet
├── .gitignore         # Fichiers à ignorer par Git
├── backend/           # Code Python/Flask
└── frontend/          # Code HTML/CSS/JS
```

## Chapitre 3 : Premiers Pas

### 3.1 Création du projet

1. **Créer le dossier du projet**
```bash
# Créer le dossier principal
mkdir FMP
cd FMP

# Créer les sous-dossiers principaux
mkdir backend frontend
```

2. **Initialiser Git**
```bash
git init
```

3. **Créer le .gitignore**
```
# Fichier .gitignore
__pycache__/
venv/
*.pyc
.env
.DS_Store
```

### 3.2 Configuration de l'environnement Python

1. **Créer l'environnement virtuel**
```bash
# Dans le dossier backend
cd backend
python -m venv venv

# Activer l'environnement
# Sur Windows :
venv\Scripts\activate
# Sur Linux/Mac :
source venv/bin/activate
```

2. **Installer les dépendances de base**
```bash
pip install flask flask-sqlalchemy python-dotenv
pip freeze > requirements.txt
```

## Chapitre 4 : Structure Détaillée

### 4.1 Structure Frontend
```
frontend/
├── static/
│   ├── css/
│   │   └── style.css      # Styles principaux
│   ├── js/
│   │   └── main.js        # JavaScript principal
│   └── images/            # Images du site
└── pages/
    └── index.html         # Page d'accueil
```

### 4.2 Structure Backend
```
backend/
├── app/
│   ├── __init__.py        # Configuration Flask
│   ├── routes/            # Endpoints API
│   └── models/            # Modèles de données
├── config.py              # Configuration
└── run.py                # Point d'entrée
```

## Chapitre 5 : Première Page

### 5.1 Créer la page d'accueil (frontend/pages/index.html)
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FMP - For My Printer</title>
    <link rel="stylesheet" href="../static/css/style.css">
</head>
<body>
    <header>
        <h1>FMP - For My Printer</h1>
        <nav>
            <ul>
                <li><a href="index.html">Accueil</a></li>
                <li><a href="products.html">Produits</a></li>
                <li><a href="cart.html">Panier</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <h2>Bienvenue sur FMP</h2>
        <p>Votre destination pour les cartouches d'imprimante</p>
    </main>

    <footer>
        <p>&copy; 2024 FMP - For My Printer</p>
    </footer>

    <script src="../static/js/main.js"></script>
</body>
</html>
```

### 5.2 Styles de base (frontend/static/css/style.css)
```css
/* Reset de base */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    line-height: 1.6;
}

header {
    background-color: #333;
    color: white;
    padding: 1rem;
}

nav ul {
    list-style: none;
}

nav ul li {
    display: inline;
    margin-right: 1rem;
}

nav ul li a {
    color: white;
    text-decoration: none;
}

main {
    padding: 2rem;
}

footer {
    background-color: #333;
    color: white;
    text-align: center;
    padding: 1rem;
    position: fixed;
    bottom: 0;
    width: 100%;
}
```

## Chapitre 6 : Configuration Backend

### 6.1 Configuration Flask (backend/app/__init__.py)
```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from config import Config

db = SQLAlchemy()

def create_app():
    app = Flask(__name__)
    app.config.from_object(Config)

    db.init_app(app)

    @app.route('/')
    def index():
        return {'message': 'FMP API is running'}

    return app
```

### 6.2 Configuration de base (backend/config.py)
```python
import os
from dotenv import load_dotenv

load_dotenv()

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'dev-key-change-me'
    SQLALCHEMY_DATABASE_URI = 'sqlite:///fmp.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
```

### 6.3 Point d'entrée (backend/run.py)
```python
from app import create_app

app = create_app()

if __name__ == '__main__':
    app.run(debug=True)
```

## Chapitre 7 : Prochaines Étapes

### 7.1 Liste des tâches à suivre
1. Créer la base de données
2. Ajouter les modèles de données
3. Créer les routes API
4. Développer l'interface utilisateur
5. Implémenter le panier
6. Ajouter l'authentification
7. Gérer les paiements

### 7.2 Commandes utiles pour démarrer
```bash
# Démarrer le serveur Flask
cd backend
python run.py

# Ouvrir le site dans le navigateur
# Ouvrir frontend/pages/index.html avec Live Server dans VS Code
```

## Bonnes Pratiques

1. **Versioning**
   - Commit régulièrement
   - Messages de commit clairs
   - Une fonctionnalité = une branche

2. **Organisation**
   - Commenter le code
   - Suivre une structure cohérente
   - Séparer les responsabilités

3. **Sécurité**
   - Ne jamais stocker de mots de passe en clair
   - Valider les entrées utilisateur
   - Protéger les routes sensibles
