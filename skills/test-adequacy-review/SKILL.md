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

**The vendor pool is Anthropic, OpenAI, and Google. Pass 2 runs on a vendor
that did not build the code — the first one, in the order below, whose
pre-flight probe passes.** (Google added 2026-09-08; the pool was two vendors
before that.)

| Builder | Pass 2 reviewer, in order of preference |
| --- | --- |
| Claude Code / Claude CLI (Anthropic) | 1. OpenAI — the `codex` CLI · 2. Google — the `agy` CLI |
| Codex (OpenAI) | 1. Anthropic — the `claude` CLI · 2. Google — the `agy` CLI |
| Antigravity CLI (Google) | 1. Anthropic — the `claude` CLI · 2. OpenAI — the `codex` CLI |

A pool of two has a reviewer pool of **one**: the builder is always a member,
so a single vendor's outage, or a subscription quota running dry for the
afternoon, blocks the gate outright. That happened on 2026-09-08, and the
third vendor exists so that the gate's only remaining hard stop is all other
vendors failing at once. The order within a row is the human's capability
ranking, re-sweepable at any time; second place is not a downgrade on the
ladder below — vendor independence and the process boundary both hold — but
it **is** recorded, as the reviewer tier (below), and the closure report says
which vendor reviewed and why the first choice was skipped.

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

**Reviewer tier.** Vendors do not sit at the same capability level at every
moment, and the ranking above encodes that. Record which tier the Pass 2
reviewer was — *first choice* or *fallback* — in the log's provenance table,
next to the independence level and separate from it. Two runs can both be
cross-vendor and still not be interchangeable evidence; the human decides how
much weight a fallback verdict carries, and cannot decide that if the log
does not say.

**Pre-flight every candidate reviewer with a live probe before Pass 1 — a
version call is not enough.** Availability is a fact to re-establish each
run, not to assume from last month: vendors reorganize products, a CLI is a
separate artifact from a desktop app, and a subscription quota runs dry
without the binary changing at all. On 2026-09-08 `gemini --version` answered
normally on a CLI Google had retired eleven weeks earlier, and `codex
--version` answered normally with the plan's credits exhausted. Both pass a
version check and fail the run. Discovering that after a remediation cycle is
the moment a silent downgrade is most tempting.

The probe is one prompt whose only correct reply is the word `OK`. A probe
**passes** only when the command exits 0 *and* the reply contains that word;
anything else — an auth error, a quota message, a hang, an empty reply — is a
failure, and the vendor's `.err` file in the run directory is the reason the
log records against it: the CLI's stderr, followed by the exit status the
wrapper appends. The `.txt` beside it holds whatever the CLI replied. Walk the ordered list and take the first vendor
that passes; probe only as far as you need to. Every probe is wrapped in a
timeout, because a CLI waiting on a login page or an open stdin waits forever
and macOS ships no `timeout`. **The timeout must kill the CLI itself, not rely
on the signal to do it**: `perl -e 'alarm N; exec @ARGV'` looks like a timeout
and is not one for a CLI that ignores SIGALRM, which every Go binary does by
default — `agy` ran a full 30 seconds under a 3-second alarm and then exited 0.
`run_with_timeout` below forks, and on the alarm kills the child (TERM, then
KILL) and exits 142. `$RUN` is the run directory.

### Pre-flight probes

```bash
# The timeout, for every probe below and for the Google launch. (The Codex and
# Claude Pass 2 shapes are not yet wrapped in it.) It keeps the CLI as a
# child and kills it itself — TERM, then KILL — because SIGALRM's default
# action cannot be relied on: agy is a Go binary, and Go ignores SIGALRM. The
# earlier wrapper (`alarm N; exec @ARGV`) let such a CLI run without limit and
# then exited 0. Measured 2026-09-19 under bash and zsh: a child that ignores
# SIGALRM stops at the budget with status 142; a child's own status passes
# through; a missing binary leaves "exec failed" as the reason.
run_with_timeout() {
  perl -e '$SIG{ALRM} = sub { kill "TERM", $p; sleep 2; kill "KILL", $p; exit 142 };
           alarm shift;
           defined($p = fork) or die "fork failed: $!\n";
           exec @ARGV or die "exec failed: $!\n" unless $p;
           waitpid $p, 0;
           my $s = $?; exit($s & 127 ? 128 + ($s & 127) : $s >> 8);' "$@"
}

# Probe output is written into the run directory, which is committed, and a CLI
# that echoes a credential in an auth or quota message would commit it with the
# rest. Every probe file is redacted as soon as it is written. Fail-closed: if
# the redaction cannot run, the probe counts as failed *and* its output is
# discarded, because a reply nobody could redact cannot be published — the
# reason file then says so instead. (Confining the probes themselves is a different job:
# `codex` and `claude` authenticate from the operator's own home directory, so
# a throwaway home would break them — B-43.)
redact_probe() {  # redact_probe <vendor>
  python3 - "$RUN" "$1" <<'REDACT'
import os, re, sys
run, vendor = sys.argv[1], sys.argv[2]
secret = re.compile(r"AQ\.[A-Za-z0-9_\-]{20,}|AIza[0-9A-Za-z_\-]{20,}"
                    r"|sk-[A-Za-z0-9_\-]{20,}|gh[pousr]_[A-Za-z0-9]{20,}")
for suffix in ("txt", "err", "out"):
    path = os.path.join(run, f"probe-{vendor}.{suffix}")
    if os.path.exists(path):
        text = open(path, errors="ignore").read()
        open(path, "w").write(secret.sub("[REDACTED]", text))
REDACT
  rc=$?
  if [ $rc -ne 0 ]; then
    # Nothing unredacted may survive, so the output goes rather than the reason:
    # the reply and stdout are deleted and the reason file is replaced with why.
    rm -f "$RUN/probe-$1.txt" "$RUN/probe-$1.out"
    printf 'probe failed: output discarded unread (redaction unavailable)%s\n' \
      "${2:+ — $2}" > "$RUN/probe-$1.err"
    return 1
  fi
}

# `$RUN` must exist before a probe runs: every redirection below writes into it,
# and if they fail the probe looks like a failed vendor with an empty reason —
# a healthy first choice skipped for nothing (B-40, extended).
probe_run_dir_ok() {
  [ -d "${RUN:-}" ] && return 0
  echo "probe skipped: RUN is not a directory: ${RUN:-<unset>}" >&2
  return 1
}

# OpenAI — codex
probe_run_dir_ok && rm -f "$RUN/probe-openai.txt"
probe_run_dir_ok && run_with_timeout 120 codex exec --ephemeral --skip-git-repo-check \
  --sandbox read-only -o "$RUN/probe-openai.txt" "Reply with the single word OK." \
  < /dev/null > "$RUN/probe-openai.out" 2> "$RUN/probe-openai.err" \
  && grep -qw OK "$RUN/probe-openai.txt" \
  && redact_probe openai \
  || { s=$?; echo "probe failed: exit $s$([ $s -eq 142 ] && echo ' (alarm fired: timed out)')" >> "$RUN/probe-openai.err"; redact_probe openai "exit $s"; false; }

# Anthropic — claude
probe_run_dir_ok && run_with_timeout 120 env -u CLAUDECODE claude -p "Reply with the single word OK." \
  --output-format json --no-session-persistence \
  < /dev/null > "$RUN/probe-anthropic.txt" 2> "$RUN/probe-anthropic.err" \
  && grep -qw OK "$RUN/probe-anthropic.txt" \
  && redact_probe anthropic \
  || { s=$?; echo "probe failed: exit $s$([ $s -eq 142 ] && echo ' (alarm fired: timed out)')" >> "$RUN/probe-anthropic.err"; redact_probe anthropic "exit $s"; false; }

# Google — agy (Antigravity CLI on a Gemini API key; the retired gemini CLI is not a candidate)
probe_run_dir_ok && run_with_timeout 120 agy -p "Reply with the single word OK." \
  --output-format json --model gemini-3.8-flash --effort low \
  < /dev/null > "$RUN/probe-google.txt" 2> "$RUN/probe-google.err" \
  && grep -qw OK "$RUN/probe-google.txt" \
  && redact_probe google \
  || { s=$?; echo "probe failed: exit $s$([ $s -eq 142 ] && echo ' (alarm fired: timed out)')" >> "$RUN/probe-google.err"; redact_probe google "exit $s"; false; }
```

All three have the same shape, and the shape is the point: the reply goes to
a **file**, so it is kept whatever it says; the CLI's **exit status is gated
with `&&`**, not dropped down a pipe into `grep`; and on any failure the
wrapper **appends the exit status to the `.err` file**, so a hang (the alarm
delivers SIGALRM, status 142), a wrong reply (status 1 from `grep`, reply in
the `.txt`), an error the CLI reports on stdout (envelope in the `.txt`,
status in the `.err`), and a CLI that is **not installed** all leave a reason
behind. That last one needs the `die`: perl's `exec` returns quietly with
status 0 when there is nothing to exec, so without it a missing binary reads
as a successful launch, and the Codex form — whose reply file is written by
the CLI rather than truncated by the shell — would then *pass* on a reply left
behind by an earlier round. Hence the `rm -f` ahead of it. The first cut of these
probes piped two of them into `grep`, which discarded both the CLI's exit
status and its reply; a same-vendor review measured that a CLI printing `OK`
and exiting 1 passed, and that three of the four failure modes named above
left an empty reason file.

These commands are load-bearing and `tools/reviewer-preflight.test.ts` runs
them, extracted from this file, against stub CLIs that answer the version
call and then fail the probe — the exact hole the version check had.

**Data sharing is the human's standing decision, one per vendor.** Sending
the worktree to another vendor is acceptable only where the human has decided
so — once per vendor, deliberately, before that vendor's first cross-vendor
run; never per-run, never by the reviewer. A fallback vendor the human has
not decided on is not a candidate, however healthy its probe: skip it and
record why.

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

Launch a reviewer that satisfies all of these: a vendor other than the
builder's, the first in the table whose probe passed; a new process, not a subagent of the build session, with no access
to the build conversation; non-persistent; top-tier model at a high
reasoning effort (see above — high enough that capability is not the
limiting factor, not reflexively the ceiling); instructed not to modify the
worktree; given the rubric,
register slice, test discipline, and change-set description inline in its
prompt; told who built the code and to ignore every prior adequacy claim,
including in commit messages; never asked to identify itself (see
Provenance below); launched with the least privilege that lets it run the
suite.

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

### Google: launch with least privilege

Google reviews through the Antigravity CLI, `agy`, on a Gemini API key
(verified 2026-09-19 on 1.2.7). **It runs inside agy's own sandbox, with the
narrowest permissions that let it run the suite — never with
`--dangerously-skip-permissions`.** That flag grants the whole machine, not
the repository, and a reviewer is an automated agent that goes looking when
it is stuck: in trials on 2026-09-18, reviewers that could not find their
files searched the home folder, read `~/.zsh_history` (which held an API
key), and dumped their environment. The fingerprint below catches a reviewer
that *changes* the repository; nothing catches one that *reads* outside it,
so reading has to be prevented rather than detected.

Set `REPO` (the repository), `RUN` (the run directory), `PROMPT` (the prompt
file) and `GEMINI_API_KEY`, then:

```bash
(
: "${REPO:?set REPO to the repository}" "${RUN:?set RUN to the run directory}" \
  "${PROMPT:?set PROMPT to the prompt file}" "${GEMINI_API_KEY:?set GEMINI_API_KEY}"
[[ -d "$REPO" && -d "$RUN" && -f "$PROMPT" ]] \
  || { echo "REPO and RUN must be directories and PROMPT a file" >&2; exit 2; }
run_with_timeout() {
  perl -e '$SIG{ALRM} = sub { kill "TERM", $p; sleep 2; kill "KILL", $p; exit 142 };
           alarm shift;
           defined($p = fork) or die "fork failed: $!\n";
           exec @ARGV or die "exec failed: $!\n" unless $p;
           waitpid $p, 0;
           my $s = $?; exit($s & 127 ? 128 + ($s & 127) : $s >> 8);' "$@"
}
WORK="$(mktemp -d "${TMPDIR:-/tmp}/pass2-sbx.XXXXXX")" || exit 2
trap 'rm -rf "$WORK"' EXIT
trap 'exit 130' INT; trap 'exit 143' TERM HUP
RHOME="$WORK/home"; RTMP="$WORK/tmp"; PARK="$WORK/park"
BRAIN="$RHOME/.gemini/antigravity-cli/brain"
mkdir -p "$BRAIN" "$RTMP" "$PARK"
# The reviewer runs the suite and its counts are compared with the builder's, so
# it must run on the same toolchain. A scrubbed PATH resolves `node` to whatever
# the default list finds first, which on the machine where this was measured was
# four major versions from the operator's own (round 9). The builder's node
# directory therefore goes first, whatever else the path holds.
NODE_BIN="$(dirname "$(command -v node || command -v false)")"
RPATH="$NODE_BIN:${REVIEWER_PATH:-/opt/homebrew/bin:/usr/local/bin:/Library/Developer/CommandLineTools/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin}"
# Confined exactly like the review itself, and without the key, which a version
# call does not need: this is still the vendor's self-updating binary running on
# the machine. Left unconfined it ran with the operator's own HOME and whole
# environment — every secret in it — which is what the rest of this block exists
# to prevent.
run_with_timeout 60 env -i HOME="$RHOME" PATH="$RPATH" LANG=en_US.UTF-8 TMPDIR="$RTMP/" \
  agy --version < /dev/null > "$PARK/pass2-agy-version.txt" 2>&1 \
  || echo "unrecorded — the version call failed" > "$PARK/pass2-agy-version.txt"
# Which toolchain actually ran the suite, for the log's provenance table.
run_with_timeout 60 env -i HOME="$RHOME" PATH="$RPATH" LANG=en_US.UTF-8 TMPDIR="$RTMP/" \
  sh -c 'printf "node %s\nnpm %s\n" "$(node -v 2>/dev/null || echo unrecorded)" \
                                      "$(npm -v 2>/dev/null || echo unrecorded)"' \
  < /dev/null >> "$PARK/pass2-agy-version.txt" 2>&1
cat > "$RHOME/.gemini/antigravity-cli/settings.json" <<JSON
{
  "modelProvider": "gemini",
  "toolPermission": "proceed-in-sandbox",
  "permissions": { "allow": [
    "read_file($REPO)", "write_file($REPO)",
    "read_file($RTMP)", "write_file($RTMP)",
    "read_file($BRAIN)"
  ] }
}
JSON
(cd "$REPO" && run_with_timeout 3600 env -i HOME="$RHOME" \
  PATH="$RPATH" \
  LANG=en_US.UTF-8 USER="$(id -un)" TMPDIR="$RTMP/" \
  npm_config_cache="$RTMP/npm-cache" npm_config_update_notifier=false \
  GEMINI_API_KEY="$GEMINI_API_KEY" \
  agy \
  -p "$(cat "$PROMPT")" \
  --model gemini-3.8-flash --effort high \
  --output-format stream-json --sandbox \
  < /dev/null > "$PARK/pass2-stream.jsonl" 2> "$PARK/pass2-provenance.log")
rc=$?
REDACT_VALUE="$GEMINI_API_KEY" python3 - "$PARK" <<'PY'
import os, re, sys
park, key = sys.argv[1], os.environ["REDACT_VALUE"]
google_key = re.compile(r"AQ\.[A-Za-z0-9_\-]{20,}|AIza[0-9A-Za-z_\-]{20,}")
for name in os.listdir(park):
    p = os.path.join(park, name)
    t = open(p, errors="ignore").read().replace(key, "[REDACTED:GEMINI_API_KEY]")
    open(p, "w").write(google_key.sub("[REDACTED]", t))
PY
[[ $? -eq 0 ]] || exit 2   # a failed redaction must never reach RUN; the trap deletes PARK
mv "$PARK"/* "$RUN/" || exit 2
exit "$rc"
)
```

Take the fingerprint before and after this block, as for every reviewer —
with `RUN` outside the repository, or pointed at a parking folder whose
contents are moved in after the second fingerprint: the block writes its
outputs into `RUN`, and inside the repository they would move the fingerprint
themselves.

- **It fails closed.** It refuses to start without all four inputs — an
  unset `RUN` would have lost every output while reporting success, an unset
  `REPO` would have written empty allow rules, and an empty key would have
  scrambled every output file — and it exits with the CLI's own status when
  the CLI fails, outputs still redacted and kept. The throwaway home holds
  unredacted transcripts, so it is deleted on any exit of the block itself,
  including an interruption that reaches it — a signal to the process group,
  which is what Ctrl-C and a killed tool call send. A signal to the launching
  shell alone orphans the subshell and leaves the folder behind.
   The signal traps are for zsh, the shell agents use on macOS:
  measured, bash runs the `EXIT` trap when its process group gets SIGTERM
  and zsh does not unless `TERM` is trapped too. The whole block is one
  subshell so that its exits and traps never reach the shell that ran it.
  Write it for both shells — `status`, for one, is read-only in zsh.

- **Every permission is granted before launch, because a refused action ends
  the session.** Headless `agy` cannot ask, so it refuses anything it would
  have asked about, and a refused action ends the run with no verdict. Each
  rule above was found that way, on 2026-09-19:
  - read and write on the repository — its files, and the build outputs and
    caches the suite writes. A permissions list that grants only reads leaves
    even the repository read-only;
  - read and write on a private temp folder, which `TMPDIR` points at — the
    install tests work there;
  - read only on the CLI's own session folder — a reviewer waiting on a slow
    suite opens the task's log, and the log lives there.

  Nothing else. Measured inside this shape: the home folder, `~/Code`, and a
  sibling project were all blocked. **Headless mode honours the allow list**:
  this skill said it did not (1.1.28, 2026-09-08), and on 1.2.7 it does.
- **The settings live in a throwaway home.** The human's own `agy`
  configuration is never edited, and the CLI's transcripts — which hold
  whatever the reviewer saw — are deleted with it.
- **The environment is scrubbed** to what the run needs. The Gemini key is
  the one secret left, because the CLI needs it, and the reviewer can still
  read it; so every output is redacted before it enters the repository.
- **`PATH` puts the Command Line Tools ahead of `/usr/bin`.** Apple's `git`
  launcher writes a cache to the shared per-user temp folder, which the
  sandbox denies, and prints warnings that a no-stderr test fails on; the
  real binary behind it does not. Any script using `mktemp -t` or a bare
  `mktemp -d` hits the same wall, because on macOS both ignore `TMPDIR` —
  create temporary files from an explicit template under `${TMPDIR:-/tmp}`.
  `REVIEWER_PATH` overrides the path; the tests use it to put stubs first.
- **The prompt must carry four things the other vendors' prompts do not:**
  1. A first line of `/goal Run all the test suites to completion, read their
     results, and then deliver the complete verdict.` The CLI enforces the
     condition and ends the reply with `<!-- GOAL_COMPLETE -->`, which tells
     a finished review from an abandoned one.
  2. How to wait: long commands become background tasks; check them with the
     task-status tool, never by opening the log; run `sleep 30` between
     checks; never end the turn while one is RUNNING. Two launches ended with
     no verdict because the reviewer stopped while the suite ran, and one
     check run made 131 back-to-back status checks — 710,000 input tokens and
     a quota error — where paced checking took 16.
  3. That it is in a sandbox: a blocked command is a finding, and it must
     never ask to leave, because the request ends the session.
  4. No request to identify itself (see Provenance below).
- **The prompt is an argument, never stdin.** `agy` does not read a prompt
  from stdin — an empty `-p` is refused and a bare `--print` swallows the next
  flag as its prompt. `"$(cat file)"` delivers it; macOS allows about 1 MB of
  argv, which a prompt file never approaches. Attach the prompt to `-p`
  directly and put every other flag after it.
- **`stream-json`, not `json`.** The single-envelope `json` output carries
  token counts but names no model; the NDJSON stream's first `init` event
  carries `init.model`, which is the provenance evidence. Effort is echoed
  nowhere, so it is recorded as requested-but-unverified. Record
  `agy --version` beside it, as the block does: the CLI updates itself. The
  block records the reviewer's `node` and `npm` versions there too, because the
  reviewer runs the suite and its counts are compared with the builder's.
- The verdict is the `response` field of the last `result` event:

  ```text
  grep '"event":"result"' <run-dir>/pass2-stream.jsonl | tail -1 \
    | python3 -c 'import sys,json; print(json.load(sys.stdin)["result"]["response"])' \
    > <run-dir>/pass2-verdict.md
  ```

- `agy` has no working-directory flag, so the block `cd`s into the repository
  in a subshell. Its `init` event records `cwd` and `permission_mode`; check
  both in the stream (`proceed-in-sandbox`, not `always-proceed`).

### Provenance: take identity from the runtime

The flags you passed are not evidence of what reviewed the code, and a
model's belief about its own identity is model output — sometimes exact,
sometimes a vague family name, sometimes absent, with nothing in the reply
distinguishing the cases.

| Reviewer | Runtime authority | Covers effort? |
| --- | --- | --- |
| `codex exec` | stderr session header | Yes |
| `claude -p` | `--output-format json` → `modelUsage` keys | No |
| `agy -p` | `--output-format stream-json` → the `init` event's `init.model` | No |

Record requested and confirmed models separately, with the source of the
confirmed value. **Prefer full model identifiers over aliases** when
launching: an alias resolves to whatever the CLI currently maps it to,
which is not necessarily the generation everyone assumes. Effort the
runtime does not echo is recorded as requested-but-unverified.

**Do not ask the reviewer to identify itself.** A self-report was once
allowed here as corroboration. It is worse than useless: on 2026-09-19 a
Gemini reviewer asked for one ran `env | grep -i gemini` to find out,
exposing its API key, and then named the wrong model. A reviewer with shell
access answers a question about itself by inspecting its environment, and the
runtime already holds the answer.

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
rows rather than asserted. The Pass 2 row also carries the **reviewer tier**
(first choice or fallback) and, for a fallback, **which vendor was skipped and
the probe's reason**, so a verdict from the second-choice vendor is never
mistaken for one from the first. Unknown values are written `unrecorded`, never
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
