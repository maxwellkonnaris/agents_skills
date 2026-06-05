# agents_skills

Portable Codex instructions, skills, and helper scripts for scientific computing workflows.

This repo keeps agent behavior version-controlled instead of rewriting the same instructions in every prompt.

## Repository structure

```text
agents_skills/
├── AGENTS.md
├── .agents/
│   └── skills/
│       ├── hpc-slurm-workflow/
│       ├── learning-deep-dive/
│       ├── python-rust-package-methods-check/
│       ├── r-package-methods-check/
│       ├── researchassistant/
│       └── scientific-writing/
├── scripts/
│   └── search_references
├── README.md
└── LICENSE
```

## Layers

### `AGENTS.md`

Always-on instructions.

Use this for rules that should apply broadly:

- scientific reasoning standards
- communication style
- coding preferences
- Git safety
- HPC defaults
- verification habits

Keep `AGENTS.md` short. If a rule becomes a detailed workflow, move it into a skill.

### `.agents/skills/`

Reusable task-specific workflows.

Each skill has its own folder and a `SKILL.md` file.

| Skill | Use it for |
|---|---|
| `hpc-slurm-workflow` | Slurm jobs, logs, quotas, scratch/work storage, micromamba, and HPC debugging |
| `learning-deep-dive` | Systematic learning for statistics, probability, causal inference, ML, math, computational biology, and technical papers |
| `python-rust-package-methods-check` | Python/Rust scientific package review, numerical robustness, APIs, tests, docs, and performance |
| `r-package-methods-check` | R package method review, statistical correctness, numerical stability, tests, and docs |
| `researchassistant` | Computational/statistical study planning, debugging, validation, simulation workflows, scaling, and interpretation |
| `scientific-writing` | Scientific prose revision for clarity, structure, precision, and reader comprehension |

### `scripts/`

Deterministic helper scripts.

| Script | Purpose |
|---|---|
| `search_references` | Search a local cached references repository by filename and text |

## Install into a project

Copy the instructions and skills into a project root:

```bash
cp AGENTS.md /path/to/project/AGENTS.md
cp -R .agents /path/to/project/.agents
```

Optional helper scripts:

```bash
mkdir -p /path/to/project/scripts
cp scripts/search_references /path/to/project/scripts/search_references
chmod +x /path/to/project/scripts/search_references
```

Then run Codex from the project root.

## When to add a new skill

Add a skill only when the workflow is repeated enough to justify its own file.

Good reasons:

- the task has a clear trigger
- the workflow has multiple steps
- the same checks are needed repeatedly
- the output format should be standardized
- putting the workflow in `AGENTS.md` would make that file too large

Do not add a skill for one-off preferences, tiny edits, or rules that should apply almost every time.

## Skill template

```bash
mkdir -p .agents/skills/new-skill-name

cat > .agents/skills/new-skill-name/SKILL.md <<'EOF'
---
name: new-skill-name
description: Use when this specific workflow is needed.
---

# New Skill Name

Use this skill when...

## Rules

- Rule 1
- Rule 2
- Rule 3

## Output

Return:

1. Summary
2. Major issues
3. Smallest safe next step
4. Verification
EOF
```

Check skills:

```bash
find .agents/skills -mindepth 2 -maxdepth 2 -name SKILL.md -print

for f in .agents/skills/*/SKILL.md; do
  echo "===== $f ====="
  grep -E '^(name|description):' "$f"
done
```

## Placement rule

| Instruction type | Put it in |
|---|---|
| Broad behavior that should apply almost always | `AGENTS.md` |
| Repeated task workflow | `.agents/skills/<skill-name>/SKILL.md` |
| Deterministic command/helper | `scripts/` |
| One-time task detail | The prompt |
| Long background material | Reference files |

## Token discipline

Prefer targeted inspection:

```bash
git status --short
git diff --stat
git diff --name-only
rg "pattern" path/
sed -n '120,220p' file
tail -n 100 logs/job.err
```

Avoid broad dumping:

```bash
cat huge_log.out
cat entire_large_file
ls -R .
```

## Prompt pattern

```text
Goal:
  What should be done?

Context:
  What repo, files, error, method, or workflow matters?

Constraints:
  What should not be changed?

Done when:
  What output, test, commit, or check proves the task is complete?
```

## Operating principle

```text
Prompt      = immediate task
AGENTS.md   = stable behavior
Skill       = task-specific workflow
Reference   = longer background material
Script      = deterministic helper
```

Small always-on rules plus focused skills are easier to maintain than one giant instruction file.
