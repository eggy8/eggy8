## Eduardo Aranda Jr

**I run a freight dispatch operation and built the multi-agent system that runs it.**

15 years in trucking and dispatch. 18 months building production AI systems.
Most people in this space have one or the other.

---

### What I've shipped

**A 16-agent orchestrated system, live in production.**

Dispatch, document parsing, invoicing, compliance tracking, driver communication,
and KPI rollup — running as coordinated agents against a shared Postgres backbone.

| | |
|---|---|
| Agents in production | **16** |
| Services deployed | **31** |
| Infrastructure cost | **~$20/month** |
| Busiest database, 7-day avg | **0.6% CPU** |
| Uptime | continuous since deployment |

Built solo. Operated daily. It runs a real business — mine.

---

### How I work

**Fail closed, not open.** From my orchestrator's auth layer:

> A missing env var must not silently turn PAUSE / OVERRIDE / KILL into open
> endpoints. **Unconfigured means refuse, not allow.**

**Record the wrong answers.** My commit messages document what didn't work and why,
not just what shipped. The next person reading it is usually me.

**Measure before you claim.** A handoff doc said eight of nine databases were idle.
I pulled seven days of metrics — 10,081 samples per instance — and it was nine of
ten. A snapshot of the present is not evidence about the past.

**Ship the diagnosis, not the guess.** Three brightness fixes in a row missed a
rendering bug because nobody checked the alpha math. Eight ghosted surfaces along
one sight line leave 1 - 0.9^8, about 57% haze. It was a transparency problem
wearing a lighting problem's clothes.

---

### Stack

`Python` · `FastAPI` · `PostgreSQL` · `Node.js` · `React` · `Three.js`
`Railway` · `WebAuthn` · `Anthropic API` · `Slack API` · `Meta Graph API`

---

### Available for contract work

Building agent systems for companies that run crews, vehicles, or field operations.
Bilingual (EN/ES). California, with US work authorization.

📫 **eddiearanda08@gmail.com**
