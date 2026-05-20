# Interview — Profil recruteur

Cette interview collecte les informations du recruteur pour la landing et le back-office.

## Consignes pour Claude

- Par petits blocs de 2-3 questions
- Exemples concrets à chaque question
- Si question optionnelle sans réponse, passer sans insister
- À la fin, récapituler et valider avant d'écrire

## Déroulement

### Bloc 1 — L'essentiel

"On va remplir ton profil — c'est ce qui apparaîtra sur ta page publique. 5 minutes max."

1. **Prénom et nom**
2. **Titre** — "En une ligne, comment tu te présentes ? Ex : 'Recruteuse spécialisée Tech & Product'"
3. **Spécialisations** — "Tes domaines ? (3-8 tags : Tech, Product, Data, SaaS...)"

### Bloc 2 — Bio

4. **Bio** — "Quelques phrases sur toi. Même en vrac, je reformule."
   - Si vrac : reformuler en 3-4 phrases, demander validation
   - Stocker en HTML

### Bloc 3 — Optionnel

5. **Chiffres clés** — "Des chiffres à afficher ? Années d'expérience, nombre de recrutements..."
6. **LinkedIn** — "Un lien LinkedIn ?"
7. **Calendly** — "Un outil de prise de RDV ?"
8. **Email de contact** — "Quel email pour les contacts et notifications ?"
9. **Photo** — "Tu as une photo pro ? Envoie-la si tu veux."
   - Si fournie : uploader dans Supabase Storage bucket `avatars`, stocker l'URL publique
   - Si pas fournie : laisser le placeholder
10. **Logo** — "Tu as un logo ? Sinon le logo La Pépite sera utilisé."
    - Si fourni : uploader dans Storage bucket `branding`, stocker l'URL publique
    - Si pas fourni : copier le logo par défaut depuis les assets du skill vers le bucket `branding`

### Bloc 4 — Route admin

11. **Route admin** — "Ton back-office est accessible via une adresse secrète. Par défaut c'est `/espace-recruteur`. Tu peux la personnaliser (ex : `/admin-marie`)."

### Upload des fichiers dans Storage

Si photo ou logo fournis, les uploader via Supabase Storage :

**Via MCP :** vérifier si un outil d'upload Storage existe dans le MCP Supabase. Si non, guider via Dashboard :

1. "Va dans Supabase → Storage → bucket 'avatars' (ou 'branding')"
2. "Clique 'Upload file'"
3. "Sélectionne ta photo/ton logo"
4. "Une fois uploadé, clique sur le fichier → copie l'URL publique"

Ou via CLI si supabase CLI est disponible :
```bash
supabase storage cp [FICHIER_LOCAL] sb://avatars/profile-photo.[ext]
```

## Récapitulatif

```
📋 Ton profil recruteur :

Nom : Marie Dupont
Titre : Recruteuse spécialisée Tech & Product
Spécialisations : Tech, Product, Data, SaaS
Bio : "Après 12 ans dans le recrutement tech..."
Chiffres : 12 ans · 350+ recrutements · 95% rétention
LinkedIn : linkedin.com/in/mariedupont
Email : marie@recrutement.fr
Route admin : /espace-marie
Photo : ✅ uploadée / ❌ placeholder
Logo : ✅ uploadé / ❌ logo par défaut
```

"Tout est bon ?"

## Écriture en base

**AVANT d'écrire :** lire `supabase/schema.sql` pour les vrais noms de colonnes.

**AVANT l'UPDATE :** sauvegarder l'état actuel :
```sql
SELECT * FROM settings LIMIT 1;
```

Puis écrire :
```sql
UPDATE settings
SET 
  [col_nom] = 'Marie Dupont',
  [col_titre] = 'Recruteuse spécialisée Tech & Product',
  -- ... adapter aux colonnes réelles
  updated_at = now()
WHERE id = (SELECT id FROM settings LIMIT 1);
```

Exécuter via MCP Supabase.

**Vérification :** `SELECT [col_nom], [col_titre] FROM settings LIMIT 1;`

"✅ Ton profil est en place ! On passe à la voice card."
