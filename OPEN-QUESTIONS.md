# shelter — Open Questions

*The living open-questions deliverable required by Janus-Facing Architecture (standard check 7). Reviewed and re-shipped with each release; staleness is nonconformance. An entry leaves only by resolution or by an explicit values call made by the people who live with the outcome. Invariant IDs cite the registry in `JFA/jfa-conformance-suite.py`. This document is the canonical home of the spec's §15 decisions.*

**shelter** is the architecture's **reintegration-support economy** shape (Part VI): the assessment scale adapted bidirectionally, offline-first for entry-level devices, dignity and exit ahead of oversight — and the sharpest case for architecture open problem 8.

## 1. Deletion rights vs. the append-only record
**Problem.** §2.6 promises retention limits, deletion rights, and a record that "should not be permanent" — while the record layer demands append-only, corrections-as-new-entries (REC-1).
**Constraints inherited.** The architecture already carries the resolution shape: PII and narrative live in an erasable, operator-local layer; only PII-free structural facts are durable (REC-3). Deletion applies to the person's data, never to the structural audit trail; disputes annotate and never delete (already INV-3). What remains is drawing the exact line for shelter's entities.
**Status.** Open — decide which fields are erasable-personal vs. durable-structural before the schema freezes.

## 2. The witness cost floor lands hardest here (architecture problem 8)
**Problem.** shelter's population is offline-first on entry-level devices, yet honest witnessing carries the heaviest resource floor in the stack — and filing-at-an-independent-witness assumes reachable witnesses.
**Constraints inherited.** A fix must not concentrate witnessing in the operator or a few large parties; membership-as-witnessing is the architecture-shaped lever. Until then, shelter's records are self-attested and dispositions must say so (problem 2).
**Status.** Open at the network level (Part VII, problem 8); shelter is its named sharpest case and should feed observations upward.

## 3. Licensing (spec §15.10 / §16)
**Problem.** Reuse LBTAS code (AGPL for the whole networked app) or reimplement the behavior independently.
**Constraints inherited.** Provenance is inbound = outbound copyleft, no CLA (GOV-10); NTARI's §1.4 open-source commitment applies either way; a reimplementation must reproduce the distribution-on-read model, the −1 comment rule, and harm-flagging — those behaviors *are* the scale.
**Status.** Open — AGPL-3.0 end-to-end is the path of least tension.

## 4. The caseworker gate and beneficiary exit (INV-1)
**Problem.** No active CaseLink, no function — the anchor that makes verification real is also a power chokepoint over the beneficiary.
**Constraints inherited.** Dignity over control; designed-to-be-exited; the humane exception (own-data access and crisis resources survive a dropped CaseLink) is a floor, not the answer. Voice and exit for the person when the caseworker relationship fails needs a real path (second caseworker? operator review?).
**Status.** Open.

## 5. Safety screening for high-risk providers (§15.1)
**Problem.** ID verification confirms identity, not suitability; do lodging/transport trustees need background or registry checks?
**Constraints inherited.** Vetting scales with the power the role holds over the beneficiary; prevention-first precedent (the sibling high-stakes shape) vs. the project's second-chance ethos — a genuine values call.
**Status.** Open; carries safety weight, decide early.

## 6. Remaining §15 decisions (consolidated)
**Problem.** ID retention policy (§15.2, INV-8 default verify-don't-retain); inspection thresholds and re-inspection cadence (§15.3); other-occupant policy (§15.4); transport proof specifics (§15.5); inspection category wording (§15.6); mentorship vetting level (§15.7); verifiable-caseworker credential bar across the expanded scope (§15.8); graduation-to-giver (§15.9 — fits designed-to-be-exited, needs conflict-of-interest thinking); PWA vs. wrapped + SMS fallback (§15.11); jurisdiction/compliance scope, retention windows, match initiation, languages, hosting (§15.12).
**Constraints inherited.** Each resolution stays inside the spec's invariants INV-1..INV-10, which already encode the covenant and record disciplines (no averaging, harm visible, no PII, no status tracking).
**Status.** All open; §15.8 (the caseworker bar) gates everything else — the gatekeeper is only as sound as that bar.
