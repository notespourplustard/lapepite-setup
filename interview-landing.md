# Interview — Contenu de la landing

Cette interview personnalise les textes de la landing page publique : hero, CTA, messages de confirmation, témoignages.

## Consignes pour Claude

- Beaucoup de champs ont déjà été remplis lors de l'interview profil (nom, bio, spécialisations). Ne PAS reposer ces questions.
- Proposer des formulations personnalisées (basées sur le profil et la voice card) et laisser choisir
- Le ton de la landing doit être cohérent avec la voice card

## Déroulement

### Bloc 1 — Hero

1. **Titre principal** — "C'est la première chose que les visiteurs voient sur ta page."

   Proposer 3 options personnalisées basées sur le profil déjà collecté. Par exemple, pour une recruteuse tech nommée Marie :
   - "Marie Dupont — Recrutement Tech & Product"
   - "Le recrutement tech, autrement."
   - "Votre prochain talent est déjà dans mon réseau."

   "Choisis celle qui te plaît, ou dis-moi ce que tu préfères et je formulerai."

2. **Sous-titre** — "Une ligne sous le titre pour préciser ce que tu fais."

   Proposer 3 options :
   - "J'accompagne les startups tech dans leurs recrutements stratégiques."
   - "Recrutement sur-mesure pour les entreprises qui refusent le standard."
   - "[X] ans d'expérience. [Y] recrutements. Un seul objectif : le bon match."

3. **CTA** — "Le bouton d'action. Tu veux qu'il emmène vers tes offres ou vers le formulaire de contact ?"
   - Proposer le texte aussi : "Voir les offres", "Découvrir les opportunités", "Recruter avec moi", "Me contacter"

### Bloc 2 — Messages automatiques

4. **Confirmation candidature** — "Quand un candidat postule, il voit un message de confirmation. Par défaut : 'Merci pour votre candidature ! Je reviens vers vous dans les meilleurs délais.'"

   Proposer une version alignée avec la voice card. Demander si OK ou si l'utilisateur veut modifier.

5. **Confirmation contact** — "Même chose pour les entreprises qui te contactent. Par défaut : 'Merci pour votre message ! Je vous recontacte rapidement.'"

### Bloc 3 — Témoignages (optionnel)

6. **Témoignages** — "Tu as des témoignages de clients ou candidats satisfaits ? Si oui, donne-moi le texte, le nom, et le poste de chaque personne. Si pas encore, tu pourras en ajouter plus tard dans les Paramètres."

## Récapitulatif

```
📋 Contenu de ta landing :

Titre : "Marie Dupont — Recrutement Tech & Product"
Sous-titre : "J'accompagne les startups en croissance dans leurs recrutements clés."
CTA : "Découvrir les opportunités" → scroll vers le board
Confirmation candidature : "Merci ! J'analyse votre profil et vous recontacte très vite."
Confirmation contact : "Merci pour votre message ! Je reviens vers vous sous 48h."
Témoignages : 2 ajoutés / aucun pour le moment
```

"C'est bon ? Je peux ajuster ce que tu veux."

## Écriture en base

**AVANT d'écrire**, lire `supabase/schema.sql` pour connaître les vrais noms de colonnes.

```sql
UPDATE settings
SET 
  [colonne_hero_title] = '[TITRE]',
  [colonne_hero_subtitle] = '[SOUS_TITRE]',
  [colonne_cta_text] = '[TEXTE_CTA]',
  [colonne_cta_target] = '[board/contact]',
  [colonne_confirmation_candidat] = '[MESSAGE]',
  [colonne_confirmation_contact] = '[MESSAGE]',
  updated_at = now()
WHERE id = (SELECT id FROM settings LIMIT 1);
```

Si témoignages fournis :
```sql
INSERT INTO testimonials (user_id, [col_auteur], [col_poste], [col_contenu])
VALUES 
  ((SELECT user_id FROM settings LIMIT 1), '[NOM]', '[POSTE]', '[TEXTE]');
```

**Vérification :** relancer l'app et vérifier visuellement la landing.

"✅ Ta landing est personnalisée ! On passe aux filtres de ton board ?"
