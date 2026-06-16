# My Claude Skills

A collection of custom Claude Code skills tailored for **Go backend development** and **Kubernetes ecosystem** work.

## Skills

| Skill | Description |
|-------|-------------|
| [go-dev](skills/go-dev/) | Go development workflow — build, test, lint, mock, pprof |
| [k8s-debug](skills/k8s-debug/) | Kubernetes troubleshooting — Pod → Workload → Network → Cluster |
| [fix-bug](skills/fix-bug/) | Systematic bug debugging — reproduce, isolate, fix, verify |
| [k8s-operator](skills/k8s-operator/) | Operator development — controller-runtime, CRD, Webhook |
| [pr-prep](skills/pr-prep/) | Pre-PR checklist — auto-detect Go/Java, build, test, lint |

## Installation

### Via CC Switch

1. Open CC Switch → **Skills** → **Repository Management**
2. Add repository:
   - Owner: `your-github-username`
   - Name: `my-claude-skills`
   - Branch: `main`
3. Refresh the skills list
4. Click **Install** on each skill you want

### Via Claude Code CLI

Skills in `~/.claude/skills/` are auto-discovered. Clone this repo and symlink:

```bash
git clone https://github.com/your-github-username/my-claude-skills.git
ln -sf "$(pwd)/my-claude-skills/skills"/* ~/.claude/skills/
```

## License

MIT
