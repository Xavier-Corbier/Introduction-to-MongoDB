# **TP 3 : Optimisation des requêtes et Indexation**  

---

## **🎯 Objectifs pédagogiques**  

À la fin de ce TP, vous serez capable de :  
✅ Comprendre l’impact des index sur les performances des requêtes MongoDB.  
✅ Créer et gérer différents types d’index pour optimiser les recherches.  
✅ Analyser les performances des requêtes avec `explain()`.  
✅ Utiliser des index avancés (textuels, partiels, TTL, géospatiaux).  

---

## **📚 Contenu du TP**  

## **1️⃣ Comprendre les index et leur importance**  

Un **index** est une structure qui **accélère** les requêtes en évitant un **scan complet de la collection**.  

📌 **Sans index** → MongoDB parcourt **tous les documents** pour trouver une correspondance (**slow query**).  
📌 **Avec index** → MongoDB accède directement aux documents recherchés (**fast query**).  

**Exemple :**  
❌ **Sans index** :  
```sh
db.utilisateurs.find({ "email": "alice@email.com" })
```
✅ **Avec index** :  
```sh
db.utilisateurs.createIndex({ "email": 1 })
db.utilisateurs.find({ "email": "alice@email.com" })
```

---

## **2️⃣ Création et gestion des index**  

### **🔹 Lister les index existants**  
```sh
db.utilisateurs.getIndexes()
```

### **🔹 Créer un index simple sur un champ**  
```sh
db.utilisateurs.createIndex({ "email": 1 })  # Index ascendant
db.utilisateurs.createIndex({ "nom": -1 })   # Index descendant
```
📌 **Explication** :  
✔ `1` = tri croissant, `-1` = tri décroissant.  
✔ Accélère les recherches sur `email` et `nom`.  

### **🔹 Créer un index composé sur plusieurs champs**  
```sh
db.utilisateurs.createIndex({ "nom": 1, "age": -1 })
```
📌 **Explication** :  
✔ Optimise les requêtes filtrant **nom et âge simultanément**.  
✔ **L’ordre des champs est important** !  

### **🔹 Supprimer un index**  
```sh
db.utilisateurs.dropIndex("nom_1_age_-1")  # Remplacer par le nom correct de l'index
```

---

## **3️⃣ Analyser les performances avec `explain()`**  

### **🔹 Exécuter une requête et analyser son plan d’exécution**  
```sh
db.utilisateurs.find({ "email": "alice@email.com" }).explain("executionStats")
```
📌 **Vérifier** :  
✔ `"totalDocsExamined"` → **Si trop élevé**, ajouter un index.  
✔ `"indexUsed"` → Vérifier que l’index est bien utilisé.  

---

## **4️⃣ Index avancés**  

### **🔹 Index unique (`unique`)**  
Empêche les doublons sur un champ.  
```sh
db.utilisateurs.createIndex({ "email": 1 }, { unique: true })
```

### **🔹 Index partiel (`partialFilterExpression`)**  
Indexe uniquement les documents respectant une condition.  
```sh
db.commandes.createIndex({ "statut": 1 }, { partialFilterExpression: { "statut": "livré" } })
```
📌 **Explication** :  
✔ Optimise les recherches pour `statut = "livré"` uniquement.  

### **🔹 Index TTL (Expiration des documents)**  
Supprime automatiquement les documents après un délai.  
```sh
db.sessions.createIndex({ "date_creation": 1 }, { expireAfterSeconds: 3600 })
```
📌 **Explication** :  
✔ Idéal pour gérer des **sessions temporaires** ou des **logs éphémères**.  

### **🔹 Index textuel (`text`)**  
Permet des **recherches avancées sur du texte**.  
```sh
db.articles.createIndex({ "titre": "text", "description": "text" })
db.articles.find({ "$text": { "$search": "MongoDB" } })
```
📌 **Explication** :  
✔ Recherche **intelligente** dans `titre` et `description`.  

### **🔹 Index géospatial (`2dsphere`)**  
Permet des **recherches de proximité**.  
```sh
db.magasins.createIndex({ "localisation": "2dsphere" })
db.magasins.find({
  "localisation": {
    "$near": {
      "$geometry": { "type": "Point", "coordinates": [2.3522, 48.8566] },
      "$maxDistance": 5000  # Rayon en mètres
    }
  }
})
```
📌 **Explication** :  
✔ Recherche des **magasins proches d’une position GPS**.  

---

## **5️⃣ Optimisation des requêtes avec `hint()`**  

### **🔹 Forcer l’utilisation d’un index spécifique**  
```sh
db.utilisateurs.find({ "nom": "Alice" }).hint({ "nom": 1 })
```
📌 **Explication** :  
✔ `hint()` force MongoDB à utiliser **un index précis** pour exécuter la requête.  

---

## **6️⃣ Utilisation de MongoDB Compass pour les index**  

### **🔹 Visualiser les index existants**  
1️⃣ Ouvrir **MongoDB Compass**.  
2️⃣ Sélectionner une **base de données** et une **collection**.  
3️⃣ Aller dans l’onglet **Indexes** pour voir les index existants.  

### **🔹 Ajouter un index**  
1️⃣ Cliquer sur **"Create Index"**.  
2️⃣ Sélectionner les champs à indexer.  
3️⃣ Spécifier les options (`unique`, `text`, `TTL`).  

### **🔹 Tester une requête avec `Explain Plan`**  
1️⃣ Exécuter une requête (`find()` avec un filtre).  
2️⃣ Cliquer sur **"Explain Plan"**.  
3️⃣ Vérifier si **MongoDB utilise un index** ou fait un **scan complet**.  

---

## **🛠️ Activités pratiques**  

### **Exercice 1 : Optimisation d’un site e-commerce**  
📌 **Objectif** : Améliorer les performances des requêtes sur une base e-commerce.  

1️⃣ **Créer une collection `produits`** avec les champs : `nom`, `prix`, `categorie`, `stock`.  
2️⃣ **Insérer 1000 produits** (peut être généré avec un script).  
3️⃣ **Créer des index optimisés** :  
   - Index **unique** sur `nom`.  
   - Index **composé** sur `categorie, prix`.  
   - Index **textuel** sur `nom, description`.  
4️⃣ **Exécuter des requêtes avec `explain()`** avant et après l’indexation.  
5️⃣ **Tester les performances avec Compass (`Explain Plan`)**.  

---

## **📖 Questions de révision**  

✔ Pourquoi les **index sont-ils essentiels** en MongoDB ?  
✔ Comment vérifier si une requête utilise un index ?  
✔ Quelle est la différence entre un **index simple** et un **index composé** ?  
✔ Dans quels cas utiliser un **index TTL** ?  
✔ Comment forcer MongoDB à utiliser un index avec `hint()` ?  

