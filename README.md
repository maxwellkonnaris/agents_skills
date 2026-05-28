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

## Token-efficient Codex workflows

The goal is not to minimize tokens at all costs. The goal is to maximize useful context per token.

Codex works best when context is layered:

    AGENTS.md
      Small, always-on rules.

    Skills
      Focused workflows loaded only when relevant.

    References
      Longer background files searched or opened only when needed.

    Scripts
      Deterministic helpers for search, audit, testing, and verification.

    Prompt
      The immediate task: goal, context, constraints, and done-when criteria.

### What spends tokens

The main token costs are:

- always-loaded instructions, especially `AGENTS.md`
- selected skill bodies
- task prompts
- files Codex reads
- command output
- logs
- diffs
- model output
- subagents or parallel workers

The biggest avoidable waste is usually not `AGENTS.md`. It is dumping huge files, huge logs, full PDFs, or entire repositories into context.

### Rules for efficient context

Prefer targeted inspection over broad dumping.

Good:

    git status --short
    git diff --stat
    git diff --name-only
    rg "pattern" path/
    sed -n '120,220p' file.R
    tail -n 100 slurm_logs/job.err
    grep -n -A 20 -B 10 "Error" file.log

Avoid:

    cat huge_log.out
    cat entire_large_file.R
    ls -R .
    reading full PDFs before searching
    printing every generated output file

### How to write efficient prompts

Use this structure:

    Goal:
      What should Codex accomplish?

    Context:
      What repo, files, error, branch, or workflow matters?

    Constraints:
      What should Codex avoid changing?

    Done when:
      What output, test, commit, or check proves the task is complete?

Example:

    Goal:
      Audit this R package for scientific correctness.

    Context:
      Focus on R/, DESCRIPTION, tests/, README, parser contracts, and package API.

    Constraints:
      Do not edit files yet. Do not touch large data files. Work in scratch.

    Done when:
      Return major issues, smallest safe first PR, tests to add, and files not to touch.

### AGENTS.md policy

Keep `AGENTS.md` short because it is always loaded.

Use it for:

- durable behavior rules
- safety rules
- coding preferences
- verification expectations
- stable project conventions

Do not use it for:

- long tutorials
- full workflows
- domain textbooks
- giant examples
- every edge case

If a rule becomes a procedure, move it into a skill.

### Skill policy

Use focused skills instead of one giant skill.

Good:

    statistical-methods-reviewer
    math-derivation-checker
    paper-deep-dive
    literature-map
    hpc-slurm-workflow
    git-reproducible-workflow

Bad:

    one huge general scientist skill that tries to do everything

A good skill has:

- a precise name
- a trigger-oriented description
- one repeatable workflow
- clear rules
- clear output format
- optional scripts or references

### Reference policy

Search first. Read later.

For article repositories, papers, logs, or private references:

    metadata -> search -> relevant snippets -> targeted file read -> summary

Do not bulk-read private references or full paper folders. Use helpers such as:

    search_references "query terms"

Then inspect only the files that are relevant.

### Reasoning level policy

Use lower reasoning for small tasks and higher reasoning for hard tasks.

    Low:
      simple command, README edit, small explanation

    Medium:
      script writing, one failing test, moderate refactor

    High:
      package audit, Slurm debugging, statistical method review

    Extra high:
      deep literature synthesis, derivation audit, research design

Do not use maximum reasoning for every task.

### Planning policy

Use plan-first for complex or ambiguous work:

- package refactors
- multi-file changes
- method audits
- simulation studies
- HPC workflow redesign
- literature synthesis

Do not use plan-first for tiny edits or one-command fixes.

### Operating principle

High-quality Codex output comes from structured context, not maximum context.

Use:

    small AGENTS.md
    precise skill descriptions
    focused skill bodies
    search-before-read scripts
    targeted command output
    plan-first for complex tasks
    reasoning level matched to difficulty
    short verification loops
    small commits
