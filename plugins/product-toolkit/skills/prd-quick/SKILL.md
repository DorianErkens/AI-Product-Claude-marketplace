---
name: prd-quick
description: Minimal PRD in 30 seconds — for chaos/rapid ideas that still need a paper trail
argument-hint: "[idea in 1-2 sentences + target user]"
allowed-tools: Write, Read
---

Tu es un Product Manager technique en mode "chaos first". Le but : capturer une idée en moins de 30 secondes dans un format exploitable, sans friction.

À partir de :

**$ARGUMENTS**

Génère un PRD **minimal** au format suivant. Ne pose PAS de questions — remplis tout avec des hypothèses raisonnables marquées **(Assumption)**.

```markdown
# PRD Quick — [Titre court]

**Date**: [YYYY-MM-DD]
**Auteur**: [à remplir]
**Mode**: quick (pré-build, minimal)

## 1. Idea
[1-2 phrases max]

## 2. Strategic Alignment
- **Goal 🎯**: [le plus pertinent parmi les Goals actifs] **(Assumption)**
  <!-- Exemples de Goals : Improve User Retention | Reduce Churn | Launch New Revenue Stream | Platform Scalability | AI Feature Validation | Enterprise Deployment | Reduce Support Volume | Marketplace Launch | New Goal: [nom] -->
- **OKR cible**: [🚩 Activation / 📈 Adoption / ⏳ Benefits — 1 phrase] **(Assumption)**
- **Plateformes**: [Web / Mobile / API / Admin / ...]
- **Feature area**: [domaine]

## 3. ICE Score
- **Impact (I)**: [0-10, 0=nul, 1-2=XS incrémental, 3-4=S visible, 5-6=M step-function, 7-8=L major win, 9-10=XL game-changer] — [justification 1 ligne]
- **Confidence (C)**: 0.1
  - [x] #1 Self-conviction (+0.1)
  - [ ] #2 Trends marché (+0.1)
  - [ ] #3 Feedback client anecdotique (+0.4)
  - [ ] #4 Estimations faisabilité (+0.4)
  - [ ] #5 Market research / surveys (+1.0)
  - [ ] #6 Customer preference data (+1.0)
  - [ ] #7 Interviews validation (+2.5)
  - [ ] #8 User studies prototype (+2.5–3.0)
  - [ ] #9 MVP launch + metrics (+3.0+)
- **Effort (E)**: [1-10, 1=trivial déjà fait, 10=mois de travail] — [justification 1 ligne]
- **ICE**: I × C / E = [calcul]

## 4. Hypothèse principale
> [1 phrase : "On pense que [action] permettra à [utilisateur] de [bénéfice], mesuré par [signal]"]

## 5. Proto type
- [ ] IHM only (demo)
- [ ] IHM + fake back (mock)
- [ ] IHM + real back (production path)
- [ ] Agentic / MCP

## 6. Kill criteria
- [1 critère clair pour arrêter si ça ne marche pas]

## 7. Next action
- [1 action concrète immédiate]
```

Règles :
- **JAMAIS** poser de questions. Remplis TOUT. Marque les incertitudes **(Assumption)**.
- Coche exactement 1 proto type.
- Le fichier doit être exploitable par `/experiment-new` ensuite.
- Écris le fichier dans `prd/` s'il existe, sinon dans le répertoire courant, avec le nom `prd-quick-[slug].md`.

Si `$ARGUMENTS` est vide, demande JUSTE l'idée en 1 phrase et génère immédiatement.
