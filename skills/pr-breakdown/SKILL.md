---
name: pr-breakdown
description: Break a PR into a zoomed-out, sidebar-navigated HTML walkthrough — overview, where it runs, technical segments, architecture changes, commit story. Use when asked to break down, explain or review a pull request as a page.
disable-model-invocation: true
---

Produce one HTML page that lets a reviewer understand a PR top-down: what it fixes, where it runs, the independent pieces it splits into, and how it changes the design. Build it from [`template.html`](template.html), which is the single source of truth for layout, styling, and every component.

Args name the PR: a number/URL (`gh pr view`, `gh pr diff`), a branch, or nothing (the current branch against its base). Extra args ("focus on X") steer emphasis. When asked to verify or review, also read [`FINDINGS.md`](FINDINGS.md).

## Steps

### 1. Read the change

Find the base with `git merge-base origin/<base> HEAD`, then read every commit message in full (`git log --format='%s%n%b' <base>..HEAD`) and the full non-test diff. List the test files and their test names separately; their bodies wait for step 5.

Done when every changed non-test file has been read and you can say, for each commit, what it adds or removes.

### 2. Place it in the system

Trace from the changed code up to its entry point (the scheduled job, request handler, worker, CLI command, or public API that runs it) and down to every external resource a new or changed path touches: databases (reader or writer), HTTP/RPC calls, queues, object storage, caches.

Done when you have written a runtime table with:

- the entry point and what triggers it;
- the concurrency around the change (per run, per item, per request; parallel or serial);
- one row per external resource: the code that calls it, the call, and how often.

Chapter 2 of the page is built from this table.

### 3. Cut the segments

A **segment** is one independently understandable capability: one behaviour, its call path, its files. Commits often map one-to-one, but cut by behaviour, not by commit. Give the gating logic (when the new path engages), scope cuts and reverts their own segments; they are what reviewers miss.

For each segment write: the files (mark new ones), one snippet, the design decisions with the *why* for each, and pills naming the resources it touches.

Pick the snippet's shape by what the segment changes, and build it from the template's `.code` component (one `.cl` line per row):

- **Diff call tree**: a change inside an existing path. Unchanged context lines around `.cl.add` / `.cl.del` lines, real symbol names, 6–15 lines.
- **Whole block**: mostly new code. Plain `.cl` lines with the `new` pill in the snippet header; the reader needs the shape, not a wall of `+`.
- **Fold** elided stretches with one `.cl.fold` line so order and ownership stay visible.

Done when every hunk of the non-test diff belongs to exactly one segment.

### 4. Name the architecture changes

Three parts: the data-model change (a class/relationship sketch), a structural-changes table (change · named pattern · why it matters), and trade-off cards (what the design costs, with numbers when the code or data gives them).

Done when every new module, new seam (a parameter threaded through layers, a composable hook, a shared per-run object), and changed shared-code semantic appears in the table.

### 5. Verify the claims

Each claim on the page is something you read in the code. Now read the test bodies listed in step 1.

- Call two paths *equivalent* only after reading both sides' inputs. When they share code but get different inputs, say exactly that ("same enricher, different base fields").
- Mark anything inferred as *inferred*.
- Tests prove behaviour only where they run the real collaborator. When a test patches out the very thing a claim rests on, the claim is untested.

Done when every claim traces to a line you read, every *equivalent* has both inputs compared, and every untested claim appears in the trade-off cards.

### 6. Build the page

Copy `template.html` to a temp or output directory as `pr-breakdown-<ticket-or-branch>.html`. Keep its `<head>`, `<style>` and `<script>` verbatim; its layout rules keep long identifiers inside their cards. Fill `<main>` and the sidebar nav: one chapter per nav entry the template names, one sub-link per segment. Use real labels, symbols and numbers from the diff throughout. Open the file in a browser.

Done when every sidebar link targets a real `id` and no template placeholder text remains.

### 7. Render check

Render the page at 1440×844 and 390×844 with any browser automation you have (e.g. `agent-browser set viewport <w> 844`, then `screenshot`) and look at every chapter. Headless Chrome's `--window-size` stops at 500px, so its narrow shots mislead; set the viewport instead.

Done when:

- every snippet renders one row per code line;
- nothing is clipped at 1440px;
- `[...document.querySelectorAll('.code,pre')].filter(e=>e.scrollWidth>e.clientWidth).length` is 0;
- `document.documentElement.scrollWidth` is 390 on the phone render.

### 8. Prose pass

Read every sentence on the page against the Language rules and rewrite each one that breaks a rule.

Done when every descriptive sentence has 25 words or fewer, every verb is active, and no paragraph has more than six sentences.

## Language

Write every explanation on the page in **ASD-STE100** Simplified Technical English: headings, the lede, card text, design decisions, table cells, trade-offs and commit notes. Code symbols, file paths, SQL, test names and table names are technical names: copy them exactly.

The STE rules that a draft breaks most often:

- One topic in each sentence. A descriptive sentence has 25 words or fewer. An instruction has 20 words or fewer.
- Name the part that does the action, in the simple present, simple past or future tense: "The gate stops the query."
- Use approved words with their one approved meaning: *remove*, *keep*, *stop*, *show*, *change*, *use*, *find*, *operate*, *make*. Use a technical name or a code symbol when no approved word fits.
- Use an *-ing* word only as part of a technical name.
- Put each condition or step in a vertical list.
- A paragraph has one topic and six sentences or fewer.
