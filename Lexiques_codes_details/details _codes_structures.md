### 1. Routes Flask avec Blueprint

#### **Fichier : `backend/app/routes/products.py`**

```python
# Importation des outils Flask pour créer des routes, retourner des réponses JSON, et gérer les requêtes HTTP.
from flask import Blueprint, jsonify, request
# Importation du modèle "Product" pour interagir avec les données des produits.
from app.models.product import Product
# Importation de l'objet "db" pour gérer les opérations de base de données.
from app import db

# Création du Blueprint pour organiser les routes relatives aux produits.
products = Blueprint('products', __name__)

# Définition d'une route pour obtenir la liste des produits.
@products.route('/api/products', methods=['GET'])
def get_products():
    # Récupération de tous les produits à partir de la base de données.
    products = Product.query.all()
    return jsonify([{  # Conversion des produits en JSON pour les retourner au client.
        'id': p.id,  # ID unique du produit.
        'name': p.name,  # Nom du produit.
        'price': p.price,  # Prix du produit.
        'description': p.description  # Description du produit.
    } for p in products])  # Liste compréhension pour transformer chaque produit en dictionnaire.

# Route pour obtenir les détails d'un produit spécifique.
@products.route('/api/products/<int:id>', methods=['GET'])
def get_product(id):
    product = Product.query.get_or_404(id)  # Recherche du produit par ID; renvoie une erreur 404 si non trouvé.
    return jsonify({  # Conversion du produit trouvé en dictionnaire JSON.
        'id': product.id,  # ID unique du produit.
        'name': product.name,  # Nom du produit.
        'price': product.price,  # Prix du produit.
        'description': product.description  # Description du produit.
    })
```

---

### 2. Initialisation de l'Application et Enregistrement des Blueprints

#### **Fichier : `backend/app/__init__.py`**

```python
# Initialisation de l'application Flask.
def create_app():
    app = Flask(__name__)
    app.config.from_object(Config)  # Chargement des configurations depuis l'objet Config.
    
    db.init_app(app)  # Initialisation de l'objet de base de données avec l'application Flask.
    
    # Importation et enregistrement des blueprints pour modulariser les routes.
    from app.routes.products import products
    app.register_blueprint(products)  # Enregistrement du Blueprint "products" dans l'application Flask.
    
    return app  # Retourne l'application Flask configurée.
```

---

### 3. Modèle de Base de Données pour les Produits

#### **Fichier : `backend/app/models/product.py`**

```python
from app import db
from datetime import datetime

class Product(db.Model):  # Classe représentant le modèle "Product" pour les produits en base de données.
    id = db.Column(db.Integer, primary_key=True)  # Identifiant unique du produit.
    name = db.Column(db.String(100), nullable=False)  # Nom du produit (obligatoire).
    price = db.Column(db.Float, nullable=False)  # Prix du produit (obligatoire).
    description = db.Column(db.Text)  # Description du produit (facultative).
    printer_model = db.Column(db.String(50))  # Modèle d'imprimante compatible (facultatif).
    stock = db.Column(db.Integer, default=0)  # Quantité en stock, par défaut 0.
    created_at = db.Column(db.DateTime, default=datetime.utcnow)  # Date de création automatique du produit.

    def to_dict(self):
        # Méthode pour convertir un produit en dictionnaire JSON.
        return {
            'id': self.id,
            'name': self.name,
            'price': self.price,
            'description': self.description,
            'printer_model': self.printer_model,
            'stock': self.stock
        }
```

---

### 4. Initialisation de la Base de Données

#### **Fichier : `backend/init_db.py`**

```python
from app import create_app, db
from app.models.product import Product

app = create_app()

def init_db():
    with app.app_context():  # Création d'un contexte d'application pour accéder à la base de données.
        db.create_all()  # Création de toutes les tables définies par les modèles SQLAlchemy.
        
        # Ajout de données de test pour initialiser la base de données.
        test_product = Product(
            name="Cartouche HP 304 Noire",
            price=19.99,
            description="Cartouche d'encre noire pour HP",
            printer_model="HP DeskJet 3750",
            stock=10
        )
        
        db.session.add(test_product)  # Ajout du produit au contexte de session.
        db.session.commit()  # Validation des modifications dans la base de données.

if __name__ == '__main__':
    init_db()  # Exécution de la fonction d'initialisation lorsque le script est lancé directement.
```

---

### 5. Gestion des Erreurs

#### **Fichier : `backend/app/errors.py`**

```python
from flask import jsonify

def register_error_handlers(app):
    @app.errorhandler(404)
    def not_found_error(error):
        # Gestion des erreurs 404 avec un message JSON clair.
        return jsonify({
            'error': 'Resource not found',
            'message': str(error)
        }), 404

    @app.errorhandler(500)
    def internal_error(error):
        db.session.rollback()  # Annule les transactions en cas d'erreur de base de données.
        return jsonify({
            'error': 'Internal server error',
            'message': str(error)
        }), 500
```

---

### 6. Tests

#### **Configuration des Tests : `backend/tests/conftest.py`**

```python
import pytest
from app import create_app, db

@pytest.fixture
# Initialisation de l'application Flask en mode test.
def app():
    app = create_app()
    app.config['TESTING'] = True
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///:memory:'  # Base de données en mémoire pour les tests.
    
    with app.app_context():
        db.create_all()  # Création des tables pour les tests.
        yield app
        db.session.remove()
        db.drop_all()  # Nettoyage après les tests.

@pytest.fixture
# Retourne un client de test pour simuler les requêtes HTTP.
def client(app):
    return app.test_client()
```

#### **Tests des Routes : `backend/tests/test_products.py`**

```python
def test_get_products(client):
    # Test de la route GET /api/products pour s'assurer qu'elle retourne un statut 200 et une liste de produits.
    response = client.get('/api/products')
    assert response.status_code == 200
    assert isinstance(response.json, list)  # Vérifie que la réponse est une liste JSON.
```
**
