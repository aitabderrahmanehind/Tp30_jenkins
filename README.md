# TP - Architecture Microservices avec Spring Boot et Spring Cloud

Ce projet est une démonstration d'une architecture microservices simple en utilisant l'écosystème Spring. Il est composé de trois services principaux :

1.  **`eureka-server`**: Le serveur de découverte (Service Discovery).
2.  **`gateway-service`**: La passerelle d'API (API Gateway).
3.  **`client-service`**: Un microservice métier qui gère des entités "Client".

---

## Description des Modules

### 1. `eureka-server`
Ce module agit comme un annuaire pour tous les autres microservices. Chaque service (comme `client-service`) s'enregistre auprès d'Eureka au démarrage. Eureka maintient un registre des instances de service disponibles, ce qui permet une communication dynamique et résiliente entre les services.

-   **Port par défaut**: `8761`
-   **Tableau de bord Eureka**: [http://localhost:8761](http://localhost:8761)
    <img width="1920" height="1032" alt="image" src="https://github.com/user-attachments/assets/71c7c60e-9c34-4b8a-9cb4-9fa947e824d6" />
    <img width="1920" height="970" alt="image" src="https://github.com/user-attachments/assets/57f8d3b4-6ea9-45ba-83cd-9c7faa11b834" />



### 2. `gateway-service`
C'est le point d'entrée unique pour toutes les requêtes externes. Au lieu d'exposer chaque microservice directement, les clients (navigateurs, applications mobiles, etc.) communiquent uniquement avec la passerelle.
    <img width="1903" height="1031" alt="image" src="https://github.com/user-attachments/assets/b37c0d84-0fe7-4d8e-b094-dd44cf430c8e" />


La passerelle a plusieurs responsabilités :
-   **Routage dynamique**: Elle consulte Eureka pour savoir où se trouvent les services et redirige les requêtes entrantes vers les instances appropriées.
-   **Sécurité**: Centralise l'authentification et l'autorisation.
-   **Équilibrage de charge (Load Balancing)**: Répartit les requêtes entre plusieurs instances d'un même service.
-   **Filtres**: Peut ajouter des en-têtes, logger des requêtes, etc.

### 3. `client-service`
Il s'agit d'un microservice métier simple qui expose une API REST pour gérer des clients. Il possède :
-   Un **contrôleur** pour définir les endpoints (`/clients`, `/clients/{id}`).
-   Une **entité** `Client` qui représente le modèle de données.
-   Un **repository** (basé sur Spring Data JPA) pour interagir avec une base de données (H2 en mémoire par défaut).
-   Un **service** pour la logique métier.
  <img width="1920" height="1028" alt="image" src="https://github.com/user-attachments/assets/ef55339c-bbfc-434f-94fe-aac0e1d36087" />


---

## Architecture Globale

Le flux d'une requête est le suivant :

```
Client Externe --> Gateway Service --> Eureka Server (pour la découverte d'adresse) --> Client Service
```

1.  Le client envoie une requête à la passerelle (par exemple, `GET /clients`).
2.  La passerelle interroge Eureka pour trouver l'adresse d'une instance disponible de `client-service`.
3.  La passerelle transfère la requête à l'instance de `client-service` trouvée.
4.  `client-service` traite la requête et renvoie la réponse à la passerelle.
5.  La passerelle renvoie la réponse au client externe.

---

## Prérequis

-   Java Development Kit (JDK) 17 ou supérieur.
-   Apache Maven.
-   Un IDE comme IntelliJ IDEA, VS Code ou Eclipse.

---

## Comment lancer le projet

L'ordre de démarrage est **crucial**. Vous devez démarrer le serveur Eureka en premier.

1.  **Démarrer `eureka-server`**:
    -   Ouvrez un terminal dans le dossier `eureka-server`.
    -   Exécutez la commande : `mvn spring-boot:run`

2.  **Démarrer `client-service`**:
    -   Ouvrez un **nouveau** terminal dans le dossier `client-service`.
    -   Exécutez la commande : `mvn spring-boot:run`
    -   Vérifiez sur le tableau de bord Eureka ([http://localhost:8761](http://localhost:8761)) que `CLIENT-SERVICE` est bien enregistré.

3.  **Démarrer `gateway-service`**:
    -   Ouvrez un **nouveau** terminal dans le dossier `gateway-service`.
    -   Exécutez la commande : `mvn spring-boot:run`

---

## Accéder aux Endpoints

Une fois que tout est démarré, vous pouvez utiliser un outil comme `curl` ou Postman pour interagir avec l'API via la passerelle.

-   **Lister tous les clients** (via la passerelle) :
    ```bash
    curl http://localhost:9999/clients
    ```

-   **Obtenir un client par son ID** (via la passerelle) :
    ```bash
    curl http://localhost:9999/clients/1
    ```
*(Note: Des clients sont pré-chargés au démarrage de `client-service`)*.
