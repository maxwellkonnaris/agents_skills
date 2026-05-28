# agents_skills

Portable Codex configuration for scientific, computational, and HPC work.

This repository is the source of truth for my Codex setup across machines. It stores always-on instructions, reusable skills, optional configuration, and later specialist subagents used for agentic scientific workflows.

## Why this repo exists

Agentic coding tools work better when they have stable context. Repeating the same instructions in every prompt is brittle. This repo makes those instructions explicit, version-controlled, and portable.

The goal is not to create one giant prompt. The goal is to separate the workflow into layers:

    AGENTS.md          = always-on behavior and rules
    .agents/skills/    = reusable task workflows
    .codex/config.toml = machine or project defaults
    .codex/agents/     = optional specialist subagents

This keeps the system understandable. General rules stay small. Detailed workflows live in skills. Machine-specific settings stay in config. Specialist agents are added only when needed.

## Repository structure

    agents_skills/
    ├── README.md
    ├── AGENTS.md
    ├── .agents/
    │   └── skills/
    │       ├── r-package-methods-check/
    │       │   └── SKILL.md
    │       ├── scientific-writing/
    │       │   └── SKILL.md
    │       └── python-rust-package-methods-check/
    │           └── SKILL.md
    └── .codex/
        ├── config.toml
        └── agents/

## 1. AGENTS.md

`AGENTS.md` is the always-on instruction layer.

Use it for rules that should apply almost every time Codex works:

- communication style
- safety rules
- coding preferences
- Git rules
- HPC defaults
- verification habits
- scientific reasoning standards

Examples:

    Work one step at a time.
    Do not delete files unless explicitly asked.
    For Slurm work, inspect logs before changing code.
    Distinguish facts, assumptions, inferences, and speculation.
    Prefer full drop-in scripts when explicitly requested.

Do not put long workflows in `AGENTS.md`. If the file starts becoming a manual, move the detailed procedure into a skill.

Good rule for `AGENTS.md`:

    For Slurm work, inspect stdout, stderr, squeue, and sacct before changing code.

Bad rule for `AGENTS.md`:

    A 200-line guide for every possible Slurm failure mode.

That belongs in a skill.

## 2. .agents/skills/

Skills are reusable workflows.

A skill is a folder with a required `SKILL.md` file:

    .agents/skills/example-skill/
    └── SKILL.md

Each `SKILL.md` starts with metadata:

    ---
    name: example-skill
    description: Use when Codex should apply this specific workflow.
    ---

Use skills for repeated tasks such as:

- reviewing statistical code
- debugging Slurm jobs
- checking R package methods
- reviewing Python or Rust scientific packages
- revising scientific writing
- designing simulation benchmarks
- reading papers systematically

Skills should be narrow. A good skill answers:

    When should this skill trigger?
    What should Codex check?
    What should Codex avoid?
    What output format should Codex use?
    What counts as done?

Current skills:

### r-package-methods-check

Reviews R package changes for statistical correctness, numerical robustness, API stability, tests, and documentation.

Use for:

- method changes
- inference-affecting bug fixes
- numerical refactors
- R package pre-PR review

### scientific-writing

Improves scientific prose by focusing on reader comprehension, sentence structure, paragraph flow, claim placement, definitions, and precision.

Use for:

- manuscript paragraphs
- methods sections
- results sections
- technical explanations
- grant or paper prose

### python-rust-package-methods-check

Reviews Python packages, Rust crates, and mixed scientific software for correctness, numerical robustness, APIs, tests, performance, and docs.

Use for:

- Python packages
- Rust crates
- scientific CLIs
- simulation code
- numerical methods
- Python/Rust bindings

## 3. .codex/config.toml

`.codex/config.toml` is the repo-level Codex configuration layer.

Use it for project or machine defaults such as:

- sandbox mode
- approval policy
- model or reasoning settings
- MCP servers
- feature flags
- project-specific Codex behavior

Do not put scientific workflows in `config.toml`. Put workflows in skills. Do not put general behavior rules in `config.toml`. Put those in `AGENTS.md`.

Config answers:

    How should Codex run?
    What permissions should it have?
    What tools or profiles should it use?

`AGENTS.md` answers:

    How should Codex behave?

Skills answer:

    How should Codex do this specific kind of task?

## 4. .codex/agents/

`.codex/agents/` is for optional custom subagents.

Subagents are specialist workers. They are useful when a task benefits from a separate role, such as:

- read-only code reviewer
- test writer
- documentation reviewer
- security reviewer
- HPC script reviewer
- literature review assistant

Do not start with subagents. Start with `AGENTS.md` and skills. Add subagents only when a workflow needs a separate specialist with a narrower job and clearer boundaries.

A subagent should have:

    name
    description
    developer_instructions

Use subagents when you want role separation. Use skills when you want reusable instructions.

## Mental model

The system should stay layered:

    Prompt
      The immediate task.

    AGENTS.md
      Stable behavior that applies broadly.

    Skill
      Specific workflow for the task type.

    Reference files
      Longer background information, if needed.

    Scripts
      Deterministic commands that can be run or edited.

    Subagent
      Optional specialist worker for separate review or parallel work.

This prevents context bloat. It also makes the setup easier to debug. If Codex behaves badly, ask which layer caused the behavior:

    Was the prompt unclear?
    Was AGENTS.md too broad?
    Was the wrong skill triggered?
    Was a skill too vague?
    Was config too permissive or too restrictive?
    Was a subagent needed?

## How to add a new skill

Create a folder:

    mkdir -p .agents/skills/new-skill-name

Create the skill file:

    cat > .agents/skills/new-skill-name/SKILL.md <<'SKILL_EOF'
    ---
    name: new-skill-name
    description: Use when this exact workflow is needed.
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
    3. Smallest fix
    4. Tests
    SKILL_EOF

Then check it:

    find .agents/skills -mindepth 2 -maxdepth 2 -name SKILL.md -print

    for f in .agents/skills/*/SKILL.md; do
      echo "===== $f ====="
      grep -E '^(name|description):' "$f"
    done

Commit it:

    git status
    git add .agents/skills
    git commit -m "Add new Codex skill"
    git push

## How to decide where something belongs

Use this rule:

    If it should apply almost every time, put it in AGENTS.md.
    If it is a repeatable workflow, make it a skill.
    If it is a separate specialist role, make it a subagent.
    If it is a machine/runtime setting, put it in config.toml.

Examples:

| Need | Where it belongs |
|---|---|
| Do not delete files unless asked. | `AGENTS.md` |
| Review R package method changes for statistical correctness. | Skill |
| Write tests as a separate reviewer. | Subagent |
| Use a specific sandbox or approval mode. | `.codex/config.toml` |
| Debug Slurm jobs using logs and sacct. | Skill |
| Always distinguish facts from assumptions. | `AGENTS.md` |
| Run a read-only security review. | Subagent or skill, depending on how separate it needs to be |

