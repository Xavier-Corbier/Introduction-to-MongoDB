# **TP 1 : Manipulation des bases MongoDB via Compass et CLI**  

---

## **🎯 Objectifs pédagogiques**  

À la fin de ce TP, vous serez capable de :  
✅ Créer et gérer des bases de données et collections via la CLI et MongoDB Compass.  
✅ Insérer, rechercher, mettre à jour et supprimer des documents.  
✅ Utiliser des filtres et requêtes avancées pour interagir avec les données.  
✅ Comprendre les différences entre l’utilisation en **ligne de commande** et via **Compass**.  

---

## **📚 Contenu du TP**  

## **1️⃣ Installation et démarrage de MongoDB**  

### **🔹 Vérifier si MongoDB est installé**  
Dans un terminal (Windows PowerShell, macOS Terminal ou Linux Bash), exécuter :  
```sh
mongod --version
mongosh --version
```
📌 **Si MongoDB n’est pas installé** : suivre la procédure d’installation de la **Session 1 - Partie 2**.  

### **🔹 Démarrer MongoDB (si ce n’est pas automatique)**  
📌 **Sur Windows**  
```sh
net start MongoDB
```
📌 **Sur macOS/Linux**  
```sh
sudo systemctl start mongod
```

### **🔹 Lancer le shell MongoDB (`mongosh`)**  
Dans un terminal, taper :  
```sh
mongosh
```
📌 Vous êtes maintenant dans l’interface **CLI** de MongoDB !  

---

## **2️⃣ Création et gestion des bases de données**  

### **🔹 Créer une nouvelle base de données**  
Dans `mongosh`, taper :  
```sh
use entreprise
```
📌 **Remarque** : MongoDB **ne crée réellement la base** que lorsqu’un premier document est inséré.  

### **🔹 Vérifier les bases existantes**  
```sh
show dbs
```
📌 **Remarque** : La base `entreprise` ne s'affichera pas tant qu’un document n’aura pas été inséré.  

---

## **3️⃣ Création et gestion des collections**  

### **🔹 Créer une collection explicitement**  
```sh
db.createCollection("employes")
```
📌 MongoDB crée les collections **automatiquement** lors de l’insertion du premier document.  

### **🔹 Lister les collections dans la base active**  
```sh
show collections
```

---

## **4️⃣ Insertion de documents**  

### **🔹 Insérer un employé avec `insertOne()`**  
```sh
db.employes.insertOne({
  "nom": "Alice Dupont",
  "poste": "Développeur",
  "email": "alice.dupont@email.com",
  "salaire": 4000
})
```

### **🔹 Insérer plusieurs documents avec `insertMany()`**  
```sh
db.employes.insertMany([
  { "nom": "Bob Martin", "poste": "Manager", "email": "bob.martin@email.com", "salaire": 5000 },
  { "nom": "Claire Durand", "poste": "Designer", "email": "claire.durand@email.com", "salaire": 3500 }
])
```

---

## **5️⃣ Recherche et filtrage des documents**  

### **🔹 Afficher tous les employés**  
```sh
db.employes.find().pretty()
```

### **🔹 Trouver un employé par son nom**  
```sh
db.employes.findOne({ "nom": "Alice Dupont" })
```

### **🔹 Trouver les employés avec un salaire supérieur à 4000**  
```sh
db.employes.find({ "salaire": { "$gt": 4000 } })
```

### **🔹 Afficher uniquement les noms et salaires (projection)**  
```sh
db.employes.find({}, { "nom": 1, "salaire": 1, "_id": 0 })
```

---

## **6️⃣ Mise à jour des documents**  

### **🔹 Modifier le salaire d’Alice avec `updateOne()`**  
```sh
db.employes.updateOne(
  { "nom": "Alice Dupont" },
  { "$set": { "salaire": 4200 } }
)
```

### **🔹 Augmenter le salaire de tous les employés avec `updateMany()`**  
```sh
db.employes.updateMany(
  {},
  { "$inc": { "salaire": 200 } }
)
```

---

## **7️⃣ Suppression de documents et collections**  

### **🔹 Supprimer un employé avec `deleteOne()`**  
```sh
db.employes.deleteOne({ "nom": "Bob Martin" })
```

### **🔹 Supprimer tous les employés ayant un salaire inférieur à 4000**  
```sh
db.employes.deleteMany({ "salaire": { "$lt": 4000 } })
```

### **🔹 Supprimer toute la collection `employes`**  
```sh
db.employes.drop()
```

### **🔹 Supprimer la base de données `entreprise`**  
```sh
db.dropDatabase()
```

---

## **8️⃣ Utilisation de MongoDB Compass**  

MongoDB Compass permet d’interagir graphiquement avec la base **sans utiliser la ligne de commande**.  

### **🔹 Se connecter à MongoDB Compass**  
1️⃣ Ouvrir **MongoDB Compass**.  
2️⃣ Entrer l’URI de connexion :  
   ```
   mongodb://localhost:27017
   ```
3️⃣ Cliquer sur **Connect**.  

### **🔹 Créer une base et une collection**  
1️⃣ Aller dans **"Create Database"**.  
2️⃣ Entrer `entreprise` comme nom de base.  
3️⃣ Ajouter une collection `employes` et cliquer sur **Create Database**.  

### **🔹 Ajouter un employé**  
1️⃣ Aller dans la collection **employes**.  
2️⃣ Cliquer sur **"Insert Document"**.  
3️⃣ Ajouter un document JSON :  
   ```json
   {
     "nom": "David Leroy",
     "poste": "Analyste",
     "email": "david.leroy@email.com",
     "salaire": 3800
   }
   ```
4️⃣ Cliquer sur **Insert**.  

### **🔹 Rechercher un employé**  
1️⃣ Dans la section **Documents**, entrer le filtre :  
   ```json
   { "poste": "Développeur" }
   ```
2️⃣ Cliquer sur **Find** pour afficher les résultats.  

### **🔹 Modifier un employé**  
1️⃣ Cliquer sur un document et sur **Edit Document**.  
2️⃣ Modifier un champ (`salaire` par exemple).  
3️⃣ Sauvegarder les changements.  

### **🔹 Supprimer un employé**  
1️⃣ Sélectionner un document.  
2️⃣ Cliquer sur **Delete** et confirmer.  

---

## **🛠️ Activités pratiques**  

### **Exercice 1 : Gestion d’une bibliothèque**  
📌 **Objectif** : Manipuler une base MongoDB pour une bibliothèque.  

1️⃣ **Créer une base `bibliotheque` et une collection `livres`**.  
2️⃣ **Insérer plusieurs livres** avec les champs : `titre`, `auteur`, `annee`, `disponible`.  
3️⃣ **Rechercher tous les livres d’un auteur spécifique**.  
4️⃣ **Afficher uniquement les titres et les années de publication**.  
5️⃣ **Mettre à jour la disponibilité d’un livre emprunté**.  
6️⃣ **Supprimer un livre de la collection**.  
7️⃣ **Tester toutes ces manipulations sur MongoDB Compass**.  

---

## **📖 Questions de révision**  

✔ Quelle est la différence entre **MongoDB Compass et la CLI** ?  
✔ Comment insérer, rechercher et modifier des documents en **MongoDB Shell** ?  
✔ Comment utiliser **Compass** pour visualiser et modifier les données ?  
✔ Quelle commande permet de **supprimer une base de données entière** ?  
