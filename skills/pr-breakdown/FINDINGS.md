# Findings mode

Read when the user asks to verify or review the PR. Keep the same page and add finding cards, built from the commented `find` block at the end of `template.html`.

Each card has:

- a severity;
- a verdict that tells how you proved it (ran it, read both sides, queried the data);
- the evidence;
- a review comment in STE that the user can paste.

Rank the cards: blocking first, then pre-existing bugs to file separately, then follow-ups. End with the claims you cleared and any earlier claims the evidence corrected.

Add a Findings chapter to the sidebar nav. Step 8's prose pass covers the review comments too.
