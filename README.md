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
| [req-dev](skills/req-dev/) | 需求开发 — 从模糊需求到可落地的方案和任务拆解 |
| [code-refactor](skills/code-refactor/) | 代码重构 — 改善结构、可读性、可维护性 |
| [code-review-zh](skills/code-review-zh/) | 代码检视 — 逐层审查正确性、健壮性、性能 |
| [arch-design](skills/arch-design/) | 方案设计 — 架构决策、API/CRD 设计、ADR |

## Installation

### Via CC Switch

1. Open CC Switch → **Skills** → **Repository Management**
2. Add repository:
   - Owner: `your-github-username`
   - Name: `my-claude-skills`
   - Branch: `master`
3. Refresh the skills list
4. Click **Install** on each skill you want

### Via Claude Code CLI

Skills in `~/.claude/skills/` are auto-discovered. Clone this repo and symlink:

```bash
git clone https://github.com/your-github-username/my-claude-skills.git
ln -sf "$(pwd)/my-claude-skills/skills"/* ~/.claude/skills/
```

## Prompts

System prompt presets for CC Switch. Import via **Prompts** panel → **+** → paste content → **Save**.

| Prompt | Description |
|--------|-------------|
| [combined](prompts/combined.md) | 综合提示词 — 需求开发、方案设计、代码重构、代码检视（推荐） |
| [req-dev](prompts/req-dev.md) | 需求开发 — 需求澄清、技术方案、任务拆解 |
| [code-refactor](prompts/code-refactor.md) | 代码重构 — 改善结构、可读性，不改业务逻辑 |
| [code-review](prompts/code-review.md) | 代码检视 — P0/P1/P2 分级审查 |
| [arch-design](prompts/arch-design.md) | 方案设计 — 架构决策、ADR、CRD 设计 |

## License

MIT
