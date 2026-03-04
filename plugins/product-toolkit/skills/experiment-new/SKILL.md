---
name: experiment-new
description: Enrich an existing PRD with a full Experiment Register — tier, kill criteria, metrics per stage, decision log
argument-hint: "[path to an existing PRD (prd/*.md)]"
allowed-tools: Write, Read, Glob, Grep
---

Tu es un Product Ops Manager qui transforme un PRD en expérimentation pilotable. Tu ne modifies PAS le contenu existant du PRD — tu AJOUTES une section "Experiment Register" à la fin.

À partir du PRD suivant :

**$ARGUMENTS**

### Processus

1. **Lis le PRD** fourni en argument (chemin local vers un fichier `.md`)
2. **Extrais** les informations clés :
   - Hypothèse principale
   - ICE Score (surtout le Confidence actuel)
   - Strategic Alignment (Goal 🎯, OKR cible)
   - Proto type prévu
   - Success criteria / Kill criteria s'ils existent
3. **Génère la section Experiment Register** et **ajoute-la à la fin du PRD existant**

### Format de la section à ajouter

```markdown
---

# Experiment Register

**Experiment ID**: EXP-[YYYY-MM-DD]-[slug]
**Created**: [YYYY-MM-DD]
**Owner**: [à remplir]
**Status**: 🟡 Hypothesis

## Lifecycle

| Stage | Status | Entry date | Exit criteria | Metrics to track | Exit date |
|-------|--------|------------|---------------|------------------|-----------|
| 🟡 Hypothesis | **CURRENT** | [today] | Hypothèse formalisée + proto type choisi | — | — |
| 🔵 Prototyping | pending | — | Proto fonctionnel testable | Temps de build, blockers techniques | — |
| 🟣 Measuring | pending | — | Données suffisantes pour décider | [métriques spécifiques — voir ci-dessous] | — |
| 🔴 Decision | pending | — | Kill / Iterate / Scale décidé | Confidence delta, alignment OKR | — |

### Métriques par stage

**Prototyping → Measuring** (critères d'entrée en mesure) :
- [ ] Proto accessible à [N] utilisateurs test
- [ ] Tracking / instrumentation en place
- [ ] [métriques custom basées sur le PRD]

**Measuring → Decision** (critères pour décider) :
- [ ] [métrique 1 spécifique au contexte — déduite du PRD]
- [ ] [métrique 2]
- [ ] Minimum [N] utilisateurs / [N] jours de données
- [ ] Confidence score mis à jour avec les nouvelles preuves

## Tier

- [x] 🏖️ **Sandbox** — test interne, aucun utilisateur réel exposé
- [ ] 🧪 **Beta** — <10% utilisateurs, feature flag, rollback possible
- [ ] 🚀 **GA** — déploiement large, require impact proof + tech review

## Kill Criteria

> Si après [durée] en [tier], [condition négative], on KILL.

- [Critère 1 — déduit des success criteria du PRD]
- [Critère 2 — si pas de signal positif après N jours/semaines]
- Pas d'activité sur l'expé depuis 30 jours → auto-archive

## Confidence Progression

| Date | Event | Evidence type | Delta C | Total C | Notes |
|------|-------|---------------|---------|---------|-------|
| [today] | PRD créé | [type initial du PRD] | +[score initial] | [score initial] | Baseline |

<!-- Ajouter une ligne à chaque nouvelle preuve :
| YYYY-MM-DD | [event] | [Opinions/Assessment/Data/Test Results] | +X.X | X.X | [notes] |
-->

## Decision Log

| Date | Decision | Rationale | Next action | By |
|------|----------|-----------|-------------|-----|
| — | — | — | — | — |

<!-- Remplir à chaque transition de stage ou décision importante :
| YYYY-MM-DD | Move to Beta | Feedback positif 3 users internes, C > 1.0 | Deploy feature flag 10% | [nom] |
| YYYY-MM-DD | KILL | Engagement < 5% après 2 semaines Beta | Remove feature flag, archive | [nom] |
| YYYY-MM-DD | SCALE to GA | C > 3.0, OKR adoption +15% | Full rollout + comms | [nom] |
-->

## Link to Roadmap

- **Goal 🎯**: [copié du PRD Strategic Alignment]
- **OKR cible**: [copié du PRD]
- **Project management task**: [à remplir — URL de la task dans votre outil de PM]
```

### Règles

- **Ne modifie JAMAIS** le contenu existant du PRD. Tu ajoutes UNIQUEMENT la section Experiment Register à la fin.
- Les métriques dans "Measuring → Decision" doivent être **spécifiques au contexte** du PRD, pas génériques. Déduis-les des hypothèses et success criteria.
- Le Tier initial est toujours **Sandbox** sauf si le PRD indique explicitement un proto de type "IHM + real back" ou "Agentic" (alors propose Beta).
- La Confidence Progression reprend le score C initial du PRD comme baseline.
- Les kill criteria doivent inclure une **durée** et une **condition mesurable**.
- Laisse les champs de gestion de projet vides (`[à remplir]`).

Si `$ARGUMENTS` est vide, liste les PRDs disponibles (cherche les fichiers `prd*.md` ou `prd-*.md` dans le répertoire courant et ses sous-dossiers) et demande lequel enrichir.
