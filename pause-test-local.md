# Pause — Test en local et personnalisation

Déclenché APRÈS la Phase 2 et AVANT la Phase 3.

---

## Contexte pour l'utilisateur

"**Ton app est fonctionnelle !** Elle tourne sur ton ordinateur avec toutes tes infos."

### Ce qui fonctionne :
- "✅ Ta landing page personnalisée (profil, accroche, filtres)"
- "✅ Ton back-office complet (clients, missions, offres, candidats)"
- "✅ Tes agents IA"
- "✅ Le dark mode"
- "✅ Tes pages légales"

### Ce qui ne fonctionne PAS encore :
- "❌ L'envoi d'emails (sera configuré au déploiement)"
- "❌ Le site n'est visible que sur ton ordinateur"
- "❌ Pas de nom de domaine"

---

## Test guidé

```bash
cd lapepite && npm run dev
```

### Parcours de test :

**1. La landing**
- "Regarde ton profil, ton titre, ton accroche. Ça te plaît ?"
- "Teste les filtres du board"
- "Vérifie les pages légales (liens en bas)"
- "Teste le formulaire de contact avec des infos fictives"

**2. Le back-office**
- "Connecte-toi via ta route admin"
- "Crée un client test → une mission → une offre avec brief"
- "Clique 'Générer l'annonce avec l'IA' — vérifie le ton"
- "Publie l'offre → vérifie qu'elle apparaît sur la landing"
- "Postule à ta propre offre (en navigation privée) avec un CV fictif"
- "Vérifie que la candidature arrive dans le back-office"

**3. Les paramètres**
- "Parcours chaque section"
- "Vérifie ta voice card et tes agents"

---

## Personnalisation visuelle

"Si quelque chose ne te plaît pas sur ta landing — une couleur, un espacement, la taille d'un titre — **tu peux me demander de le changer**."

"Exemples de choses que tu peux me demander :"
- "'Je voudrais que le titre soit plus gros'"
- "'Change la couleur du bouton en bleu foncé'"
- "'Mets ma photo en rond'"
- "'Change l'ordre des sections'"

### Règles pour Claude pendant la personnalisation :

1. **Avant de modifier**, lire le composant concerné pour comprendre sa structure. Ne pas modifier à l'aveugle.
2. **Modifications ciblées.** Changer uniquement ce qui est demandé. Ne pas "améliorer" d'autres choses en passant.
3. **Tester après chaque changement.** L'app se recharge automatiquement (hot reload). Demander à l'utilisateur : "Rafraîchis ta page — c'est mieux ?"
4. **Si un changement casse quelque chose :** annuler immédiatement avec `git checkout -- [fichier]` et recommencer différemment. Expliquer à l'utilisateur : "Ça n'a pas marché comme prévu, je reviens en arrière et j'essaie autrement."
5. **Grouper les commits.** Ne PAS committer après chaque micro-changement. Attendre que l'utilisateur soit satisfait d'un ensemble de modifications, puis committer une seule fois :

```bash
git add .
git commit -m "Personnalisation visuelle : [résumé des changements]"
git push
```

### Ce que Claude ne doit PAS faire pendant la personnalisation :

- Modifier la logique métier (routing, appels API, structure de données)
- Ajouter des dépendances npm
- Changer la structure des composants (uniquement le style/contenu)
- Promettre des changements qu'il ne peut pas vérifier ("je peux rendre ta landing 10x plus belle")

---

## Quand passer au déploiement ?

"Quand tu es satisfaite, on met le site en ligne. Il te faudra ~20 minutes."

"Tu veux continuer maintenant ou prendre du temps pour tester ?"

**Si l'utilisateur veut attendre :**

"Pas de problème ! Pour relancer ton app plus tard :"
1. "Ouvre un terminal"
2. "Tape `cd lapepite && npm run dev`"
3. "Ouvre l'adresse dans ton navigateur"

"Quand tu es prête, dis-moi 'on déploie La Pépite' et on continue."

**Si l'utilisateur continue :** Phase 3 → `references/deploy-guide.md`.
