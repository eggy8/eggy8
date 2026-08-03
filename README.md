## Eduardo Aranda Jr

I built a 17-agent freight dispatch system. It is deployed, it runs, and **it has
never carried a load.**

**15 years in trucking. 18 months building multi-agent systems.**
Most people in this space have one or the other.

---

### The scope, before any of the numbers

Everything below is real and measured. None of it has survived contact with a
paying customer.

| | |
|---|---|
| Completed loads, ever | **0** |
| Revenue | **$0** |
| Drivers in the database | 3, all test records |
| Settled invoices, rate history, lane performance | none — those tables are empty |

The software works. The business has not started. Every capability that would
need completed loads to demonstrate — margin analysis, lane pricing, utilisation
— is built and waiting for the first real load, not operating today.

I lead with this because the rest of the page is a set of verification claims,
and a verification claim from someone who hides the denominator is worth nothing.

---

### Verification, because it's the part most people skip

Every figure in this table was re-measured **3 Aug 2026** by running the command
named beside it. Figures elsewhere on the page that were not are marked as such.

| | Measured | How |
|---|---|---|
| Python tests, passing | **1,749 / 1,749** | `pytest` across 15 repos |
| Node assertions | **2,532** | 3 repos, `npm test` |
| — of those, the 3D operations view | <!--counts:dungeon.short-->2,072, 0 failing<!--/counts:dungeon.short--> | `npm run counts` |
| **Automated checks, executed** | **4,281** | the two rows above, summed |
| Hand-written mutations, **all killed** | **772** | `mutate.py` / `mutate.mjs` |
| Repositories carrying tests | **18 of 21** | — |

**Yesterday that read 919 tests across 5 repos, 144 mutations, and 6 of 21
repositories.** Fifteen repos had no tests at all. That is the more honest
version of the old number: a fleet-wide claim resting on the six repositories
that happened to have a suite.

The Python figure is 595 (dispatch policy + API) + 194 (orchestrator) + 150
(driver tracker) + 135 (audit) + 105 (driver backbone) + 90 (KPI) + 76 (JARVIS)
+ 72 (command proxy) + 72 (traffic) + 64 (travel) + 48 (security) + 45 (founders)
+ 44 (compliance) + 33 (growth) + 26 (pre-destination). I list the split because
the single number is the part that goes stale quietly.

**A green test suite proves nothing until you've watched every assertion fail on
purpose.** So I break the decision logic one edit at a time and require the suite
to go red. **772 such mutations are planted across 15 repositories, and every one
of them was run today: 772 killed, 0 survived.**

**Two of those repositories taught me that a clean mutation score can be a lie of
omission.** The harness in each reported a full sweep — and neither had ever
opened the most consequential file it shipped:

| Module | What it decides | Mutations before | After |
|---|---|---|---|
| `orchestrator/policy/control.py` | whether the fleet is halted | **0** | 10, all killed |
| `dispatch-agent/policy/verification.py` | whether a load may be **booked** | **0** | 9, all killed |

`control.py` had 51 tests and a docstring crediting it with "45 mutants killed" —
a repo-wide figure from a run that never touched the file. `verification.py` holds
`may_commit()`, the single question asked before freight is booked, and had 20
tests with nothing proving any of them could fail. **A green score says nothing
about the files the harness never opened**, and both were found by counting rather
than by reading.

Then I did the same thing with a mutator that has no judgement — on 30 Jul it
generated 389 edits, not the 93 I chose:

| Suite | Mutants | Kill rate |
|---|---|---|
| 282 tests — control, 30 Jul | 389 | **86.2%** |
| 316 tests — 30 Jul | 389 | **93.0%** |
| 362 tests — 30 Jul | 389 | **98.4%** |
| 592 tests — re-run 2 Aug | 426 | **97.7%** |

**The last row is not a decline, it is a different denominator.** `policy/` gained
`verification.py` between the runs, so the 2 Aug run scores a larger mutant set:
426 generated, 416 killed, 10 survived, 0 invalid. I ran it today rather than
carry the 98.4% forward onto a directory it was never measured against.

**The control run is the part that makes the rest mean anything.** Without a
baseline over the same mutant set, "we added tests and the number went up" isn't
attributable to anything. I re-ran the control from a rebuilt working copy with a
rewritten tool and it reproduced exactly — 332 / 53 / 4, all six per-file
survivor counts identical.

**Those six survivors are closed, not outstanding.** Four are provable
equivalents — one checked exhaustively over the integers, two proven bit-identical
in IEEE double arithmetic. Two are observable but *arbitrary*, where pinning them
would freeze an unspecified choice into a contract. **I stopped at 98.4% on
purpose. Chasing 100% would have meant writing assertions that cannot fail**, and
I'd rather have a saturated score with a written floor than a perfect one that
invites the question of how many of those assertions are real.

**Four more are open, and "closed" had a date on it.** Today's run left ten
survivors. Six sit on lines `git blame` dates to 28–29 Jul, so they were in scope
when the six above were classified. The other four are not — and I have neither
classified nor closed them:

| Surviving mutant | What it does |
|---|---|
| `economics.py:252` `return 0.15` → `0.3` | the constant doubles, nothing fails |
| `verification.py:113` `[:120]` → `[:121]` | error text truncated one character later |
| `verification.py:113` `[:120]` → `[:0]` | the diagnostic text disappears entirely |
| `verification.py:152` `or` → `and` | a fallback becomes a conjunction |

`git blame` dates `economics.py:252` to 1 Aug and both `verification.py` lines to
30 Jul, the day that file was added. **"Zero outstanding" was true of a six-file
`policy/`. It is not true of the eight-file one, and I would rather say that than
re-print 98.4%.**

**Still open, and deliberately not marked closed.** `verification.py` now carries
nine hand-written mutations and all nine die — but those four survivors came from
the *generated* mutator, which I have **not** re-run since 2 Aug. Two different
tools, two different mutant sets. Killing the ones I chose says nothing about the
ones a generator finds, and folding the two together to reach a rounder number is
the exact move this page exists to refuse.

*(The three 30 Jul rows and the survivor classification are from that run and are
not re-derived here. The 2 Aug row I ran myself:
`automut.py --repo dispatch-agent --target policy/ --cmd "python -m pytest -q"`.
A fleet audit on 1 Aug measured the same directory at ~97.9% over ~422 mutants.)*

---

### What the `policy/` kill rate is not

Those numbers — 98.4% on 30 Jul, 97.7% today — cover `policy/`, and on 30 Jul
`policy/` was **six files.** It is **eight files today**
(`ls dispatch-agent/policy/*.py`), seven of which generate mutants:
`verification.py` arrived on 30 Jul in commit `c6745d7`, and `__init__.py`
generates none. The repository containing them scored **14.2%** across all 3,303
of its mutants in the 1 Aug fleet audit.

Every row below is from that 1 Aug audit and none of it is re-measured here. The
denominators have already moved: the same generator emits **3,326** whole-repo
mutants and **426** in `policy/` today, and **207** in the orchestrator's
`policy/`, so treat the mutant counts as the audit's, not this morning's.

| Scope | Mutants (1 Aug) | Kill rate (1 Aug) |
|---|---|---|
| `policy/` — HOS, compliance, rate floors, retry | ~422 | **~97.9%** |
| Orchestrator `policy/` — arbitration, load identity | 203 | **83.3%** |
| Decision modules — load scoring, triangulation | 884 | **4–5%** |
| API surface — request in, answer out | 2,179 | **0.6–14.7%** |
| **Whole repo** | **3,303** | **14.2%** |

Both numbers are correctly measured; they have different denominators. **A
high-nineties kill rate on the decision layer and 14.2% on the repository
containing it are not contradictory findings, and holding both is the point.**

**The judgement this system makes is well-guarded. The code that carries that
judgement to a driver is not.** The cause is mechanical rather than cultural:
policy modules are pure functions, trivially testable, and were tested well. The
perimeter files import database drivers and HTTP clients at module load, those
imports fail under test, so **nothing runs that code at all** — it is read as
text. The pattern reproduced independently in three repositories, which is what
makes it structural. The fix is dependency stubs, not diligence.

Fleet-wide, the 1 Aug audit measured **23,766 mutants across 19 repositories**, up
from 389. I have not re-generated that fleet figure today, and dispatch-agent
alone has grown by 23 mutants since, so read it as a 1 Aug measurement. I would
rather publish the 14.2% than let the `policy/` figure stand in for it.

---

### What's running

| | Measured |
|---|---|
| Agents in the registry | **17** |
| Railway services | **31**, across 17 projects |
| — of those, application services | 20 |
| — of those, database services | 11 — ten Postgres and one MongoDB |
| Infrastructure cost | ~$20/month |

**Nine of the ten Postgres instances are idle** — every live database variable in
the fleet points at one shared instance, and the rest measured zero network
traffic over 48 hours. "31 services" is an accurate count and a misleading
impression, so here is the breakdown instead.

"Agent is ACTIVE" in my own orchestrator means one thing only: a 30-second probe
got HTTP 200 from `/health`. It does not mean the agent is doing useful work. One
of the seventeen is ACTIVE and correctly reporting that it is unconfigured —
checked again today: it answers `/health` with `ok` and its own status endpoint
with `UNCONFIGURED`, `tracked: 0`.

*The service, project and database counts above were re-listed from the Railway
API today. Cost and the 0.6% database CPU figure are carried forward from 30 Jul
and not re-measured here.*

---

### How I work

**Fail closed where it matters, fail open where it doesn't — and write down
which.** From my orchestrator's auth layer:

> *A missing env var must not silently turn PAUSE / OVERRIDE / KILL into open
> endpoints. Unconfigured means refuse, not allow.*

Two guards, one file, opposite defaults, because the consequences differ. The
read endpoints keep working half-configured; command dispatch returns 503.

**Not knowing is its own state.** My compliance service reports `UNCONFIGURED`
rather than `COMPLIANT` when it has no obligations loaded — verified live today,
`tracked: 0`. Its obligations table is **deliberately empty and must not be
filled from a model's memory**: what a dispatch operation owes differs depending
on whether the entity is a broker, a dispatch service or a motor carrier, and a
plausible-looking compliance calendar fails silently and confidently. **A
dashboard reading COMPLIANT about a filing nobody made is worse than an empty
room.** The list comes from me or from FMCSA primary sources, or it doesn't exist.

**Assume the measuring instrument is broken too.** My mutation harness once
reported 39 of 39 passing while silently testing an unmutated file. It now
self-checks with two canaries before it's allowed to report anything, restores
every touched file from an in-memory copy, and re-hashes them all before it will
exit 0. I have since caught the same class of bug **five** times in this
codebase, including three in tools I wrote myself, and every fix was a gate
rather than more care.

**The same discipline applies to the numbers on this page.** The assertion count
above sits inside a generated marker, written by a script that refuses to write
anything at all if a single verification script fails. It also refuses to fill
the cross-repo total, because it can only measure one repo and writing a
repo-local number into a field labelled cross-repo is the exact category error it
exists to stop.

**Prove equivalence, don't assume it.** When a mutation survives, "probably fine"
isn't an answer. Either there's an input that distinguishes the two versions or
there isn't, and finding out is arithmetic.

**Uncertainty resolved favourably is the defect I keep finding.** An audit found
nine instances of one shape of it in this fleet: a fallback of `or 11.0` for
unknown hours-of-service time, appearing four times, so **an unknown driver
silently became a legal driver.** A downstream agent read an authentication
failure as "this driver has a full 11 hours available." A safety check that fails
open is worse than no safety check, because it also produces confidence. Fixed
and deployed.

**Ship the diagnosis, not the guess.** Three brightness fixes in a row missed a
rendering bug because nobody checked the alpha math. Eight ghosted surfaces along
one sight line leave `1 - 0.9^8` — about 57% haze. **It was a transparency
problem wearing a lighting problem's clothes.**

**Record the wrong answers.** My commit messages document what didn't work and
why, not just what shipped. The next person reading them is usually me.

---

### And one project I killed, for two measured reasons

I built an autonomous trading system — a deterministic risk kernel with no
override path, enforced by a self-test that greps its own source.

**Reason one: the predictor had no edge, and failed in the dangerous direction.**
50.5% directional accuracy against a 53.6% base rate, negative Brier skill score,
and **most wrong at its highest confidence** — so every mechanism designed to
press the advantage would have pressed the mistakes instead. **I recommended
against funding it. Zero capital deployed.**

**Reason two, found afterward: I pointed the mutation harness at the risk kernel
I'd called sound.** It scored **44.6%** — against the freight decision layer's
98.4% that same week, same tool. **The maximum risk per trade could be doubled
without a single self-test failing.**

That figure has since been reproduced independently. The original tool was lost
when a temp directory cleared; a rebuilt tool landed at **44.1%** on the same
file. Half a percentage point apart means the number was a property of the code,
not of the instrument.

**44.6% is the useful number.** A tool that returns 98% everywhere isn't
measuring anything; the spread is what makes both figures mean something. And
nobody audits a dead project — there was no funding left to protect. I ran it
because I'd written "sound risk kernel" and hadn't earned it.

Most portfolios describe systems that shipped. Being able to describe one you
correctly stopped — and then keep measuring it — is rarer and, I'd argue, more
useful.

---

### Stack

`Python` `FastAPI` `PostgreSQL` `Node.js` `React` `Three.js` `Railway`
`WebAuthn` `Anthropic API` `Slack API` `Stripe` `Twilio`

---

### Available for contract work

Agent systems for companies that run crews, vehicles or field operations. I'm
most useful where the domain is operational and the failure modes are physical.

**What I haven't done:** worked inside a team's code review process, run anything
under real load, or operated this system with a paying customer on it. The API
surface of my own fleet is thinly tested and I have said so above rather than
waiting to be asked. All three are worth knowing before you hire me.

📧 eduardoarandajr@gmail.com

<sub>Test, assertion, mutation and agent figures re-measured by execution
<!--counts:dungeon.date-->3 Aug 2026<!--/counts:dungeon.date--> — every suite and
every hand-written mutation harness in the fleet was run, not sampled: 1,749
Python tests, 2,532 Node assertions, 772 mutations killed with none surviving.
The 426-mutant generated `policy/` run reported above is from 2 Aug and was NOT
re-run. Figures explicitly marked as
carried forward — cost, database CPU, the three 30 Jul kill rates and the
classification of the six survivors from that run, and the 1 Aug whole-repo and
fleet-wide mutant counts — were not re-run. No live hostnames, deployment project
names or internal service inventory appear on this page, deliberately. Full
methodology, the control-run data and the classification of the six 30 Jul
survivors are written up in detail — happy to walk through them. The four
survivors found on 2 Aug are not classified yet, and are listed above rather than
folded into that six.</sub>
