# Frontend FMP - Parties 3, 4 et 5

## Frontend - Partie 3 : Détail Produit

### product.html
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FMP - Détail Produit</title>
    <link rel="stylesheet" href="../static/css/style.css">
    <link rel="stylesheet" href="../static/css/responsive.css">
</head>
<body>
    <header>
        <!-- En-tête identique aux autres pages -->
    </header>

    <main>
        <section class="product-detail">
            <div class="product-images">
                <div class="main-image">
                    <img id="main-product-image" src="" alt="">
                </div>
                <div class="thumbnail-images" id="thumbnail-container">
                    <!-- Miniatures ajoutées via JS -->
                </div>
            </div>
            
            <div class="product-info">
                <h1 id="product-name"></h1>
                <div class="product-meta">
                    <span class="product-reference">Réf: <span id="product-ref"></span></span>
                    <span class="product-brand">Marque: <span id="product-brand"></span></span>
                </div>
                
                <div class="product-price-section">
                    <div class="product-price" id="product-price"></div>
                    <div class="stock-status" id="stock-status"></div>
                </div>
                
                <div class="purchase-options">
                    <div class="quantity-selector">
                        <button class="quantity-btn minus">-</button>
                        <input type="number" value="1" min="1" max="99" id="quantity-input">
                        <button class="quantity-btn plus">+</button>
                    </div>
                    <button class="add-to-cart-btn" id="add-to-cart">
                        Ajouter au panier
                    </button>
                </div>
                
                <div class="product-description">
                    <h2>Description</h2>
                    <div id="product-description"></div>
                </div>
                
                <div class="product-specifications">
                    <h2>Caractéristiques</h2>
                    <table id="specifications-table">
                        <!-- Spécifications ajoutées via JS -->
                    </table>
                </div>
            </div>
        </section>

        <section class="related-products">
            <h2>Produits similaires</h2>
            <div class="products-grid" id="related-products">
                <!-- Produits similaires ajoutés via JS -->
            </div>
        </section>
    </main>

    <footer>
        <!-- Pied de page identique aux autres pages -->
    </footer>

    <script src="../static/js/api.js"></script>
    <script src="../static/js/product-detail.js"></script>
</body>
</html>
```

### product-detail.js
```javascript
// static/js/product-detail.js

document.addEventListener('DOMContentLoaded', () => {
    // Récupérer l'ID du produit depuis l'URL
    const urlParams = new URLSearchParams(window.location.search);
    const productId = urlParams.get('id');

    if (productId) {
        loadProductDetails(productId);
    } else {
        showError('Produit non trouvé');
    }
});

async function loadProductDetails(productId) {
    try {
        const product = await getProduct(productId);
        if (!product) throw new Error('Produit non trouvé');

        updateProductPage(product);
        loadRelatedProducts(product.category);
    } catch (error) {
        showError(error.message);
    }
}

function updateProductPage(product) {
    // Mise à jour des éléments de la page
    document.getElementById('product-name').textContent = product.name;
    document.getElementById('product-ref').textContent = product.reference;
    document.getElementById('product-brand').textContent = product.brand;
    document.getElementById('product-price').textContent = `${product.price.toFixed(2)} €`;
    document.getElementById('product-description').textContent = product.description;
    document.getElementById('main-product-image').src = product.image;
    
    // Mise à jour du statut du stock
    const stockStatus = document.getElementById('stock-status');
    if (product.stock > 0) {
        stockStatus.textContent = 'En stock';
        stockStatus.className = 'stock-status in-stock';
    } else {
        stockStatus.textContent = 'Rupture de stock';
        stockStatus.className = 'stock-status out-of-stock';
    }

    // Gestionnaire pour le bouton d'ajout au panier
    document.getElementById('add-to-cart').addEventListener('click', () => {
        const quantity = parseInt(document.getElementById('quantity-input').value);
        addToCart(product.id, quantity);
    });
}

// Gestionnaire de quantité
document.querySelector('.minus').addEventListener('click', () => {
    const input = document.getElementById('quantity-input');
    input.value = Math.max(1, parseInt(input.value) - 1);
});

document.querySelector('.plus').addEventListener('click', () => {
    const input = document.getElementById('quantity-input');
    input.value = Math.min(99, parseInt(input.value) + 1);
});

async function loadRelatedProducts(category) {
    try {
        const products = await getRelatedProducts(category);
        const container = document.getElementById('related-products');
        container.innerHTML = '';

        products.forEach(product => {
            if (product.id !== currentProductId) {
                container.appendChild(createProductCard(product));
            }
        });
    } catch (error) {
        console.error('Erreur chargement produits similaires:', error);
    }
}

function showError(message) {
    const main = document.querySelector('main');
    main.innerHTML = `
        <div class="error-message">
            <h2>Erreur</h2>
            <p>${message}</p>
            <a href="products.html" class="button">Retour aux produits</a>
        </div>
    `;
}
```

## Frontend - Partie 4 : Espace Client

### account.html
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FMP - Mon Compte</title>
    <link rel="stylesheet" href="../static/css/style.css">
    <link rel="stylesheet" href="../static/css/responsive.css">
</head>
<body>
    <header>
        <!-- En-tête identique aux autres pages -->
    </header>

    <main>
        <section class="account-section">
            <div class="account-nav">
                <h2>Mon Compte</h2>
                <nav>
                    <ul>
                        <li><a href="#profile" class="active">Profil</a></li>
                        <li><a href="#orders">Mes Commandes</a></li>
                        <li><a href="#addresses">Mes Adresses</a></li>
                        <li><a href="#settings">Paramètres</a></li>
                    </ul>
                </nav>
            </div>

            <div class="account-content">
                <!-- Section Profil -->
                <div id="profile" class="account-tab">
                    <h2>Mon Profil</h2>
                    <form id="profile-form" class="account-form">
                        <div class="form-group">
                            <label>Prénom</label>
                            <input type="text" id="firstname" name="firstname">
                        </div>
                        <div class="form-group">
                            <label>Nom</label>
                            <input type="text" id="lastname" name="lastname">
                        </div>
                        <div class="form-group">
                            <label>Email</label>
                            <input type="email" id="email" name="email">
                        </div>
                        <div class="form-group">
                            <label>Téléphone</label>
                            <input type="tel" id="phone" name="phone">
                        </div>
                        <button type="submit" class="save-button">
                            Enregistrer les modifications
                        </button>
                    </form>
                </div>

                <!-- Section Commandes -->
                <div id="orders" class="account-tab" style="display: none;">
                    <h2>Mes Commandes</h2>
                    <div id="orders-list">
                        <!-- Liste des commandes ajoutée via JS -->
                    </div>
                </div>

                <!-- Section Adresses -->
                <div id="addresses" class="account-tab" style="display: none;">
                    <h2>Mes Adresses</h2>
                    <div class="addresses-list" id="addresses-list">
                        <!-- Adresses ajoutées via JS -->
                    </div>
                    <button class="add-address-btn">
                        Ajouter une adresse
                    </button>
                </div>

                <!-- Section Paramètres -->
                <div id="settings" class="account-tab" style="display: none;">
                    <h2>Paramètres</h2>
                    <form id="settings-form" class="account-form">
                        <div class="form-group">
                            <label>Changer le mot de passe</label>
                            <input type="password" name="current-password" placeholder="Mot de passe actuel">
                            <input type="password" name="new-password" placeholder="Nouveau mot de passe">
                            <input type="password" name="confirm-password" placeholder="Confirmer le mot de passe">
                        </div>
                        <button type="submit" class="save-button">
                            Mettre à jour le mot de passe
                        </button>
                    </form>
                </div>
            </div>
        </section>
    </main>

    <footer>
        <!-- Pied de page identique aux autres pages -->
    </footer>

    <script src="../static/js/api.js"></script>
    <script src="../static/js/account.js"></script>
</body>
</html>
```

### login.html
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FMP - Connexion</title>
    <link rel="stylesheet" href="../static/css/style.css">
    <link rel="stylesheet" href="../static/css/responsive.css">
</head>
<body>
    <header>
        <!-- En-tête identique aux autres pages -->
    </header>

    <main>
        <section class="auth-section">
            <div class="auth-container">
                <div class="auth-box">
                    <h2>Connexion</h2>
                    <form id="login-form" class="auth-form">
                        <div class="form-group">
                            <label>Email</label>
                            <input type="email" name="email" required>
                        </div>
                        <div class="form-group">
                            <label>Mot de passe</label>
                            <input type="password" name="password" required>
                        </div>
                        <div class="form-options">
                            <label>
                                <input type="checkbox" name="remember">
                                Se souvenir de moi
                            </label>
                            <a href="#" class="forgot-password">
                                Mot de passe oublié ?
                            </a>
                        </div>
                        <button type="submit" class="auth-button">
                            Se connecter
                        </button>
                    </form>
                    <div class="auth-links">
                        <p>
                            Pas encore de compte ?
                            <a href="register.html">S'inscrire</a>
                        </p>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <footer>
        <!-- Pied de page identique aux autres pages -->
    </footer>

    <script src="../static/js/api.js"></script>
    <script src="../static/js/login.js"></script>
</body>
</html>
```

## Frontend - Partie 5 : Processus de Commande

### checkout.html
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FMP - Paiement</title>
    <link rel="stylesheet" href="../static/css/style.css">
    <link rel="stylesheet" href="../static/css/responsive.css">
</head>
<body>
    <header>
        <!-- En-tête simplifié pour le processus de commande -->
        <nav class="checkout-nav">
            <div class="logo">
                <h1>FMP</h1>
            </div>
            <div class="checkout-steps">
                <div class="step active">1. Adresse</div>
                <div class="step">2. Livraison</div>
                <div class="step">3. Paiement</div>
                <div class="step">4. Confirmation</div>
            </div>
        </nav>
    </header>

    <main>
        <section class="checkout-section">
            <!-- Étape 1: Adresse -->
            <div id="address-step" class="checkout-step">
                <h2>Adresse de livraison</h2>
                <div class="saved-addresses" id="saved-addresses">
                    <!-- Adresses sauvegardées ajoutées via JS -->
                </div>
                <button class="new-address-btn">
                    Nouvelle adresse
                </button>
            </div>

            <!-- Étape 2: Livraison -->
            <div id="shipping-step" class="checkout-step" style="display: none;">
                <h2>Mode de livraison</h2>
                <div class="shipping-options">
                    <div class="shipping-option">
                        <input type="radio" name="shipping" id="standard">
                        <label for="standard">
                            <span class="option-name">Standard</span>
                            <span class="option-price">5.90 €</span>
                            <span class="option-delay">3-5 jours ouvrés</span>
                        </label>
                    </div>
                    <div class="shipping-option">
                        <input type="radio" name="shipping" id="express">
