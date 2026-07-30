## Eduardo Aranda Jr

I run a freight dispatch operation and built the 16-agent system that runs it.

**15 years in trucking. 18 months building production multi-agent systems.**
Most people in this space have one or the other.

---

### Verification, because it's the part most people skip

| | |
|---|---|
| Automated checks, executed | **1,347** |
| Python tests | 462 |
| Assertions in the 3D ops view | 885, 0 failing |
| Hand-written mutations | **138 planted, 138 killed, 0 survived** |
| Mechanically generated mutations | **389** |
| **Mechanical kill rate** | **98.4%** — 379 killed / 6 survived / 4 invalid |

**A green test suite proves nothing until you've watched every assertion fail on
purpose.** So I break the decision logic one edit at a time and require the
suite to go red.

Then I did the same thing with a mutator that has no judgement — 389 edits it
generated, not 93 I chose:

| Suite | Kill rate |
|---|---|
| 282 tests — control | **86.2%** |
| 316 tests | **93.0%** |
| 362 tests — current | **98.4%** |

**The control run is the part that makes the rest mean anything.** Without a
baseline over the same mutant set, "we added tests and the number went up" isn't
attributable to anything. I re-ran the control from a rebuilt working copy with a
rewritten tool and it reproduced exactly — 332 / 53 / 4, all six per-file
survivor counts identical.

**The six survivors are closed, not outstanding.** Four are provable equivalents
— one checked exhaustively over the integers, two proven bit-identical in IEEE
double arithmetic. Two are observable but *arbitrary*, where pinning them would
freeze an unspecified choice into a contract. **I stopped at 98.4% on purpose.
Chasing 100% would have meant writing assertions that cannot fail**, and I'd
rather have a saturated score with a written floor than a perfect one that
invites the question of how many of those assertions are real.

**And the same tool scored 44.6% on a different codebase** — see below. A harness
that returns 98% everywhere isn't measuring anything.

---

### What's running

| | |
|---|---|
| Agents in production | 16 |
| Services deployed | 31 |
| Infrastructure cost | **~$20/month** |
| Busiest database | **0.6% CPU** across 10,081 measured samples |

Built solo. Operated daily. **It runs a real business, and the only fleet on it
is mine** — that's the honest scope.

---

### How I work

**Fail closed where it matters, fail open where it doesn't — and write down
which.** From my orchestrator's auth layer:

> *A missing env var must not silently turn PAUSE / OVERRIDE / KILL into open
> endpoints. Unconfigured means refuse, not allow.*

Two guards, one file, opposite defaults, because the consequences differ.

**Not knowing is its own state.** My compliance service reports `UNCONFIGURED`
rather than `COMPLIANT` when it has no obligations loaded. A system that
approves when it can't check is worse than one that refuses.

**Assume the measuring instrument is broken too.** My mutation harness once
reported 39 of 39 passing while silently testing an unmutated file. It now
self-checks with two canaries before it's allowed to report anything. I have
since caught the same class of bug **five** times in this codebase, including
three in tools I wrote myself, and every fix was a gate rather than more care.

**Prove equivalence, don't assume it.** When a mutation survives, "probably
fine" isn't an answer. Either there's an input that distinguishes the two
versions or there isn't, and finding out is arithmetic.

**Ship the diagnosis, not the guess.** Three brightness fixes in a row missed a
rendering bug because nobody checked the alpha math. Eight ghosted surfaces along
one sight line leave `1 - 0.9^8` — about 57% haze. **It was a transparency
problem wearing a lighting problem's clothes.**

**Record the wrong answers.** My commit messages document what didn't work and
why, not just what shipped. The next person reading them is usually me.

---

### And one project I killed, for two measured reasons

I built an autonomous trading system — 4,884 lines, 100 doctests, zero failures,
a deterministic risk kernel with no override path, enforced by a self-test that
greps its own source.

**Reason one: the predictor had no edge, and failed in the dangerous direction.**
50.5% directional accuracy against a 53.6% base rate, negative Brier skill score,
and **most wrong at its highest confidence** — so every mechanism designed to
press the advantage would have pressed the mistakes instead. **I recommended
against funding it. Zero capital deployed.**

**Reason two, found afterward: I pointed the mutation harness at the risk
kernel I'd called sound.** It scored **44.6%** — 129 killed of 289 mutants —
against 98.4% for the freight code, same tool, same week. **The maximum risk per
trade could be doubled without a single self-test failing.**

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

**What I haven't done:** worked inside a team's code review process, or run
anything under real load. Both are worth knowing before you hire me.

📧 eduardoarandajr@gmail.com

<sub>Figures verified by execution 30 Jul 2026. Every number above is the output of
a command, not an estimate. Full methodology, the control-run data and the
classification of all six surviving mutants are written up in detail — happy to
walk through them or share the repo.</sub>
