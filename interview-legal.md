# Interview — Trames légales

Cette interview personnalise les pages légales obligatoires : mentions légales, politique de confidentialité, politique cookies, et transparence IA (IA Act).

## Consignes pour Claude

- C'est CRITIQUE pour la conformité RGPD — un recruteur qui traite des CV sans politique de confidentialité est en infraction
- Les trames par défaut (seed) sont pré-remplies avec des placeholders. L'objectif est de remplacer les placeholders par les vraies infos.
- Ne PAS jouer au juriste. Les trames sont des modèles standards. Recommander de les faire valider par un avocat si l'utilisateur le souhaite.
- Être efficace : collecter les infos une fois, les injecter dans toutes les trames

## Déroulement

### Introduction

"Dernière étape : les pages légales de ton site. C'est important parce que tu traites des CV — des données personnelles sensibles. J'ai des trames pré-remplies, il faut juste les personnaliser avec tes infos."

"Je te préviens : ce n'est pas un avis juridique. Si tu veux une validation juridique formelle, je te recommande de consulter un avocat spécialisé RGPD après l'installation."

### Collecte d'infos (une seule fois, réutilisées dans toutes les trames)

1. **Statut juridique** — "Tu es en micro-entreprise, SASU, EURL, portage salarial, autre ?"

2. **Nom légal** — "Le nom complet de ta structure ? (ex : 'Marie Dupont EI' ou 'Dupont Recrutement SASU')"

3. **SIRET** — "Ton numéro SIRET ?"

4. **Adresse** — "L'adresse de ton siège social ? (C'est obligatoire dans les mentions légales. Si tu travailles de chez toi et que tu ne veux pas afficher ton adresse perso, tu peux utiliser une adresse de domiciliation.)"

5. **Hébergeur** — "Ton site sera hébergé par Vercel. Je mettrai les infos de Vercel automatiquement." (Vercel Inc., 440 N Barranca Avenue, Suite 4133, Covina, CA 91723, USA)

6. **Durée de conservation des CV** — "Combien de temps tu gardes les CV des candidats ? La norme RGPD dans le recrutement c'est **24 mois maximum** après le dernier contact. Tu veux garder ce délai ou le raccourcir ?"

7. **Responsable du traitement** — "C'est toi le/la responsable du traitement des données. Tu veux afficher un email dédié pour les demandes RGPD ? (ex : rgpd@ton-domaine.fr ou simplement ton email de contact)"

### Injection dans les trames

Les trames sont stockées dans la table `legal_pages`. Lire le contenu actuel (placeholders du seed), puis faire un find/replace sur les placeholders :

| Placeholder | Remplacement |
|---|---|
| `[NOM_STRUCTURE]` | Nom légal |
| `[STATUT_JURIDIQUE]` | Micro-entreprise / SASU / etc. |
| `[SIRET]` | Numéro SIRET |
| `[ADRESSE]` | Adresse siège social |
| `[EMAIL_CONTACT]` | Email de contact (de l'interview profil) |
| `[EMAIL_RGPD]` | Email RGPD |
| `[NOM_RECRUTEUR]` | Prénom Nom (de l'interview profil) |
| `[DUREE_CONSERVATION_CV]` | 24 mois / X mois |
| `[NOM_HEBERGEUR]` | Vercel Inc. |
| `[ADRESSE_HEBERGEUR]` | 440 N Barranca Avenue, Suite 4133, Covina, CA 91723, USA |
| `[URL_SITE]` | URL du site (placeholder si pas encore déployé — sera mis à jour en Phase 3) |

### Pages à personnaliser :

1. **Mentions légales** — identité de l'éditeur, hébergeur, propriété intellectuelle
2. **Politique de confidentialité** — types de données collectées (CV, coordonnées, email, cookies), finalités, base légale (consentement pour les candidats, intérêt légitime pour les contacts entreprise), durée de conservation, droits des personnes, transferts hors UE (Supabase + Vercel = serveurs US → mention des clauses contractuelles types)
3. **Politique cookies** — cookies techniques (session), cookies analytics (si activés), pas de cookies publicitaires
4. **Transparence IA (IA Act)** — informer que des systèmes d'IA sont utilisés dans le processus de recrutement, liste des cas d'usage (matching, pré-sélection), consentement explicite recueilli au moment de la candidature

### Récapitulatif

```
📋 Tes pages légales :

Structure : Marie Dupont EI (micro-entreprise)
SIRET : 123 456 789 00012
Adresse : 12 rue des Lilas, 75011 Paris
Email RGPD : contact@recrutement-marie.fr
Conservation CV : 24 mois
Hébergeur : Vercel Inc. (USA)

Pages personnalisées :
✅ Mentions légales
✅ Politique de confidentialité
✅ Politique cookies
✅ Transparence IA
```

"Tout est bon ? Ces trames sont standard et couvrent les obligations légales de base. Si tu veux les faire valider juridiquement, je te recommande un avocat RGPD."

## Écriture en base

**AVANT d'écrire**, lire `supabase/schema.sql` pour connaître la structure de la table `legal_pages`.

Pour chaque page légale, mettre à jour le contenu avec les vrais infos :

```sql
UPDATE legal_pages
SET 
  [col_content] = '[CONTENU_HTML_PERSONNALISÉ]',
  updated_at = now()
WHERE [col_slug] = 'mentions-legales';
```

Répéter pour `confidentialite`, `cookies`, `transparence-ia`.

Aussi mettre à jour la durée de conservation dans `settings` :

```sql
UPDATE settings
SET 
  [col_retention_duration] = [NOMBRE_MOIS],
  updated_at = now()
WHERE id = (SELECT id FROM settings LIMIT 1);
```

**Vérification :** ouvrir les pages légales sur la landing (liens en footer) et vérifier que les infos sont correctes.

"✅ Tes pages légales sont en place ! La personnalisation est terminée. On passe au déploiement ?"
