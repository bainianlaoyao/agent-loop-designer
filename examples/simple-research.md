<!--
EXAMPLE — minimal loop.
Shows: the four-section minimum-sufficient set for a single-domain, reversible,
low-risk task. No Approval Gates, no multi-domain Measurement — they are not
needed here. This is what tailoring down looks like.
-->

# Loop: Find the most-cited 2026 agent-memory paper and summarize it

## Goal & Success Signal

**Goal:** Produce a one-paragraph summary of the single most-cited paper on
agent memory published in 2026.

**Success signal:** A summary exists AND it names the paper's title, its
citation count, and at least two concrete findings drawn from the paper's own
text (not from search snippets). The loop is done only when all three are
present.

## Termination Conditions

The agent stops when any one holds:

- **Goal achieved** — the success signal above is satisfied.
- **Max iterations** — 6 iterations reached.
- **No progress** — two consecutive iterations add no new candidate paper and
  no new extracted finding.

On hitting max-iterations or no-progress without the goal, the agent stops and
reports what it has, labeled incomplete. It does not silently keep looping.

## Progress Invariant

The bounded quantities each iteration must advance:

```
candidates_found: N      # papers identified, with citation counts
findings_extracted: M    # concrete findings pulled from the chosen paper
```

Every iteration must increase `candidates_found`, increase
`findings_extracted`, or narrow to the final paper. An iteration that moves
none of these is a stall and counts toward no-progress termination.

## Measurement Domain

Output domain: **textual research artifact.**

- Verify the citation count by re-fetching it from the source, not from memory.
- Verify each finding is grounded: it must be traceable to a specific section
  of the paper, not to a search-result snippet. A finding that cannot be traced
  to the paper's text is rejected and does not count toward the success signal.
