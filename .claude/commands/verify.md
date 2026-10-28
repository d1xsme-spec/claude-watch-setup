---
description: Triple-independent-pass verification for a research note, to catch extraction errors before treating it as authoritative
argument-hint: [source: video URL, PDF/ebook path, article URL, or "the note at <path>"] [optional: what to focus on]
---

# /verify

An **optional, on-demand** deep-check for a research note — not something to run automatically after every `/watch` or every source read. Reach for it when the source is long or dense enough that a single extraction pass could plausibly drop or misattribute something, and getting it exactly right actually matters (e.g. the note contains verbatim instructions/prompts/quotes the user intends to copy and run or cite later).

Works on any source type: a YouTube video (via the `watch` skill/transcript), a PDF, an ebook, an academic paper, a long-form article, or any other text-based source — not video-specific. `$ARGUMENTS` names the source and, optionally, what aspect to focus the check on.

## When this is worth it (judgment call, not automatic)

Good fit: long/dense sources with many extractable facts, steps, or verbatim quotes, especially where the note's whole value depends on those being exact (a setup guide with copy-pasteable prompts, a paper's specific findings/numbers, a legal or technical document).

Not worth it: short sources, sources that are mostly narrative/opinion with little to extract precisely, or a note that's already been read closely once and looks internally consistent. Running this on everything triples the read/write cost for marginal gain — don't default to it.

If it's unclear whether a source warrants this, say so and let the user decide rather than assuming.

## Process

1. **Get the source content once.** Don't re-fetch/re-download 3 times — the source itself doesn't change between passes (a transcript's captions, a PDF's text, an article's HTML are deterministic). Fetch it once, read it in full.

2. **Do 3 independent extraction passes.** For each pass, re-read the source and write a fresh extraction from scratch — do NOT look at the other passes, any existing note for this source, or your own prior output while writing the current pass. Each pass should capture:
   - The actual structure of the source (steps, sections, chapters, arguments — whatever the source's own organization is; don't impose numbering or structure the source doesn't have).
   - Every verbatim quote/prompt/figure that matters, quoted exactly, not paraphrased.
   - Enough context that each pass could stand alone as a usable note.

   It's fine (encouraged, even) for each pass to use a different organizing lens — e.g. pass 1 grouped by the source's own sections, pass 2 as a chronological log, pass 3 double-checking quote boundaries against the raw source — since varying the approach is what surfaces different errors. Write each pass to a scratch file so they can be compared afterward.

3. **Compare all 3 passes against each other, and against the raw source.** Look specifically for:
   - Quotes that appear in some passes but not others (something one pass missed).
   - Quotes that differ in wording between passes (a transcription/extraction error — re-check against the raw source to see which is right, or whether both are wrong).
   - Structural claims one pass made that the others didn't (e.g. inventing a numbered step/section the source never actually states — re-verify against the raw source directly, since this is the easiest error to make and the easiest to miss without a cross-check).
   - Anything a later pass caught that an earlier one didn't, and vice versa.

4. **Produce one final, reconciled version.** Merge the passes: take the union of correctly-verified content, fix any discrepancy by re-checking the raw source (never split the difference between two disagreeing passes without checking which one is actually right), and write it in the note format already used for this project (see root `CLAUDE.md` for the video-note conventions; for non-video sources, follow the same spirit — real structure, verbatim quotes in blockquotes, explicit flags where the source itself is ambiguous or a quote isn't fully available rather than inventing one).

5. **Report what the cross-check actually found**, briefly — confirmed-consistent, or specific errors caught and fixed. Don't pad this into a big writeup if nothing was caught; a couple of sentences either way is enough. If this note already exists and this is a re-verification of it, update it in place rather than creating a duplicate file.

6. **Clean up.** Delete the 3 scratch pass files once the final version is written — same principle as the video-cleanup rule in `CLAUDE.md`: don't leave working files behind once the deliverable is done.
