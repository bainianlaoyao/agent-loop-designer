<!--
ANTI-EXAMPLE — DO NOT DELIVER A LOOP LIKE THIS.
This document is intentionally broken. It is the exact shape that produced the
documented failure: an agent that called a broken tool 400 times in five
minutes. Read the "WHY THIS IS UNDELIVERABLE" notes after each section.
-->

# Loop: Scrape the product catalog from the vendor site

## Goal & Success Signal

**Goal:** Get the product catalog data from the vendor website.

**Success signal:** We have the data.

<!--
WHY THIS IS UNDELIVERABLE — residual: ambiguous_success (HUMAN).
"We have the data" is not checkable. How much data? In what shape? When is it
"enough"? An agent cannot self-test this signal, so it can never know it is
done. Fix: "a JSON file at out/catalog.json with >= 1 record per category,
each record having id, name, and price". That is checkable.
-->

## Termination Conditions

If a fetch returns empty, **retry until you get data.** The loop ends when the
catalog is fully scraped.

<!--
WHY THIS IS UNDELIVERABLE — residual: unbounded_path + term_missing (LOCAL).
"Retry until you get data" is the literal instruction that caused 400 broken
calls in five minutes. When the site changed structure and the parser returned
empty, nothing decreased and nothing terminated — the retry path advanced no
bounded quantity and had no exit. "Fully scraped" just restates the vague goal;
it is not a stop condition an agent can evaluate. There is no max-iteration
bound, no budget, no no-progress detector. This loop has no way to ever stop
on failure. Fix: max_iterations, a per-run call budget, and a no-progress
detector ("empty result twice in a row -> stop and report").
-->

<!--
MISSING ENTIRELY — residual: progress_invariant_missing (LOCAL).
There is no Progress Invariant section. Nothing here defines a bounded quantity
(pages_remaining, records_collected) that every path must advance. Without it,
the executing mode has no convergence measure and literally cannot detect that
it is stalled. The single most important section is absent. UNDELIVERABLE.
-->

<!--
ALSO LEAKING — residual: platform_leak (LOCAL).
(Imagine this line were present: "Use the Claude `web_fetch` tool with model
claude-sonnet.") That binds the loop to one platform and one model. Fix: "use
a fetch-a-URL capability" and let the executing mode map it.
-->

## Summary of what makes this undeliverable

1. Unbounded termination ("retry until you get data") — no convergence, no exit.
2. No Progress Invariant — stalls are undetectable.
3. Unchecking success signal and termination — the loop can never know it is done.

Any one of these blocks delivery. A correct version states a checkable success
signal, bounds every path with a progress invariant, and adds explicit
max-iteration / budget / no-progress stops.
