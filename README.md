# Système de Gestion des Flux et Supervision pour une Organisation Publique
## IMPORTANT : recuperer le projet dans votre projet nomme "flux" pour eviter les erreurs de chemins relatifs

Ce projet a pour but de créer une application web permettant de superviser les flux de données internes (fiches d’impôts des clients au format XML), de générer des rapports statistiques (en PDF et TXT via Java), et d'offrir une assistance technique aux utilisateurs (administrateurs, membres du support et clients).

## Table des Matières

- [Objectif du Projet](#objectif-du-projet)
- [Technologies et Compétences Utilisées](#technologies-et-compétences-utilisées)
- [Objectifs du Projet](#objectifs-du-projet)
- [Fonctionnalités Principales](#fonctionnalités-principales)
- [Scénarios Utilisateurs](#scénarios-utilisateurs)
- [Spécifications Techniques](#spécifications-techniques)
- [Structure du Projet](#structure-du-projet)
- [Commandes de Lancement](#commandes-de-lancement)
- [Base de Données](#base-de-données)

## Objectif du Projet

Créer une application web permettant de :
1. Superviser les flux de données internes.
2. Offrir une assistance technique aux utilisateurs.
3. Générer des rapports statistiques.
4. Gérer les utilisateurs avec un CRUD complet.
5. Mettre en place un système de communication entre les membres du support et les utilisateurs.

## Technologies et Compétences Utilisées

- **PHP** : Développement de l'application web et automatisation des tâches.
- **XML** : Manipulation et validation des données échangées (fiches d’impôts).
- **Bootstrap** : Gestion de l'affichage et du responsive design.
- **Postgres/PL/SQL** : Gestion de la base de données.
- **Java** : Intégration de modules pour générer des résultats statistiques en TXT.
- **CFT (Cross File Transfer)** : Gestion des transferts de fichiers sécurisés.
- **Shell** : Scripts d'automatisation pour l'archivage des fichiers PDF et TXT.

## Objectifs du Projet

1. **Supervision des Flux de Données Internes**
2. **Assistance Technique aux Utilisateurs**
3. **Génération de Rapports Statistiques**
4. **Gestion des Utilisateurs**
5. **Communication entre le Support et les Utilisateurs**

## Fonctionnalités Principales

### Gestion des Flux
- Importation de données XML.
- Affichage et gestion des flux de données.

### Supervision et Rapports
- Suivi des flux en temps réel.
- Génération de rapports statistiques.

### Assistance Technique
- Interface pour les demandes d'assistance.

### Gestion des Utilisateurs
- CRUD complet pour les utilisateurs.
- Gestion des rôles (clients, support, administrateurs).

### Communication
- Système de messagerie interne.
- Gestion des tickets d'assistance.

## Scénarios Utilisateurs

### Administrateur
- **Connexion**
- **Gestion des Utilisateurs** : Créer, modifier, supprimer des utilisateurs.
- **Consulter les Rapports**
- **Gérer les Flux** : Ajouter des flux de données (fichiers XML).

### Client
- **Connexion**
- **Envoyer un Message** : Envoyer des messages au support.
- **Consulter l'État des Tickets**

### Membre du Support
- **Connexion**
- **Gérer les Tickets** : Accepter, suivre, et valider des tâches.
- **Envoyer un Message** : Communiquer avec les clients.
- **Modifier les Informations des Clients**

## Spécifications Techniques

### Technologies Utilisées
- **PHP** : Développement web.
- **Linux** : Hébergement et scripts shell.
- **XML** : Gestion des flux de données.
- **Postgres** : Base de données.
- **PL/SQL** : Logique de la base de données.
- **Java** : Modules de traitement avancé.
- **CFT** : Transferts de fichiers sécurisés.



## Commandes de Lancement

### Pour Lancer le Projet PHP

Naviguez vers le répertoire `public` et lancez le serveur PHP :
```bash
cd public
php -S localhost:8000
```

### Pour Lancer le Projet Java

Naviguez vers le répertoire `flux/utils/` et exécutez les commandes Maven :
```bash
cd flux/utils/
mvn clean install
mvn exec:java
```

### Pour Exécuter le Script d'Archivage

1. **Rendre le Script Exécutable** :
    ```bash
    chmod +x archive_script.sh
    ```
    
2. **Exécuter le Script** :
    ```bash
    ./archive_script.sh
    ```

## Base de Données

### Création des Tables

```sql
-- Table des utilisateurs
CREATE TABLE utilisateur (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    mot_de_passe VARCHAR(255) NOT NULL,
    role INTEGER NOT NULL
);

-- Table des flux de données
CREATE TABLE flux (
    id SERIAL PRIMARY KEY,
    client_id INTEGER NOT NULL REFERENCES utilisateur(id) ON DELETE CASCADE,
    revenu_brut DECIMAL(15, 2) NOT NULL,
    impots_payes DECIMAL(15, 2) NOT NULL,
    annee INTEGER NOT NULL CHECK (annee >= 1900 AND annee <= EXTRACT(YEAR FROM CURRENT_DATE)),
    date_declaration DATE NOT NULL
);

-- Table des rapports
CREATE TABLE rapports (
    id SERIAL PRIMARY KEY,
    type VARCHAR(10) NOT NULL CHECK (type IN ('pdf', 'txt')),
    chemin VARCHAR(255) NOT NULL,
    date_generation DATE NOT NULL
);

-- Table des tickets
CREATE TABLE tickets (
    id SERIAL PRIMARY KEY,
    client_id INTEGER NOT NULL REFERENCES utilisateur(id) ON DELETE SET NULL,
    support_id INTEGER REFERENCES utilisateur(id) ON DELETE SET NULL,
    titre VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    statut INTEGER NOT NULL CHECK (statut IN (0, 1, 2)),
    date_creation DATE DEFAULT CURRENT_DATE
);

-- Table des messages
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    ticket_id INTEGER REFERENCES tickets(id) ON DELETE CASCADE,
    envoyeur_id INTEGER NOT NULL REFERENCES utilisateur(id) ON DELETE CASCADE,
    destinataire_id INTEGER REFERENCES utilisateur(id) ON DELETE SET NULL,
    contenu TEXT NOT NULL,
    date_envoi DATE NOT NULL,
    titre VARCHAR(

255) NOT NULL
);
```

### Insertion des Données Initiales

```sql
-- Insertion des rôles d'utilisateurs
INSERT INTO utilisateur (nom, email, mot_de_passe, role) VALUES ('Admin', 'admin@example.com', 'password', 0);
INSERT INTO utilisateur (nom, email, mot_de_passe, role) VALUES ('Support', 'support@example.com', 'password', 1);
INSERT INTO utilisateur (nom, email, mot_de_passe, role) VALUES ('Client', 'client@example.com', 'password', 2);
```

### Création des Index

```sql
-- Index pour améliorer les performances
CREATE INDEX idx_flux_client_id ON flux(client_id);
CREATE INDEX idx_tickets_client_id ON tickets(client_id);
CREATE INDEX idx_messages_ticket_id ON messages(ticket_id);
CREATE INDEX idx_messages_envoyeur_id ON messages(envoyeur_id);
CREATE INDEX idx_messages_destinataire_id ON messages(destinataire_id);
```

---
