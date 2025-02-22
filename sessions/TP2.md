# **TP 2 : Développement d’une API avec MongoDB et Node.js**  

---

## **🎯 Objectifs pédagogiques**  

À la fin de ce TP, vous serez capable de :  
✅ Installer et configurer **MongoDB** avec **Node.js**.  
✅ Développer une **API REST** en utilisant **Express.js**.  
✅ Effectuer des opérations CRUD avec MongoDB à travers l’API.  
✅ Tester l’API avec **Postman** ou **cURL**.  

---

## **📚 Contenu du TP**  

## **1️⃣ Prérequis et installation**  

### **🔹 Vérifier que Node.js et npm sont installés**  
Dans un terminal, taper :  
```sh
node -v
npm -v
```
📌 **Si Node.js n’est pas installé** : télécharger depuis [nodejs.org](https://nodejs.org/).  

### **🔹 Installer MongoDB et le démarrer**  
📌 **Démarrer MongoDB** (si ce n’est pas automatique) :  
```sh
mongod --dbpath /data/db
```

### **🔹 Créer un projet Node.js**  
Dans un terminal, naviguer vers un dossier de travail et exécuter :  
```sh
mkdir api-mongodb
cd api-mongodb
npm init -y
```
📌 **Explication** :  
✔ `mkdir api-mongodb` → Crée un dossier pour le projet.  
✔ `npm init -y` → Initialise un projet **Node.js** avec un `package.json`.  

---

## **2️⃣ Installation des dépendances**  

### **🔹 Installer Express et MongoDB Driver**  
```sh
npm install express mongoose dotenv cors
```
📌 **Explication** :  
✔ `express` → Framework pour créer l’API.  
✔ `mongoose` → Bibliothèque pour interagir avec MongoDB.  
✔ `dotenv` → Gestion des variables d’environnement.  
✔ `cors` → Permettre les requêtes depuis d’autres domaines (Cross-Origin Resource Sharing).  

---

## **3️⃣ Configuration du projet**  

### **🔹 Créer les fichiers principaux**  
Dans le dossier `api-mongodb`, créer les fichiers suivants :  
```sh
touch server.js .env
mkdir models routes
touch models/Produit.js routes/produitRoutes.js
```

### **🔹 Configurer MongoDB dans `.env`**  
Dans `.env`, ajouter :  
```
PORT=5000
MONGO_URI=mongodb://localhost:27017/api_mongodb
```

---

## **4️⃣ Création du serveur Express**  

Ouvrir `server.js` et ajouter :  
```js
require("dotenv").config();
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");

// Initialisation de l'application
const app = express();
app.use(express.json());
app.use(cors());

// Connexion à MongoDB
mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log("✅ Connecté à MongoDB"))
  .catch(err => console.error("❌ Erreur de connexion à MongoDB", err));

// Importer les routes
const produitRoutes = require("./routes/produitRoutes");
app.use("/api/produits", produitRoutes);

// Démarrer le serveur
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`🚀 Serveur démarré sur le port ${PORT}`));
```

📌 **Explication** :  
✔ Charge les variables `.env` avec `dotenv`.  
✔ Initialise **Express** et **MongoDB**.  
✔ Active les **CORS** pour autoriser les requêtes.  
✔ Déclare une route `/api/produits` pour les produits.  
✔ Démarre le serveur sur `http://localhost:5000`.  

---

## **5️⃣ Création du modèle MongoDB avec Mongoose**  

Ouvrir `models/Produit.js` et ajouter :  
```js
const mongoose = require("mongoose");

const produitSchema = new mongoose.Schema({
  nom: { type: String, required: true },
  prix: { type: Number, required: true },
  stock: { type: Number, default: 0 }
});

module.exports = mongoose.model("Produit", produitSchema);
```

📌 **Explication** :  
✔ Définit un **modèle Produit** avec `nom`, `prix` et `stock`.  
✔ `required: true` impose que `nom` et `prix` soient obligatoires.  
✔ Exporte le modèle pour l’utiliser dans l’API.  

---

## **6️⃣ Création des routes CRUD**  

Ouvrir `routes/produitRoutes.js` et ajouter :  
```js
const express = require("express");
const Produit = require("../models/Produit");
const router = express.Router();

// 🟢 GET - Récupérer tous les produits
router.get("/", async (req, res) => {
  const produits = await Produit.find();
  res.json(produits);
});

// 🔵 POST - Ajouter un produit
router.post("/", async (req, res) => {
  const { nom, prix, stock } = req.body;
  const produit = new Produit({ nom, prix, stock });
  await produit.save();
  res.status(201).json(produit);
});

// 🟡 PUT - Mettre à jour un produit
router.put("/:id", async (req, res) => {
  const { nom, prix, stock } = req.body;
  const produit = await Produit.findByIdAndUpdate(req.params.id, { nom, prix, stock }, { new: true });
  res.json(produit);
});

// 🔴 DELETE - Supprimer un produit
router.delete("/:id", async (req, res) => {
  await Produit.findByIdAndDelete(req.params.id);
  res.json({ message: "Produit supprimé" });
});

module.exports = router;
```

📌 **Explication** :  
✔ Définit **quatre routes CRUD** pour gérer les produits :  
  - `GET /api/produits` → Récupère tous les produits.  
  - `POST /api/produits` → Ajoute un produit.  
  - `PUT /api/produits/:id` → Modifie un produit.  
  - `DELETE /api/produits/:id` → Supprime un produit.  

---

## **7️⃣ Tester l’API avec Postman ou cURL**  

### **🔹 Démarrer le serveur**  
```sh
node server.js
```
📌 **L’API tourne sur `http://localhost:5000`**.  

### **🔹 Tester avec Postman ou cURL**  

#### ✅ **1. Récupérer tous les produits**  
```sh
curl -X GET http://localhost:5000/api/produits
```

#### ✅ **2. Ajouter un produit**  
```sh
curl -X POST http://localhost:5000/api/produits -H "Content-Type: application/json" -d '{
  "nom": "Ordinateur Portable",
  "prix": 1200,
  "stock": 10
}'
```

#### ✅ **3. Modifier un produit (mettre un `id` existant)**  
```sh
curl -X PUT http://localhost:5000/api/produits/ID_DU_PRODUIT -H "Content-Type: application/json" -d '{
  "nom": "Ordinateur Gamer",
  "prix": 1500,
  "stock": 5
}'
```

#### ✅ **4. Supprimer un produit (mettre un `id` existant)**  
```sh
curl -X DELETE http://localhost:5000/api/produits/ID_DU_PRODUIT
```

---

## **🛠️ Activités pratiques**  

### **Exercice 1 : Gestion des utilisateurs**  
📌 **Objectif** : Ajouter une gestion des utilisateurs à l’API.  

1️⃣ **Créer un modèle `Utilisateur`** avec `nom`, `email`, `motDePasse`.  
2️⃣ **Créer un fichier `routes/utilisateurRoutes.js`** pour gérer :  
   - `GET /api/utilisateurs` → Récupérer tous les utilisateurs.  
   - `POST /api/utilisateurs` → Ajouter un utilisateur.  
   - `PUT /api/utilisateurs/:id` → Modifier un utilisateur.  
   - `DELETE /api/utilisateurs/:id` → Supprimer un utilisateur.  
3️⃣ **Tester l’API avec Postman ou cURL**.  

---

## **📖 Questions de révision**  

✔ Comment connecter MongoDB à une API Node.js ?  
✔ Quelle est la différence entre **Mongoose** et **MongoDB Driver natif** ?  
✔ Quelles sont les étapes pour créer un **CRUD complet** en Express.js ?  
✔ Comment tester une API avec **Postman** ou **cURL** ?  
