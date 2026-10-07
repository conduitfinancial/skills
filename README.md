# Conduit skills

Agent skills for the [Conduit](https://conduit.financial) API. Each skill uses only your own Conduit API key.

| Skill | What it does |
|---|---|
| [`docs`](docs/) | Gives the location of the Conduit API documentation, OpenAPI spec and discovery endpoints. Shows how to call the API: onboard a business, provision a virtual account and a crypto wallet, deposit, convert and pay out. Works on production (`api.conduit.financial/v2`) and the production sandbox (`api.sandbox.conduit.financial/v2`). |

## Install

A skill is a folder with a `SKILL.md` file. Claude Code loads skills from `.claude/skills/` in a project, or from `~/.claude/skills/` for all projects.

```bash
git clone https://github.com/conduitfinancial/skills.git
mkdir -p ~/.claude/skills
ln -s "$PWD/skills/docs" ~/.claude/skills/conduit-docs
```

Restart Claude Code. Type `/` to see the skill in the list.

## Configuration

Set `CONDUIT_API_KEY` to your API key, or give the key to the agent when it asks. The [authentication page](https://docs.conduit.financial/authentication) explains which key fits which environment. No other credential is necessary.
