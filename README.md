# skills

Agent skills by [@gertzgal](https://github.com/gertzgal). Each skill is a folder under `skills/` with a `SKILL.md` and its supporting files.

## Install

With the [`skills`](https://www.npmjs.com/package/skills) CLI:

```sh
# every skill
pnpx skills add gertzgal/skills

# one skill
pnpx skills add gertzgal/skills -s pr-breakdown

# list what's in the repo
pnpx skills add gertzgal/skills --list
```

Add `-g` to install for your user instead of the current project.

## Skills

| Skill | What it does |
| --- | --- |
| [`pr-breakdown`](skills/pr-breakdown) | Turns a pull request into one sidebar-navigated HTML page: what it fixes, where it runs, the independent segments it splits into, the architecture changes, and the commit story. Prose is written in ASD-STE100 Simplified Technical English. Add "review" or "verify" to get severity-ranked finding cards with paste-ready review comments. |

### pr-breakdown

```
/pr-breakdown 1234            # PR number or URL
/pr-breakdown my-branch       # a branch
/pr-breakdown                 # current branch against its base
/pr-breakdown 1234 review     # add findings
```

Needs `git` and the [GitHub CLI](https://cli.github.com/) for PR numbers. The render check needs browser automation that can set the viewport and take screenshots, for example [`agent-browser`](https://github.com/vercel-labs/agent-browser).

## License

MIT
