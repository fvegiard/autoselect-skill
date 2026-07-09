# autoselect-skill

A skill that recommends which other skills to load for a given user turn.

## What it does

When a user message arrives, `autoselect-skill` scans the available skills and emits a
ranked list of recommendations. The invoking LLM (you) then loads the recommended
skills via your normal `skill`-loading mechanism.

It's a tiny piece of glue, but it cuts down on the "I forgot which skill to use" cost
for any agent with more than a handful of skills.

## Trigger conditions

- **Auto** — when the user message implies a domain (e.g. "review my UI", "debug this
  stack trace", "research market trends")
- **Manual** — when the user types `/autoselect`
- **Always** — when more than one available skill plausibly matches

## Output format

```
<autoselect>
recommended_skills:
  - name: <skill-name>
    score: <0-3>
    reason: <one sentence>
  ...
ignored_skills:
  - name: <skill-name>
    reason: <why skipped>
next_action: load <skill-name>[, ...]
</autoselect>
```

Score 3 = name match. 2 = description keyword overlap. 1 = adjacent domain. 0 = exclude.

## Install

```bash
# Mavis-format agent
mkdir -p ~/.skills/autoselect-skill
curl -L https://raw.githubusercontent.com/fvegiard/autoselect-skill/main/SKILL.md \
  > ~/.skills/autoselect-skill/SKILL.md
# restart agent
```

Or just drop `SKILL.md` into whichever directory your skill-loader reads from.

## Universal — works for any LLM

The skill does NOT depend on Mavis-specific runtime features beyond the
`<available_skills>` injection. Any LLM with:
- a way to list its skills
- a way to load a skill (file / system prompt / tool call)

can run this workflow.

## License

MIT.
