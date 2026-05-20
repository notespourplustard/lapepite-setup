# Interview — Agents IA

Cette interview configure les 8 agents IA de La Pépite.

## Consignes pour Claude

- L'utilisateur ne connaît pas la différence entre les modèles Claude. Expliquer simplement.
- Tous les agents sont activés par défaut — présenter ce que chaque agent fait et laisser l'utilisateur décider
- Être transparent sur les coûts SANS effrayer : ce sont des centimes par utilisation
- Ne PAS surestimer la précision de l'estimation de coût — les coûts varient selon la taille des CV, des briefs, et les prix Anthropic changent

## Déroulement

### Introduction

"Ton app intègre 8 agents IA. Par défaut, ils sont tous activés. Je vais te les présenter rapidement — tu pourras désactiver ceux qui ne t'intéressent pas."

"Deux choses à savoir :"
- "Chaque utilisation a un petit coût (quelques centimes) qui est facturé sur ta clé API Anthropic"
- "Il y a deux niveaux de modèle : **Sonnet** (plus intelligent, pour l'analyse et la rédaction) et **Haiku** (plus rapide et moins cher, pour les tâches simples). Je te recommande le bon modèle pour chaque agent."

### Présentation rapide

Présenter les 8 agents en mode tableau, pas un par un (pour ne pas allonger) :

```
🤖 Tes agents IA :

1. Matching CV/Offre — Analyse un CV vs une offre, score + recommandation [Sonnet]
2. Mail de refus — Rédige un refus bienveillant et personnalisé [Haiku]
3. Guide d'entretien — Génère une trame d'entretien structurée [Sonnet]
4. Cas pratique — Crée un exercice adapté au poste [Sonnet]
5. Présentation candidat — Dossier structuré pour le client [Haiku]
6. Proposition d'embauche — Brouillon de proposition [Haiku]
7. Structuration contact — Transforme un message entrant en fiche CRM [Haiku]
8. Rédaction d'annonce — Génère une annonce d'emploi depuis le brief [Sonnet]
```

### Questions

1. **Activation** — "Tu veux désactiver certains agents ? (Si tu ne sais pas, garde-les tous — tu pourras toujours les désactiver dans les Paramètres plus tard.)"

2. **Modèles** — "Les agents 'intelligents' (matching, entretien, cas pratique, annonce) sont sur Sonnet, les agents de reformatage (refus, présentation, proposition, structuration) sont sur Haiku. Tu veux tout passer sur Sonnet pour une meilleure qualité partout ? C'est un peu plus cher mais la qualité est nettement meilleure."

3. **Estimation de coût** — "Combien de candidatures tu traites en moyenne par mois ? Et combien d'offres tu publies ?"

   Calculer une estimation :
   - Matching : nb candidatures × ~0.01-0.03€ (varie selon la taille du CV)
   - Refus : ~60% des candidatures × ~0.002€
   - Entretien : ~30% des candidatures × ~0.02€
   - Annonce : nb offres × ~0.02€
   - Autres : usage ponctuel

   **Présenter avec une fourchette, pas un chiffre exact :**
   "Avec [X] candidatures et [Y] offres par mois, tes agents IA te coûteront **entre [min]€ et [max]€ par mois**. C'est une estimation — le coût réel dépend de la taille des CV et des briefs, et les prix Anthropic peuvent évoluer. Tu pourras suivre tes coûts en détail dans l'onglet 'Coûts IA' de ton back-office."

## Écriture en base

**AVANT d'écrire**, lire `supabase/schema.sql` pour connaître les vrais noms de colonnes de la table `agents_config`.

```sql
UPDATE agents_config
SET 
  [col_is_active] = [true/false],
  [col_model] = '[claude-sonnet-4-20250514 ou claude-haiku-4-5-20251001]',
  updated_at = now()
WHERE [col_slug] = '[SLUG_AGENT]';
```

Slugs des agents :
- `agent-matching`
- `agent-refus`
- `agent-entretien`
- `agent-cas-pratique`
- `agent-presentation`
- `agent-offre`
- `agent-structuration`
- `agent-annonce`

**Note :** vérifier les slugs réels dans le seed ou dans `agents_config`.

**Vérification :** dans le back-office, vérifier que seuls les agents activés apparaissent dans l'interface candidature et dans Paramètres → Agents IA.

"✅ Tes agents IA sont configurés ! Plus qu'une étape de personnalisation : les trames légales."
