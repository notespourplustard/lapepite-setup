# La Pépite — Skill d'installation Claude Code

Skill Claude Code pour installer et personnaliser [La Pépite](https://github.com/notespourplustard/lapepite), l'application web clé en main pour recruteurs freelance indépendants.

## Ce que fait ce skill

Il installe l'app La Pépite en **3 phases** :

1. **Installation technique** — Clone le repo, installe les dépendances, configure Supabase (base de données, Edge Functions, Storage), crée le repo GitHub de l'utilisateur
2. **Personnalisation** — 6 interviews conversationnelles (profil, voice card, landing, filtres, agents IA, trames légales) + mise à jour du CLAUDE.md
3. **Déploiement** — Vercel, Resend (emails), domaine custom

Une **pause test local** est prévue entre la Phase 2 et la Phase 3 pour que l'utilisateur teste et personnalise visuellement son app avant la mise en ligne.

## Cible

Recruteurs freelance non développeurs. Le skill est entièrement en français, n'utilise aucun jargon technique sans explication, et ne demande jamais à l'utilisateur de lire ou modifier du code.

## Structure

```
├── SKILL.md                              ← Routeur principal
├── references/
│   ├── install-guide.md                  ← Phase 1 : installation technique
│   ├── interview-profil.md               ← Phase 2.1 : identité, bio, branding
│   ├── interview-voicecard.md            ← Phase 2.2 : ton des agents IA
│   ├── interview-landing.md              ← Phase 2.3 : hero, CTA, témoignages
│   ├── interview-filters.md              ← Phase 2.4 : filtres du board d'offres
│   ├── interview-agents.md               ← Phase 2.5 : activation et modèles IA
│   ├── interview-legal.md                ← Phase 2.6 : trames RGPD et légales
│   ├── update-claude-md.md               ← Mise à jour CLAUDE.md avec infos instance
│   ├── pause-test-local.md               ← Pause : test et personnalisation visuelle
│   └── deploy-guide.md                   ← Phase 3 : Vercel, Resend, domaine
└── assets/
    └── la_pepite_logo.png                ← Logo par défaut
```

## Prérequis pour l'utilisateur

- [Claude Code](https://claude.ai) (abonnement Pro ou Max)
- Un compte [Supabase](https://supabase.com) (gratuit)
- Une clé API [Anthropic](https://console.anthropic.com)
- Un compte [GitHub](https://github.com) (gratuit)

## Utilisation

Dans Claude Code, dire :

> "Installe La Pépite"

Le skill se déclenche automatiquement.

## Projet lié

Le code de l'application est dans le repo [notespourplustard/lapepite](https://github.com/notespourplustard/lapepite).

---

*Construit avec ❤️ par [Notes pour plus tard](https://notespourplustard.com)*
