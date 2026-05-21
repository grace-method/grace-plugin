---
name: feedback
description: >
  Help the user surface methodology-level feedback about GRACE — unclear
  guidance, missing capability, friction in a skill, ideas for improvement,
  or bugs in the plugin itself. Walks the user through articulating the
  issue, applies a data-sensitivity check, and delivers the feedback as a
  Gmail draft to david.allen@gravie.com (falls back to a copy-ready
  markdown block if Gmail tools are unavailable). Use when the user says
  things like "I want to flag something about GRACE", "the methodology
  could be better at X", "send feedback", or invokes `/grace:feedback`.
model: sonnet
---

# GRACE Feedback

This skill helps the user articulate and send methodology-level feedback
about GRACE itself — not project work performed under GRACE. Typical
feedback shapes:

- A rule, skill, or template is unclear or hard to apply
- A methodology gap (something GRACE should help with but does not)
- Friction in an interaction pattern (a skill asks the wrong questions,
  surfaces too much, surfaces too little)
- A bug in the plugin (skill fails, install misbehaves, broken link)
- An improvement idea grounded in real project experience

The output is a Gmail draft in the user's account, addressed to
`david.allen@gravie.com`, that the user can review, edit, and send. If
Gmail tools are not available, fall back to a copy-ready markdown block.

## Interaction Pattern

Move through the steps in order. Keep each prompt short. One question at
a time when the user needs to think.

### Step 1 — Anchor the feedback

Ask the user what they want to flag. If their opening prompt already
contains the substance (e.g. `/grace:feedback the consultant-reflex rule
fires too eagerly on trivial edits`), use that as the seed and skip to
Step 2.

If they invoked the skill with no content, ask:

> What's on your mind about GRACE? It can be unclear guidance, a missing
> capability, friction with a skill or rule, a bug, or just an idea.

### Step 2 — Help them articulate

Reflect back what you heard in one sentence, then probe for the
information that makes feedback actionable. Ask at most two of these,
chosen by what the seed already covers:

- **What were you trying to do?** (the immediate goal, not the
  methodology theory)
- **What happened or didn't happen?** (concrete observation)
- **What would have worked better?** (the user's idea, even if rough)
- **Which skill, rule, or artifact is involved?** (if known)

Do not push for all four. If the user is venting an impression rather
than reporting an incident, that is still valid feedback — capture it
as an impression.

### Step 3 — Summarize

Produce a short summary the user can confirm or correct. Format:

```
**Subject:** <one-line headline, max ~70 chars>

**Context:** <what they were doing / where this came up>

**Issue:** <what felt wrong, unclear, or missing>

**Suggestion (optional):** <their idea, if they offered one>

**Affected:** <skill / rule / template / docs, if known>
```

Ask: "Does that capture it? Anything to add or change?"

### Step 4 — Data-sensitivity check

Before drafting the email, walk the user through a brief sensitivity
review. Say something like:

> Quick check before drafting the email. This will go to
> david.allen@gravie.com, which is outside any project's trust
> boundary. Please make sure the summary above does **not** include:
>
> - Patient information (PHI)
> - Student records (FERPA-protected data)
> - Specific customer names, account IDs, or contract terms
> - Credentials, API keys, or internal URLs
> - Confidential business strategy or financial figures
>
> If your example needs to reference something sensitive, paraphrase or
> use a placeholder like `<a specific customer>`.

Ask: "Anything in the summary you'd like to redact or rephrase?"

Apply any redactions the user requests. Do **not** scan the content
yourself for sensitivity patterns — the user owns this judgment. The
prompt is a reminder, not a gate.

### Step 5 — Optional contact info

Ask:

> Want to include a way for me to follow up if I have questions? Your
> name, email, or Slack handle — whatever you're comfortable sharing.
> You can also skip this and stay anonymous.

If they share contact info, append it to the draft. If they skip,
include a single line "(submitted without follow-up contact)" so the
recipient knows it was an explicit choice rather than forgotten.

### Step 6 — Draft the email

Compose the email body in this shape:

```
GRACE methodology feedback.

<one short framing sentence>

Context
-------
<the context paragraph from the summary>

Issue
-----
<the issue paragraph from the summary>

Suggestion
----------
<the suggestion paragraph, or omit this section if none>

Affected
--------
<skill / rule / template / docs reference>

Follow-up
---------
<contact info, or "(submitted without follow-up contact)">

---
Submitted via /grace:feedback. Plugin version: <if discoverable from
.claude/grace-methodology-version, otherwise omit this line>.
```

Subject line: the headline from Step 3.

Recipient: `david.allen@gravie.com`.

Now create the Gmail draft. Prefer the Gmail draft tool
(`mcp__claude_ai_Gmail__create_draft`) with these parameters:

- `to`: `["david.allen@gravie.com"]`
- `subject`: the headline
- `body`: the composed body above

If the Gmail tool is available and succeeds, report:

> Drafted in your Gmail. Open your Gmail Drafts folder, review it
> alongside the original conversation, edit if needed, and send when
> you're ready.

If the Gmail tool is not available or the call fails, fall back to
printing the email as a copy-ready markdown block:

````
**To:** david.allen@gravie.com
**Subject:** <headline>

<body>
````

Then say:

> Gmail draft tools aren't available in this environment. Copy the block
> above into your email client, review it, and send when you're ready.

### Step 7 — Close

Briefly thank the user. Do not promise a response timeline; the
recipient is one person and follow-up is best-effort.

## Constraints

- **Do not send the email.** Only ever create a draft or output text
  for the user to send. Sending is the user's action.
- **Do not write the feedback to any file in the project.** This skill
  produces a draft, not a project artifact. Feedback is a meta-channel,
  not project state.
- **Do not record the feedback in memory.** It belongs to the recipient,
  not to future conversations. (Memory may capture that `/grace:feedback`
  exists, but not the contents of any individual feedback.)
- **Recipient is fixed.** The skill always addresses
  `david.allen@gravie.com`. If the user asks to send to someone else,
  decline and suggest they edit the draft in Gmail after it is created.
- **No scope creep.** This skill captures feedback. It does not also
  triage, prioritize, or open backlog items. The recipient does that.
