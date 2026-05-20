# Interview — Voice Card

Cette interview construit la "voice card" du recruteur : le document qui donne le ton à tous les agents IA qui rédigent du texte (mails de refus, présentations candidats, annonces d'emploi, propositions d'embauche).

## Consignes pour Claude

- C'est l'interview la plus importante — elle conditionne la qualité de TOUTE la communication IA
- **Approche inversée** : ne PAS demander à l'utilisateur de décrire son ton. Lui demander de montrer, pas de dire. On analyse ses mails, pas ses mots pour parler de ses mots.
- Si l'utilisateur ne peut pas fournir de mails, basculer sur le mode guidé (questions simples avec choix)
- Garder ça court — 5 minutes max, pas 15

## Déroulement

### Étape 1 — Collecter des exemples (méthode préférée)

"Tes agents IA vont écrire à ta place — des mails de refus, des présentations candidats, des annonces. Pour qu'ils te ressemblent, le mieux c'est que je regarde comment tu écris. Tu pourrais me coller 2-3 mails que tu as envoyés récemment ? (N'importe lesquels — un mail à un candidat, un client, un refus...)"

**Si l'utilisateur fournit des mails :**

Analyser silencieusement et extraire :
- Vouvoiement ou tutoiement
- Longueur moyenne des phrases
- Registre (formel, semi-formel, décontracté)
- Signature utilisée
- Tournures récurrentes
- Ton global (direct, chaleureux, sobre, expressif...)
- Mots ou expressions caractéristiques

Puis présenter l'analyse : "Voici ce que je retiens de ton style :"
- "Tu [vouvoies/tutoies]"
- "Ton ton est [description en une phrase]"
- "Tu signes [signature]"
- "Tu utilises des phrases [courtes/moyennes/longues]"
- "J'ai noté des expressions qui te sont propres : [liste]"

Demander : "C'est bien ça ? Tu veux ajuster quelque chose ?"

Puis poser uniquement les questions complémentaires auxquelles les mails ne répondent pas :

**Si l'utilisateur ne peut pas fournir de mails → Mode guidé :**

### Étape 2 (alternative) — Mode guidé rapide

Si pas de mails disponibles, poser ces 4 questions avec des choix simples :

1. "Tu vouvoies ou tu tutoies tes candidats ?"

2. "Ton style c'est plutôt :" (proposer 3 options concrètes)
   - "Pro et chaleureux — le candidat se sent respecté et accueilli"
   - "Direct et efficace — on va droit au but, pas de blabla"
   - "Formel et soigné — chaque mot est pesé"

3. "Comment tu signes tes mails ?" (ex : Marie, Cordialement Marie, Belle journée, Marie Dupont)

4. "Des mots ou expressions que tu détestes dans un mail pro ?" (ex : 'n'hésitez pas', 'je reviens vers vous', 'nous avons le regret')

### Étape 3 — Compilation

Compiler la voice card en markdown structuré :

```markdown
# Voice Card — [Prénom Nom]

## Règles de communication
- **Adresse :** [Vouvoiement/Tutoiement]
- **Signature :** [Signature]
- **Ton :** [Description en une phrase]

## Style d'écriture
- Phrases [courtes/moyennes/longues]
- Registre [formel/semi-formel/décontracté]
- [Caractéristiques spécifiques extraites des mails]

## Valeurs à transmettre
- [Valeur 1]
- [Valeur 2]
- [Valeur 3]

## Expressions à utiliser
- [Si identifiées dans les mails]

## Expressions à éviter
- [Si mentionnées par l'utilisateur]

## Exemple de référence
[Le meilleur mail fourni, comme modèle de ton]
```

### Étape 4 — Test en live

**Générer un court test** pour que l'utilisateur voie le résultat :

"Voici ce que donnerait un mail de refus avec ta voice card :"

Inventer un candidat fictif (prénom + poste) et générer un mail de refus de 5-6 lignes en appliquant strictement la voice card.

"Ce ton te convient ? Tu veux ajuster quelque chose ?"

Si l'utilisateur veut ajuster : modifier la voice card et regénérer le test. Maximum 2 itérations — au-delà, c'est de la suroptimisation.

## Écriture en base

**AVANT d'écrire**, lire `supabase/schema.sql` pour connaître les vrais noms de colonnes liées à la voice card dans la table `settings`.

```sql
UPDATE settings
SET 
  [colonne_voice_card] = '[VOICE_CARD_MARKDOWN_COMPLET]',
  [colonne_formalite] = '[vouvoiement/tutoiement]',
  [colonne_signature] = '[signature]',
  -- ... adapter aux colonnes réelles
  updated_at = now()
WHERE id = (SELECT id FROM settings LIMIT 1);
```

Exécuter via MCP Supabase.

"✅ Ta voice card est enregistrée ! Tous tes agents IA parleront avec ton ton. On passe au contenu de ta landing ?"
