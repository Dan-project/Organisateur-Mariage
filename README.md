# Eden & Dan — Organisateur de mariage

Un site web à deux : vous et votre fiancé·e éditez la même page, vos
modifications apparaissent l'un chez l'autre en temps réel, sans compte à
créer et sans copier-coller.

- **Hébergement** : GitHub Pages (ce dépôt).
- **Données partagées** : Firebase Firestore (base de données gratuite en
  temps réel).

## Mise en route (à faire une seule fois, ~5 minutes)

### 1. Créer le projet Firebase

1. Allez sur [console.firebase.google.com](https://console.firebase.google.com)
   et connectez-vous avec un compte Google (le vôtre).
2. **Ajouter un projet** → donnez-lui un nom (ex. `mariage-eden-dan`) →
   désactivez Google Analytics (pas utile ici) → **Créer le projet**.
3. Dans le menu de gauche : **Créer une application web** (icône `</>`) →
   donnez-lui un nom → **Enregistrer l'application**. Firebase affiche un
   bloc `firebaseConfig = { apiKey: "...", ... }` : gardez cette page ouverte,
   vous en aurez besoin à l'étape 4.

### 2. Activer Firestore (la base de données)

1. Menu de gauche → **Build** → **Firestore Database** → **Créer une base de
   données**.
2. Choisissez un emplacement (ex. `eur3 (europe-west)`), puis démarrez en
   **mode production**.
3. Une fois créée, onglet **Règles** → remplacez le contenu par :

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /wedding/{document=**} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```

   → **Publier**.

   Cela veut dire : seule une personne "connectée" (même anonymement, voir
   étape 3) peut lire/écrire les données du mariage.

### 3. Activer la connexion anonyme

1. Menu de gauche → **Build** → **Authentication** → **Get started**.
2. Onglet **Sign-in method** → **Anonymous** → activer → **Enregistrer**.

   Grâce à ça, le site connecte chaque visiteur silencieusement en
   arrière-plan — ni vous ni votre fiancé·e ne verrez jamais d'écran de
   connexion.

### 4. Brancher la config dans le site

Ouvrez `index.html`, cherchez ce bloc (vers la ligne 957) :

```js
const firebaseConfig = {
  apiKey: "REMPLACER_MOI",
  authDomain: "REMPLACER_MOI.firebaseapp.com",
  projectId: "REMPLACER_MOI",
  storageBucket: "REMPLACER_MOI.appspot.com",
  messagingSenderId: "REMPLACER_MOI",
  appId: "REMPLACER_MOI"
};
```

Remplacez ces 6 valeurs par celles de la page Firebase de l'étape 1 (copiez-collez
tel quel), enregistrez, et poussez (`git commit` + `git push`) sur GitHub.

Ces clés ne sont pas secrètes : c'est la configuration standard d'une app web
Firebase, elles sont censées apparaître dans le code envoyé au navigateur. La
sécurité vient des **règles Firestore** de l'étape 2, pas du secret de ces
valeurs.

### 5. Activer GitHub Pages

1. Sur GitHub, dans ce dépôt : **Settings → Pages**.
2. **Source** : `Deploy from a branch`.
3. **Branch** : choisissez la branche où vit `index.html` (actuellement
   `claude/wedding-planner-shared-lj6bc1`, ou `main` une fois fusionné) et le
   dossier `/ (root)`.
4. **Save**. GitHub vous donne une URL du type
   `https://dan-project.github.io/organisateur-mariage/` — c'est le lien à
   partager avec votre fiancé·e (favoris, message, peu importe).

## À savoir

- N'importe qui possédant ce lien (et connu de personne d'autre) peut voir et
  modifier les données — comme un Google Doc partagé par lien. Ne le publiez
  pas publiquement.
- Le plan gratuit Firebase (Spark) est largement suffisant pour cet usage
  (deux personnes, quelques centaines de lignes de données).
- Aucune installation nécessaire pour la consulter : un navigateur suffit,
  ordinateur ou téléphone.
