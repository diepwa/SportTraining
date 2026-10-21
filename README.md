# Calisthénie

Une app d'entraînement en un seul fichier (`index.html`), sans build. Ouvre-la dans le navigateur ou ajoute-la à l'écran d'accueil.

## Ce qu'elle fait

- **Les semaines s'enchaînent toutes seules.** Chaque séance est enregistrée à sa date. Le lundi, une nouvelle semaine commence et les flèches ‹ › permettent de revoir les semaines passées.
- **Tu notes ce que tu fais vraiment.** Touche une série pour la valider à la cible. Touche-la à nouveau pour corriger le nombre de reps (ou de secondes).
- **Ressenti en fin de séance** : 😵 J'en ai chié, 👍 OK ou 😎 Facile.
- **La progression est automatique.** Chaque exercice a une fourchette (par exemple 8-12) et la cible de la semaine est calculée le lundi :
  - si tu as réussi toutes les séries et que c'était OK ou facile : +1 rep (ou +5 s). Si c'était facile et que tu as dépassé la cible de 2 sur toutes les séries : +2 ;
  - si c'était réussi mais très dur : la cible ne bouge pas ;
  - si tu as manqué la cible : elle ne bouge pas. Deux semaines manquées de suite : −1 ;
  - si tu as eu 3 séances très dures dans la semaine : la cible ne bouge sur aucun exercice (consolidation) ;
  - en haut de la fourchette, l'app te propose le **niveau suivant** (par exemple frog → crow) et la cible repart du bas.
  - Le fractionné progresse en nombre de répétitions (6 → 8). Le yoga et les échauffements ne progressent pas.
- **Synchro cloud avec Firebase** (optionnelle), plus une sauvegarde manuelle en `.json`.

Les données de l'ancienne version (`calisthenie.v1`) sont reprises automatiquement au premier lancement.

## Activer la synchro Firebase (environ 5 minutes)

1. Va sur <https://console.firebase.google.com>, clique sur **Créer un projet** (Google Analytics n'est pas utile).
2. **Authentication** → Commencer → **Sign-in method** → active **Adresse e-mail/Mot de passe**.
3. **Firestore Database** → Créer une base de données → mode **production** → région `eur3` (Europe).
4. Onglet **Règles** de Firestore : colle le contenu de [`firestore.rules`](firestore.rules), puis **Publier**. Avec ces règles, chaque compte ne peut lire et écrire que ses propres données.
5. Paramètres du projet (⚙️) → **Vos applications** → icône Web `</>` → donne un nom → **Enregistrer**. Copie l'objet `firebaseConfig`.
6. Dans `index.html`, la config du projet `sporttraining-130bb` est déjà en place (`var FIREBASE_CONFIG`). Pour un autre projet, remplace-la par ton objet `firebaseConfig`.
7. Authentication → **Paramètres** → **Domaines autorisés** : ajoute le domaine où l'app est hébergée (par exemple `diepwa.github.io` ou `ton-site.netlify.app`).
8. Ouvre l'app, touche le bouton **Connexion** en haut, puis **Créer un compte**. Ton historique local est envoyé dans le cloud.

La config Firebase n'est pas secrète : elle peut être publique dans le repo. Ce sont les règles Firestore qui protègent les données.

## Données

- Sur l'appareil : `localStorage`, clé `calisthenie.v2`. L'app sert aussi de cache hors ligne.
- Dans Firestore : `users/{uid}` (programme et records), `users/{uid}/logs/{date}` (séances) et `users/{uid}/weeks/{lundi}` (cibles et changements de chaque semaine).
- Pour la fusion entre appareils, c'est l'élément modifié le plus récemment qui gagne.
