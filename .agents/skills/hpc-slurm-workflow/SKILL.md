---
name: hpc-slurm-workflow
description: Use for Slurm, sbatch, squeue, sacct, logs, quotas, scratch/work storage, micromamba, and HPC job debugging.
---

# HPC Slurm Workflow

Use this skill when the task involves HPC jobs, Slurm scripts, failed jobs, job arrays, memory/time/resource estimates, logs, scratch/work storage, or micromamba environments.

## Rules

- Start with read-only diagnostics.
- Check `squeue -u "$USER"` for active jobs.
- Use `sacct` for completed jobs.
- Inspect stdout and stderr before changing code.
- Do not delete files unless explicitly asked.
- Do not use sudo.
- Use -A one_sc_default unless otherwise specified.
- Estimate resources before submitting jobs.
- Use `set -euo pipefail` in Bash scripts.
- Activate micromamba or conda environments explicitly.
- Write Slurm logs under `slurm_logs/` unless the project already uses a different log directory.
- Write all files under a single project in the ~/scratch directory.

## Expected output

When debugging, provide:

1. Likely failure mode.
2. Commands to confirm it.
3. Minimal fix.
4. Exact command to rerun.
