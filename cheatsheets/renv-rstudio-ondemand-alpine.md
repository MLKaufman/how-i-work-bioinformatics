# Using renv with RStudio OnDemand on Alpine

This guide combines the existing RStudio setup process with `renv` for reproducible project environments on the Alpine HPC cluster.

## Prerequisites

- Access to Alpine cluster
- Sufficient space in your `/projects/$USER` directory (not `/home`)
- Completed the [OS library dependencies setup](https://github.com/kf-cuanschutz/CU-Anschutz-HPC-documentation/tree/main/Rstudio_related_scripts) (one-time setup)

---

## Step 1: Set Up RStudio OnDemand Session

1. **Login** to OnDemand portal: https://ondemand-rmacc.rc.colorado.edu

2. Navigate to `Interactive Apps` → `RStudio Server (Presets)`

3. **Configure your session**:
   - Account: `amc-general`
   - Partition: `ahub`
   - R version: `4.4.1` (or latest available)
   - Cores: as needed (max 16 for OnDemand)
   - Time: as needed

4. Click `Launch` and wait for resources to be allocated

5. Click `Connect to RStudio Server` when ready

---

## Step 2: Configure Your Project Directory

1. **Open the Terminal** in RStudio (Tools → Terminal → New Terminal)

2. **Create a project directory** in your `/projects` space:
   ```bash
   mkdir -p /projects/$USER/my_renv_project
   cd /projects/$USER/my_renv_project
   ```

3. **Set this as your working directory** in RStudio:
   - Go to: Tools → Global Options → General
   - Under "Default working directory", click Browse
   - Navigate to `/projects/$USER/my_renv_project`
   - Click "Select" and then "Apply"

---

## Step 3: Initialize renv in Your Project

1. **In the RStudio Console**, install `renv` (if not already installed):
   ```r
   install.packages("renv")
   ```

2. **Initialize renv** in your project:
   ```r
   renv::init()
   ```

   This creates:
   - `renv/` directory (stores project-specific libraries)
   - `renv.lock` file (tracks package versions)
   - `.Rprofile` (activates renv automatically)

3. **Configure renv cache location** (important for Alpine):
   
   Create or edit `~/.Renviron` in your home directory:
   ```r
   # In RStudio Console
   file.edit("~/.Renviron")
   ```
   
   Add the following line to use your projects directory for the cache:
   ```bash
   RENV_PATHS_CACHE=/projects/$USER/.renv_cache
   ```
   
   **Restart your R session**: Session → Restart R

---

## Step 4: Install Packages with renv

1. **Install packages as usual**:
   ```r
   install.packages("dplyr")
   install.packages("ggplot2")
   
   # For Bioconductor packages
   renv::install("bioc::DESeq2")
   ```

2. **Snapshot your environment**:
   ```r
   renv::snapshot()
   ```
   
   This updates `renv.lock` with all current package versions.

---

## Step 5: Using renv Across Sessions

### Starting a New Session

When you start a new RStudio OnDemand session:

1. Set your working directory to your project: `/projects/$USER/my_renv_project`
2. renv will automatically activate (via `.Rprofile`)
3. Your project-specific packages will be available

### Restoring a Project on a New Machine or After Time

```r
renv::restore()
```

This reinstalls all packages from `renv.lock` at the exact versions recorded.

---

## Step 6: renv Best Practices on Alpine

### Cache Configuration

Since `/home` has limited space (2GB), configure renv to use your projects directory:

**Option A: Global cache (shared across all projects)**
```bash
# In ~/.Renviron
RENV_PATHS_CACHE=/projects/$USER/.renv_cache
```

**Option B: Per-project cache**
```r
# In your project's .Rprofile (created by renv::init())
Sys.setenv(RENV_PATHS_CACHE = "/projects/$USER/my_renv_project/renv/cache")
```

### Working with Scratch Space

If using scratch space (`/gpfs/alpine1/scratch/$USER`):

```r
# Set temporary directory for package installations
Sys.setenv(TMPDIR = "/gpfs/alpine1/scratch/$USER/tmp")
dir.create(Sys.getenv("TMPDIR"), showWarnings = FALSE, recursive = TRUE)
```

### Version Control Integration

Add to your `.gitignore`:
```
renv/library/
renv/staging/
.Rproj.user/
.Rhistory
.RData
```

**DO commit**:
- `renv.lock`
- `renv/activate.R`
- `renv/settings.json`
- `.Rprofile`

---

## Step 7: Using renv Projects with Slurm

For long-running jobs or jobs needing >16 cores, use the Slurm container approach:

1. **Create your R script** (e.g., `analysis.R`) in your renv project directory:
   ```r
   # analysis.R
   # renv will auto-activate via .Rprofile
   library(dplyr)
   library(ggplot2)
   
   # Your analysis code here
   ```

2. **Create a Slurm script** (e.g., `run_analysis.slurm`):
   ```bash
   #!/bin/bash
   #SBATCH --nodes=1
   #SBATCH --ntasks=32
   #SBATCH --time=04:00:00
   #SBATCH --partition=amilan
   #SBATCH --qos=normal
   #SBATCH --output=analysis_%j.out
   
   # Set up environment
   export ALPINE_SCRATCH=/gpfs/alpine1/scratch/$USER
   export APPTAINER_TMPDIR=$ALPINE_SCRATCH/apptainer/tmp
   export APPTAINER_CACHEDIR=$ALPINE_SCRATCH/apptainer/cache
   mkdir -pv $APPTAINER_CACHEDIR $APPTAINER_TMPDIR
   
   # Set R version
   export r_app_version="4.4.1"
   
   # Run R script in container with renv project
   cd /projects/$USER/my_renv_project
   
   apptainer exec \
     --bind /projects,/scratch/alpine,$CURC_CONTAINER_DIR_OOD \
     --fakeroot \
     --overlay /projects/$USER/.rstudioserver/rstudio-${r_app_version}/rstudio-server-${r_app_version}_overlay.img:ro \
     /curc/sw/containers/open_ondemand/rstudio-server-${r_app_version}.sif \
     Rscript analysis.R
   ```

3. **Submit the job**:
   ```bash
   sbatch run_analysis.slurm
   ```

---

## Step 8: Sharing Your renv Project

### With Collaborators

1. **Share your project directory** (or git repository) including:
   - `renv.lock`
   - `.Rprofile`
   - `renv/activate.R`

2. **Collaborator setup**:
   ```r
   # After opening the project
   renv::restore()  # Installs all packages from renv.lock
   ```

### Archiving Projects

When archiving, renv keeps your environment reproducible:

```r
# Check project status
renv::status()

# Ensure everything is captured
renv::snapshot()

# Create a portable archive
tar -czf my_project_archive.tar.gz my_renv_project/
```

---

## Troubleshooting

### Issue: Package installation fails due to missing system dependencies

**Solution**: Ensure you've completed the [OS library dependencies setup](https://github.com/kf-cuanschutz/CU-Anschutz-HPC-documentation/tree/main/Rstudio_related_scripts)

### Issue: "/home directory full" error

**Solution**: Check that `RENV_PATHS_CACHE` is set to use `/projects`:
```r
Sys.getenv("RENV_PATHS_CACHE")
# Should show: /projects/$USER/.renv_cache
```

### Issue: renv not activating automatically

**Solution**: Ensure you're setting your working directory to the project root (where `.Rprofile` exists):
```r
setwd("/projects/$USER/my_renv_project")
source(".Rprofile")
```

### Issue: Slow package installation

**Solution**: Use the Alpine scratch space for temporary files:
```r
Sys.setenv(TMPDIR = "/gpfs/alpine1/scratch/$USER/tmp")
```

---

## Additional renv Commands

```r
# View project status
renv::status()

# Update a specific package
renv::update("dplyr")

# Update all packages
renv::update()

# Remove unused packages
renv::clean()

# Deactivate renv (temporary)
renv::deactivate()

# Reactivate renv
renv::activate()

# View package history
renv::history()

# Rollback to previous state
renv::revert()
```

---

## Summary

This protocol allows you to:
- ✅ Use renv with RStudio OnDemand on Alpine
- ✅ Keep reproducible project environments
- ✅ Share projects with exact package versions
- ✅ Scale to Slurm for larger jobs
- ✅ Avoid filling up your `/home` directory

**Key Points**:
1. Always use `/projects/$USER` for your projects and renv cache
2. Initialize renv within your project directory
3. Commit `renv.lock` to version control
4. Use `renv::snapshot()` regularly to capture your environment

---

## References

- [renv Documentation](https://rstudio.github.io/renv/)
- [CU Anschutz HPC Documentation](https://github.com/kf-cuanschutz/CU-Anschutz-HPC-documentation)
- [Alpine OnDemand Portal](https://ondemand-rmacc.rc.colorado.edu)

---

**Created**: 2026-02-03  
**Author**: Based on CU Anschutz Alpine HPC documentation