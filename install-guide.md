# Phase 1 — Installation technique

**Règle absolue :** faire le maximum automatiquement. L'utilisateur ne doit jamais ouvrir un fichier de code.

**Rappel au début :** "On attaque l'installation technique. C'est moi qui fais tout — tu n'as qu'à répondre à quelques questions et vérifier que ça marche. Si à un moment tu ne comprends pas, demande-moi."

---

## Étape 1.1 — Cloner le repo

```bash
git clone https://github.com/notespourplustard/lapepite.git
cd lapepite
```

**Vérification :** `ls package.json` réussit.

### Si erreur :

| Erreur | Diagnostic | Correction |
|---|---|---|
| `Repository not found` | Repo privé ou URL incorrecte | Guider Personal Access Token GitHub |
| `git: command not found` | Git pas installé | Mac: `xcode-select --install`. Windows: https://git-scm.com |
| `Permission denied` | Droits dossier | `cd ~ && git clone ...` |

---

## Étape 1.2 — Vérifier la structure du repo

**CRITIQUE — ne pas sauter.** Vérifier que les fichiers attendus existent :

```bash
echo "=== Vérification structure ===" && \
ls supabase/schema.sql 2>/dev/null && echo "✅ schema.sql" || echo "❌ schema.sql MANQUANT" && \
ls supabase/seed.sql 2>/dev/null && echo "✅ seed.sql" || echo "❌ seed.sql MANQUANT" && \
ls supabase/rls-policies.sql 2>/dev/null && echo "✅ rls-policies.sql" || echo "⚠️ rls-policies.sql absent (peut être inclus dans schema.sql)" && \
ls -d supabase/functions/ 2>/dev/null && echo "✅ functions/" || echo "❌ functions/ MANQUANT" && \
ls .env.example 2>/dev/null && echo "✅ .env.example" || echo "❌ .env.example MANQUANT"
```

**Si un fichier porte un nom différent** (ex: `migrations/` au lieu de `supabase/`, ou `init.sql` au lieu de `schema.sql`) : adapter TOUTES les commandes qui suivent aux vrais noms. Ne pas continuer avec des chemins fictifs.

**Si un fichier critique manque** (schema.sql ou functions/) : le repo est probablement incomplet. Signaler à l'utilisateur et arrêter.

---

## Étape 1.3 — Lire le schéma SQL

```bash
cat supabase/schema.sql
```

Et si présents : `supabase/seed.sql`, `supabase/rls-policies.sql`.

**Stocker les noms exacts de colonnes** de `settings`, `agents_config`, `filters`, `filter_values`, `legal_pages`, `testimonials`. On en aura besoin dans TOUTES les interviews.

---

## Étape 1.4 — Installer les dépendances

```bash
npm install
```

**Vérification :** `ls node_modules/.package-lock.json` réussit.

### Si erreur :

| Erreur | Diagnostic | Correction |
|---|---|---|
| `npm: command not found` | Node.js manquant | Retour prérequis |
| `ERESOLVE` | Conflit versions | `npm install --legacy-peer-deps` |
| `EACCES` | Droits npm | `sudo chown -R $(whoami) ~/.npm && npm install` |

---

## Étape 1.5 — Créer le projet Supabase

**D'abord**, vérifier les projets existants via `Supabase:list_projects`.

Si **2+ projets actifs** et plan Free : avertir l'utilisateur (voir SKILL.md, section prérequis).

### Si MCP disponible :

1. Demander le nom du projet
2. Créer via `Supabase:create_project` (région `eu-central-1`)
3. Attendre `ACTIVE_HEALTHY`
4. Noter le `project_id`

### Si MCP non disponible :

Guider via Dashboard (https://supabase.com/dashboard → New Project → Settings → API).

---

## Étape 1.6 — Configurer les variables d'environnement

```bash
cp .env.example .env
sed -i "s|VITE_SUPABASE_URL=.*|VITE_SUPABASE_URL=https://[PROJECT_ID].supabase.co|" .env
sed -i "s|VITE_SUPABASE_ANON_KEY=.*|VITE_SUPABASE_ANON_KEY=[ANON_KEY]|" .env
```

**Vérification :** `cat .env` montre les variables remplies.

---

## Étape 1.7 — Déployer le schéma de base de données

### Via MCP Supabase :

1. Lire `supabase/schema.sql` → `Supabase:apply_migration` (name: `initial_schema`)
2. Si `supabase/rls-policies.sql` existe → `Supabase:apply_migration` (name: `rls_policies`)
3. Si `supabase/seed.sql` existe → `Supabase:execute_sql`

**Lire le contenu RÉEL des fichiers, pas copier depuis ce guide.**

### Si MCP non disponible :

Découper en blocs logiques, guider un bloc à la fois via le SQL Editor du Dashboard.

### Vérification :

```sql
SELECT table_name FROM information_schema.tables WHERE table_schema = 'public' ORDER BY table_name;
```

Comparer avec les tables du schéma.

### Si erreur :

| Erreur | Diagnostic | Correction |
|---|---|---|
| `relation already exists` | Schéma partiellement appliqué | Vérifier quelles tables existent |
| `permission denied` | Projet pas prêt | Attendre 2 minutes |
| `syntax error` | Erreur SQL | Relire le fichier source |

---

## Étape 1.8 — Créer les buckets Storage

"Je vais créer les espaces de stockage pour les photos, logos, et CV."

### Via MCP ou SQL :

```sql
INSERT INTO storage.buckets (id, name, public) VALUES
  ('avatars', 'avatars', true),
  ('branding', 'branding', true),
  ('cv', 'cv', false);
```

Et les policies d'accès :

```sql
-- Avatars et branding : lecture publique, écriture auth
CREATE POLICY "Public read avatars" ON storage.objects FOR SELECT USING (bucket_id = 'avatars');
CREATE POLICY "Auth upload avatars" ON storage.objects FOR INSERT WITH CHECK (bucket_id = 'avatars' AND auth.role() = 'authenticated');
CREATE POLICY "Public read branding" ON storage.objects FOR SELECT USING (bucket_id = 'branding');
CREATE POLICY "Auth upload branding" ON storage.objects FOR INSERT WITH CHECK (bucket_id = 'branding' AND auth.role() = 'authenticated');

-- CV : lecture et écriture auth uniquement (données sensibles)
CREATE POLICY "Auth read cv" ON storage.objects FOR SELECT USING (bucket_id = 'cv' AND auth.role() = 'authenticated');
CREATE POLICY "Auth upload cv" ON storage.objects FOR INSERT WITH CHECK (bucket_id = 'cv' AND auth.role() = 'authenticated');
CREATE POLICY "Auth delete cv" ON storage.objects FOR DELETE USING (bucket_id = 'cv' AND auth.role() = 'authenticated');
```

**Note :** vérifier d'abord si ces buckets/policies ne sont pas déjà créés dans le `schema.sql`. Si c'est le cas, ne pas les recréer.

**Vérification :** Dashboard Supabase → Storage → vérifier que les 3 buckets existent.

---

## Étape 1.9 — Stocker la clé API Anthropic

Demander la clé : "Colle ta clé API Anthropic ici (elle commence par 'sk-ant-...'). Elle sera stockée de manière sécurisée."

Via Dashboard : Settings → Edge Functions → Manage Secrets → `ANTHROPIC_API_KEY`.

---

## Étape 1.10 — Déployer les Edge Functions

### Option A — Via MCP Supabase (prioritaire)

**Toujours tenter le MCP d'abord.** Le CLI Supabase n'est qu'un fallback.

1. Lister les dossiers dans `supabase/functions/` pour identifier toutes les Edge Functions à déployer
2. Pour chaque function, lire le fichier `index.ts` (et les fichiers partagés s'il y en a dans `supabase/functions/shared/` ou `supabase/functions/_shared/`)
3. Déployer chaque function via `Supabase:deploy_edge_function` avec le `project_id` et le code lu

```
Pour chaque dossier dans supabase/functions/ (sauf shared/_shared) :
  → Lire supabase/functions/[nom]/index.ts
  → Supabase:deploy_edge_function(project_id, name=[nom], code=[contenu du fichier])
```

**Si une function importe du code partagé** (ex : `import { aiCaller } from "../shared/ai-caller.ts"`), inclure le code partagé dans le déploiement ou l'inliner dans la function.

### Option B — Via Supabase CLI (fallback si le MCP ne peut pas déployer les functions)

```bash
npm install -g supabase  # si pas installé
supabase login           # ouvre le navigateur pour se connecter
supabase link --project-ref [PROJECT_ID]
supabase functions deploy --no-verify-jwt
```

### Si erreur :

| Erreur | Diagnostic | Correction |
|---|---|---|
| MCP `deploy_edge_function` échoue | Peut être un problème de format | Basculer sur le CLI (option B) |
| CLI `Cannot find project` | Project-ref incorrect | Dashboard → Settings → General |
| CLI `Access token not provided` | Login manquant | `supabase login` |
| CLI `Failed to deploy` | Erreur dans le code | Identifier quelle function, lire l'erreur |
| `functions/ not found` | Dossier absent ou mal nommé | Vérifier structure repo (étape 1.2) |

**Vérification :** `Supabase:list_edge_functions` → vérifier que toutes les functions sont listées.

---

## Étape 1.11 — Configurer Supabase Auth

### Créer le compte admin :

1. Dashboard → Authentication → Users → Add User → Create New User
2. Email + mot de passe + Auto Confirm User

### Configurer les URLs d'authentification :

**IMPORTANT :** ajouter DEUX URLs pour que la connexion fonctionne en local ET en production.

Dashboard → Authentication → URL Configuration :
- Site URL : `http://localhost:5173` (pour le développement local)
- Redirect URLs : ajouter `http://localhost:5173/**`

"On mettra à jour ces URLs quand ton site sera en ligne (Phase 3). Pour l'instant, ça permet de travailler en local."

---

## Étape 1.12 — Créer le repo GitHub de l'utilisateur

"On va sauvegarder ton code sur GitHub. C'est important :"
- "**Sécurité** : si ton ordi plante, ton code est en sécurité"
- "**Versioning** : chaque modification est enregistrée, on peut revenir en arrière"
- "**Déploiement** : Vercel se connectera à ton GitHub pour mettre ton site en ligne"

### Si pas de compte GitHub :

Guider : https://github.com → Sign up.

### Créer le repo :

1. "Va sur https://github.com/new"
2. "Nom : [choix utilisateur]"
3. "**Private**"
4. "Ne coche rien d'autre"
5. "Clique 'Create repository'"
6. "Copie-colle l'URL ici"

```bash
git remote remove origin
git remote add origin [URL_REPO]
git add .
git commit -m "Installation La Pépite - setup initial"
git branch -M main
git push -u origin main
```

### Si erreur de push :

Guider la config d'un Personal Access Token : https://github.com/settings/tokens/new → scope `repo`.

---

## Étape 1.13 — Test de l'app en local

```bash
npm run dev
```

### Vérifications :

1. "Tu vois une page d'accueil ?" → Si non : vérifier `.env`
2. "Va sur [URL]/espace-recruteur. Écran de connexion ?" → Si non : vérifier routing
3. "Connecte-toi. Tu vois le back-office ?" → Si non : vérifier Auth
4. "Clique sur 'Paramètres'. La page s'affiche ?" → Si oui : tout est bon

### Si erreur :

| Symptôme | Diagnostic | Correction |
|---|---|---|
| Page blanche | `.env` mal configuré | `cat .env`, corriger |
| "Failed to fetch" | URL Supabase incorrecte | Vérifier `.env` |
| Connexion échoue | Auth URLs pas configurées | Vérifier redirect URLs (étape 1.11) |
| Back-office vide | Seed non exécuté | Relancer `seed.sql` |

"🎉 **L'installation technique est terminée !** Maintenant on va la personnaliser."

Passer à la Phase 2.
