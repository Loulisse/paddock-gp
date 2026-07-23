# PADDOCK GP — Guide d'installation (pour Loris)

App single-file, GitHub Pages, sync via API GitHub (même principe qu'Aurora Budget).
Temps d'installation : ~15 minutes. À faire AVANT d'offrir.

## 1. Créer le repo

1. Sur GitHub (compte **Loulisse**) : nouveau repo **public** nommé `paddock-gp`.
2. Glisse-dépose tous les fichiers de ce dossier (ta méthode habituelle) en
   respectant la structure :
   ```
   index.html
   MODE_EMPLOI.txt
   data/records.json
   data/gp/index.json
   data/archives/README.txt
   ```
3. Settings → Pages → Source : branche `main`, dossier `/ (root)` → Save.
   L'app sera sur : `https://loulisse.github.io/paddock-gp/`

Si tu choisis un autre nom de repo, adapte `GH_REPO` dans le CONFIG de
`index.html`.

## 2. Créer le token GitHub (le point important)

1. GitHub → Settings (ton profil) → Developer settings →
   **Fine-grained personal access tokens** → Generate new token.
2. Réglages :
   - Repository access : **Only select repositories** → `paddock-gp` UNIQUEMENT.
   - Permissions → Repository permissions → **Contents : Read and write**.
     Rien d'autre.
   - Expiration : mets le maximum (1 an) et **note la date dans ton agenda**
     — il faudra en régénérer un et le recoller dans index.html.
3. Copie le token (commence par `github_pat_...`).

## 3. Coller le token EN DEUX MOITIÉS (obligatoire)

⚠️ GitHub scanne les repos publics et **révoque automatiquement** tout token
collé en clair. C'est pour ça que le CONFIG a deux champs : coupé en deux,
le token n'est pas détecté.

1. Coupe ton token grosso modo au milieu. Exemple :
   - Token : `github_pat_11ABCDEF0123456789XYZxyzXYZxyz`
   - Partie 1 : `github_pat_11ABCDEF01234`
   - Partie 2 : `56789XYZxyzXYZxyz`
2. Ouvre `index.html` (éditeur web GitHub, comme d'habitude) et remplace dans
   le bloc `CONFIG` en haut du `<script>` :
   ```js
   TOKEN_PART_1: "github_pat_11ABCDEF01234",
   TOKEN_PART_2: "56789XYZxyzXYZxyz",
   ```
3. Personne n'aura jamais à taper le token : il est embarqué dans l'app,
   tous les invités l'utilisent sans le voir.

Sécurité : le token ne donne accès QU'À ce repo (écriture de fichiers JSON de
chronos). Risque acceptable pour un cercle de 6–10 potes. Ne réutilise jamais
ce token ailleurs.

## 4. Le PIN du race director

Dans le CONFIG, change `DIRECTOR_PIN: "4646"` par le PIN que tu donneras à
ton papa. C'est ce qui protège la création de GP, l'ajout de sessions et le
« Fin du GP ».

## 5. Cloudinary (photos) — optionnel mais sympa

Le CONFIG pointe déjà sur ton cloud Aurora (`ddn6im7rn`). Il manque le **nom
du preset unsigned** (celui qu'on devait confirmer) :

1. Cloudinary Console → Settings → Upload → Upload presets.
2. Repère (ou crée) un preset en mode **Unsigned**, copie son nom.
3. Colle-le dans `CLOUDINARY_PRESET: "ton_preset"`.

Sans preset configuré, l'upload de fichier affichera un message et les pilotes
pourront toujours **coller une URL d'image** (champ prévu sous chaque photo).
Jamais d'API secret dans le code — le preset unsigned suffit.

## 6. Check-list de test avant d'offrir

1. Ouvre l'app → l'accueil doit afficher « Aucun record pour l'instant »
   (si erreur GitHub : token/owner/repo à revérifier).
2. Race director → PIN → Nouveau GP (circuit test, dates) → un code s'affiche.
3. Crée ta carte pilote → tu arrives sur le live timing.
4. Entre un chrono `1:43.256` → il apparaît dans le classement.
5. Depuis un AUTRE téléphone (ou navigation privée) : Rejoindre un GP avec le
   code → deuxième carte pilote → deuxième chrono → les deux téléphones voient
   les deux temps (bouton Actualiser).
6. Fin du GP → podium + le record apparaît sur l'accueil au palmarès.
7. Nettoyage : remets `data/gp/index.json` à `{ "active": null }` si besoin,
   et supprime le dossier `data/gp/GP-...` de test + l'archive de test.
   (records.json : supprime la ligne du circuit test.)

## 7. Le jour J

Envoie-lui le lien `https://loulisse.github.io/paddock-gp/` + le PIN + le
MODE_EMPLOI.txt. Dis-lui d'ajouter l'app à son écran d'accueil (Partager →
Sur l'écran d'accueil) : icône plein écran, effet vraie app.

## Notes techniques (pour toi)

- **Anti-conflit** : chaque pilote n'écrit que dans son fichier
  `data/gp/<code>/pilots/<id>.json`. Papa (director) est le seul à écrire
  `config.json`, `index.json` et `records.json`. Zéro collision possible.
- **Polling** : 5 min (réglable via `POLL_MINUTES`), + bouton Actualiser.
- **Identité pilote** : stockée en localStorage par GP (`paddock_pid_<code>`).
  Si un invité change de téléphone en plein week-end, il recrée simplement sa
  carte (l'ancienne reste mais sans nouveaux temps — pas grave, ou tu la
  supprimes du repo).
- **Profil en cache** : le formulaire se pré-remplit d'un week-end à l'autre
  sur le même téléphone (localStorage), mais seules les données de papa
  persistent côté serveur, comme convenu.
- **Quota API** : ~12 lectures par refresh pour 10 pilotes, token = 5000
  requêtes/h. Très large.
- **Fichiers d'un GP fini** : ils restent dans le repo (inactifs). Tu peux
  les supprimer de temps en temps si tu veux faire le ménage ; les résumés
  sont dans `data/archives/`.
