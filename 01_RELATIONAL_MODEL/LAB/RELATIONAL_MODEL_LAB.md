
# **Lab : Modèle Relationnel avec Microsoft SQL Server**

## **Objectifs**

- Concevoir une base de données relationnelle complète en appliquant les concepts théoriques.
- Manipuler les tables pour **insérer, interroger, et mettre à jour** les données.
- Comprendre l’impact des contraintes relationnelles et les concepts avancés comme les jointures complexes.
- Effectuer des analyses sur les données (agrégations, calculs, etc.).

---

## **Contexte**

Une librairie en ligne souhaite structurer ses données pour mieux gérer **ses clients, ses livres, et ses commandes**. Vous devez créer une base de données relationnelle complète qui respecte les meilleures pratiques.

### **Les besoins métiers :**

1. Gérer une liste de **clients**, incluant leurs coordonnées **(nom, adresse, téléphone)**.  
2. Gérer un catalogue de **livres**, incluant leurs **titres, auteurs, prix**, et **stock disponible**.  
3. Enregistrer les **commandes** passées par les clients, en précisant les livres achetés et leurs quantités.  
4. Fournir des rapports, par exemple :  

   - Liste des clients avec leurs commandes.  
   - Total des ventes pour chaque livre.  
   - Stock restant après les ventes.  

---

## **Étape 1 : Analyse et Conception du Schéma Relationnel**

### **1.1 Identification des Entités**

- **Clients** : Une table pour enregistrer les informations des clients.  
- **Livres** : Une table pour gérer le catalogue des livres.  
- **Commandes** : Une table pour enregistrer chaque commande.  
- **Détails_Commandes** : Une table pour gérer les livres inclus dans chaque commande.

### **1.2 Définition des Relations**

- Chaque client peut passer plusieurs commandes (relation **1:N** entre **Clients** et **Commandes**).  
- Une commande peut inclure plusieurs livres, et un livre peut apparaître dans plusieurs commandes (relation **N:M** entre **Commandes** et **Livres**).  

### **1.3 Conception des Tables**

#### **Clients**

| Attribut       | Type       | Contrainte          | Description                       |
|----------------|------------|---------------------|-----------------------------------|
| ID_Client      | INT        | PRIMARY KEY         | Identifiant unique du client.     |
| Nom            | VARCHAR(50)| NOT NULL            | Nom complet du client.           |
| Adresse        | VARCHAR(100)| NULLABLE           | Adresse du client.               |
| Téléphone      | VARCHAR(15)| UNIQUE, NOT NULL    | Numéro de téléphone du client.   |

#### **Livres**

| Attribut       | Type        | Contrainte          | Description                       |
|----------------|-------------|---------------------|-----------------------------------|
| ID_Livre       | INT         | PRIMARY KEY         | Identifiant unique du livre.      |
| Titre          | VARCHAR(100)| NOT NULL            | Titre du livre.                  |
| Auteur         | VARCHAR(50) | NOT NULL            | Auteur du livre.                 |
| Prix           | DECIMAL(5,2)| NOT NULL            | Prix unitaire du livre.          |
| Stock          | INT         | NOT NULL            | Quantité disponible en stock.    |

#### **Commandes**

| Attribut       | Type        | Contrainte          | Description                       |
|----------------|-------------|---------------------|-----------------------------------|
| ID_Commande    | INT         | PRIMARY KEY         | Identifiant unique de la commande.|
| ID_Client      | INT         | FOREIGN KEY         | Référence au client (Clients).    |
| Date_Commande  | DATE        | NOT NULL            | Date de la commande.             |

#### **Détails_Commandes**

| Attribut       | Type        | Contrainte          | Description                       |
|----------------|-------------|---------------------|-----------------------------------|
| ID_Commande    | INT         | FOREIGN KEY         | Référence à la commande (Commandes). |
| ID_Livre       | INT         | FOREIGN KEY         | Référence au livre (Livres).          |
| Quantité       | INT         | NOT NULL            | Nombre d'exemplaires commandés.      |

---

## **Étape 2 : Implémentation en SQL**

### **2.1 Création des Tables**

```sql
CREATE TABLE Clients (
    ID_Client INT PRIMARY KEY,
    Nom VARCHAR(50) NOT NULL,
    Adresse VARCHAR(100),
    Téléphone VARCHAR(15) UNIQUE NOT NULL
);

CREATE TABLE Livres (
    ID_Livre INT PRIMARY KEY,
    Titre VARCHAR(100) NOT NULL,
    Auteur VARCHAR(50) NOT NULL,
    Prix DECIMAL(5,2) NOT NULL,
    Stock INT NOT NULL
);

CREATE TABLE Commandes (
    ID_Commande INT PRIMARY KEY,
    ID_Client INT,
    Date_Commande DATE NOT NULL,
    FOREIGN KEY (ID_Client) REFERENCES Clients(ID_Client)
);

CREATE TABLE Détails_Commandes (
    ID_Commande INT,
    ID_Livre INT,
    Quantité INT NOT NULL,
    PRIMARY KEY (ID_Commande, ID_Livre),
    FOREIGN KEY (ID_Commande) REFERENCES Commandes(ID_Commande),
    FOREIGN KEY (ID_Livre) REFERENCES Livres(ID_Livre)
);
```

---

## **Étape 3 : Insertion des Données**

### **3.1 Insertion des Clients**

```sql
INSERT INTO Clients (ID_Client, Nom, Adresse, Téléphone)
VALUES 
(1, 'Alice Dupont', '12, Rue des Lilas', '0600000001'),
(2, 'Bob Martin', '45, Avenue des Champs', '0600000002'),
(3, 'Clara Moreau', '78, Boulevard Haussmann', '0600000003');
```

### **3.2 Insertion des Livres**

```sql
INSERT INTO Livres (ID_Livre, Titre, Auteur, Prix, Stock)
VALUES
(101, 'Les Misérables', 'Victor Hugo', 15.99, 20),
(102, 'Le Petit Prince', 'Antoine de Saint-Exupéry', 10.50, 15),
(103, '1984', 'George Orwell', 12.99, 25),
(104, 'L''Étranger', 'Albert Camus', 9.99, 30);
```

### **3.3 Insertion des Commandes**

```sql
INSERT INTO Commandes (ID_Commande, ID_Client, Date_Commande)
VALUES
(1, 1, '2024-01-15'),
(2, 2, '2024-01-16'),
(3, 3, '2024-01-17');
```

### **3.4 Insertion des Détails des Commandes**

```sql
INSERT INTO Détails_Commandes (ID_Commande, ID_Livre, Quantité)
VALUES
(1, 101, 2), -- Alice achète 2 exemplaires de "Les Misérables"
(1, 102, 1), -- Alice achète 1 exemplaire de "Le Petit Prince"
(2, 103, 3), -- Bob achète 3 exemplaires de "1984"
(3, 104, 1); -- Clara achète 1 exemplaire de "L'Étranger"
```

---

## **Étape 4 : Requêtes SQL**

### **4.1 Lister toutes les commandes avec leurs clients**

```sql
SELECT Commandes.ID_Commande, Clients.Nom, Commandes.Date_Commande
FROM Commandes
JOIN Clients ON Commandes.ID_Client = Clients.ID_Client;
```

### **4.2 Détails des livres commandés pour une commande spécifique**

```sql
SELECT Détails_Commandes.ID_Commande, Livres.Titre, Détails_Commandes.Quantité
FROM Détails_Commandes
JOIN Livres ON Détails_Commandes.ID_Livre = Livres.ID_Livre
WHERE Détails_Commandes.ID_Commande = 1;
```

### **4.3 Calculer le total des ventes par livre**

```sql
SELECT Livres.Titre, SUM(Détails_Commandes.Quantité) AS Total_Vendus
FROM Livres
JOIN Détails_Commandes ON Livres.ID_Livre = Détails_Commandes.ID_Livre
GROUP BY Livres.Titre;
```

### **4.4 Afficher le stock restant**

```sql
SELECT Livres.Titre, (Livres.Stock - COALESCE(SUM(Détails_Commandes.Quantité), 0)) AS Stock_Restant
FROM Livres
LEFT JOIN Détails_Commandes ON Livres.ID_Livre = Détails_Commandes.ID_Livre
GROUP BY Livres.Titre, Livres.Stock;
```

---

## **Étape 5 : Exercices**

1. Ajouter un nouveau client, passer une commande, et vérifier l'impact sur le stock.  
2. Identifier le client ayant dépensé le plus d'argent.  
3. Mettre à jour le prix d’un livre et recalculer les totaux de ventes.  

---
