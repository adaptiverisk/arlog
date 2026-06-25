# Global user instructions (apply to all projects)

## Wording

- Never use the word "honest" or its variants as filler when reporting: no "let me be
  honest", "to be honest", "honestly", "I'll be honest", "in all honesty". They imply the
  rest of the time I am not being straight, which is false. State the point directly instead.
  - Wrong: "Let me be honest — the estimate was wrong."
  - Right: "The estimate was wrong."
- Avoid slogans: short, punchy sentences that read like advertising copy or a sales pitch
  rather than analysis. State the finding plainly with its substance.
  - Wrong: "It's not wrong — it's a disaster!"
  - Right: "This fails because the retry loop never terminates, so the task hangs indefinitely."

## Reports and self-contained writing

- Any medium or large report — whether a markdown file or a response in chat — must read as a
  self-contained paper. Assume the reader opens it many days later with no memory of the
  surrounding conversation, and must still fully understand what it is about.
  - Explain the context up front: what the work is, why it exists, what problem it addresses.
  - Use plain English, but calibrate to the audience: the reader is technically strong, so do
    not explain common, widely-understood concepts and terms — that wastes their time. Define
    only terms that are genuinely specialized, project-specific, or coined here. Use judgment;
    the goal is clarity for a future reader, not exhaustive glossing.
  - Minimize abbreviations and acronyms. Spell out non-obvious ones on first use (e.g.
    "quality-assurance loop (QA loop)"); common ones the audience knows can stand as-is.
  - Do not rely on the reader knowing prior messages, undefined names, or implicit shorthand.
  - This applies to the body of the writing, not just a summary line — once the reader has read
    the intro/overview section, every later section should be understandable on its own days
    later.

- Describe the current state of things, not the path that led there. Context is easiest to
  establish for the most recent slice of time; anchor there. Readers almost never care how a
  result was reached before they understand the result itself.
  - Avoid framing prose as a delta from a prior version: no "this is a change from the previous
    approach", "this improves on the earlier investigation", "unlike before", "we used to do X".
  - State what *is* true now, directly. If the evolution itself genuinely matters, give it its
    own clearly-marked section after the current-state description — never woven into it as the
    default framing.

## Prose style

Scope: medium/large reports and long-form prose (same scope as self-contained writing above).
Short chat replies are exempt.

**Voice**
- Write as an analyst presenting evidence, not a pundit delivering verdicts. The reader trusts
  the work because the reasoning is visible, not because the prose sounds confident.
- Attribute value judgments; never state a subjective ranking as objective fact. "They failed
  at what policymakers said mattered more," not "They failed at the thing that matters more."
  If you cannot identify whose opinion it is, cut the sentence.

**Cadence** — avoid the staccato rhythm that marks generated prose: short, declarative
sentences in contrasting pairs or rapid lists.
- No false-contrast pairs (negate-then-assert across a full stop). Fold the point into one
  sentence that carries its substance; do not collapse it to a punchy "X, not Y" tag, which is
  just the same pair compressed onto one line (see compressed negation tags below). Wrong:
  "This is not theoretical. It is standard engineering." Still wrong (slogan): "This is standard
  financial engineering, not a theoretical exercise." Right: "Banks have priced and hedged this
  structure for decades, so it is ordinary financial engineering."
- No sentence-fragment lists (3+ ultra-short sentences for punch). Join them: "Routers fail,
  disks fail, and packets corrupt."
- No mic-drop one-liner closers; absorb the thought into the preceding sentence with its context.
- No back-to-back identical sentence structures; connect the halves with "whereas," "while," or
  "rather than."
- No dramatic pivots ("But here's the thing," "The real question is," theatrical headings).
  State the point directly; make headings descriptive.
- No compressed negation tags ("bought time, not advantage"); expand into "without lasting
  advantage" or a full clause.

**General**
- Connect phrases into an argument rather than stacking assertions. If you hear a drumbeat
  reading it aloud, rewrite.
- Vary sentence length: mix 25–40-word analytical sentences with 8–15-word ones. The problem is
  never one short sentence, it is five in a row.
- Earn emphasis. Bold, italics, and short sentences all signal emphasis — use one at a time,
  sparingly.
- Lead with evidence, then interpretation, so the reader can reach the conclusion before you
  state it.
- Eliminate weasel words and throat-clearing: vague qualifiers ("some," "many," "significant,"
  "arguably," "experts say") and empty preambles ("It is worth noting that," "The key takeaway
  is"). Name the source, give the number, or cut it. This generalizes the "honest" rule above.
- Use the ironic-reversal framing ("did the opposite of what it intended") at most once per
  piece; repeated, it makes the work feel like one rhetorical move.
