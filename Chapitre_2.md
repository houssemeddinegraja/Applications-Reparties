# Résumé du chapitre 2 : Architecture Microservices

Ce chapitre explique comment passer d’une application **monolithique** (tout en un bloc) à une architecture **microservices** (découpée en petits services indépendants). Il présente les défis de distribution, les avantages/inconvénients, les composants clés (API Gateway, Message Broker) et le fonctionnement des **API REST**, très utilisées dans ce type d’architecture.

## 1. Pourquoi ne pas rester sur une application monolithique ?

Une application **monolithique** regroupe toutes les fonctionnalités (interface, logique métier, accès aux données) dans un seul programme.

| Avantages | Inconvénients |
|-----------|----------------|
| Simple à développer au début | Difficile à faire évoluer (modules couplés) |
| Performant (appels directs en mémoire) | Scalabilité limitée : on doit dupliquer toute l’application |
| Déploiement unique | Mise à jour risquée : une petite modification peut casser tout le système |
| Partage facile des ressources | Difficulté à adopter de nouvelles technologies |

> **Exemple** : un site e-commerce où la gestion des utilisateurs, des commandes et du paiement sont dans le même code. Si le paiement a du succès, on est obligé de scaler tout le site.

## 2. L’alternative : les microservices

Un **microservice** est une petite brique logicielle qui fait **une seule chose** (ex : service Utilisateur, service Paiement, service Catalogue).  
Chaque microservice :

- a sa propre base de données (si besoin)
- peut être développé dans un langage différent
- peut être déployé et mis à l’échelle **indépendamment**

**Objectifs** : modularité, résilience, scalabilité fine.

## 3. Comparaison rapide : Monolithe vs Microservices

| Monolithe | Microservices |
|-----------|----------------|
| Un seul bloc | Plusieurs petits services |
| Un langage / une techno | Technologies variées possibles |
| Base de données unique | Chaque service peut avoir sa propre BDD |
| Déploiement global | Déploiement indépendant |
| Communication en mémoire | Communication via réseau (API, messages) |

> **Idée clé** : on échange la simplicité interne contre la flexibilité et la résilience.

## 4. Les nouveaux défis des microservices

Passer aux microservices **ne résout pas tout** – cela introduit de la complexité :

- **Communication réseau** : les services doivent parler via le réseau (latence, pannes possibles).
- **Gestion des données** : chaque service peut avoir sa BDD → il faut synchroniser les données (ex : commande et facturation).
- **Découverte de services** : comment savoir où se trouve tel service ?
- **Sécurité** : authentification centralisée ou par service ?

Pour y faire face, on ajoute des composants supplémentaires :

### a) API Gateway (passerelle API)

C’est un **point d’entrée unique** pour les clients. Il fait :
- authentification / autorisation
- routage des requêtes vers le bon microservice
- équilibrage de charge (load balancing)
- limitation du nombre de requêtes (rate limiting)
- agrégation de réponses (ex : appeler plusieurs services et fusionner les résultats)

### b) Message Broker (courtier de messages)

Permet une communication **asynchrone** entre services.  
**Principe** : un service publie un message (ex : "commande créée"), le broker le stocke et le transmet aux services abonnés (ex : service d’envoi de mail, service de facturation).

**Exemples** : RabbitMQ, Apache Kafka, Redis Pub/Sub.

## 5. Comment les microservices communiquent-ils ?

| Type de communication | Caractéristiques | Exemple |
|----------------------|------------------|---------|
| **RPC/RMI** | Appel distant comme un appel local – simple mais couplage fort | Java RMI |
| **REST** | Basé sur HTTP, sans état, flexible, très utilisé | API REST JSON |
| **Messagerie** | Asynchrone, découplage fort, bonne résilience | RabbitMQ, Kafka |

Le chapitre se concentre ensuite sur **REST**, car c’est le plus courant pour les microservices.

## 6. Les API REST (pour les microservices)

**REST** = style d’architecture qui utilise les verbes HTTP pour manipuler des **ressources** (objets métier).

### Principes de base

- **Stateless** : chaque requête contient toutes les infos nécessaires (pas de session côté serveur).
- **Interface uniforme** : utilisation standard de GET, POST, PUT, PATCH, DELETE.
- **Ressources identifiées par des URI** (ex : `/users/123`).
- **Représentation** : souvent en JSON (léger, lisible).

### Méthodes HTTP et leur sens

| Méthode | Action (CRUD) | Exemple |
|---------|---------------|---------|
| GET | Lire (Read) | `GET /products/123` |
| POST | Créer (Create) | `POST /users` |
| PUT | Remplacer complètement (Update) | `PUT /users/123` |
| PATCH | Modification partielle | `PATCH /users/123` |
| DELETE | Supprimer (Delete) | `DELETE /users/123` |

### Composants d’une requête/réponse REST

- **URI** = adresse de la ressource (ex : `https://api.exemple.com/users/123`)
- **Headers** : métadonnées (Content-Type, Authorization, Accept...)
- **Body** : données (JSON) pour POST/PUT/PATCH
- **Codes de statut** : 200 (OK), 201 (Créé), 204 (Pas de contenu), 400 (Erreur client), 404 (Non trouvé), 500 (Erreur serveur)

### Bonnes pratiques pour concevoir une API REST

1. **Définir les ressources** (noms au pluriel) : `/users`, `/orders`
2. **Utiliser correctement les méthodes HTTP**
3. **URIs simples** : pas de verbes (`/users/123/orders` au lieu de `/getOrdersForUser`)
4. **Réponses JSON** avec codes HTTP appropriés
5. **Pagination et filtrage** : `?page=2&limit=10`, `?category=electronics`
6. **Gestion claire des erreurs** : message explicite en JSON
7. **Sécurité** : HTTPS, authentification (OAuth2, JWT), rate limiting
8. **Documentation** : Swagger / OpenAPI

### Exemple concret (e-commerce)

- `GET /api/users/123` → récupère l’utilisateur 123
- `POST /api/orders` → crée une commande (données dans le body JSON)
- `GET /api/products` → liste des produits
- `PUT /api/products/456` → remplace les infos du produit 456

## 7. Concepts difficiles simplifiés

| Terme | Explication simple |
|-------|--------------------|
| **Monolithe** | Une seule grosse application où tout est mélangé |
| **Microservice** | Un petit programme qui fait une seule tâche (ex : envoi de mails) |
| **API Gateway** | Un réceptionniste intelligent qui oriente les demandes clients vers les bons services et vérifie les badges |
| **Message Broker** | Une boîte aux lettres : un service dépose un message, d'autres le récupèrent quand ils peuvent |
| **REST** | Une façon de parler aux services via Internet en utilisant des verbes simples (GET, POST…) et du JSON |
| **Endpoint** | Une adresse précise (URL) qui correspond à une ressource ou une action |
| **Stateless** | Chaque requête est autonome ; le serveur ne se souvient de rien entre deux requêtes |

## 8. Conclusion du chapitre

- Les **microservices** répondent aux limites des applications monolithiques : évolutivité fine, indépendance des équipes, résilience.
- Mais ils apportent une **complexité réseau** et nécessitent de nouveaux outils (API Gateway, Message Broker, etc.).
- La communication la plus courante est **REST** : simple, basée sur HTTP/JSON, adaptée au web.
- Concevoir une bonne API REST demande de suivre des règles claires (URI, méthodes HTTP, codes, sécurité).

> **À retenir** : l’architecture microservices n’est pas une solution miracle – c’est un compromis entre flexibilité et complexité. REST est le langage standard pour les faire communiquer.
