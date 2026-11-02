# Padel Stats – version Firebase (GitHub Pages)

Un seul fichier d'appli (`index.html`) + les règles de sécurité (`firestore.rules`).
Les données sont dans Firestore, projet **padelstats-e4b27**.

## 1. Préparer Firebase (console Firebase, une seule fois)

1. **Build → Firestore Database → Créer une base** (mode production).
2. **Build → Authentication → Sign-in method** : activer **Google** et **Anonyme**.
3. **Authentication → Settings → Authorized domains** : ajouter `TON-PSEUDO.github.io`
   (sans cela, la connexion Google est refusée sur GitHub Pages).
4. **Firestore → Règles** : ouvrir `firestore.rules`, remplacer
   `ton.adresse@gmail.com` par **l'adresse Google de l'admin** (en minuscules),
   coller le tout dans l'éditeur et **Publier**.

## 2. Publier sur GitHub Pages

1. Créer un dépôt GitHub, y déposer `index.html` (à la racine).
2. **Settings → Pages → Deploy from a branch → main / (root)**.
3. L'adresse sera `https://TON-PSEUDO.github.io/NOM-DU-DEPOT/`.

## 3. Premier démarrage (admin)

Ouvrir l'adresse → **Connexion admin (Google)** avec l'adresse mise dans les règles.
Tout est alors enregistré dans Firestore sous ce compte. Un autre compte Google est refusé.

### Reprendre les analyses de la version Claude
Dans l'ancienne version, bouton d'export de chaque analyse (fichier `.json`).
Dans la nouvelle : icône d'import (plusieurs fichiers possibles). Les joueurs sont
rattachés par leur nom (créés s'ils n'existent pas). Les **alias vocaux** ne sont pas
dans les fichiers : à ressaisir dans la page de chaque joueur.

## 4. Partager avec un joueur

1. Admin : **profil → clic sur le joueur → Accès du joueur → Générer un code**.
2. Donner au joueur l'adresse de l'appli et son code.
3. Le joueur ouvre l'appli → **Je suis un joueur** → choisit son profil → saisit le code.
   Il voit uniquement les analyses où il joue (Overview, Statistiques, PDF), en lecture seule.
   Son appareil reste connecté.
4. **Révoquer les connexions de ce joueur** coupe l'accès de ses appareils ; **Nouveau code**
   protège les futures connexions.

## 5. À savoir

- La clé `apiKey` n'est pas un secret : la sécurité vient des règles Firestore.
- La liste des **noms de joueurs** est lisible par toute personne qui ouvre l'appli et se
  connecte en anonyme (c'est ce qui permet d'afficher « Qui es-tu ? »). Les analyses, elles,
  ne le sont pas.
- Hors connexion, l'admin peut continuer à saisir : Firestore met les écritures en attente et
  les envoie au retour du réseau (ne pas vider les données du navigateur avant).
- Une analyse est limitée à ~1 Mo ; l'historique d'annulation est réduit aux 60 derniers
  clics de chaque set à l'enregistrement.
- Limite connue : le micro / la reconnaissance vocale dépendent du navigateur (Chrome, Edge,
  Safari). Sur GitHub Pages (HTTPS) le micro peut être autorisé normalement.

## 6. Vérifier que tout est bien sécurisé (5 minutes)

1. Connexion admin : créer un joueur → il apparaît dans Firestore (`players`).
2. Créer une analyse → document dans `matches` avec `playerIds`.
3. Dans une fenêtre privée : **Je suis un joueur** → mauvais code refusé, bon code accepté,
   seules ses analyses s'affichent.
4. Console Firebase → Firestore → **Règles → Simulateur** : lire `/matches/x` sans être
   connecté doit être refusé.
