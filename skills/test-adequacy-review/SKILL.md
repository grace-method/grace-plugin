---
name: test-adequacy-review
description: >
  Risk-anchored test-adequacy gate. Runs after every non-trivial
  implementation, not only when asked. An isolated same-vendor reviewer and a
  cross-vendor reviewer audit the change set's testing against the project's
  risk register; the builder fixes register-coverage gaps autonomously and the
  loop repeats until both reviewers agree the register is covered, escalating
  to the human only on defined triggers. New exposures the reviewers discover
  become register proposals for the human to triage in one batch, never a
  stream of interruptions. Also use when the user asks for a test adequacy
  review or a hostile review of the tests.
---

# Test-Adequacy Review

A blunt review of whether the current change is adequately tested — where
"adequate" is defined against the project's risk register, not against
everything a skeptic can imagine. It applies the enforcement-point rule from
`test-discipline.md`, hunts for behaviors wired through a real boundary but
never exercised through it, and returns a verdict a human can act on without
re-deriving it.

**This is a completion gate, not an optional review.** Run it whether or not
the human asks. It applies at medium and large change scale, and at small
scale whenever any acceptance criterion is feature-level — more than one
enforcement point, or any "the user sees X." Trivial changes are exempt. The
scale floor is deliberate: the builder counts the enforcement points, and the
builder is the party being checked, so under-counting must not be able to
skip the gate on substantial work. Do not report the build done until the
gate closes or escalates.

## The risk register anchors the verdict

The unbounded version of this gate has been run, and it does not terminate.
A capable reviewer instructed to be a cynic always has a deeper assertion
available — existence but not completeness, the name but not the path — and
its marginal cost per finding is near zero, so it manufactures demand
indefinitely and every finding lands on the human's desk. That is the
pathology risk-based testing was invented for, rebuilt with a tireless
reviewer. The fix is structural: separate risk *identification* (unbounded,
human-gated) from coverage *judgment* (bounded, autonomous).

**Before Pass 1, the builder assembles the register slice**: every risk,
invariant, requirement, and named problem from the project's analysis and
design artifacts that the change set's behaviors touch. Include it verbatim
in both reviewers' prompts, and record which artifact versions it came from
in the log. The reviewers may also read the analysis and design documents
directly — the register is an anchor, not a blindfold.

**Both passes get the identical slice, byte for byte.** Assemble it once, save
it as a file, and feed that same file to every reviewer. Two passes given
different definitions of "adequate" produce verdicts that cannot be compared,
which is the one thing a two-pass gate exists to make them. This is easy to
get wrong when the prompts are built separately: a component listed
below-the-line for Pass 1 and omitted for Pass 2 will be a GAP for one and
not the other, and the disagreement will look like reviewer insight rather
than builder error.

**The slice must carry the human's accepted deferrals**, in their own
section, marked as decisions rather than oversights. Record what was
deferred, when it was accepted, why, and what pins the repayment. A reviewer
cannot distinguish a deliberate deferral from an unnoticed gap, so anything
accepted but unrecorded gets re-raised **every round, forever** — burning
budget on a question the human already answered. When the human accepts a
gap, writing it into the slice is part of accepting it.

**The line.** Above the line: documented invariants, must-level requirements,
named problems, and register risks the project scores high. Below: everything
else. The human owns the line's placement; the project's own artifacts *are*
the default placement, so in the normal case no per-gate ceremony is needed.

**If the project has no register**, the gate still runs, unanchored — but it
must say so in the verdict and the log, escalate to the human at the first
INADEQUATE rather than looping, and recommend bootstrapping a register (the
`risk-management` skill) before the next gate. Unanchored is the degraded
mode; it is how the non-terminating version behaved.

## Finding taxonomy

Every finding in every verdict is classified into exactly one lane. The lane
decides who acts, and when.

| Lane | Definition | Effect |
| --- | --- | --- |
| **GAP** | An above-the-line register item uncovered — or covered only at the wrong layer — at an enforcement point the change set owns | **Blocks.** The builder fixes it autonomously and the loop repeats |
| **PROPOSAL** | An exposure not in the register: stated with suggested likelihood and impact | Never blocks. **Filed in the tracking artifact** and cited by identifier in the closure report, batched |
| **NOTE** | A deepening of an assertion on already-covered behavior; mutation-hardness against future edits; style | Never blocks. Recorded |
| **DEFECT** | A product bug discovered incidentally | Reported loudly and routed through change-assessment; does not block the *testing* verdict |

**ADEQUATE means zero GAPs.** Notes and proposals do not dirty a verdict —
"adequate, with notes and proposals" is the normal good outcome, and a
reviewer that finds no GAPs says so briefly instead of hunting for a reason
to say INADEQUATE.

Two classification rules that keep the autonomous lane finite:

- **Scoring is coarse.** Likelihood is one bit: *operationally reachable* —
  the failure can arrive through an input, a data condition, an operator
  action, an environment change, or a step of the documented operating cycle
  — versus *edit-only*, reachable solely through a future hand-edit to a
  source file. Impact is one bit: traces to a documented consequence or does
  not. No matrices, no numbers; false precision from a model is still false.
- **Edit-only exposures are never GAPs.** They are proposals or notes. The
  edit-only family is infinite by construction — every assertion at every
  depth has a future edit that would evade it — so it cannot be allowed to
  block a loop.

  That rule is absolute on purpose, and it is derived, not arbitrary. **Do
  not soften it into an exception clause** ("never a GAP where a write-path
  control exists…") — an exception is a seam, and the regress re-enters
  through it: the reviewer argues no control exists here, so its roster
  demand blocks after all. The derivation that makes the absolute safe: a
  file that changes as part of normal work makes those edits *operational*
  — invariants that must survive them are operationally reachable and can
  be GAPs on their own merits. A file that should never change can always
  be given a write-path control. The two cases exhaust the category, and
  neither needs a blocking test. Treatments, for the human at triage:

  | Treatment | Forms |
  | --- | --- |
  | Eliminate | OS immutable flag (`chflags uchg`) with a freeze ceremony; protected paths / CODEOWNERS |
  | Cheap-detect | a single pinned-hash check — one line, no content restated, updated deliberately on legitimate change |
  | Accept | version control plus review discipline |

  Choosing among these is line-placement work: the human's, at triage, not
  the builder's mid-loop — which is also why the valve excludes edit-only.
  A test that restates the protected content forever is almost never the
  answer; it is a second copy of the data, drifting on its own schedule.

**Scope every finding to the change set.** A behavior the change did not
build cannot be under-tested by it. A specified-but-unbuilt component is a
traceability matter — hand it to release planning as an end-note, never a
GAP. The in-scope form of that concern is valid and is a GAP when it applies:
a test *in the change set* whose name, docstring, or comment claims coverage
its assertions do not provide.

## The loop

The gate is a loop designed to run without the human until it either closes
or hits a named escalation trigger.

```
assemble register slice
  → Pass 1 (isolated same-vendor) → fix GAPs → repeat until zero GAPs (≤ 3 cycles)
  → Pass 2 (cross-vendor, fresh process) → zero GAPs?
       yes → UNANIMITY → close: write log, deliver closure report
       no  → fix GAPs → any change invalidates both passes → back to Pass 1
  (≤ 6 Pass-2 launches; triggers below can escalate at any point)
```

**Pass 1 — isolated same-vendor reviewer.** A subagent with **no visibility
into the build conversation**, given the rubric, the register slice, and the
change-set description in its prompt. Not the builder auditing itself in its
own session: a session-resident audit inherits every rationalization made
while writing the code, and "I already considered that" is not available to
a fresh context. Same vendor, so it still shares training-level blind spots
— that is what Pass 2 is for — but it is cheap, so cycle it until it reports
zero GAPs before spending a Pass 2. Every shallow gap that reaches Pass 2
wastes cross-vendor attention on findings that did not need different eyes;
cycled to zero, Pass 2's findings are by construction things a same-vendor
reviewer could not see.

**Pass 1 is bounded too: three fix-cycles.** Still reporting GAPs on the
fourth is not slowness, it is a symptom — the change set is gap-rich beyond
what in-place remediation should absorb, the register slice is wrong, or the
builder's fixes are under-shooting — and the human should see that before
cross-vendor budget is spent on it. With this cap, every loop in the gate
has a budget and a defined exit to a human: Pass 1 at 3, Pass 2 at 6,
disputes at 2 (1 for validity). No unbounded loop survives anywhere in the
design.

These loop economics — the cheap pass cycled to zero before the expensive
pass spends, fixes assumed additive — are tuned for **test adequacy of
implemented code**, where remediation adds tests beside existing ones and
re-runs are cheap. An adequacy review for analysis, design, or architecture
would tune differently: there, findings reshape the artifact itself, and
early diverse eyes may be worth more than budget protection. Recorded so the
tradeoff is inherited deliberately, not by copy-paste (decided 2026-08-15).

**Pass 2 — cross-vendor reviewer.** The authoritative pass. Mechanics in the
next section.

**Unanimity** — Pass 1 and Pass 2 both report zero GAPs for the same
unchanged worktree — closes the gate. Any code or test change invalidates
both passes; restart the pair.

**Autonomous remediation.** For each GAP: confirm it against the code (the
reviewer is blunt by design and sometimes wrong — a disagreement is a
trigger, below), reproduce it with a failing test, implement the smallest
in-scope fix, verify the new test red-then-green (by mutation where the
green would otherwise be untrusted), run the full suites. No human approval
needed inside the loop for fixes that are in-scope, non-destructive, and
demand no product decision.

**Verify the verification.** A wrong confirmation method dismisses a correct
finding, and the builder is the party with a motive to dismiss — every
confirmed finding is more work. So when a check contradicts the reviewer,
suspect the check first. Real example: a reviewer reported an export held 18
rows where the file claimed 17; `wc -l` said 18 total lines, which read as 17
data rows after the header and looked like a clean refutation. The file had no
trailing newline, so `wc -l` undercounted by one — a proper parse found 18 data
rows and the reviewer was right. Prefer a parser over a line count, an
independent recomputation over a spot check, and state the method you used
when you record the outcome.

**Mutation verification can go green for the wrong reason.** Cached bytecode,
a stale build artifact, or a test runner reusing a compiled module can mean
the code you *think* you mutated never ran. That produces a false green:
either the mutation appears uncaught (and you over-engineer a test that was
already fine) or a restore appears not to take (and you chase a phantom).
Clear caches between mutation and run — for pytest, remove `__pycache__` and
`.pytest_cache` — and if a restore seems not to work, check for stale
artifacts before concluding anything about the code.

**The provisional valve.** When a reviewer's PROPOSAL is severe, do not let
it sit in a queue: the builder may provisionally score it above the line and
fix it in the same loop — but only when all four conditions hold: it is
operationally reachable; it traces to a documented consequence; the fix is
in-scope; the fix is non-destructive. Edit-only proposals never qualify.
**At most two valve uses per gate; a third escalates** — a change set
generating that many severe surprises has outgrown autonomous remediation.
Valve uses are listed **first** in the closure report, each showing its four
conditions as evidence, so ratification is a real check rather than a
rubber stamp. If the human demotes one, the added test is **removed by
default** — over-scoring must produce visible reversals, not quiet
accretion. The valve exists because severe discoveries are
disproportionately proposals: the register captures what someone already
thought of, and the reviewer's whole value is finding what nobody did.

**The closure report.** On unanimity, deliver to the human in one message:
both verdicts with provenance, the residual-risk statement (below-line items
and accepted notes), the batched PROPOSAL queue with suggested scores, any
DEFECTs routed, and anything fixed under the provisional valve awaiting
ratification. One sitting, not a stream.

**File before you report, and cite what you filed.** Every proposal, routed
defect, and accepted deferral goes into the project's tracking artifact — the
backlog, the debt register, the risk register — *before* the closure report is
written, and the report names each one by its identifier. A verdict file and a
log entry are records of what a reviewer said; they are not a queue, and nobody
opens them again. Writing "queued for triage" into a message queues nothing:
the message scrolls away, and the finding is rediscovered months later by a
reviewer who cannot tell it was ever seen. This has happened repeatedly and it
is the reason the register slice must carry accepted deferrals — same defect,
one stage earlier. If you cannot cite an identifier, you have not filed it, and
the closure report is not ready.

## Escalation triggers — when the loop yields to the human

Escalate — stop the loop, present state, wait — on any of these. Nothing
else interrupts.

1. **Budget exhausted.** Six Pass-2 launches without unanimity — or an
   isolated Pass 1 still reporting GAPs after three fix-cycles, which is the
   same symptom earlier and cheaper. Either way, stop and write the
   convergence report: GAP counts per round, whether findings are new
   territory or deepenings of prior rounds, reflections on why the gate is
   not converging, and a recommendation. The budgets exist because the
   scarce resource is not testing labor — that is nearly free now — but the
   human's attention and the remediation cycles the gate consumes.
2. **Dispute.** "The same GAP" means the same register item at the same
   enforcement point — a deeper assertion on the same item is a deepening,
   classified as a NOTE, not a re-report; the regress does not get to wear
   a dispute costume. Three kinds of dispute, two speeds:
   - *Sufficiency* — the builder believes the fix covers; the reviewer
     re-reports it. Escalate on the **second consecutive** re-report. One
     re-report is the system working — partial fix, clarification, complete
     fix. Two is a stable disagreement, and a third round would spend a
     launch on an argument whose positions will not move.
   - *Validity* — the builder, having confirmed against the code, believes
     the GAP is not real. Rebut **once**, in the next pass's prompt,
     explicitly labeled as the builder's claim; the reviewer verifies it
     independently rather than deferring to it. If the reviewer re-asserts,
     escalate immediately — validity positions do not change without new
     evidence, and a fake-fix to appease the reviewer is worse than either
     outcome.
   - *Register ambiguity* — both readings of the register item are
     defensible. Escalate at once: the item's wording is the defect, and
     only the human can amend the register.
   Every dispute escalation states both positions and names the dispute
   type, because the adjudication differs — pick a winner, or fix the
   register.
3. **The fix needs a product decision,** a scope expansion, a destructive
   action, or unlocking a frozen file.
4. **An above-line item is untestable as built.** That is a design problem
   wearing a test costume; it needs a human, not a workaround.
5. **No register exists.** The gate runs unanchored and escalates at the
   **first INADEQUATE** rather than looping — that is the whole of the rule;
   there is no separate scale condition, because the gate's own scale floor
   already decided whether it runs at all.
6. **A third provisional-valve use.** The valve is capped at two per gate;
   the third is an escalation, not a silent third fix.

A gap the human knowingly accepts at any escalation is a legitimate outcome
— recorded as intentional residual risk with a repayment trigger, never
laundered into an ADEQUATE verdict.

## Independence and vendors

The failure mode this gate guards against is the builder self-reporting
"tests pass, it works." Authority comes from the reviewers not being the
builder — and the stronger pass not sharing the builder's vendor.

**The vendor pool is Anthropic and OpenAI. Pass 2 runs on the vendor that
did not build the code.**

| Builder | Pass 2 reviewer |
| --- | --- |
| Claude Code / Claude CLI (Anthropic) | OpenAI — the `codex` CLI |
| Codex (OpenAI) | Anthropic — the `claude` CLI |

Models from one vendor share training data and a common notion of what
"well tested" looks like; agreement between two of them is partly family
resemblance, which is the agreement Pass 2 exists to break. The first
cross-vendor run over a feature a same-vendor pass had blessed found five
gaps it had missed, including the suite's most damning.

**Run both passes on the most capable model available** for their stack, at a
high reasoning effort — but **not reflexively the maximum**. No specific model
is named here on purpose: model names go stale faster than the methodology
does.

On current models the tier below the ceiling is generally the better review
setting: the top tier can overthink, and a reviewer that overthinks produces
exactly the deepening-assertion findings this gate's taxonomy then has to
suppress. Sweep once against your own runs rather than inheriting a default.
The rule is "high enough that capability is not the limiting factor," not
"the highest number available."

One practical constraint worth knowing: a subagent's effort may be inherited
from the session rather than settable per-agent, so Pass 1's effort can be
pinned only by setting the session's. Record what actually ran — the log's
provenance table has a field for exactly this — rather than what you asked
for.

**The independence ladder.** Rank the run by what the reviewer actually was:

| Level | Pass 2 reviewer | Standing |
| --- | --- | --- |
| Cross-vendor | Other vendor, separate process (CLI) | The default. Required unless a downgrade is accepted |
| Cross-vendor via MCP | Other vendor, called from the build session | **Downgrade**, accepted like any other; vendor independence holds, the process boundary does not |
| Cross-model | Same vendor, different model family | Downgrade |
| Fresh-context | Same vendor and model, separate process | Downgrade; weakest that still counts |

**A downgrade requires explicit human acceptance and is recorded** with the
reason the cross-vendor path failed and a repayment trigger to re-run once
it works. Never claim a level you did not achieve. If no independent
reviewer is available at all, report the gate as blocked; do not silently
substitute a same-session reviewer.

**Only the top row closes the gate on its own.** Pass 2's requirements below
include a separate process, so an MCP run does not satisfy them — it can
stand in *as an accepted downgrade*, never as an unremarked equivalent. An
earlier version of this table called that row simply "acceptable", which
read as a second way to close the gate and contradicted the Pass 2
requirements outright.

**Pre-flight the other vendor's CLI before Pass 1** — a version call is
enough. Availability is a fact to re-establish each run, not to assume from
last month: vendors reorganize products, and a CLI is a separate artifact
from a desktop app. Discovering the reviewer is uninstallable after a
remediation cycle is the moment a silent downgrade is most tempting.

**Data sharing is the human's standing decision.** Sending the worktree to
the other vendor is acceptable only where the human has decided so — once,
deliberately, before the first cross-vendor run; never per-run, never by
the reviewer.

## The audit rubric

Both passes run this same audit.

1. Establish the exact change set from git — `git status --porcelain`
   (read untracked files directly; they appear in no diff), `git diff
   HEAD`, and `git diff <trunk>...HEAD` with `git log --oneline
   <trunk>..HEAD`. An empty change set is not an ADEQUATE verdict; stop and
   ask what to review.
2. Read the register slice and the project's test discipline (both are in
   your prompt). Apply the enforcement-point rule, not a coverage
   percentage.
3. Enumerate the behaviors the change set introduces or alters; for each,
   list its enforcement points. More than one enforcement point, or any
   "the user sees X," is feature-level and demands an outer-loop test.
4. Map tests to behaviors. **Allocate depth by register score**: dig
   hardest where the register says the risk is, and state in the verdict
   which register items received scrutiny. Do not sweep uniformly and let
   skepticism pool wherever assertions happen to be shallowest.
5. Run the test suite. Report real observed counts — failures, skips,
   xfails (judge each xfail: legitimate pin or dodge?), suspiciously fast
   tests. **Counts that disagree with the builder's are a finding in
   themselves**, and the interesting direction is either one: a test that
   passes for the builder and fails for you is environment-dependent, not
   broken-by-the-reviewer, and the dependency is the defect. The usual shape is
   a test that simulates *absence* — a missing binary, an unset variable, a
   closed port — by relying on the ambient environment not to supply it. It is
   green or red according to whose shell it ran in, which means it certifies
   nothing on either result. A gate run in a differently-configured process is
   often the first thing to notice; say so rather than quietly re-running until
   the numbers match.
6. Run the suites the default command excludes — find the markers and env
   gates, select them explicitly. Gated tests are disproportionately the
   integration and real-data ones. A gated suite that cannot run is a
   finding: name what it therefore does not prove.
7. Run the outer-loop / end-to-end command if one exists. Confirm with the
   human first only if it mutates state outside the repository.
8. Check fixture fidelity against reality: where a test encodes a rule
   derived from real-world data, open the real artifact and check the
   fixture reproduces the hazard. Fixtures drift toward tidiness. A fixture
   untraceable to a real example is a finding; so is a documented hazard
   with no fixture — scoped to hazards the change set claims to cover.
9. Hunt the boundary-skip signature: behavior wired through a real boundary
   (HTTP route, CLI entry point, shell script, loader) but tested only in
   pieces, never end-to-end through the boundary.
10. Audit claims against assertions: a test whose name, docstring, or comment
    promises more than its assertions check is a finding the change set owns.
    **It is a GAP only when the overstated claim is about an above-the-line
    register item** — otherwise it is a NOTE, however irritating the
    overclaim. Every GAP traces to the register; there is no second route in.
    "Tested manually" is not tested. A behavior that cannot be tested as
    built is a finding (and if above-line, an escalation).

**Verdict format.** Lead with **ADEQUATE** or **INADEQUATE** (zero GAPs is
the only test). Then, in order: **GAPS** — numbered; each names the register
item, the behavior, its enforcement points, the gap, what it lets through,
and the test to add. **PROPOSALS** — each with the exposure, one-bit
likelihood (operational / edit-only), one-bit impact (documented trace /
none), and suggested placement. **NOTES.** **DEFECTS.** Then the register
coverage statement (items examined, depth), the single most damning GAP if
any, one line of generosity for what is genuinely covered, and compliance
statements (exclusions honored; nothing modified). No padding. A reviewer
with no GAPs says so in a few lines and stops.

**Every GAP states its kind:** *newly uncovered behavior*, *an overstated
claim about an above-the-line item*, or *a returning finding*. The log tracks
findings by identity and cannot do so unless the verdict supplies the
classification. It is also the distinction the human needs at a budget
ceiling: three new findings and three returning ones are the same count and
opposite situations.

Note what is **not** on that list: "a deeper assertion on behavior already
covered" is a NOTE by definition and can never be a GAP kind. An earlier
version of this section listed it as one, contradicting the taxonomy two
sections above and quietly reopening the unbounded channel the taxonomy
exists to close.

**A GAP resting on an overstated claim must quote the claim.** Requiring the
quote makes the finding checkable rather than asserted, exactly as the
provenance table does for model identity — and it forces the reviewer to
show that the overclaim is about a register item rather than merely about
prose. Without the quote, it is a NOTE.

## Pass 2 mechanics

Launch a reviewer that satisfies all of these: the other vendor per the
table; a new process, not a subagent of the build session, with no access
to the build conversation; non-persistent; top-tier model at a high
reasoning effort (see above — high enough that capability is not the
limiting factor, not reflexively the ceiling); instructed not to modify the
worktree; given the rubric,
register slice, test discipline, and change-set description inline in its
prompt; told who built the code and to ignore every prior adequacy claim,
including in commit messages; told to identify itself.

### What the reviewer may read

**Scope the reviewer to the whole worktree, including files git ignores.**
Ignored means do-not-commit, not do-not-examine — raw inputs and local data
are routinely the ground truth a data rule was written against, and the
fixture-fidelity step is impossible without them. The counterweight is need:
read what the audit requires and stop. Secret material answers no adequacy
question and is out of scope on relevance grounds. Quote the smallest
excerpt that makes a finding checkable — findings land in committed files.

**Mandatory exclusions: prior adequacy artifacts.** Name the verdict log
and the run-artifact directory in the prompt and forbid reading them; ask
the reviewer to confirm compliance. Telling it to "ignore prior claims" is
not enough once it can read them — it will confirm and extend the previous
list instead of building its own, which is the same-vendor failure arriving
by another road. The technical-debt register may be read, as builder claims
rather than settled fact.

### Command shapes

Verify flags against the installed CLI before relying on them — an
unrecognized flag can be absorbed silently, handing you a default-tier
reviewer whose verdict looks identical to the one you commissioned.

Claude built it, so OpenAI reviews (`codex exec` is the non-interactive
mode):

```text
codex exec --sandbox workspace-write --ephemeral \
  --model <top-tier reasoning model> -c model_reasoning_effort="<high tier, not reflexively the ceiling>" \
  --output-last-message <run-dir>/pass2-verdict.md \
  -C <repo> \
  < <run-dir>/pass2-prompt.md 2> <run-dir>/pass2-provenance.log
```

- **Prompt on stdin, from a file.** A real prompt is far too long for argv,
  and stdin left open makes `codex exec` hang silently forever; the file
  redirect delivers the prompt and its EOF ends the read.
- **`workspace-write`, not `read-only`** — counter-intuitive and learned
  the hard way: the read-only sandbox denies every temp directory, pytest
  cannot even start, and rubric steps 5–7 run degraded or not at all.
  Read-only is enforced by verification instead (below).
- Keep stderr: the session header there — CLI version, resolved model,
  sandbox, reasoning effort — is the provenance evidence. **Do not add
  `--json`**: it replaces that header with an event stream that names no
  model anywhere.
- `--output-last-message` lands slightly after the last visible activity;
  automate waits on that file, not on the log going quiet.

Codex built it, so Anthropic reviews:

```text
env -u CLAUDECODE claude -p "<prompt>" \
  --model <top-tier model> --effort <high tier> --no-session-persistence \
  --permission-mode bypassPermissions --allowed-tools "Bash Read Grep Glob" \
  --output-format json < /dev/null > <run-dir>/pass2-result.json
```

`--output-format json` preserves the evidence: the envelope's `modelUsage`
keys carry the resolved model. The verdict text is `.result`.

### Provenance: take identity from the runtime

The flags you passed are not evidence of what reviewed the code, and a
model's belief about its own identity is model output — sometimes exact,
sometimes a vague family name, sometimes absent, with nothing in the reply
distinguishing the cases.

| Reviewer | Runtime authority | Covers effort? |
| --- | --- | --- |
| `codex exec` | stderr session header | Yes |
| `claude -p` | `--output-format json` → `modelUsage` keys | No |

Record requested and confirmed models separately, with the source of the
confirmed value. **Prefer full model identifiers over aliases** when
launching: an alias resolves to whatever the CLI currently maps it to,
which is not necessarily the generation everyone assumes. Effort the
runtime does not echo is recorded as requested-but-unverified. You may also
ask the reviewer to open with a self-report block (vendor / model /
effort); treat it as corroboration that often arrives vague or not at all,
never as the mechanism.

### Verify the reviewer changed nothing

The fingerprint is the enforcement — the sandbox was opened so the suite
could run. Capture before and after, and void the review if they differ:

```text
{ git ls-files -c -z; git ls-files -o -z; } \
  | grep -zvE "__pycache__|\.pytest_cache|\.DS_Store|(^|/)\.git/" \
  | sort -z -u | xargs -0 shasum | shasum
```

**Every stage is NUL-delimited, and that is load-bearing, not style.** A
whitespace-delimited pipeline hands `xargs` one filename per *word*: a path
like `Joint Checking 0227 Statement.pdf` becomes four nonexistent paths,
`shasum` errors on each, and the file contributes **nothing** to the hash —
while the pipeline still exits 0 and prints an authoritative-looking digest.
Measured on a real repository, the whitespace form covered **12 of 68** files
under `imports/`; the other 56 had spaces in their names. Verified against a
purpose-built repo, the whitespace form is blind to a space-named ignored file
being **modified, added, or deleted** — all three leave the digest byte for
byte identical, while the NUL-delimited form detects each and returns to
baseline on restore.

**Do not use `--exclude-standard` here either.** It drops every git-ignored
path from the listing, and git-ignored is precisely where this gate sends the
reviewer: raw inputs, generated output, local working data. Measured on the
same repository, the `--exclude-standard` form covered **2 of 68** files under
`imports/` — the two force-included `.gitkeep`s.

These are the same failure by two different mechanisms, and the second was
introduced by the fix for the first. Read-only was deliberately shifted off the
sandbox and onto this check (the sandbox blocks the suite from starting), so
the check has to cover the whole surface the reviewer was granted. **A control
with a hole where the sensitive data lives is worse than no control, because it
is trusted.** Bank statements and brokerage exports are exactly the files whose
names contain spaces, so the two holes selected for the same victims.

**Re-measure rather than re-reason.** Both holes passed inspection and both
were caught only by counting. Before trusting any edit to this command, prove
it on a scratch repo: create an ignored file *with a space in its name*, record
the digest, modify it, and confirm the digest moves. A digest that does not
move is the whole failure, and it looks identical to a clean run.

Exclude only what the audit itself must be allowed to write — derived caches,
and `.git` internals, which move on any read-only git command.

Derived caches are excluded because running the suite rewrites them, and
running the suite is what the reviewer was told to do; a fingerprint that
includes them voids every compliant run and teaches you to ignore the
check.

## Record the run

**Write the log entry when the run returns, before remediation begins** —
not when the gate closes. Deferring loses the log's main purpose: a
four-round gate and a one-round gate look identical when only the closing
round is written, so nobody can tell converging from churning, or see that
this round's findings are last round's one level deeper (the signature of
remediation under-shooting). It also stakes the record on an unfinished
process: reviewer output lives in session-scoped scratch, and a closed
terminal takes the evidence with it. A superseded INADEQUATE entry is not
clutter; it is the reason the next entry is believable.

**But never write it into the worktree while a reviewer is still running.**
Record-on-return and the compliance fingerprint collide exactly once, and it is
easy to walk straight into: Pass 1 returns, you write its entry, and Pass 2 is
still working — so the tracked log file changes mid-run, the before/after
digests differ, and the control can no longer tell your edit from the
reviewer's. **Park the entry outside the repository** (session scratch) and
land it once Pass 2's fingerprint has been verified. The rule is
record-on-return, not commit-on-return: the point is that the entry is composed
while the evidence is live, not that it reaches git before the round ends.

Do not resolve this the other way, by narrowing the fingerprint to exclude the
log. The digest's value is that it covers everything the reviewer could touch,
and a builder who is editing files mid-run is the one case where a per-file
diff would be needed to interpret a mismatch — which is precisely the
interpretation work the single digest exists to avoid.

Append to `docs/04-implementation/test-adequacy-log.md`; save raw verdicts
in `docs/04-implementation/test-adequacy-runs/`. Each entry records the
provenance table — vendor, product + version, model requested, model
confirmed (with source), effort, process, verbatim command — for builder
and both reviewers, with the independence level derived from the vendor
rows rather than asserted. Unknown values are written `unrecorded`, never
omitted or inferred. Per round, record counts **by lane**: the GAP-count
trend across rounds is the convergence signal; proposals are tracked
separately as register-refresh yield, and do not count against
convergence. Record the register slice's source artifacts and versions.

Keep a per-feature convergence table at the top of the gate's entries:
round, reviewer, verdict, GAPs, proposals, most damning. That table is what
the human reads to judge churn versus convergence — it is the gate's own
adequacy evidence.

**Track findings by identity, not only by count.** Give each finding a stable
short handle and carry it across rounds, so the table shows whether this
round's findings are *new* or the *same ones returning*. Counts alone hide
the failure this catches: a finding acknowledged in one round and quietly not
fixed can reappear several rounds later looking like fresh reviewer insight,
while the count trend suggests healthy convergence. Identity also makes the
per-finding distinction visible — newly uncovered behavior, a deeper
assertion on behavior already covered, or a returning finding — which is what
tells the human whether the gate is converging or the remediation is
under-shooting.

**A returning finding is a signal about the builder, not the reviewer.** When
one comes back, the question is not whether the reviewer is being pedantic;
it is why the first fix did not hold, or whether it was ever made. Say which
in the log.

In the final implementation report, state both verdicts, the independence
level, the observed test counts, every remediated GAP, the residual risk
accepted, and any escalation that occurred.
