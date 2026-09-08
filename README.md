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

### 3. Activer le mot de passe (Authentication)

Le site est protégé par un mot de passe unique, partagé entre vous deux (ce
n'est pas un "compte" à créer de votre côté — juste un mot de passe à retenir).

1. Menu de gauche → **Build** → **Authentication** → **Get started**.
2. Onglet **Sign-in method** → **Email/Password** → activer (le premier
   interrupteur, pas "Email link") → **Enregistrer**.
3. Si **Anonymous** apparaît dans la liste des fournisseurs et qu'il est
   activé, **désactivez-le** — sinon quelqu'un de technique pourrait
   contourner le mot de passe.
4. Onglet **Users** → **Add user** :
   - Email : `acces@mariage-eden-dan.app` (exactement ce texte — c'est un
     identifiant technique, pas une vraie adresse email, il doit
     correspondre à `SITE_LOGIN_EMAIL` dans `index.html`)
   - Password : choisissez le mot de passe que vous partagerez avec votre
     fiancé·e (au moins 6 caractères).
   - **Add user**.

C'est ce mot de passe (pas l'email) que vous communiquerez à votre fiancé·e.
Une fois entré dans son navigateur, elle n'aura plus à le retaper (session
gardée en mémoire par le navigateur), sauf si elle vide son cache ou change
d'appareil.

### 4. Vérifier que les règles Firestore sont bien publiées

C'est l'étape la plus facile à rater : si elle n'est pas faite, le site
s'affiche normalement mais **rien ne s'enregistre** (un ajout au budget par
exemple semble fonctionner puis disparaît quelques secondes après).

1. Firestore Database → onglet **Règles**.
2. Le contenu doit être *exactement* :

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

   Si vous voyez encore `allow read, write: if false;`, remplacez tout le
   texte par le bloc ci-dessus et cliquez **Publier**.

3. Pour vérifier que ça fonctionne : ouvrez le site, connectez-vous avec le
   mot de passe, ajoutez n'importe quoi (une tâche, une ligne de budget).
   Puis dans Firestore → onglet **Données**, vous devez voir apparaître une
   collection `wedding` avec un document `state` contenant ce que vous venez
   d'ajouter. Si cette collection reste vide après un ajout, les règles ne
   sont pas publiées correctement — recommencez l'étape 2.

### 5. Brancher la config dans le site

Ouvrez `index.html`, cherchez le bloc `const firebaseConfig = { ... }`
(recherchez `firebaseConfig`). Remplacez les 6-7 valeurs par celles de votre
projet (Project settings → Vos applications → SDK config), enregistrez, et
poussez (`git commit` + `git push`) sur GitHub.

Ces clés ne sont pas secrètes : c'est la configuration standard d'une app web
Firebase, elles sont censées apparaître dans le code envoyé au navigateur. La
sécurité vient des **règles Firestore** (étape 4) et du **mot de passe**
(étape 3), pas du secret de ces valeurs.

### 6. Activer GitHub Pages

1. Sur GitHub, dans ce dépôt : **Settings → Pages**.
2. **Source** : `Deploy from a branch`.
3. **Branch** : choisissez la branche où vit `index.html` (actuellement
   `claude/wedding-planner-shared-lj6bc1`, ou `main` une fois fusionné) et le
   dossier `/ (root)`.
4. **Save**. GitHub vous donne une URL du type
   `https://dan-project.github.io/organisateur-mariage/` — c'est le lien à
   partager avec votre fiancé·e (favoris, message, peu importe).

## À savoir

- Le dépôt GitHub est public (nécessaire pour GitHub Pages gratuit), donc le
  code du site est visible par tout le monde — mais pas vos données : sans le
  mot de passe (étape 3), un visiteur ne voit qu'un écran de connexion vide,
  jamais le budget, les prestataires ou les notes.
- Ne partagez le mot de passe qu'avec votre fiancé·e.
- Le plan gratuit Firebase (Spark) est largement suffisant pour cet usage
  (deux personnes, quelques centaines de lignes de données).
- Aucune installation nécessaire pour la consulter : un navigateur suffit,
  ordinateur ou téléphone.
