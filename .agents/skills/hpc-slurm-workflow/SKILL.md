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
- Estimate resources before submitting jobs.
- Use `set -euo pipefail` in Bash scripts.
- Activate micromamba or conda environments explicitly.
- Write Slurm logs under `slurm_logs/` unless the project already uses a different log directory.
- Write all generated HPC outputs under a single project directory in scratch unless the user specifies another location.

## Containerized HPC Execution

Use this section when the task involves Docker, Apptainer, Singularity, containerized Slurm jobs, or moving a local/container workflow to HPC.

Rules:

- Prefer Apptainer/Singularity for HPC execution.
- Do not use Docker directly on HPC unless the cluster explicitly supports it.
- Do not use sudo.
- Do not copy raw data, processed data, model files, large results, credentials, secrets, tokens, or private keys into container images.
- Use bind mounts for project directories, scratch directories, data directories, result directories, logs, and MLflow run directories.
- Prefer explicit bind mounts over relying on implicit host paths.
- Print the container image path, bind mounts, working directory, config path, output directory, Git commit if available, and random seed if relevant before long jobs.
- Keep Slurm logs under the projects log convention. If none exists, use `slurm_logs/`.
- Test the container with a small smoke command before submitting a full Slurm job.
- Do not submit full Slurm jobs until a small local, interactive, or dry-run test has passed unless the user explicitly overrides.

Expected Apptainer pattern:

    apptainer exec \
      --bind /scratch/$USER/project:/work \
      image.sif \
      python /work/scripts/run.py --config /work/configs/small.yaml

Expected Example Slurm pattern:

    #!/usr/bin/env bash
    #SBATCH -A one_sc_default
    #SBATCH --job-name=project_test
    #SBATCH --cpus-per-task=1
    #SBATCH --mem=4G
    #SBATCH --time=00:10:00
    #SBATCH --output=slurm_logs/%x_%j.out
    #SBATCH --error=slurm_logs/%x_%j.err

    set -euo pipefail

    echo "Job ID: ${SLURM_JOB_ID:-NA}"
    echo "Host: $(hostname)"
    echo "Working directory: $(pwd)"
    echo "Image: /path/to/image.sif"
    echo "Started: $(date)"

    apptainer exec \
      --bind /scratch/$USER/project:/work \
      /path/to/image.sif \
      python /work/scripts/run.py --config /work/configs/small.yaml

    echo "Finished: $(date)"

When debugging a containerized Slurm job, inspect in this order:

1. Slurm stdout and stderr.
2. `sacct` resource usage.
3. Whether the image path exists.
4. Whether bind-mounted host paths exist.
5. Whether paths inside the container match the script.
6. Whether the expected conda/micromamba/container environment is actually active.
7. Whether the failure is a path issue, dependency issue, permission issue, resource issue, or scientific-code issue.

## Expected output

When debugging, provide:

1. Likely failure mode.
2. Commands to confirm it.
3. Minimal fix.
4. Exact command to rerun.
5. Ask follow up question to ensure direction and goal is correct.
