---
title: Site Web E-commerce {php}
publishDate: 2024-12-04 00:00:00
img: /assets/stock-2.jpg
img_alt: A bright pink sheet of paper used to wrap flowers curves in front of rich blue background
description: |
  Ce site web e-commerce est basé sur du PHP, HTML, CSS, Bootstrap, JavaScript et MySQL.
  
  
tags:
  - front-end
  - back-end
  - database
---

Ce projet e-commerce a été conçu comme un exercice de développement full-stack pour un projet de cours. Le site web vise à vendre des montres de luxe telles que Rolex, Hublot, et Richard Mille. Le développement de ce site inclut à la fois le design et les fonctionnalités de back-end, ainsi que la gestion de la base de données.                
[dépôt GitHub](https://github.com/RedaBenzakour/PHP).


#### Technologies Utilisées

- **Front-end** : HTML, CSS, JavaScript
- **Back-end** : PHP
- **Base de Données** : MySQL


#### Pages Principales

- `index.php` : Page d'accueil
- `acceuil.php` : Page d'accueil alternative
- `about.php` : Page "À propos"
- `register.php` : Page d'inscription
- `login.php` : Page de connexion
- `profil.php` : Page de profil utilisateur
- `panier.php` : Page du panier
- `payment.php` : Page de paiement
- `commandes.php` : Page des commandes
- `commandeUser.php` : Page de commandes utilisateur
- `suivre.php` : Page de suivi des commandes
- `deconnexion.php` : Page de déconnexion

#### Administration

- `admistration.php` : Page d'administration
- `ajoutProduit.php` : Page d'ajout de produits
- `ajoutMarque.php` : Page d'ajout de marques
- `edit.php` : Page de modification
- `miseAJour.php` : Page de mise à jour des informations
- `modifierUtilisateur.php` : Page de modification des utilisateurs
- `afficherProduit.php` : Page d'affichage des produits
- `afficherProduitA.php` : Page d'affichage des produits (admin)
- `ajouterPanier.php` : Page d'ajout au panier
- `supprimerPanier.php` : Page de suppression du panier

#### Includes

- `config.php` : Fichier de configuration
- `fonction.php` : Fichier de fonctions utilitaires

#### Public

- `footer.php` : Pied de page
- `header.php` : En-tête
- `navbar.php` : Barre de navigation

#### Base de Données

- **Fichier SQL** : `mywatch (1).sql` pour la création et la population de la base de données
- **Tables principales** : utilisateurs, produits, marques, commandes, paniers

#### Assets

- **Images** : Logo, captures d'écran, images des produits
- **Vidéos** : Vidéo promotionnelle d'Omega

#### Fonctionnalités Clés

- Système d'authentification des utilisateurs
- Gestion des produits et des marques
- Ajout et suppression de produits au panier
- Suivi des commandes
- Interface d'administration pour la gestion des produits et des utilisateurs

#### Démonstration

Pour une vue d'ensemble du projet, des captures d'écran ont été incluses dans le dossier `assets/images`, montrant différentes pages et fonctionnalités du site.

#### Conclusion

Ce projet e-commerce démontre une compétence complète en développement full-stack, de la conception de l'interface utilisateur à la mise en œuvre des fonctionnalités de back-end et de la base de données.

#### Lien GitHub

Pour accéder au code source complet de ce projet, visitez le [dépôt GitHub](https://github.com/RedaBenzakour/PHP).





#### config.php
```php
    if (isset($_GET['id'])) 
    $product_id = $_GET['id'];
    $query = "SELECT * FROM produits WHERE id = $product_id";
    $result = mysqli_query($conn, $query);
    $product = mysqli_fetch_assoc($result);

    if ($product) {
        $item = [
            'id' => $product['id'],
            'nom' => $product['nom'],
            'prix' => $product['prix'],
            'quantite' => 1
        ];

        if (isset($_SESSION['panier'])) {
            $panier = $_SESSION['panier'];
            $product_ids = array_column($panier, 'id');

            if (in_array($product_id, $product_ids)) {
                foreach ($panier as &$item) {
                    if ($item['id'] == $product_id) {
                        $item['quantite']++;
                        break;
                    }
                }
            } else {
                $panier[] = $item;
            }
        } else {
            $panier = [$item];
        }

        $_SESSION['panier'] = $panier;
    }
