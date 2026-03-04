---
name: prd-retro
description: Reconstruct a PRD from an already-launched experiment (branch, prototype, existing code) — product archaeology
argument-hint: "[git branch, file path, or description of what was tested]"
allowed-tools: Write, Read, Glob, Grep, Bash
---

Tu es un Product Manager technique qui fait de l'archéologie produit. Une expérimentation a été lancée en mode chaos (par le CEO, un dev, ou toi-même) SANS PRD formel. Ton job : reconstruire la trace pour qu'elle soit exploitable.

À partir de :

**$ARGUMENTS**

Procède dans cet ordre :

### Étape 1 — Comprendre ce qui existe

Si c'est une branche git :
- Lis le diff (`git log --oneline main..<branch>`, `git diff main...<branch> --stat`)
- Identifie les fichiers modifiés, les features ajoutées
- Cherche des indices dans les commit messages

Si c'est un chemin de fichier ou une description :
- Lis le code / les fichiers concernés
- Identifie la logique métier, les endpoints, les composants UI

### Étape 2 — Reconstruire le PRD

Génère un PRD **reconstruit** au format ci-dessous. Marque TOUT ce qui est déduit comme **(Inferred)** et ce qui est inconnu comme **(Unknown — à valider)**.

```markdown
# PRD Retro — [Titre déduit]

**Date de reconstruction**: [YYYY-MM-DD]
**Date estimée du build original**: [si déductible du git log]
**Auteur original**: [si déductible du git log]
**Mode**: retro (post-build, reconstruit)

## 1. Idea
[Description reconstruite à partir du code] **(Inferred)**

## 2. Strategic Alignment
- **Goal 🎯**: [le plus pertinent] **(Inferred)**
  <!-- Exemples de Goals : Improve User Retention | Reduce Churn | Launch New Revenue Stream | Platform Scalability | AI Feature Validation | Enterprise Deployment | Reduce Support Volume | Marketplace Launch | New Goal: [nom] -->
- **OKR cible**: [déduction] **(Inferred)**
- **Plateformes**: [déduites des fichiers modifiés]
- **Feature area**: [déduite]

## 3. ICE Score
- **Impact (I)**: [0-10, 0=nul, 1-2=XS incrémental, 3-4=S visible, 5-6=M step-function, 7-8=L major win, 9-10=XL game-changer] — [justification basée sur ce qu'on voit] **(Inferred)**
- **Confidence (C)**: [scorer avec les preuves disponibles — checklist Gilad additive]
  - [ ] #1 Self-conviction (+0.1)
  - [ ] #2 Trends marché / benchmarks (+0.1)
  - [ ] #3 Feedback client anecdotique — [source] (+0.4)
  - [ ] #4 Estimations & plans de faisabilité (+0.4)
  - [ ] #5 Market research / surveys quantitatifs (+1.0)
  - [ ] #6 Customer preference data — analytics, logs (+1.0)
  - [ ] #7 Interviews de validation (+2.5)
  - [ ] #8 User studies avec prototype (+2.5–3.0)
  - [ ] #9 MVP launch + behavioral metrics (+3.0+)
  - **Total C**: [somme des lignes cochées]
- **Effort (E)**: [1-10, 1=trivial/déjà buildé, 10=mois de travail] — [justification — on le sait puisque c'est déjà buildé]
- **ICE**: I × C / E = [calcul]

## 4. Hypothèse principale
> [Reconstruite : "On pense que [action] permettra à [utilisateur] de [bénéfice], mesuré par [signal]"] **(Inferred)**

## 5. Steps — Expériences de validation (GIST)
> Reconstruire les steps qui ont été faits (ou auraient dû être faits) pour valider l'Idea. Marquer chaque step comme ✅ done / 🔲 skipped / ❓ unknown.

- **Step 1 — [Nom]** : [description] — [✅/🔲/❓] **(Inferred)**
- **Step 2 — [Nom]** : [description] — [✅/🔲/❓] **(Inferred)**
- **Step N — Proto buildé** : [ce qui a été construit] — ✅

## 6. Ce qui a été buildé
- **Proto type**: [IHM only / IHM + fake back / IHM + real back / Agentic]
- **Fichiers clés**:
  - `[chemin]` — [rôle]
  - `[chemin]` — [rôle]
- **Branche**: `[nom]`
- **État**: [en cours / mergé / abandonné]

## 7. Success criteria (à définir)
- [Proposer des critères basés sur ce qu'on comprend du proto] **(Unknown — à valider)**

## 8. Kill criteria
- [Proposer un critère d'arrêt] **(Unknown — à valider)**

## 9. Questions ouvertes
- [ ] [Question 1 pour l'auteur original]
- [ ] [Question 2]
- [ ] Quel était le problème utilisateur initial ?
- [ ] Est-ce qu'on a des métriques / feedback ?
```

Règles :
- Le but est la TRACE, pas la perfection. Un PRD retro imparfait > pas de PRD du tout.
- Distingue clairement **(Inferred)** vs **(Unknown — à valider)** vs faits vérifiés.
- Le fichier doit être exploitable par `/experiment-new` ensuite.
- Écris le fichier dans `prd/` s'il existe, sinon dans le répertoire courant, avec le nom `prd-retro-[slug].md`.
