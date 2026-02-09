# Slurm Cheatsheet

## Job Submission
| Command | Description |
| :--- | :--- |
| `sbatch script.sh` | Submit a batch script for background execution |
| `srun <command>` | Run a command (typically used for parallel steps or interactive sessions) |
| `salloc` | Allocate resources for an interactive session |
| `srun --pty bash -i` | Start an interactive bash session on a compute node |

## Common `#SBATCH` Options
Add these to the top of your submission script:

| Option | Description | Example |
| :--- | :--- | :--- |
| `--job-name` | Name of the job | `#SBATCH --job-name=align` |
| `--output` | Standard output file (`%j` is JobID) | `#SBATCH --output=logs/%j.out` |
| `--error` | Standard error file | `#SBATCH --error=logs/%j.err` |
| `--nodes` | Number of nodes to request | `#SBATCH --nodes=1` |
| `--ntasks` | Number of tasks (usually MPI ranks) | `#SBATCH --ntasks=1` |
| `--cpus-per-task` | CPUs per task (for multithreading/OpenMP) | `#SBATCH --cpus-per-task=8` |
| `--mem` | Memory per node | `#SBATCH --mem=32G` |
| `--time` | Time limit (D-HH:MM:SS) | `#SBATCH --time=04:00:00` |
| `--partition` | Partition/Queue to submit to | `#SBATCH --partition=compute` |
| `--mail-type` | Email notification events (BEGIN, END, FAIL, ALL) | `#SBATCH --mail-type=END` |
| `--mail-user` | Email address for notifications | `#SBATCH --mail-user=you@example.com` |

## Monitoring & Managing Jobs
| Command | Description |
| :--- | :--- |
| `squeue -u $USER` | Show status of your jobs |
| `sinfo -p <partition>` | View status of nodes in a specific partition |
| `scancel <jobid>` | Cancel a specific job |
| `scancel -u $USER` | Cancel all of your jobs |
| `scancel -t PENDING -u $USER` | Cancel all your pending jobs |
| `scontrol show job <jobid>` | Detailed info on a running or recently finished job |
| `sacct -j <jobid>` | Historical job data (CPU, memory usage, etc.) |

## Checking Resource Usage
To see how much memory a finished job actually used:
```bash
sacct -j <jobid> --format=JobID,JobName,MaxRSS,Elapsed,State
```

## Useful Environment Variables
These are available inside your Slurm script:
- `$SLURM_JOB_ID`: The ID of the current job.
- `$SLURM_SUBMIT_DIR`: The directory from which `sbatch` was called.
- `$SLURM_NODELIST`: List of nodes allocated to the job.
- `$SLURM_NTASKS`: Number of tasks requested.
- `$SLURM_CPUS_PER_TASK`: Number of CPUs per task requested.

