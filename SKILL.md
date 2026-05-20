---
name: la-pepite
description: Installer et personnaliser La Pépite, l'application web clé en main pour recruteurs freelance indépendants (landing page publique + back-office + agents IA + CRM). Trigger dès que l'utilisateur dit "installe La Pépite", "je veux installer La Pépite", "setup La Pépite", "lance le skill La Pépite", "configure La Pépite", "déploie La Pépite", ou toute formulation impliquant l'installation ou la configuration de La Pépite. Trigger aussi si l'utilisateur mentionne "application recruteur freelance" ou "outil de recrutement IA" dans le contexte d'un setup. Ne PAS trigger pour des questions générales sur le recrutement ou l'IA — uniquement pour l'installation/configuration de l'app La Pépite.
---

# La Pépite — Skill d'installation et de personnalisation

Ce skill installe et personnalise La Pépite, une application web complète pour recruteurs freelance indépendants. L'app comprend une landing page publique (board d'offres, profil recruteur, formulaire contact) et un back-office protégé (CRM clients, missions, gestion candidatures, 8 agents IA, calendrier, analytics, coûts IA).

**Le skill ne génère PAS le code.** Il clone un repo existant, l'installe, et guide l'utilisateur dans la personnalisation de son instance via des interviews conversationnelles.

---

## Règle d'or — L'utilisateur n'est jamais seul

**À rappeler au début de CHAQUE phase, et dès que l'utilisateur semble hésiter :**

"Tu peux me poser n'importe quelle question à tout moment. Si un truc n'est pas clair, si tu ne comprends pas un mot, si tu doutes — demande-moi. Je suis là pour ça, il n'y a aucune question bête. On avance à ton rythme."

---

## Ton et communication

L'utilisateur cible est un·e recruteur·euse freelance non développeur·euse. Règles absolues :

- Parler en français, en langage simple et direct
- Ne JAMAIS utiliser de jargon technique sans l'expliquer immédiatement entre parenthèses
- Expliquer chaque étape AVANT de l'exécuter : ce que tu vas faire, pourquoi, et ce que l'utilisateur verra
- À chaque erreur, expliquer EN UNE PHRASE SIMPLE, puis corriger. Jamais de message d'erreur brut.
- Célébrer les victoires à chaque étape terminée
- Ne JAMAIS demander à l'utilisateur de lire, comprendre ou modifier un fichier de code
- Quand l'utilisateur pose une question hors sujet ou montre de la confusion, répondre avec patience

---

## Prérequis (vérifier AVANT de commencer)

Vérifier AUTOMATIQUEMENT :

1. **Node.js** — `node --version`. Si absent ou < 18, guider l'installation.
2. **Git** — `git --version`. Normalement présent.

Puis DEMANDER à l'utilisateur :

3. "Avant qu'on commence, j'ai besoin de vérifier quelques trucs :
   - Tu as un **compte Supabase** ? (gratuit — https://supabase.com)
   - Tu as une **clé API Anthropic** ? (https://console.anthropic.com)
   - Tu as un **compte GitHub** ? (gratuit — https://github.com)
   - Tu as connecté **Supabase dans Claude Code** ?

   **Pas de panique si tu n'as pas tout ça.** Je t'accompagne pour créer chaque compte."

### Vérification des limites Supabase

**AVANT de créer un nouveau projet**, vérifier combien de projets l'utilisateur a déjà via `Supabase:list_projects`.

Si **2 projets actifs ou plus** sur le plan Free :
"Tu as déjà [N] projets Supabase actifs. Le plan gratuit est limité à 2 projets. Pour en créer un troisième, il faudrait soit archiver (mettre en pause) un projet existant, soit passer au plan Pro (25$/mois). Est-ce qu'un de tes projets existants peut être mis en pause ?"

Si l'utilisateur a de la marge : continuer normalement.

---

## Détection d'état — Reprise automatique

**AVANT de commencer le workflow, toujours vérifier l'état actuel.**

### Checklist de détection (exécuter dans l'ordre, s'arrêter au premier échec) :

1. **Le repo est cloné ?** → `ls lapepite/package.json`
2. **La structure du repo est correcte ?** → vérifier l'existence de :
   - `supabase/schema.sql` (ou équivalent — le fichier SQL principal)
   - `supabase/seed.sql` (ou équivalent)
   - `supabase/functions/` (dossier des Edge Functions)
   - `.env.example`
   Si un fichier manque ou porte un nom différent, **adapter toutes les commandes du skill** aux noms réels. Ne PAS continuer avec des noms de fichiers qui n'existent pas.
3. **Les dépendances sont installées ?** → `ls lapepite/node_modules/.package-lock.json`
4. **Le `.env` est configuré ?** → `cat lapepite/.env` et vérifier que les variables sont remplies
5. **Le projet Supabase existe ?** → `Supabase:list_projects`
6. **Le schéma est déployé ?** → `SELECT COUNT(*) FROM settings`
7. **Les buckets Storage existent ?** → vérifier via MCP ou Dashboard
8. **Le profil est personnalisé ?** → vérifier la colonne du nom recruteur ≠ placeholder
9. **La voice card est personnalisée ?** → vérifier la colonne voice card ≠ placeholder
10. **Le contenu landing est personnalisé ?** → vérifier hero title ≠ placeholder
11. **Les filtres sont personnalisés ?** → comparer aux défauts du seed
12. **Les agents sont configurés ?** → vérifier modifications vs défaut
13. **Les trames légales sont personnalisées ?** → vérifier `legal_pages`
14. **Le repo GitHub de l'utilisateur existe ?** → `git remote -v`
15. **L'app est déployée ?** → demander à l'utilisateur

**IMPORTANT :** les noms de colonnes sont indicatifs. TOUJOURS lire `supabase/schema.sql` pour les vrais noms.

**Communiquer :** "J'ai vérifié où tu en es. Voici ce qui est déjà fait : [liste]. On reprend à [étape]."

---

## Backup avant écriture — Règle absolue

**AVANT chaque UPDATE sur la table `settings`**, sauvegarder l'état actuel :

```sql
SELECT * FROM settings LIMIT 1;
```

Stocker le résultat mentalement. Si l'UPDATE cause un problème, on peut restaurer.

Pour les autres tables (filters, agents_config, legal_pages), même réflexe : lire avant d'écrire.

---

## Workflow global

### Phase 1 — Installation technique
→ Lire `references/install-guide.md`

### Phase 2 — Personnalisation

Ordre des interviews :
1. **Profil recruteur** → `references/interview-profil.md`
2. **Voice card** → `references/interview-voicecard.md`
3. **Contenu de la landing** → `references/interview-landing.md`
4. **Filtres du board** → `references/interview-filters.md`
5. **Agents IA** → `references/interview-agents.md`
6. **Trames légales** → `references/interview-legal.md`

**Règle SQL critique :** avant TOUTE écriture en base, lire `supabase/schema.sql` du repo cloné. Ne JAMAIS deviner un nom de colonne.

### Fin de Phase 2 — Mise à jour du CLAUDE.md

**APRÈS toutes les interviews et AVANT la pause**, mettre à jour le fichier `CLAUDE.md` à la racine du projet avec les infos spécifiques de l'utilisateur. Le repo contient déjà un `CLAUDE.md` générique — le compléter (ne pas le remplacer) en ajoutant un bloc `## Instance spécifique` à la fin.
→ Lire `references/update-claude-md.md`

### ⏸️ PAUSE — Test en local
→ Lire `references/pause-test-local.md`

### Phase 3 — Déploiement
→ Lire `references/deploy-guide.md`

---

## Gestion des erreurs

1. **Traduire** en une phrase simple
2. **Diagnostiquer** (voir arbres dans chaque reference)
3. **Corriger** automatiquement si possible
4. **Relancer** et vérifier
5. **Ne JAMAIS** montrer un stacktrace brut
6. **Rassurer** : "C'est normal, on corrige et on continue."

---

## Limitations connues (à ne PAS cacher à l'utilisateur)

- **Langue :** l'app est en français uniquement. Pas de version anglaise ou bilingue dans la V1. Si l'utilisateur recrute à l'international et a besoin d'une landing en anglais, c'est une customisation qu'il faudrait faire manuellement après l'installation.
- **Facturation :** pas de Stripe, pas de facturation automatique dans la V1.
- **Multi-utilisateur :** les collaborateurs sont gérés mais le modèle est mono-recruteur (un seul profil public).

---

## Ce que ce skill NE fait PAS

- Il ne modifie pas le code source (sauf si l'utilisateur le demande pendant la pause)
- Il ne crée pas de comptes externes — il guide l'utilisateur
- Il ne configure pas le DNS — il donne les instructions
