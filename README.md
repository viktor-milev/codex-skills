# codex-skills

A small library of [Codex skills](https://learn.chatgpt.com/docs/build-skills), built for real analytical work and validated before publishing.

Each skill is a self-contained folder whose `SKILL.md` supplies the instructions Codex loads on demand. Optional `agents/openai.yaml` metadata controls invocation behavior.

---

## Skills

### Investment

| Skill | What it does |
|---|---|
| [`investment-council-challenge`](./investment-council-challenge) | Pressure-tests a listed-equity thesis through five independent advisors, anonymous peer review, an adversarial challenge round, and a chairman's final verdict. It can use verified market data and explicitly authorized portfolio context, while remaining strictly read-only with brokerage tools. |

---

## Installation

Clone or download this repository, then copy the skill folder into your Codex skills directory.

**macOS / Linux**

```bash
mkdir -p ~/.codex/skills
cp -R investment-council-challenge ~/.codex/skills/
```

**Windows PowerShell**

```powershell
New-Item -ItemType Directory -Path "$env:USERPROFILE\.codex\skills" -Force
Copy-Item -Recurse .\investment-council-challenge "$env:USERPROFILE\.codex\skills\"
```

Restart Codex if the skill does not appear immediately.

### Invocation

This skill deliberately disables implicit activation. Invoke it explicitly:

```text
$investment-council-challenge Challenge this thesis: ...
```

---

## Optional integrations

The skill can use the Interactive Brokers (IBKR) connector for read-only market data and, only when the user explicitly requests it, relevant portfolio context. Without IBKR it falls back to public web sources and omits account context.

IBKR access is never required for installation. The skill prohibits creating, changing, or submitting orders and does not modify alerts, watchlists, or account state.

---

## Important notice

This repository provides analytical workflows, not investment advice or trade execution. Market data may be delayed, incomplete, or unavailable. Users remain responsible for independently verifying information and for all investment decisions.

---

## Maintenance and updates

Each skill is periodically reviewed as Codex models, tools, and connectors evolve. See the skill's `CHANGELOG.md` for version history.

---

## License

MIT. Use, fork, modify, and integrate these skills in your own tools. Attribution is appreciated but not required.

---

## Contact

[@bizarcoin](https://x.com/bizarcoin) on X.
