# Résumé du chapitre 1 : Fondements des systèmes et applications répartis

Ce chapitre introduit les bases des **systèmes répartis** (ou distribués) et les techniques pour construire des applications réparties, comme **RPC** et **Java RMI**.

## 1. Qu’est-ce qu’un système réparti ?

Un système réparti est un ensemble d’ordinateurs (ou de nœuds) reliés par un réseau, qui **collaborent** pour accomplir une tâche.  
Les ressources (processeurs, mémoire, données) sont **partagées** entre plusieurs éléments.

> **Idée clé** : Le système se comporte comme un tout cohérent, même si ses composants sont physiquement séparés.

## 2. Pourquoi utiliser des systèmes répartis ?

- **Économie** : meilleur rapport performance/prix avec des machines standards.
- **Partage** : ressources, données, services.
- **Disponibilité** : si une machine tombe en panne, d’autres prennent le relais.
- **Évolutivité** : on peut ajouter des machines facilement.

## 3. Propriétés souhaitées d’un système réparti

- **Fiabilité** : le système continue de fonctionner même si certains éléments tombent en panne.
- **Sécurité** : protection contre les accès non autorisés.
- **Passage à l’échelle (scalabilité)** : supporte un nombre croissant d’utilisateurs ou de données.

**Exemples célèbres** :  
- DNS (traduit les noms de domaine en adresses IP)  
- World Wide Web

## 4. Qu’est-ce qu’une application répartie ?

Une application dont le code **ne tourne pas sur une seule machine** : plusieurs programmes s’exécutent sur différents nœuds et communiquent pour traiter des données réparties.

**Différence avec une application centralisée** :

| Centralisée | Répartie |
|-------------|----------|
| Un seul OS | Différents OS |
| Mémoire partagée | Mémoires séparées |
| Pas de réseau | Communication par réseau |

## 5. Middleware (intergiciel)

C’est une **couche logicielle** qui se place entre le système d’exploitation et les applications.  
**Rôles** :  
- Cacher l’hétérogénéité des machines (différents OS, langages, protocoles).  
- Donner l’illusion que tout est local → **transparence**.  
- Fournir des services communs : nommage, communication, transactions, etc.

## 6. Communication dans les applications réparties

Deux grandes approches historiques :

### a) RPC (Remote Procedure Call) – à base de procédures

**Principe** : un client appelle une procédure qui se trouve sur un serveur distant **comme si elle était locale**.

**Comment ça marche ?**  
1. On décrit l’interface de la procédure dans un langage spécial (IDL).  
2. Un compilateur IDL génère deux morceaux de code :  
   - **Stub client** : se fait passer pour la procédure locale, mais envoie un message au serveur.  
   - **Stub serveur (ou skeleton)** : reçoit le message, exécute la vraie procédure, renvoie le résultat.  
3. Le client et le serveur communiquent via le réseau.

**Avantages** : simplicité, portabilité.  
**Limites** : structure statique (pas de création dynamique de serveur), pas de passage par référence.

### b) RMI (Remote Method Invocation) – à base d’objets

Extension du RPC aux **objets** (Java RMI).  
Permet d’appeler une **méthode d’un objet distant** comme s’il était local.

**Principes clés** :  
- L’objet distant doit implémenter l’interface `Remote`.  
- Chaque méthode doit déclarer `RemoteException`.  
- Le **stub client** est une référence à l’objet distant obtenue dynamiquement.  
- Un **registre RMI (RMI Registry)** permet d’enregistrer et de retrouver les objets distants.  

**Protocole JRMP** : spécifique à Java, utilise TCP/IP.

**Exemple simple (Hello World)** :  
1. Interface `HelloInterface` (déclare `sayHello()`).  
2. Classe `Hello` qui implémente l’interface et étend `UnicastRemoteObject`.  
3. Serveur : crée un objet `Hello` et l’enregistre (`Naming.rebind`).  
4. Client : recherche l’objet (`Naming.lookup`) et appelle sa méthode.

> **Simplification** : RMI est plus simple que CORBA car tout reste en Java. Il est encore utilisé pour l’enseignement ou dans des environnements fermés.

## 7. Concepts difficiles simplifiés

| Concept | Explication simple |
|---------|--------------------|
| **Stub client** | Un petit programme qui fait semblant d’être l’objet distant. Le client l’appelle, et il se charge d’envoyer la demande au serveur. |
| **Skeleton** | Côté serveur, il reçoit la demande, appelle le vrai objet et renvoie la réponse. |
| **IDL** | Un langage neutre pour décrire quelles fonctions/méthodes sont disponibles à distance. |
| **Registre RMI** | Un annuaire où les serveurs publient leurs objets. Le client consulte l’annuaire pour trouver l’objet dont il a besoin. |
| **Transparence** | L’utilisateur (ou le programmeur) a l’impression que tout s’exécute sur sa machine, alors que les traitements sont répartis. |

## 8. Conclusion du chapitre

- Les **systèmes répartis** apportent disponibilité, partage et évolutivité, mais introduisent des problèmes de communication, de sécurité et de coordination.  
- Le **middleware** masque la complexité.  
- Deux approches historiques : **RPC** (procédures) et **RMI** (objets, dans Java).  
- RMI permet d’appeler des méthodes sur des objets distants simplement, mais reste limité à Java.

> **À retenir** : les systèmes répartis sont partout (Web, DNS, cloud). Les techniques comme RMI ou les microservices (vu dans le chapitre 2) sont au cœur des applications modernes.
