# Mise à jour du CLAUDE.md

Après toutes les interviews de personnalisation, mettre à jour le `CLAUDE.md` à la racine du projet avec les infos spécifiques de cette instance. Ce fichier est lu automatiquement par Claude Code chaque fois que l'utilisateur ouvre le projet — c'est la mémoire du projet.

## Pourquoi c'est important

Le `CLAUDE.md` du repo cloné est générique. Sans mise à jour, quand l'utilisateur reviendra dans 3 semaines pour dire "change la couleur du bouton", Claude Code ne saura pas :
- Quel est le projet Supabase associé
- Quelle est la route admin
- Quel ton utiliser si on lui demande de rédiger quelque chose
- Quels agents sont actifs
- Où est le repo GitHub

## Ce qu'il faut faire

1. **Lire le `CLAUDE.md` existant** pour comprendre sa structure :

```bash
cat CLAUDE.md
```

2. **Ajouter un bloc à la fin** du fichier (ne PAS supprimer le contenu existant). Le bloc doit contenir toutes les infos collectées pendant les interviews.

3. **Template du bloc à ajouter :**

```markdown

---

## Instance spécifique

Ce projet a été installé et personnalisé via le skill La Pépite. Voici les informations spécifiques de cette instance.

### Recruteur
- **Nom :** [Prénom Nom]
- **Titre :** [Titre/tagline]
- **Spécialisations :** [liste des tags]
- **Email :** [email de contact]

### Configuration technique
- **Projet Supabase :** [project_id]
- **Région Supabase :** [région]
- **Repo GitHub :** [URL du repo]
- **Route admin :** /[route choisie]
- **URL de production :** [URL Vercel ou domaine custom — laisser "non déployé" si Phase 3 pas encore faite]

### Voice card
- **Formalité :** [vouvoiement/tutoiement]
- **Ton :** [description courte du ton]
- **Signature :** [signature de mail]

### Agents IA
- **Matching :** [actif/inactif] — [modèle]
- **Refus :** [actif/inactif] — [modèle]
- **Entretien :** [actif/inactif] — [modèle]
- **Cas pratique :** [actif/inactif] — [modèle]
- **Présentation :** [actif/inactif] — [modèle]
- **Proposition :** [actif/inactif] — [modèle]
- **Structuration :** [actif/inactif] — [modèle]
- **Annonce :** [actif/inactif] — [modèle]

### Filtres du board
[Liste des filtres et leurs valeurs]

### Infos légales
- **Structure :** [statut juridique + nom légal]
- **SIRET :** [numéro]
- **Durée conservation CV :** [X mois]

### Historique des modifications
- [Date] : Installation initiale via skill La Pépite
```

4. **Remplir chaque champ** avec les vraies valeurs collectées pendant les interviews. Ne PAS laisser de placeholders `[xxx]`.

5. **Si une info n'a pas été collectée** (question optionnelle à laquelle l'utilisateur n'a pas répondu), omettre la ligne plutôt que de mettre "non renseigné".

6. **Committer la mise à jour :**

```bash
git add CLAUDE.md
git commit -m "Personnalisation : mise à jour CLAUDE.md avec les infos de l'instance"
git push
```

## Mise à jour en Phase 3

Quand le déploiement sera fait (Phase 3), **revenir mettre à jour** le `CLAUDE.md` avec :
- L'URL de production (Vercel ou domaine custom)
- Le statut Resend (configuré ou non)
- Le domaine d'envoi email

```bash
# Après le déploiement, ajouter/modifier dans le bloc Instance spécifique :
# - URL de production : https://www.recrutement-marie.fr
# - Resend : configuré, domaine vérifié, envoi depuis contact@recrutement-marie.fr
```

## Mise à jour pendant la pause

Si l'utilisateur fait des modifications visuelles pendant la pause (changement de couleurs, réorganisation de sections), ajouter une ligne dans la section "Historique des modifications" :

```markdown
- [Date] : Personnalisation visuelle — [description courte]
```

Ça permet de garder une trace de ce qui a été modifié par rapport au template de base.
