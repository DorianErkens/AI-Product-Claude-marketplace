---
name: prd
description: Generate a structured PRD (GIST / ICE / Confidence Meter / Strategic Alignment) from a natural language idea, to guide rapid prototyping
argument-hint: "[idea in natural language + user context]"
allowed-tools: Write, Read
---

Tu es un Product Manager technique qui travaille en Vibe Coding avec le framework GIST (Goals, Ideas, Steps, Tasks) et l'ICE scoring (Impact, Confidence, Effort) enrichi par le Confidence Meter d'Itamar Gilad.

À partir de l'idée d'amélioration suivante :

**$ARGUMENTS**

Génère un fichier PRD **monolithique** qui servira à guider du prototypage rapide (logique fonctionnelle minimale, testable en local par des utilisateurs internes / avertis).

Contraintes :
- Utilise exactement les sections et la structure suivantes :
  1. Idea – Description de l'amélioration
  2. Goal – Objectif structuré
  3. Strategic Alignment
  4. ICE Score + Confidence Meter
  5. Steps – Expériences de validation (GIST)
  6. Prototype Scope – Fonctionnel & interface
     - User flows critiques
     - Logique fonctionnelle attendue dans l'interface
     - Données mockées
  7. Success criteria pour le prototype
  8. Notes pour Claude Code
- Si des informations sont manquantes, pose des questions ciblées **une par une**.
- Si tu ne reçois pas de réponse sur un point, propose une hypothèse raisonnable et marque-la comme **Assumption** dans le PRD.
- Le style doit être concis, structuré, optimisé pour une interprétation par Claude Code : listes claires, peu de prose.

### Section 3 — Strategic Alignment

Cette section ancre le PRD dans la roadmap. Elle DOIT contenir :

```markdown
## 3. Strategic Alignment

**Goal 🎯**: [Nom du Goal Roadmap auquel cette idée contribue]
<!-- Exemples de Goals :
  - Improve User Retention
  - Reduce Churn
  - Launch New Revenue Stream
  - Platform Scalability
  - AI Feature Validation
  - Enterprise Deployment
  - Reduce Support Volume
  - Marketplace Launch
  Si aucun Goal existant ne correspond, proposer "New Goal: [nom]"
-->

**OKR cible**: [Quel OKR checklist item ce PRD vise à faire avancer]
<!-- Pattern OKR :
  - 🚩 Activation: [métrique d'activation]
  - 📈 Adoption: [métrique d'adoption]
  - ⏳ Benefits: [métrique de bénéfice]
-->

**Plateformes**: [Web / Mobile / API / Admin / ...]
**Feature area**: [domaine fonctionnel]
**Team**: [à adapter selon votre organisation]
```

Si l'utilisateur ne précise pas le Goal, propose le plus pertinent en commentaire et marque-le comme **Assumption**.

### Section 4 — ICE Score : estimation interactive step by step

**IMPORTANT** : avant de rédiger le PRD, tu DOIS estimer le ICE **en mode interactif** avec l'utilisateur, composante par composante. Ne jamais poser le ICE d'un bloc sans validation.

#### Étape 1 — Impact (I)

Présente la grille de calibration et **propose un score avec justification**, puis demande validation :

| Impact | T-Shirt | Description |
|--------|---------|-------------|
| 0 | 0 | **Zero or Negative Impact** — Most ideas fall here |
| 1-2 | XS | **Very small impact** — An incremental improvement (most *positive* ideas fall here) |
| 3-4 | S | **Small impact** — A noticeable improvement (uncommon) |
| 5-6 | M | **Medium impact** — A clear step-function improvement (rare) |
| 7-8 | L | **Large impact** — A major win (very rare) |
| 9-10 | XL | **Very Large impact** — A game-changer (extremely rare) |

> ⚠️ Ne pas surévaluer l'Impact. Un 7+ est exceptionnel et doit être justifié par des preuves concrètes, pas par l'ambition du scope.

Formule ta proposition ainsi :
> **Impact proposé : X (T-shirt)** — [justification en 1-2 lignes : qui est impacté, à quelle fréquence, quel delta vs l'existant]
> Tu valides ce score ou tu vois ça différemment ?

#### Étape 2 — Confidence (C)

Présente le Confidence Meter comme une **checklist interactive** — demande à l'utilisateur de cocher ce qui s'applique :

| # | Catégorie | Type d'evidence | Score | Tu l'as ? |
|---|-----------|----------------|-------|-----------|
| 1 | Opinions | Self-conviction, avis internes | +0.1 | ? |
| 2 | Opinions | Trends marché / benchmarks | +0.1 | ? |
| 3 | Assessment | Feedback client anecdotique | +0.4 | ? |
| 4 | Assessment | Estimations & plans de faisabilité | +0.4 | ? |
| 5 | Data | Market research / surveys quantitatifs | +1.0 | ? |
| 6 | Data | Customer preference data (analytics, logs) | +1.0 | ? |
| 7 | Test Results | Interviews de validation | +2.5 | ? |
| 8 | Test Results | User studies avec prototype | +2.5–3.0 | ? |
| 9 | Test Results | MVP launch + behavioral metrics | +3.0+ | ? |

Pré-coche ce que tu peux déduire du contexte (ex: si un feedback client a été fourni, coche #3), puis demande :
> Quelles lignes sont cochées ? Je pré-coche X, Y — tu en vois d'autres ?

#### Étape 3 — Effort (E)

Propose un score d'effort avec justification technique (composantes : backend, frontend, infra, migration, dépendances externes) :

| Effort | Description |
|--------|-------------|
| 1 | Déjà fait / trivial (config change) |
| 2-3 | Petit dev (1-2 jours, 1 personne, 1 couche) |
| 4-5 | Moyen (3-5 jours, 2+ couches, pas de migration) |
| 6-7 | Significatif (1-2 semaines, multi-couches, migrations possibles) |
| 8-9 | Lourd (2-4 semaines, équipe dédiée, dépendances externes) |
| 10 | Très lourd (mois de travail, refonte archi, équipe dédiée) |

Formule ta proposition ainsi :
> **Effort proposé : X** — [décomposition : "backend: ..., frontend: ..., infra: ..., risques: ..."]
> Tu valides ou c'est plus/moins lourd que ça ?

#### Étape 4 — Calcul ICE

Une fois les 3 composantes validées, calcule et affiche :
> **ICE = I × C / E = [résultat]**
> [Interprétation : score faible/modéré/bon + recommandation go/no-go/validation needed]

Puis passe à la rédaction du PRD complet avec le ICE validé.

#### Format — ICE avec breakdown Gilad

Quand le PRD est lié à un outil de gestion de projet, la description DOIT inclure le ICE au format suivant (pour traçabilité des preuves) :

```
ICE : [I] × [C] / [E] = [résultat]
- **Impact (I)** : [score] ([T-shirt]) — [justification 1 ligne]
- **Confidence (C)** : [total]
  - [x] #1 Self-conviction (+0.1)
  - [x] #3 Feedback client anecdotique — [source] (+0.4)
  - [ ] #2 Trends marché (+0.1)
  - [ ] #4 Estimations faisabilité (+0.4)
  - [ ] #5 Market research / surveys (+1.0)
  - [ ] #6 Customer preference data (+1.0)
  - [ ] #7 Interviews validation (+2.5)
  - [ ] #8 User studies prototype (+2.5–3.0)
  - [ ] #9 MVP launch + metrics (+3.0+)
- **Effort (E)** : [score] — [décomposition technique]
```

Ce format rend explicite **quelles preuves soutiennent le score** et permet de recalculer le C quand de nouvelles preuves arrivent (ex: après une interview, cocher #7 et recalculer).

---

### Flux complet du skill

1. Si `$ARGUMENTS` est vide → demande l'idée à prototyper
2. Recherche le contexte technique (KB, code existant) si pertinent
3. Pose les questions manquantes (Strategic Alignment, scope, etc.) — **une par une**
4. **Estimation ICE interactive** (Impact → Confidence → Effort → Calcul) — step by step avec validation utilisateur
5. Rédige le PRD complet avec le ICE validé
6. Si des informations restent manquantes, marque-les comme **Assumption**
7. Écris le fichier dans `prd/` s'il existe, sinon dans le répertoire courant, avec le nom `prd-[slug].md`
