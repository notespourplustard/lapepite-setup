# Phase 3 — Déploiement

Ce guide met l'app en ligne : Vercel, Resend, domaine custom.

**Prérequis :** Phases 1 et 2 terminées, l'app fonctionne en local, le code est sur GitHub.

**Rappel :** "On attaque la dernière ligne droite — la mise en ligne. Comme d'habitude, tu peux me poser n'importe quelle question en cours de route."

---

## Étape 3.1 — Déployer sur Vercel

"Vercel, c'est le service qui va héberger ton site — c'est lui qui le rend accessible à tout le monde sur internet. C'est gratuit pour un site comme le tien."

### Créer un compte Vercel

1. "Va sur https://vercel.com"
2. "Clique 'Sign Up' et choisis **'Continue with GitHub'** — comme ça Vercel aura accès à ton code automatiquement"
3. "Autorise Vercel à accéder à ton GitHub quand il te le demande"

### Connecter le projet

4. "Une fois connecté, clique 'Add New...' → 'Project'"
5. "Tu devrais voir la liste de tes repos GitHub. Trouve celui de ton app ([NOM_REPO]) et clique 'Import'"

### Configurer le build

6. "Vercel va te montrer un écran de configuration. Vérifie :"
   - "Framework Preset : **Vite** (normalement détecté automatiquement)"
   - "Build Command : `npm run build` (c'est le défaut, ne change rien)"
   - "Output Directory : `dist` (c'est le défaut, ne change rien)"

### Ajouter les variables d'environnement

7. "**AVANT de cliquer Deploy**, déroule la section 'Environment Variables'. C'est important — sans ça, ton site sera une page blanche."

8. "Ajoute ces deux variables :"

   "Première variable :"
   - "Name : `VITE_SUPABASE_URL`"
   - "Value : colle l'URL de ton projet Supabase (elle ressemble à `https://xxxxx.supabase.co`)"
   - "Clique 'Add'"

   "Deuxième variable :"
   - "Name : `VITE_SUPABASE_ANON_KEY`"
   - "Value : colle ta clé anon Supabase (la longue chaîne de caractères)"
   - "Clique 'Add'"

   "Tu peux retrouver ces infos dans ton fichier `.env` local — c'est exactement les mêmes valeurs."

9. "Maintenant clique 'Deploy' et attends ~1-2 minutes"

### Vérification

10. "Vercel va te donner une URL (quelque chose comme `ton-projet.vercel.app`). Ouvre-la dans ton navigateur."
    - "Tu vois ta landing avec ton profil ? → Super !"
    - "Page blanche ? → Les variables d'environnement sont probablement manquantes. Va dans le projet Vercel → Settings → Environment Variables et vérifie."
    - "Erreur quand tu navigues ? → Il faut peut-être ajouter un fichier de configuration."

### Configurer le routing SPA

Si les pages (route admin, pages légales) donnent une erreur 404 :

"C'est un problème classique avec les apps React. Je vais le corriger :"

Créer le fichier `vercel.json` à la racine du projet :
```json
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

Puis :
```bash
git add vercel.json
git commit -m "Fix: routing SPA Vercel"
git push
```

"Vercel va automatiquement redéployer. Attends ~1 minute et reteste."

### Mettre à jour Supabase Auth

"Maintenant que ton site est en ligne, je dois dire à Supabase quelle est l'adresse de ton site."

1. "Va dans ton Dashboard Supabase → Authentication → URL Configuration"
2. "Site URL : remplace par `https://[ton-projet].vercel.app`"
3. "Redirect URLs : clique 'Add URL' et ajoute `https://[ton-projet].vercel.app/**`"
4. "Clique 'Save'"

---

## Étape 3.2 — Configurer Resend (emails)

"Resend c'est le service qui enverra tes emails : les mails de refus aux candidats, les propositions d'embauche, etc. C'est gratuit jusqu'à 100 emails par jour."

### Créer le compte et la clé API

1. "Va sur https://resend.com et crée un compte gratuit"
2. "Une fois connecté, va dans le menu à gauche → 'API Keys'"
3. "Clique 'Create API Key'"
4. "Donne un nom (ex : 'La Pépite') et clique 'Add'"
5. "Copie la clé — **attention, elle ne s'affiche qu'une seule fois**"

Stocker le secret dans Supabase :
1. "Va dans ton Dashboard Supabase → Settings → Edge Functions"
2. "Clique 'Manage Secrets'"
3. "Ajoute : nom = `RESEND_API_KEY`, valeur = la clé que tu viens de copier"
4. "Clique 'Save'"

### Configurer le domaine d'envoi (recommandé)

"Par défaut, tes emails partiront d'une adresse Resend générique (`onboarding@resend.dev`). Le problème, c'est que ces mails finiront souvent en spam. Pour que tes mails arrivent bien, il faut configurer ton propre domaine."

"Tu as un nom de domaine ? (ex : recrutement-marie.fr)"

**Si oui :**

1. "Dans Resend, va dans 'Domains' → 'Add Domain'"
2. "Entre ton domaine (ex : `recrutement-marie.fr`)"
3. "Resend va te donner **des enregistrements DNS** à ajouter chez ton fournisseur de domaine. Il y en a 3 types :"
   - "**SPF** — dit aux boîtes mail que Resend a le droit d'envoyer des mails depuis ton domaine"
   - "**DKIM** — signe tes mails pour prouver qu'ils viennent bien de toi"
   - "**DMARC** — dit aux boîtes mail quoi faire si un mail échoue les vérifications"

4. "Ton domaine est chez quel fournisseur ? (OVH, Gandi, Namecheap, Google Domains, Cloudflare, autre ?)"

   Guider l'ajout des enregistrements DNS spécifiquement pour le fournisseur de l'utilisateur :

   **OVH :**
   - "Connecte-toi sur https://www.ovh.com/manager"
   - "Va dans 'Web Cloud' → 'Noms de domaine' → ton domaine → 'Zone DNS'"
   - "Pour chaque enregistrement que Resend te demande : clique 'Ajouter une entrée', choisis le type (TXT, CNAME, ou MX selon ce que Resend indique), remplis les champs, et valide"

   **Gandi :**
   - "Connecte-toi sur https://admin.gandi.net"
   - "Va dans 'Nom de domaine' → ton domaine → 'Enregistrements DNS'"
   - "Ajoute chaque enregistrement avec le type et la valeur indiqués par Resend"

   **Autres fournisseurs :** adapter ou demander à l'utilisateur de chercher "ajouter enregistrement DNS" dans l'aide de son fournisseur.

5. "Une fois ajoutés, retourne sur Resend → Domains. Clique 'Verify'. Ça peut prendre quelques minutes à quelques heures."

6. **DMARC — aller au bout :** "Le dernier enregistrement (DMARC) est crucial. Vérifie qu'il est bien en place :"
   ```
   Type : TXT
   Nom : _dmarc
   Valeur : v=DMARC1; p=none; rua=mailto:[ton-email]
   ```
   "Sans DMARC, Gmail et Outlook risquent de mettre tes mails en spam."

7. Une fois le domaine vérifié, ajouter le secret dans Supabase :
   - `RESEND_FROM_EMAIL` = `contact@recrutement-marie.fr` (ou l'adresse choisie)

**Si pas de domaine :**

"Pas de problème, les emails partiront quand même. Mais ils risquent d'arriver en spam chez certains candidats. Je te recommande de configurer un domaine quand tu en auras un."

### Test anti-spam complet

"On va vérifier que tes mails arrivent bien et ne finissent pas en spam."

1. **Test d'envoi :** "Va dans ton back-office. Crée une offre test et une candidature test (ou utilise celles de la pause). Clique 'Générer un mail de refus', puis 'Envoyer'."

2. **Vérifier l'arrivée :** "Le mail est arrivé ? Regarde dans ta boîte principale ET dans tes spams."

3. **Si le mail est en spam :**
   - "Vérifie que les 3 enregistrements DNS sont bien en place (SPF, DKIM, DMARC)"
   - "Vérifie dans Resend → Domains que le statut est bien 'Verified' pour les 3"
   - "Attends quelques heures et reteste — la propagation DNS peut prendre du temps"
   - "Si toujours en spam après 24h avec tout vérifié : essaie d'envoyer à une autre boîte mail (Gmail, Outlook, Yahoo) pour isoler le problème"

4. **Si le mail n'arrive pas du tout :**
   - Vérifier le secret `RESEND_API_KEY` dans Supabase
   - Vérifier les logs dans Resend → Emails → chercher le mail
   - Si erreur "not verified" : le domaine n'est pas encore vérifié dans Resend

5. **Test croisé :** "Envoie un test à au moins 2 adresses différentes (ex : une Gmail et une Outlook/Hotmail). Si ça arrive bien dans les deux, c'est bon."

"✅ Tes emails sont configurés et arrivent bien !"

---

## Étape 3.3 — Domaine custom (optionnel)

"Si tu as un nom de domaine, on peut le brancher sur ton site. Sinon, ton app fonctionne parfaitement sur l'URL Vercel."

**Si l'utilisateur veut configurer :**

1. "Va dans Vercel → ton projet → Settings → Domains"
2. "Clique 'Add' et entre ton domaine (ex : `www.recrutement-marie.fr`)"
3. "Vercel va te dire quel enregistrement DNS ajouter"
4. "Chez ton fournisseur de domaine (le même qu'on a utilisé pour Resend) :"
   - "Ajoute un enregistrement CNAME : nom = `www`, valeur = `cname.vercel-dns.com`"
   - "Si tu veux le domaine nu (sans www) : ajoute un enregistrement A vers `76.76.21.21`"
5. "Attends la propagation DNS (quelques minutes à 24h)"

### Mettre à jour les URLs partout :

Une fois le domaine actif :

1. Supabase Auth → URL Configuration :
   - Site URL : `https://www.recrutement-marie.fr`
   - Redirect URLs : `https://www.recrutement-marie.fr/**`

2. Mettre à jour les pages légales si l'URL était en placeholder

3. Resend : si pas encore fait, configurer le domaine d'envoi email sur le même domaine

---

## Étape 3.4 — Vérifications finales

"Dernière étape ! On vérifie que tout fonctionne en production."

1. "Ouvre ta landing depuis ton téléphone. Ça s'affiche bien ?"
2. "Ouvre ton back-office depuis ton téléphone. Tu peux naviguer ?"
3. "Demande à quelqu'un (un·e ami·e, un·e collègue) d'ouvrir ta landing. Il/elle voit ton profil ?"
4. "Teste le parcours complet : créer une offre → la publier → postuler depuis un autre appareil → voir la candidature dans le back-office"
5. "Teste l'envoi d'un email"
6. "Vérifie les pages légales depuis un navigateur mobile"

---

## Étape 3.5 — Mettre à jour le CLAUDE.md

Maintenant que le déploiement est terminé, mettre à jour le `CLAUDE.md` avec les infos de production :

- URL de production (Vercel ou domaine custom)
- Statut Resend (configuré ou non, domaine vérifié ou non)
- Domaine d'envoi email

Ajouter une ligne dans l'historique des modifications :
```
- [Date] : Déploiement en production — [URL]
```

Committer :
```bash
git add CLAUDE.md
git commit -m "Déploiement : mise à jour CLAUDE.md avec URL de production"
git push
```

---

## Message de fin

"🎉 **Félicitations, ton app La Pépite est en production !**

Récap :
🌐 **Ton site :** [URL]
🔐 **Ton back-office :** [URL]/[route-admin]
📧 **Emails :** [configuré / à configurer plus tard]

**Pour démarrer :**
1. Crée ta première vraie offre avec un brief client
2. Partage le lien de ton board sur LinkedIn
3. Ajoute des témoignages clients quand tu en auras (dans Paramètres)

**N'oublie pas :** tu peux me recontacter à tout moment si tu as une question, si tu veux modifier quelque chose sur ton site, ou si tu rencontres un problème. Il te suffit de lancer Claude Code et de me parler. 😊

Bonne route !"
