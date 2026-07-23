# CLAUDE.md — "shelter"

> **shelter** is a lightweight mobile/web app for the **reintegration of newcomers into a local society** — people experiencing homelessness, leaving incarceration, returning from military service, immigrating into a new culture, or relocating from another city. Verified social workers connect them with local citizens who offer concrete help, and an adapted **LBTAS** reputation system lets them build trust through **inclusive, two-directional exchange** — giving as well as receiving, as participants rather than charity cases. Its two pillars are **verification** (real people, vouched-for and, where it matters, inspected) and **inclusive exchange** (help that flows both ways and is rated in both directions).

This file is the source of truth for anyone (human or AI agent) working on this codebase. **Read sections 2–4 and §6 before writing any code.** For this project, the ethics *are* the spec: the population is vulnerable, the power dynamics are real, and small design choices have outsized consequences for people's safety and dignity.

> **Naming note:** the app is **shelter** (lowercase, as styled). To avoid a collision with the old "shelter" help-type, that help-type has been renamed **`LODGING`** throughout — so in this document "shelter" now means *only* the app. Bold **shelter** marks the app name where it could be misread.

**Relationship to LBTAS:** shelter adapts NTARI's Leveson-Based Trade Assessment Scale and tracks its **canonical** guidance — in particular the **no-averaging / distribution** model and the **−1 comment requirement** described in the upstream `CLAUDE.md`. Where the upstream rules and this file describe the same mechanic, upstream wins on the mechanic; this file governs the *adaptation to the reintegration context*.

---

## 1. What this project is

**shelter** serves four groups around a single goal — helping someone become a full participant in a local society they aren't yet woven into:

- **Beneficiaries** — newcomers to the local society: people experiencing homelessness, leaving incarceration, returning from military service, immigrating into a new culture, or relocating from another city. What unites them is the *situation*, not a category — in the app each is simply a *verified beneficiary* (see §2, principle 9, on never tracking which of these brought someone in).
- **Citizens (Trustees)** — community members who offer concrete help: **meals, transportation, lodging, clothing, mentorship**.
- **Social Workers (Caseworkers)** — verified professionals who anchor every beneficiary's participation. Their remit here is broad and deliberately *expanding*: reentry, homelessness, veteran transition, resettlement, and relocation all run **through** a verified caseworker rather than around one. This is a bet that folding newcomer integration into the caseworker's role makes that role more capable and more central, not less.
- **Operator** — the backend entity running the service and verifying social workers.

Social workers verify beneficiaries **in person**. Trustees register with **verified government ID**; those offering **lodging or transportation** are additionally **inspected in person by a caseworker** before they may be listed. Every completed interaction is rated **bidirectionally** on the adapted LBTAS scale, and lodgings/vehicles carry a caseworker **inspection rating**. Social workers use that data to support — not police — the people they work with.

**The one-line mental model:** **shelter** is a *trust-building and coordination tool*, not a surveillance or scoring system. Design every feature so it helps a newcomer build trust, exchange help in both directions, and eventually *graduate out* of needing the app.

---

## 2. Non-negotiable principles (read first)

These override feature requests, convenience, and performance when they conflict. If a task seems to require violating one, **stop and raise it**.

1. **Dignity over control.** The app gives caseworkers oversight, which creates a power imbalance — counterbalance it everywhere: plain language, no shaming UI, beneficiary-facing transparency about what is tracked and why, and agency wherever possible. Never build features whose primary effect is surveillance or punishment.
2. **Reputation is a distribution, never an average.** A person's reputation is the *count of ratings at each level* (−1..+4) plus a *total*, never a mean, composite, or "trust score" that gates their worth (see §6). A total count is allowed and is a *positive* signal; a harm-diluting average is banned.
3. **Two-directional accountability is the point.** Citizens are rated on how well they treated the beneficiary (respect, safety, follow-through) just as much as the reverse. This is what makes exchange *inclusive* rather than charitable: the newcomer is a rated, accountable participant, not a passive recipient.
4. **Data minimization.** Collect the least data needed for an interaction to happen. **Do not** store criminal history, diagnoses, immigration status, incarceration specifics, or which population brought a beneficiary in (principle 9). **Do not** hoard raw identity documents (see §11) — verify identity and record the *fact* of verification.
5. **Privacy by default.** A citizen never sees a beneficiary's legal name, address, status category, or history unless strictly necessary for a confirmed interaction. Default to pseudonymous display names.
6. **Designed to be exited.** Reintegration means the goal is to *leave* the system. Build in retention limits, deletion rights, and the sense that this is a passage, not a permanent file that follows someone for life. The fullest form of reintegration is a former beneficiary who becomes a giver; the design should not preclude that arc (see §15).
7. **Safety in physical meetings is safety-critical.** This app sends people to meet in person, and into each other's homes and cars. No default address sharing, public-meeting guidance for first contact, easy reporting, and a serious answer to *who is vetted and how* (see §7.2 and §15).
8. **Accessibility and low-barrier access are core, not polish.** Users may be in crisis, low-literacy, multilingual, disabled, or on a $20 phone with metered data. The app must work for them or it fails its mission.
9. **The person, not the label.** A verified beneficiary is a full participant, not a case type. The app never stores, displays, or branches on *which* situation brought someone in — homelessness, reentry, veteran status, immigration, or relocation. This prevents stigma, keeps the data model uniform, and — critically for immigrants — ensures **shelter** can never become a de facto status registry that could be breached or subpoenaed. What matters is that the person is verified and building trust; the door they came through is not the app's business.

---

## 3. Core invariants (the code must always enforce these)

Hard rules. Write tests that fail loudly if any is violated.

- **INV-1 — No active caseworker, no function.** A beneficiary account is *dormant* unless it has an **active CaseLink** to a **verified** social worker — this holds for every beneficiary regardless of which population they came from. While dormant, all help/rating/coordination features are disabled. (Humane exception: the beneficiary may still view/export their own data and reach safety/crisis resources — being dropped by a caseworker must not lock someone out of their own information.)
- **INV-2 — Verification gates roles.** A social worker cannot act until the Operator has verified them. A beneficiary cannot transact until a verified social worker completes **in-person** verification and establishes a CaseLink.
- **INV-3 — Ratings are events, bounded, and accountable.** Ratings are integers in **[−1, +4]**, stored as **individual rating events** (not running tallies), each tied to a completed interaction (or, for inspections, to an asset). A user may only rate something they were a party to / authorized to inspect. **A −1 requires a justifying comment ≤ 500 words** — reject a −1 without one, and reject comments over the limit rather than truncating. Ratings are immutable once submitted but can be **flagged/disputed** and **contextualized**.
- **INV-4 — No averaging; harm stays visible.** Reputation is computed on read as a **per-level distribution + total count** (plus `first_rated_at`, `last_rated_at`, event count). **There is no mean, composite score, average-ranked leaderboard, or "overall average" anywhere** — not in a return value, report, or display. Every −1 remains individually visible and is surfaced via a **harm flag**. Acceptance bar: a search for averaging logic (`average`, `mean`, `system_average`) finds none in any read/report path (a comment explaining the ban is fine).
- **INV-5 — Least-visibility identity.** Beneficiary PII is never serialized into any payload, log, or response sent to a citizen client beyond the documented minimum field set. Default to pseudonyms.
- **INV-6 — No PII in logs/telemetry.** Application logs, error reports, and analytics never contain names, locations, message bodies, rating comments, or identity-document data.
- **INV-7 — Help-type verification gating.** No help offer is bookable unless the trustee is **ID-verified at registration**. **Lodging and transportation offers are additionally non-listable** until a caseworker has completed an **in-person inspection** and recorded a passing **inspection rating** (see §6.4, §7.2). A −1 inspection rating blocks listing.
- **INV-8 — Verify identity, don't retain the document.** Identity verification is recorded as a verification event (verifier, method, ID type, last digits, timestamp). Raw ID images/numbers are **not** stored by default; if a jurisdiction requires retention, they live encrypted, access-controlled, audited, and retention-limited — never in general application storage or logs.
- **INV-9 — Audit, don't expose.** Sensitive reads/writes append to an immutable audit log. Operator/admin access to interaction content and verification records is narrow, logged, and justified.
- **INV-10 — No entry-category or status tracking.** Beneficiary records carry **no** field for the population or situation that brought someone in, and **no** code path branches on it. Immigration or legal status is never stored. Every beneficiary is modeled and treated uniformly regardless of origin (see principle 9 and §11).

---

## 4. User roles & permissions

| Capability | Operator | Social Worker | Beneficiary | Citizen (Trustee) |
|---|---|---|---|---|
| Verify social workers | ✅ | — | — | — |
| Be verified | — | by Operator | in person, by Social Worker | ID at registry; **inspection** for lodging/transport |
| Establish/revoke a CaseLink | — | ✅ (their caseload) | — | — |
| Register a help offer | — | — | — | ✅ |
| Inspect & rate a lodging/vehicle | — | ✅ | — | present for inspection |
| Request / accept help | — | optionally on behalf | ✅ (if active CaseLink) | ✅ (accept requests) |
| Submit interaction LBTAS rating | — | — | ✅ (rates Trustee) | ✅ (rates Beneficiary) |
| View a beneficiary's distribution | — | ✅ (their caseload only) | ✅ (their own) | ❌ |
| Message beneficiary (goals/opportunities/expectations) | — | ✅ (their caseload) | receive + reply | — |
| See beneficiary legal identity/history | ❌ (minimized + audited) | ✅ (their caseload, minimal) | n/a | ❌ |

Notes:
- **Operator** is a platform-operations role, not a super-user with free access to people's lives. Keep its access narrow and audited (INV-9).
- A **Social Worker** only ever sees their **own caseload** — never the whole population.
- **Citizens** are always ID-verified at registration; lodging/transport providers are additionally inspected. Whether higher-risk providers also get a background/registry screen is an open policy decision (§15).

---

## 5. Domain concepts & glossary

- **Beneficiary** — a newcomer building a place in the local society; the user the app exists to serve. Modeled uniformly regardless of which situation brought them in (INV-10).
- **Citizen / Trustee** — a community member offering help. ("Trustee" is the LBTAS-side term; "Citizen" is the friendlier UI term — pick one for the UI and be consistent.)
- **Social Worker / Caseworker** — verified professional managing a caseload; also the verifier of beneficiaries and the inspector of lodgings/vehicles. Scope now spans homelessness, reentry, veteran transition, resettlement, and relocation.
- **Operator** — backend entity running the service and verifying social workers.
- **CaseLink** — the active relationship between a social worker and a beneficiary. Drives INV-1. States: `pending_in_person → active → ended`.
- **Help Offer** — a citizen's offer of a specific help type with availability/details.
- **Interaction (Exchange)** — a single instance of help being given, match to completion. The unit that gets a *bidirectional* rating.
- **Inspection Rating** — a *caseworker's* LBTAS rating of a lodging or vehicle, recorded at verification. Gates listing (INV-7); distinct from interaction ratings.
- **Help types** — `MEAL`, `TRANSPORTATION`, `LODGING`, `CLOTHING`, `MENTORSHIP`.
  - **Mentorship** = *help navigating systems and settling in*: paperwork, benefits applications, IDs, regulations, appointments, housing/employment/court/VA/immigration forms, plus local and cultural orientation and language navigation for those new to the area or country. A distinct, structured flow, not generic chat.
  - **Higher-risk types** (`LODGING`, `TRANSPORTATION`) put a beneficiary alone in a trustee's private space — they carry the inspection requirement.

---

## 6. The reputation system: LBTAS, adapted

shelter builds on the **Leveson-Based Trade Assessment Scale** (NTARI): a 6-point bidirectional scale derived from Nancy Leveson's system-safety work, where harm is a discrete event to surface, not smooth away. We **adapt the categories** to a human-help context, keep the scale's shape, and **inherit the no-averaging and −1-comment rules** from upstream.

### 6.1 The scale (adapted wording)

A **6-point scale from +4 down to −1**, with a deliberate asymmetry — four positive levels and only two non-positive ones, floor at **−1**. **Preserve this.** Do not extend the negative tail.

| Value | Label | Meaning in this context |
|---|---|---|
| **+4** | Exceptional | Went beyond the moment — anticipated the person's next need, opened a door, built lasting trust. |
| **+3** | No negative consequences | Thoughtful and safe; prevented problems; clearly exceeded a basic standard. |
| **+2** | Solid | Met the agreed help and the unspoken reasonable expectations around it. |
| **+1** | Promise kept | Did exactly what was agreed, no more, no less. |
| **0** | Went through the motions | Technically fulfilled, but with little care or engagement. |
| **−1** | Trust broken | Someone was harmed, exploited, treated with disrespect, or there was clear bad faith. Requires a comment ≤ 500 words. |

### 6.2 Reputation is a distribution (not a number that ranks people)

This is the central rule, inherited from upstream LBTAS and reinforced by §2.2.

- A reputation is the **count of ratings received at each level** (`-1, 0, +1, +2, +3, +4`) plus the **total**. Compute distributions **on read** from individual rating events; never store running tallies, and never compute a mean.
- **The total count is itself a signal**, and in this context a positive one: it reflects *how much trust a party has built over time*. A beneficiary with many `+2`/`+3` interactions accumulated over months is visibly demonstrating reintegration — exactly what we want to recognize. A trustee with a long, clean history of help given is a stronger match than a brand-new one with the same shape. Always report the count alongside the breakdown, plus `first_rated_at` / `last_rated_at` for a coarse "active since."
- **Harm is never diluted.** Because there is no average, a single −1 cannot be buried under praise. Every −1 stays individually visible and is raised in a **harm-flagged** list for the relevant caseworker, together with its mandatory comment. The caseworker's job is to *respond to surfaced harm* (support, mediation, re-inspection, or removal) — not to watch a score.
- Aggregation into a distribution is encouraged; collapsing it into a mean, composite, or leaderboard is forbidden (INV-4).

### 6.3 Categories (context-adapted — replace the e-commerce defaults)

Upstream ships `reliability / usability / performance / support`. **Do not use those.** Use direction-appropriate categories, kept short and config-driven (tunable with field feedback):

**Citizen → Beneficiary** (interaction, helper's side): `follow_through` (showed up / honored the arrangement), `respect` (treated helper and property with respect), `communication` (clear and honest).

**Beneficiary → Citizen** (interaction, where dignity is protected): `follow_through` (delivered the help as offered), `dignity` (respect, no condescension or strings attached), `safety` (the interaction felt safe).

### 6.4 The caseworker inspection rating (lodging & transportation)

When a caseworker inspects a lodging or vehicle (§7.2), they record an **inspection rating** on the same −1..+4 scale, using **facility-appropriate categories** — because the §6.1 wording is written for interactions, not rooms and cars:

- **Lodging:** `safety` (working locks, smoke detectors, clear exits, no obvious hazards), `condition` (clean, functional, genuinely private space), `suitability` (appropriate for the beneficiary's needs).
- **Transportation:** `safety` (vehicle condition, seatbelts), `legitimacy` (valid driver's license **and** current insurance/registration — confirm, don't assume), `suitability`.

Gating rules:
- A lodging or vehicle is **listable only if every inspection category is at or above a listing threshold** (recommend **≥ +1 "meets baseline"**; the exact bar is a §15 decision) **with no −1**.
- A **−1 inspection rating blocks listing**, requires the ≤500-word comment, and is harm-flagged.
- **Re-inspection triggers** (don't treat "verified once" as permanent): address/vehicle change, expiry of license/insurance, a complaint, a **−1 from an actual interaction** at that lodging/vehicle, or a time-based interval. A −1 from real use should suspend the listing pending re-inspection.
- Inspecting *with the trustee present* does **not** vet other occupants of a dwelling who will have access to the beneficiary — capture that in the inspection record and policy (§15).

### 6.5 Adaptation rules (summary)

- −1 (any direction) → mandatory comment ≤ 500 words, harm-flagged, caseworker notified. No auto-suspension of *people*; suspension applies to *lodging/vehicle listings*, decided by a human.
- Provide a **dispute/flag** path so an unfair rating can be contested and annotated — never deleted. A single bad interaction must never irreversibly define a beneficiary.
- Citizens see only their own ratings; beneficiaries see their own; caseworkers see their caseload's, with context. No cross-party exposure, no public ranking.

### 6.6 Reuse vs. reimplement

LBTAS has reference implementations in TypeScript, Go, Rust, and Python. If we go TS end-to-end, `lbtas.ts` is the natural fit — **but read §16 (AGPL) first**. If we reimplement rather than import, we must reproduce the **distribution-on-read model, the −1 comment rule, and harm-flagging**, not just the six levels — those behaviors *are* LBTAS.

---

## 7. Primary workflows

### 7.1 Social worker verification (Operator, backend)
1. Social worker requests access (agency, role, contact, any required credential).
2. Request enters an Operator review **queue**.
3. Operator verifies out-of-band (agency confirmation / licensure) and activates the account — across the expanded caseworker scope (reentry, homelessness, veteran transition, resettlement, relocation); see §15 on what counts as verifiable.
4. Only then can the social worker act (INV-2).

### 7.2 Trustee verification & onboarding (risk-tiered)
**All trustees, at registration:** provide **government-issued ID**, which the system **verifies and records as a verification event** (verifier/method/ID type/last digits/timestamp). The raw document is **not** retained by default (INV-8). This is the accountability layer behind every help type.

**Meals, clothing, mentorship:** ID verification at registration is sufficient to begin offering help.

**Lodging and transportation (additional, before any offer is listed):**
1. Trustee submits the offer; it stays **un-listable** (INV-7).
2. Trustee provides government ID **prior to the inspection meeting**.
3. A caseworker **visits and inspects the lodging / vehicle in person, with the trustee present**, using a structured checklist (works offline — see §8). For transport, confirm **license + insurance**, not just vehicle condition.
4. The caseworker records an **inspection rating** (§6.4). If it meets the listing threshold with no −1, the offer becomes listable; a −1 blocks it and is harm-flagged.
5. Listing is subject to **re-inspection triggers** (§6.4).

> Open policy question (§15): do lodging/transport providers also require a background/registry screen? ID confirms identity, not suitability.

### 7.3 Beneficiary onboarding (in person)
1. A **verified** social worker meets the beneficiary **in person**.
2. The worker initiates a CaseLink (`pending_in_person`) and completes in-person verification (e.g., a one-time code on the worker's device that the beneficiary enters — keep it offline-friendly and low-tech).
3. CaseLink becomes `active`; the beneficiary's app becomes functional (INV-1).
4. The beneficiary sets a display name / pseudonym and consents (transparently) to what's collected. The app records that they are a verified beneficiary — never which situation brought them in (INV-10).

### 7.4 Offering & receiving help
1. Citizen registers a **Help Offer** (type, details, availability, **coarse** location); lodging/transport must clear §7.2 first.
2. Match occurs (see §15 on who initiates).
3. Interaction states: `proposed → accepted → scheduled → completed` (plus `cancelled`, `no_show`).
4. After `completed`, both parties are prompted to rate (§6).

### 7.5 Rating
1. Each party rates the other on the direction-appropriate categories; −1 requires a comment ≤ 500 words.
2. Ratings persist immutably as events; distributions and harm flags are computed on read.
3. Either party can flag/dispute; disputes annotate, never delete.

### 7.6 Caseworker support loop (the steady state)
Once set up, the caseworker mostly:
- **Reviews** each beneficiary's distribution and any harm flags — never a ranked score.
- **Messages** beneficiaries with **goals, opportunities, requirements, and expectations** (typed: `GOAL`, `OPPORTUNITY`, `REQUIREMENT`, `GENERAL`).
- Acts on −1s and flags (support, mediation, lodging/vehicle re-inspection or suspension) and connects people to more help.

Keep this loop **lightweight** — usable in a few taps on a slow phone with a spotty connection.

---

## 8. Technical architecture & constraints

**Primary constraint:** must run well on entry-level Android — including **"Hot Pepper"-class and Android Go devices** (≈1–2 GB RAM, low storage, older Android/WebView, slow CPUs) — **and** on standard phones, **and** in a browser. Many users have **metered, intermittent data**. Caseworkers may also be **inspecting lodgings/vehicles in places with poor signal**, so field flows must work offline.

Design implications (treat as requirements):

- **Offline-first.** Core flows (view goals/messages, record that an interaction happened, draft a rating, run an **inspection checklist**, view your own data) must work offline and **sync opportunistically**. Use a local store (IndexedDB) + a sync layer with conflict handling. Prefer append-only event semantics for ratings/interactions/inspections; last-write-wins only where it can't cause harm.
- **Tiny footprint.** Every dependency must justify its bytes (budget below).
- **Low data usage.** Small JSON payloads, delta sync, no autoplay media, compress everything, cache aggressively, prefer system fonts + inline SVG icons.
- **Graceful degradation.** Assume old WebViews and missing APIs; feature-detect; never hard-crash because a modern API is absent.
- **SMS-friendly path (evaluate, §15).** For users who can't reliably reach data, consider an SMS fallback for critical caseworker→beneficiary messages. Decide early — it shapes the backend.

### Performance budget (target low-end Android Go on 3G)
- Initial JS **< 100 KB gzipped** for the core shell (hard ceiling ~150 KB); lazy-load the rest.
- First meaningful paint **< 3 s** on throttled "slow 3G."
- Usable on **1 GB RAM**; no runaway memory.
- Installable to home screen; no app-store download required.
- Every core flow — including the inspection checklist — has an **offline path**.

---

## 9. Recommended stack

Pragmatic defaults for a small operator: tiny bundles, one language, easy updates. Adjust per §15 and **record decisions here once made.**

- **Client: a PWA** (installable, offline via service worker, cross-platform, no app-store gatekeeping for updates).
  - **Framework:** prefer a *tiny* one — **Svelte/SvelteKit** or **Preact** over React, to hit the budget. If app-store presence or native APIs (push, SMS) are needed, wrap the PWA in a **Trusted Web Activity / Capacitor** shell rather than going fully native.
  - **Language:** TypeScript throughout. **Local store:** IndexedDB.
- **Backend: TypeScript (Node)** for code-sharing (and possible `lbtas.ts` reuse), **or Go** for a tiny memory footprint (`lbtas.go` exists). The backend is the production reputation surface and **system of record**; model it as an **event store** (individual rating events), computing distributions on read — mirroring upstream LBTAS. Pick one language and document it here.
- **Database:** start simple — **SQLite/libSQL (e.g., Turso)** or **managed Postgres (e.g., Supabase)** if you want built-in auth + row-level security to enforce caseload isolation at the DB layer.
- **Auth:** role-aware, modeling Operator-verification, in-person beneficiary verification, and trustee ID/inspection states explicitly. Enforce caseload isolation (INV-5) at both API and DB layers.

---

## 10. Data model (entities)

Keep it minimal (§2.4). Don't add sensitive fields without revisiting §2–3.

- **User** — `id`, `role` (`operator|social_worker|beneficiary|citizen`), `status`, `locale`, `created_at`.
- **SocialWorkerProfile** — `agency`, `verification_status`, `verified_by`, `verified_at`.
- **BeneficiaryProfile** — `display_name` (pseudonym), `active_case_link_id` (nullable → drives INV-1), `help_categories_needed` (which *help types* they need — not a population label), `consent_records`. **No history/diagnosis/legal-status field, and no field for which population brought them in (INV-10).**
- **CitizenProfile** — `display_name`, `offered_help_types`, `id_verification_id` (→ IdentityVerification), `verification_status`.
- **IdentityVerification** — `subject_user_id`, `method`, `id_type`, `id_last_digits`, `verified_by`, `verified_at`. **No raw document by default** (INV-8).
- **CaseLink** — `social_worker_id`, `beneficiary_id`, `status` (`pending_in_person|active|ended`), `in_person_verified_at`, `established_at`, `ended_at`.
- **HelpOffer** — `citizen_id`, `type`, `details`, `availability`, `coarse_location`, `status`, `asset_id?` (for lodging/transport), `listable` (bool, gated by INV-7).
- **InspectedAsset** — `citizen_id`, `kind` (`lodging|vehicle`), `inspection_status` (`pending|passed|blocked|needs_reinspection`), `last_inspected_at`, `reinspection_due`, `other_occupants_noted` (lodging), `license_insurance_confirmed` (vehicle).
- **Interaction** — `offer_id`, `citizen_id`, `beneficiary_id`, `type`, `status`, `scheduled_at`, `completed_at`.
- **RatingEvent** — `target_kind` (`beneficiary|citizen|asset`), `target_id`, `rater_id`, `direction` (`citizen_to_beneficiary|beneficiary_to_citizen|caseworker_inspection`), `category`, `value` (−1..+4), `comment` (**required iff value = −1, ≤ 500 words**), `interaction_id?`, `asset_id?`, `flag_status`, `created_at`. **Immutable.** Distributions/harm flags computed on read.
- **Message** — `from_social_worker_id`, `to_beneficiary_id`, `kind` (`GOAL|OPPORTUNITY|REQUIREMENT|GENERAL`), `body`, `created_at`, `read_at`.
- **ConsentRecord**, **VerificationRequest** (Operator queue), **AuditLogEntry** (append-only).

Reputation reads return, per target: `{ distribution: {-1..+4 counts}, total, first_rated_at, last_rated_at, harm_flagged[] }`. **Never** an average field.

---

## 11. Privacy, security & safety engineering

- **Immigration & legal status — a core-population risk.** **shelter** now serves immigrants, which can make it a target for enforcement data requests, subpoenas, and breaches. The defense is architectural: it stores **no** immigration or legal status and **no** entry-category (INV-10), and keeps identity data minimal (INV-8) — so there is little to hand over or leak that could expose anyone to enforcement. Treat any government or third-party request for user data as a serious, counsel-reviewed event, and design so that "we don't collect that" is the honest answer wherever possible.
- **Identity documents (INV-8).** Verify, don't retain. Record the verification event, not the scan. If law requires retention, store encrypted, access-controlled, audited, and retention-limited; never in general storage, payloads, or logs. ID confirms identity only — it is **not** a safety screen (see §15).
- **PII handling:** pseudonyms by default; minimal field sets per role (INV-5); never log PII or rating comments (INV-6); encrypt in transit and at rest; encrypt sensitive local data on-device where feasible.
- **Location:** **coarse only**, consent-based, ephemeral. shelter is **not** a tracking system — never store continuous location or movement history. Inspection records a lodging/vehicle's existence and condition, not the beneficiary's whereabouts.
- **Physical-meeting safety:** no default address sharing; suggest public meeting spots for first interactions; one-tap **report** on every interaction; fast escalation to the caseworker; lodging/transport gated behind inspection (§7.2) and re-inspection (§6.4).
- **Mandatory reporting / duty of care:** caseworkers may be mandated reporters (abuse/neglect — distinct from and never a channel for immigration enforcement), and the app facilitates housing/transport of vulnerable adults — real legal exposure for the Operator. Make reporting/escalation explicit and document the intended legal posture with counsel.
- **Retention & deletion (§2.6):** define windows; give beneficiaries export + deletion rights; age out ended CaseLinks and old interactions. The record should not be permanent.
- **Consent under power imbalance:** beneficiaries may feel they can't refuse. Make consent meaningful and revocable; never make core *safety* features contingent on consenting to *data sharing*.
- **Auth & isolation:** enforce caseload isolation at API **and** DB layers; least-privilege; audit Operator access (INV-9).

---

## 12. Coding conventions

- **TypeScript**, strict mode. No `any` in domain logic.
- **Dependencies are a liability.** Audit size and security before adding anything; the performance budget is a hard constraint.
- **Accessibility (WCAG 2.1 AA minimum):** large touch targets, high contrast, screen-reader labels, no color-only signaling, keyboard navigable.
- **Plain language + i18n from day one.** Short sentences, no jargon, icon support; externalize all strings; assume multilingual, low-literacy users (immigrants and relocators make multilingual support a first-class need, not an afterthought).
- **Privacy in code:** structured logging that *cannot* include PII, rating comments, or ID data; redact by default; never `console.log` a user/identity object.
- **Encode invariants as guards, not comments** — especially INV-3/4/7/8/10.
- **Comments explain *why*,** particularly around ethical/safety/no-averaging/no-categorization constraints, so future contributors don't "optimize them away."

---

## 13. Testing & quality

- **Invariant tests are mandatory**, especially:
  - **INV-1** (dormant without active CaseLink), **INV-7** (lodging/transport un-listable without passing inspection; un-bookable without ID verification).
  - **INV-3** (rating bounds; −1 requires ≤500-word comment; immutability; party/authorization-only).
  - **INV-4 (no averaging):** assert reads return distribution + total and **no average field**; keep a CI guard that fails if averaging logic (`average|mean|system_average`) appears in any read/report path; assert a single −1 surfaces in `harm_flagged` regardless of surrounding praise.
  - **INV-8** (no raw ID document persisted in the default path; verification event recorded).
  - **INV-10** (no population/entry-category field on beneficiaries; no branch on origin; no immigration/legal-status field anywhere).
  - **INV-5** (no PII leaks into citizen payloads).
- **LBTAS logic:** scale bounds, category sets, distribution computation, harm-flag ordering, dispute/flag behavior, inspection-rating gating thresholds.
- **Offline & sync:** airplane-mode flows (including the inspection checklist), reconnection sync, conflict resolution.
- **Low-end performance:** throttled-network + low-memory profiles in CI where possible; bundle size as a CI gate.
- **Accessibility:** automated a11y checks plus manual screen-reader passes.
- **PII/comment-in-logs check:** lint/test rule scanning for accidental PII or rating-comment logging.

---

## 14. What NOT to do

- ❌ Don't average ratings or compute any mean/composite/"trust score." Reputation is a distribution + total (INV-4).
- ❌ Don't bury a −1 — every one stays visible and harm-flagged, with its mandatory comment.
- ❌ Don't add any field or branch that records which population a beneficiary came from, or that stores immigration/legal status (INV-10).
- ❌ Don't store raw identity documents in the default path, or put ID data in payloads/logs.
- ❌ Don't list a lodging or vehicle that hasn't passed caseworker inspection, or treat "verified once" as permanent.
- ❌ Don't expose beneficiary legal identity, address, status category, or history to citizens.
- ❌ Don't build precise/continuous location tracking or movement history.
- ❌ Don't extend the negative tail below −1, or auto-suspend *people* on a −1 (suspend *listings*, by human decision).
- ❌ Don't let a beneficiary transact without an active CaseLink, or a social worker act before Operator verification.
- ❌ Don't add heavy dependencies or blow the performance budget.
- ❌ Don't store sensitive history (criminal/medical/immigration) — the app doesn't need it.

---

## 15. Open decisions to confirm with stakeholders

> **Canonical home:** these decisions now live in [`OPEN-QUESTIONS.md`](./OPEN-QUESTIONS.md) — the living open-questions deliverable required by Janus-Facing Architecture — with the constraints each resolution inherits (including two this section doesn't carry: deletion rights vs. the append-only record, and the witness cost floor). Record answers there.

Resolve early; several change the architecture or carry safety/legal weight.

1. **Background/registry screening for lodging & transport providers.** ID verification (now baked in) confirms identity, not suitability — it's reactive accountability. Do high-risk providers also need a background or sex-offender-registry check? Weigh the safety benefit against cost and the project's second-chance ethos; consider that vetting should scale with the *power the role holds over the beneficiary*.
2. **ID retention policy.** Default is verify-don't-retain (INV-8). Does your jurisdiction require retaining the document? If so, define encrypted storage, access controls, audit, and a retention window.
3. **Inspection thresholds & re-inspection cadence.** Confirm the listing threshold (recommended ≥ +1, no −1) and the time-based re-inspection interval, on top of event-driven triggers (§6.4).
4. **Other-occupant policy for lodging.** How to handle / vet other people living in a dwelling who'll have access to the beneficiary.
5. **Transport specifics.** Required proof of license + insurance; one-off rides vs. recurring arrangements; whether each is re-confirmed.
6. **Inspection-rating category wording.** Sign off on the lodging (safety/condition/suitability) and transport (safety/legitimacy/suitability) categories in §6.4.
7. **Citizen verification (lower-risk types).** Confirm ID-at-registration is sufficient for meals/clothing/mentorship, or whether mentorship (close, ongoing contact) warrants more.
8. **Verifiable-caseworker scope.** With the caseworker role now spanning homelessness, reentry, veteran transition, resettlement/immigration, and relocation, define which credentials the Operator will accept as a verifiable caseworker (§7.1) across all of these — the gatekeeper is only as sound as that bar.
9. **Graduation to giver.** Should a reintegrated beneficiary be able to become a trustee (offering help they once received)? It fits the mission and "designed to be exited," but needs its own verification and conflict-of-interest thinking.
10. **AGPL / licensing (§16).** Reuse LBTAS code (accept AGPL for the whole networked app) or reimplement the distribution model independently?
11. **Pure PWA vs. wrapped, and SMS fallback** for lowest-connectivity users.
12. **Jurisdiction & compliance scope** (HIPAA / VA rules / immigration confidentiality / state social-services confidentiality), **retention windows**, **match initiation** (beneficiary self-serve vs. caseworker-mediated vs. citizen-initiated), **languages**, and **hosting/budget**.

---

## 16. Licensing note (LBTAS / AGPL-3.0)

LBTAS is **AGPL-3.0**. Under AGPL, running *modified or incorporated* code as a **network service** generally requires making the **corresponding source available to users of that service**, under the same license.

- **Option A — Reuse LBTAS code** and comply with AGPL by releasing this app's source under AGPL. Clean if you intend to be open (plausible for an NTARI/cooperative project).
- **Option B — Reimplement independently.** The methodology — the six levels, the **distribution-on-read model**, the **−1 comment rule**, harm-flagging, plus our adapted categories — is a specification; a fresh implementation avoids importing AGPL code while honoring the approach. If you reimplement, you must reproduce the *behavior*, not just the scale.

Not legal advice — **confirm with counsel**, since this constrains how the whole app may be licensed and distributed. Either way, **credit NTARI / Jodson B. Graves and the LBTAS work** per their citation guidance.

---

*Keep this file current. When you make an architectural or policy decision, update the relevant section with a one-line rationale so the next contributor (human or agent) inherits the reasoning, not just the result.*
