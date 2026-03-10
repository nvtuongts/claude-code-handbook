# claude-code-handbook

Experimenting with Claude Code in daily software engineering — workflows, slash commands, skills, agents, and MCP integrations.

---

## Contents

| File | Description | Status |
|---|---|---|
| [`workflow-basics.md`](01-workflow-basics.md) | 5-phase workflow, global slash commands, per-project setup | ✅ Available |
| `skills.md` | Writing and managing reusable skills | 🔜 Coming soon |
| `agents.md` | Building specialized subagents | 🔜 Coming soon |
| `mcps.md` | MCP server setup and integrations | 🔜 Coming soon |

---

## The Workflow

```
REQUIREMENTS → CLARIFY → PLAN → IMPLEMENT → VERIFY
```

Six global slash commands encode the full workflow and work across every project:

```bash
/start                    # orient to the project
/clarify <feature>        # agree on requirements
/plan <feature>           # write implementation plan, wait for approval
/implement <feature>      # execute plan task by task
/verify <feature>         # run all checks, save report
/new-project              # bootstrap workflow structure in any new project
```

---

## License

MIT
