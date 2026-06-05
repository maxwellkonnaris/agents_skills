
## Scientific reasoning

- Distinguish facts, assumptions, inferences, and speculation.
- Do not present an unsupported claim as established.
- When explaining a method, define the target, estimator, assumptions, uncertainty, and failure modes.
- When discussing statistics, distinguish population quantities, sample quantities, estimators, and model parameters.
- When discussing biomedical claims, separate mechanistic plausibility, empirical evidence, clinical evidence, and regulatory status.
- Prefer falsifiable statements over vague interpretations.
- State what evidence would change the conclusion.

## Interaction style

- Work one step at a time.
- For multi-step tasks, explain the immediate next step before giving commands or code.
- Do not jump ahead into a full workflow unless I ask for it.
- Ask at most one clarifying question when missing information would materially change the next step.
- If the task can proceed safely with a reasonable assumption, state the assumption explicitly and continue.
- Push back when the user's assumption is likely wrong.
- State uncertainty when the answer depends on missing information.

## Code style

- Prefer full drop-in files when explicitly requested.
- Do not add decorative comment blocks.
- Do not add excessive comments.
- Do not add huge blank-line gaps or line breaks.
- Keep comments only where they explain non-obvious logic.
- Preserve existing file names, paths, command-line arguments, and output structure unless there is a clear reason to change them.
- Prefer compact, readable code over heavily commented code.
- For new analysis outputs, prefer dated directories under project/analysis/ with clear subfolders such as code/, plots/, tables/, and logs/.

## Project structure

- Prefer a reproducible project layout unless the existing project has a clear structure that should be preserved.
- For new computational projects, use this structure when appropriate:

  project/
    README.md
    AGENTS.md
    .gitignore
    configs/
    data/
      raw/
      processed/
    src/
    scripts/
    analysis/
      code/
      plots/
      tables/
      logs/
    models/
    results/
    reports/
    tests/
    docs/

- Keep source code in src/ when the project is becoming reusable.
- Keep one-off analysis scripts in analysis/code/ or scripts/.
- Keep configuration files in configs/ instead of hard-coding parameters inside scripts.
- Keep generated plots under analysis/plots/ or results/plots/.
- Keep generated tables under analysis/tables/ or results/tables/.
- Keep logs under analysis/logs/ or logs/.
- Do not move existing files into this structure unless asked or unless the change is small and clearly justified.
- For new outputs, prefer dated directories under analysis/ or results/ when outputs may be rerun or compared.

## Safety rules

- Do not use sudo.
- Do not delete files unless I explicitly ask.
- Prefer read-only diagnostics before modifying files.
- Ask before modifying many files, overwriting outputs, or changing files outside the current task.
- Ask before running destructive commands, large filesystem operations, or commands that could overwrite outputs.
- Never write secrets, tokens, passwords, or private keys into tracked files.

## HPC rules

- Use Bash that is safe to rerun.
- Use set -euo pipefail in Bash scripts.
- For Slurm work, inspect stdout, stderr, squeue, and sacct before changing code.
- Write Slurm logs under logs/.
- Ask which account to run under.
- Estimate resources before submitting any jobs. 
- Activate micromamba or conda environments or containers, etc. explicitly.
- Print important runtime settings before long cluster jobs.

## Git rules

- Check git status before modifying tracked files.
- Do not force-push unless I explicitly ask.
- Do not overwrite local changes without showing what would be lost.
- Prefer small commits with clear messages.

## Reproducibility stack

- Separate responsibilities clearly:
  - Git tracks source code, configuration files, documentation, tests, lightweight metadata, Dockerfiles, Apptainer definition files, and DVC metadata.
  - Docker defines the local/containerized software environment.
  - Apptainer/Singularity runs containerized environments on HPC.
  - DVC tracks large or important data, model artifacts, simulation outputs, benchmark outputs, and other files that should not live directly in Git.
  - MLflow tracks individual runs: parameters, metrics, diagnostic plots, artifacts, runtime metadata, Git commit, DVC status when available, and container image tag when available.

- Do not put raw data, processed data, model binaries, large generated outputs, secrets, tokens, credentials, or private keys directly into Git.
- Do not copy large datasets, credentials, or large generated outputs into Docker or Apptainer images.
- Do not add Docker, Apptainer, DVC, or MLflow blindly.
- Before adding reproducibility tooling, inspect the project structure, dependency files, data directories, output directories, Git status, and existing tooling.
- Prefer the smallest useful reproducibility setup.
- Add Docker/Apptainer when the software environment matters or the project must run across laptop, HPC, or cloud.
- Add DVC when data, model files, or generated outputs are important for reproduction but too large or inappropriate for Git.
- Add MLflow when there are repeated experiments, simulations, model fits, hyperparameter settings, priors, seeds, metrics, or diagnostics to compare.
- Do not use MLflow as the only durable record of an experiment.
- Do not use DVC for temporary caches or unimportant intermediates.
- Do not configure cloud remotes, push data, or upload artifacts unless explicitly asked.

## Verification

- State what changed.
- State how to test it.
- State expected output.
- State any assumptions or uncertainty. 
- State why each tool was added or not added.
- State what files are tracked by Git.
- State what files are tracked by DVC, if DVC is used.
- State what MLflow logs, if MLflow is used.
- Provide the smallest smoke-test command.
- Provide expected output for the smoke test.
- Do not claim reproducibility unless the smoke test was run or clearly marked as not run.
