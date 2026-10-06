> Traduction communautaire (brouillon) — politique NTARI P2-002, Diffusion multilingue mondiale. Source : CONFORMANCE.md (original anglais, instantané du 2026-10-05). Brouillon communautaire assisté par machine, en attente de relecture par le mainteneur régional conformément à P2-002 §3.1. Les spécifications techniques essentielles restent en anglais conformément au §2.2.
>
> Vous avez remarqué une erreur de traduction ? N'hésitez pas à la corriger
> vous-même : forkez le dépôt https://github.com/NTARI-RAND/shelter et
> ouvrez une pull request. Les corrections de traduction sont des
> contributions précieuses, tout autant que le code.

> L'original anglais est normatif. Cette traduction est proposée pour la lecture, non pour l'interprétation : là où elle diverge de `CONFORMANCE.md`, l'anglais prévaut. Les mots-clés normatifs (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY), les noms de champs, les libellés et les identifiants sont conservés en anglais, car ils constituent le contrat de conformité.

# shelter — Autodescription de conformité

*Auto-attestée jusqu'à ce qu'une suite de tests cite ces identifiants (registre : `JFA/jfa-conformance-suite.py`). Stade de proposition ; « respecté par conception » signifie que la spécification énonce la règle et exige le test.*

**Forme :** l'économie du soutien à la réinsertion de la partie VI de l'architecture — évaluation bidirectionnelle, hors ligne d'abord sur des appareils d'entrée de gamme, la dignité et la sortie avant la surveillance ; le cas désigné comme le plus aigu du problème ouvert 8.

| ID | Invariant | Statut |
|---|---|---|
| COV-1 | Aucune moyenne ; distribution complète | **Respecté par conception** — INV-4, avec un garde-fou grep en CI exigé |
| COV-2 | La note la plus basse est le manquement | **Respecté par conception** — −1 signalé comme préjudice, commentaire obligatoire, travailleur social averti |
| COV-3 | Symétrique ; toute affirmation peut recevoir une réponse | **Respecté par conception** — évaluations bidirectionnelles ; la voie de contestation annote, n'efface jamais |
| COV-4 | Détermine si, jamais combien | **Respecté par conception** — aucun score ne conditionne la valeur de quiconque ; le décompte est un signal positif, pas un rang |
| REC-1 | En ajout seul | **Respecté par conception** — événements d'évaluation immuables ; la frontière entre effaçable et durable reste ouverte (OQ-1) |
| REC-2 | Pardonner, ne jamais cacher | **Respecté par conception** — les signalements et contestations contextualisent |
| REC-3 | Aucune donnée personnelle (PII) dans ce qui est partagé | **Respecté par conception** — INV-5/6/8/10 : pseudonymes, aucune PII dans les journaux, vérifier sans conserver, aucune catégorie de statut |
| REC-9 | Types de relations | **Partiel** — les évaluations typées par direction (citoyen↔bénéficiaire, inspection) s'y rattachent naturellement ; typage formel lors de la construction |
| GOV-9 | Document vivant des questions ouvertes | **Respecté** — `OPEN-QUESTIONS.md` est livré avec ce document fondateur |
| GOV-10 | Communs copyleft | **Ouvert** — réutiliser LBTAS ou le réimplémenter (OQ-3) |
| Problème 8 | Plancher de coût du témoignage | **Nommé** — registres auto-attestés tant que l'attestation par témoins n'est pas abordable ici (OQ-2) |

**Ligne d'honnêteté :** aucun registre attesté par témoins n'existe ; toute lecture de réputation est attestée par l'opérateur. La défense « nous ne collectons pas cela » d'INV-10 est architecturale et doit survivre à chaque modification future du schéma.
