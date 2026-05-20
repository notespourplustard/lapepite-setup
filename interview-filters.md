# Interview — Filtres du board d'offres

Cette interview configure les filtres du board d'offres — ceux que les candidats utilisent pour chercher, et que le recruteur utilise pour taguer ses offres.

## Consignes pour Claude

- Proposer des filtres pertinents basés sur les spécialisations déclarées à l'interview profil
- 3-5 filtres suffisent. Plus c'est confus pour les candidats.
- Le seed contient déjà 2 filtres par défaut ("Type de contrat" et "Secteur") — on peut les garder, les modifier, ou les remplacer

## Déroulement

1. **Filtres existants** — "Par défaut, ton board a deux filtres : 'Type de contrat' (CDI, CDD, Freelance) et 'Secteur' (Tech, Finance, Santé). On les garde comme ça, on les modifie, ou on repart de zéro ?"

2. **Filtres supplémentaires** — "Tu veux ajouter d'autres filtres ? Voici ce que je te suggère vu tes spécialisations :"

   Adapter les suggestions au profil :

   **Recruteur Tech :**
   - Niveau d'expérience (Junior, Confirmé, Senior, Lead)
   - Télétravail (Full remote, Hybride, Présentiel)
   - Localisation (Paris, Lyon, Remote France, International)

   **Recruteur Finance :**
   - Type de poste (Front office, Middle office, Back office)
   - Niveau (Analyst, Associate, VP, Director)

   **Recruteur généraliste :**
   - Fonction (Tech, Marketing, Sales, RH, Finance, Ops)
   - Niveau (Junior, Confirmé, Senior, Direction)
   - Localisation

3. **Pour chaque filtre ajouté** — "Quelles valeurs dans '[NOM]' ?"

## Récapitulatif

```
📋 Tes filtres :

1. Type de contrat : CDI, CDD, Freelance, Alternance
2. Secteur : Tech, SaaS, E-commerce, Fintech
3. Niveau : Junior, Confirmé, Senior, Lead/Manager
4. Télétravail : Full remote, Hybride, Présentiel
```

"Ça te va ?"

## Écriture en base

**AVANT d'écrire**, lire `supabase/schema.sql` pour connaître les noms exacts des tables `filters` et `filter_values` et leurs colonnes.

1. Si remplacement des filtres par défaut : supprimer les valeurs puis les filtres existants
2. Insérer les nouveaux filtres
3. Pour chaque filtre, récupérer son ID et insérer ses valeurs

**Ne PAS coder les IDs en dur.** Utiliser des sous-requêtes pour récupérer les IDs dynamiquement.

```sql
-- Exemple indicatif — adapter aux vrais noms de colonnes
INSERT INTO filters (user_id, [col_name]) VALUES
  ((SELECT user_id FROM settings LIMIT 1), 'Type de contrat');

INSERT INTO filter_values ([col_filter_id], [col_value]) VALUES
  ((SELECT id FROM filters WHERE [col_name] = 'Type de contrat' LIMIT 1), 'CDI'),
  ((SELECT id FROM filters WHERE [col_name] = 'Type de contrat' LIMIT 1), 'CDD');
-- etc.
```

**Vérification :** dans le back-office, aller sur la création d'une offre et vérifier que les filtres apparaissent.

"✅ Tes filtres sont en place ! On passe à la configuration des agents IA ?"
