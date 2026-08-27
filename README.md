# Eduardo Aranda Jr.

**Applied AI Systems Architect & Reliability Engineer**  
Agent reliability · authority & provenance · consequential workflow hardening · physical-world AI

I am building **ARANDA**, a private multi-service freight operating prototype shaped by about 15 years in trucking and an AI-assisted engineering workflow.

My strongest work is not “make a chatbot.” It is the control layer around software that can affect the real world: **who is acting, what they are allowed to do, what evidence is current, what remains unknown, what a human actually approved, what happened outside the system, and whether the tests can prove the difference.**

Most of the engineering repositories are private by design. This public profile summarizes selected evidence without exposing source code, credentials, live hostnames, customer data, or proprietary operating details.

**The contribution graph is intentionally public while the underlying ARANDA source repositories remain private.** The activity can be visible without turning proprietary code into a public portfolio. The case studies below are the public disclosure layer.

---

## Current truth

ARANDA is an **advanced distributed prototype undergoing production hardening**.

It has **not** carried a verified real freight load, has **not** generated dispatch revenue, and is **not** production-proven. I do not use test counts, deployments, or mutation scores as substitutes for that missing real-world proof.

As of the **2026-08-25 weekly estate freeze**, the private estate is stronger on **containment, assurance integrity, release provenance, and truthful verification** than it was at the 18 Aug snapshot. That progression is not an expansion of the freight architecture and is not Load #001.

What the freeze supports saying, and what it does not:

- Three Dispatch-Agent temporary containments on unauthenticated top-picks paths reached **merged + deployed + bounded production verification + closed/frozen**.
- Temporary route containment is **not** permanent authenticated HumanPrincipal / carrier authority.
- Bounded production verification of those routes is **not** production-proven business operation.
- Assurance now fences the reviewed ACTIVE mutation population by **exact identity**, not only by count.
- Engineering execution after that checkpoint was a **controlled pause** for an external execution-capacity constraint, not because the frozen containments failed.

The current engineering estate includes a 17-service/agent operating architecture spanning freight evaluation, driver state, orchestration, JARVIS, command/control, compliance, planning, audit/assurance, and supporting services.

The next important proof is not another dashboard or bigger test number. It is an authenticated, carrier-authorized, end-to-end freight workflow that survives contact with a real load.

### Why ARANDA is raising now

The pre-seed raise is not being framed as generic runway. The purpose is to convert a substantial prototype into controlled operating proof by funding the specific gaps that still separate the system from a real freight workflow:

- production identity and security boundaries,
- authorized freight/provider integration,
- booking and external-effect reconciliation,
- a controlled **Load #001**, and
- repeated real operating evidence after that first load.

The goal is to preserve the current fail-closed posture while moving from implementation and test evidence to authenticated, carrier-authorized, real-world execution.

---

## What I work on

### Consequential AI / agent workflows

I focus on systems where an AI or automation can do more than generate text — it can read business state, use tools, call APIs, change records, prepare decisions, or contribute to an external action.

The questions I care about are things like:

- **Service authentication ≠ human authority.** A valid internal credential does not prove a person authorized a business action.
- **Recommendation ≠ approval ≠ execution.** Each needs its own state and evidence.
- **UNKNOWN must stay UNKNOWN.** Missing, stale, invalid, or unreachable data must not quietly become a favorable default.
- **Request ≠ external effect.** A timeout does not prove nothing happened, and an HTTP 200 does not prove the intended business outcome occurred.
- **A test suite can be wrong about itself.** Harness integrity, fixtures, dependency boundaries, negative controls, and mutation validity are part of the thing being tested.
- **Identity must survive service boundaries.** Human-readable names, caller-supplied tenant IDs, and echoed request fields are not authoritative identity.
- **Temporary containment ≠ permanent authority.** Refusing an unauthenticated consequential route is a real boundary. It is not the same thing as proving who is allowed to use that route later.
- **A measurement that cannot prove its own population is not a score.** Numeric totals can hide substitution. Invalid instruments must stay invalid.

---

## Selected engineering cases

These are redacted summaries of defects found and hardened in the private ARANDA estate.

### 1. A service credential was being mistaken for human authority

A public conversational path could recognize a driver-creation request and then use JARVIS's internal service credential to write into the driver system. The downstream service correctly required a credential; the problem was that the public-facing service was **lending its own authority to an unauthenticated caller**.

The repair preserved the useful part — conversational intake, field extraction, missing-field analysis — while removing the consequential write. The system now prepares the action and states that authenticated human authority is still required.

**Pattern:** confused deputy · service auth vs human auth · preserve preparation while containing execution.

### 2. UNKNOWN values were improving decisions

Several freight-ranking paths converted missing operational facts into favorable known values: an ungraded broker could become a mid-tier broker, unmeasured deadhead could behave like zero miles, and missing regional information could select a favorable rate floor.

The repair carries absence explicitly, prevents unknown inputs from buying rank, and makes downstream UI/JARVIS language say when a fact was not measured rather than inventing a clean answer.

**Pattern:** favorable defaults · provenance · explicit uncertainty · grounded UI/model context.

### 3. An upstream outage looked like an empty freight board

A booked-load fetch did not raise on non-2xx responses. An upstream 500 with a JSON error body became an empty list, so the scan reported success with zero freight — indistinguishable from a legitimately quiet board.

The repaired path separates **“we looked and found nothing”** from **“we could not look.”**

**Pattern:** dependency failure semantics · false-success prevention · operational truth.

### 4. Green tests did not prove the real path worked

Cross-repository testing exposed cases where fixtures or stubs were richer than the real producer/consumer contract. In one important freight path, a large green suite did not establish that the actual endpoint and real package topology could execute correctly.

The response was not “add more tests” in the abstract. It was to make the real topology explicit, fail loudly when required sibling context is absent, and drive behavior through the actual boundary.

**Pattern:** contract testing · fixture realism · test topology · green-suite skepticism.

### 5. The mutation harness itself became part of the threat model

A mutation runner originally edited the canonical checkout and restored files in `finally`. A killed process proved that restoration-on-exit was not a safety boundary: the canonical source could be left mutated.

The harness was moved into disposable Git worktrees, given dirty-tree refusal, source fingerprints, mutation-match validation, and negative/self-check controls. Later work found mutation definitions that had silently stopped matching after refactors, reinforcing the rule that **a clean mutation report is meaningless if the mutations did not actually run.**

**Pattern:** semantic mutation testing · harness integrity · negative controls · reproducible evidence.

### 6. Website intake had to survive retries and concurrency honestly

A public carrier-intake path was hardened around durable event identity rather than carrier identity. Replays of the same submission return the same lead; conflicting content under the same event is refused; a database unique index arbitrates concurrent inserts; the original intake subject is stored immutably and versioned.

Notification work was deliberately ordered **after** the lead became durable so a Slack failure could never cost the underlying enquiry. Real PostgreSQL concurrency probes were used where SQLite could not prove the locking behavior.

**Pattern:** idempotency · immutable creation evidence · PostgreSQL concurrency · durable-before-notify ordering.

### 7. A received audit report was not the same thing as a verified finding

An audit-report endpoint accepted a submitted result and the receiving system stamped the audit agent's identity onto the stored row. The dashboard then presented the row as a pass/fail finding even though the receiving system had not proved that the named agent authored it or that an independent audit actually ran.

The repair tightened the submission boundary, changed the presentation to **received / reported pass / reported fail**, and kept those stored reports from silently becoming control or booking authority. A shared service credential improves the boundary, but it still does not prove independent authorship.

**Pattern:** provenance overclaim · attribution · audit-trail honesty · reporting vs verification.

### 8. Unauthenticated consequential routes were contained without calling that permanent authority

Unauthenticated freight-recommendation paths could accept caller-chosen identifiers and then read or mutate cached recommendation state. Auth middleware in the observed production posture was audit-only / call-through, so a missing credential was not the actual stop.

The accepted move was **temporary route containment**: keep the route registered, refuse at the handler boundary before the identifier is treated as a subject and before the database path runs, and verify the refusal with a bounded production probe. The parked pre-containment body remains available so assurance can prove what containment removed.

That is a real public-boundary improvement. It is not HumanPrincipal. It is not carrier-scoped authority. It is not proof that no database activity occurred unless that fact was separately observed.

**Pattern:** temporary containment · capability is not authority · bounded production verification · do not inflate the evidence.

### 9. Assurance scores were fenced by identity after a count proved too weak

A mutation catalog can keep the same numeric ACTIVE total after one reviewed mutation is deleted and another is added. A score that only checks cardinality cannot see that substitution.

The current instrument therefore carries an exact reviewed ACTIVE-ID inventory and treats instrument failure as `INVALID` rather than a favorable kill. A concurrent harness collision that shared a workspace lock was declared `VOID`, excluded from the record, and rerun sequentially.

**Pattern:** identity fence · VOID vs INVALID vs killed · do not let the measuring instrument grade itself.

---

## Evidence-backed skills

These are skills I can demonstrate from the private estate. They are not certifications and they are not a claim that ARANDA is production-proven.

**Authority and confused-deputy control**  
Separate service credentials from human/carrier authority. Preserve preparation while containing execution.

**Provenance and UNKNOWN preservation**  
Keep subject, source, freshness, and absence explicit. Do not let missing facts improve a rank, a dashboard, or a booking decision.

**External-effect honesty**  
Treat request, attempt, HTTP status, local commit, outside effect, and business outcome as different states.

**Temporary containment design**  
Refuse an unauthenticated consequential route at a named boundary without inventing a fake authorization model or deleting the route.

**Release provenance**  
Bind reviewed source identity, merge identity, and deployed identity separately. Do not collapse them into “it shipped.”

**Assurance-instrument integrity**  
Worktree isolation, dirty-tree refusal, exact mutation match, negative controls, identity-fenced ACTIVE inventories, and VOID/INVALID discipline.

**Cross-service contract skepticism**  
Prefer the real producer/consumer boundary over a richer fixture. Treat a green suite as a claim that still has to name its topology.

**Bounded production verification**  
Prove the exact observed response with the smallest honest probe. Do not convert one health check plus one refusal into operating proof.

**AI-assisted engineering with human decision ownership**  
Use Claude, ChatGPT, Grok, and other systems as specialized collaborators. Keep problem framing, invariants, acceptance, and consequential decisions human-owned.

**Physical-world / logistics workflow architecture**  
Map freight, fleet, and field operations onto identity, authority, evidence, approval, execution, reconciliation, and recovery.

---

## How I build

**Fail closed where the consequence requires proof.** Missing security or authority configuration is not permission.

**Keep observation separate from authority.** A system can observe, summarize, rank, warn, or prepare without automatically gaining permission to act.

**Bind approvals to the exact thing approved.** If material terms or the subject change, the old approval should not silently travel with them.

**Preserve provenance.** A value without a trustworthy source, subject, and observation context is not equivalent to the same number with evidence.

**Make failure states diagnostic.** `UNKNOWN`, `UNCONFIGURED`, `COULD_NOT_CHECK`, `REFUSED`, `CONTAINED`, and `EXTERNAL_EFFECT_UNKNOWN` are different operational states and should not collapse into one boolean.

**Attack the measuring instrument too.** Tests, mutation harnesses, mocks, fixtures, route inventories, and audit records can all create false confidence.

**Record what did not work.** My commit history is intentionally detailed because failed assumptions are part of the engineering record.

---

## How I use AI

AI is a major part of my engineering workflow, not something I hide. I use **Claude, ChatGPT, Grok, and other AI systems as specialized collaborators** across architecture, repository investigation, implementation, testing, review, research, contradiction-finding, and technical translation.

I do not treat model output as authority or proof. The working pattern is closer to:

`problem/domain reality → architecture and invariants → AI-assisted investigation/build → tests and adversarial evidence → independent challenge where warranted → human acceptance`

Different models are useful for different responsibilities. I may use one system to reason about architecture, another to inspect or implement against the repository, and an independent pass to challenge assumptions or evidence. For consequential decisions, agreement between models is not treated as correctness by itself.

My responsibility is to own the **problem framing, domain decomposition, architecture direction, invariants, acceptance criteria, evidence standard, and final decision about whether a result is supported.** AI can contribute heavily to the work without silently becoming the decision owner.

I describe ARANDA as a **ground-up, greenfield system built through an AI-assisted engineering workflow**. That does not mean every underlying pattern is novel or that every line was typed manually. It means the system was derived from the operating problem and built forward rather than copied from an existing freight-agent product and rebranded.

The detailed internal orchestration prompts, assurance procedures, unpublished failure taxonomies, and proprietary engineering methods remain private.

---

## Contract work

I am available for bounded technical engagements involving consequential AI/software workflows.

### Business Workflow / AI System Discovery

Start with the real business process, actors, constraints, state, failure modes, and consequences. Then determine what should belong to AI, deterministic software, tools, durable state, or explicit human authority before implementation begins.

### AI Workflow & Agent Systems Architecture

Design agentic workflows with explicit authority boundaries, durable state, bounded tool access, trustworthy evidence, and clear separation between recommendation, approval, and execution.

### AI System Implementation

Implement bounded workflow components where the desired behavior and authority model are sufficiently defined. The emphasis is on operational correctness and evidence, not adding an LLM where deterministic software would be more appropriate.

### AI Agent Reliability Review

Trace one workflow end-to-end: actors, credentials, authority, evidence, state, retries, external effects, failure modes, and test coverage.

Typical deliverables can include a system map, authority/evidence map, concrete findings, adversarial reproductions where appropriate, and a prioritized remediation plan.

### AI Permissions & Human-Control / Authority & Provenance Review

Useful when an agent can use tools or cause business actions. I look for places where service identity, caller input, model output, cached state, or human approval are being granted more authority than the evidence supports.

### Reliability Hardening Sprint

Implement and prove scoped remediations: authority boundaries, explicit unknown states, idempotency, concurrency controls, cross-service contracts, retry/reconciliation behavior, and adversarial regression tests.

### Test / Evaluation / Mutation Harness Integrity Review

A focused engagement on whether the assurance machinery can actually detect the failure classes it claims to cover: dead mutation targets, misleading fixtures, partial-suite execution, mock/real divergence, canonical-source contamination, negative-control gaps, identity-preserving catalog substitution, and false confidence created by the test harness itself.

### Logistics / Physical-World AI Architecture

I am especially useful in **logistics, transportation, fleets, field operations, and other physical-world systems** where software decisions meet changing real conditions and human authority cannot be treated as an afterthought.

Custom consulting can be scoped around a specific consequential workflow rather than forcing a company into a generic AI package.

---

## Engineering stack

`Python` · `FastAPI` · `PostgreSQL` · `SQLAlchemy` · `Node.js` · `React` · `Three.js` · `Railway` · `WebAuthn` · REST APIs · semantic mutation testing · contract testing · concurrency/idempotency testing

I use AI heavily in the engineering workflow. I do not present AI-generated code as proof by itself. My role is to own the problem framing, architecture, invariants, acceptance criteria, review, adversarial verification, and the decision about whether the resulting behavior is actually supported by evidence.

---

## What I do not claim

I am not presenting this work as penetration testing, SOC 2/ISO certification, formal verification, ML-model research, or a guarantee that software is defect-free.

I also do not claim ARANDA is production-proven. The private estate demonstrates substantial architecture, implementation, testing, failure analysis, and hardening work. Real operating proof is a separate milestone and I treat it that way.

---

## Contact

If you are building an AI/agent workflow where a bad assumption can turn into a real business action, I am interested in reviewing the consequential path.

**Eduardo Aranda Jr.**  
📧 eduardoarandajr@gmail.com
