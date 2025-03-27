# MLK-howiwork-roundrobin scRNAseq project management

Notes and prep for how I manage scRNAseq projects for the BBSR round robin

## Local system setup - MacOSX Sequioa 15.1 Intel architecture

Homebrew <https://brew.sh/> is used to manage system wide installations of software and packages.

Example of installed software and packages:

```bash
➜  ~ brew list
==> Formulae
bat			icu4c@76		libvterm		micromamba		reproc
ca-certificates		igv			libx11			mpdecimal		samtools
cairo			jpeg-turbo		libxau			msgpack			simdjson
certifi			libarchive		libxcb			neovim			sqlite
fmt			libb2			libxdmcp		oniguruma		tree-sitter
fontconfig		libdeflate		libxext			openjdk			unibilium
freetype		libgit2			libxrender		openssl@3		unison
gettext			libpng			little-cms2		pcre2			xorgproto
giflib			libsolv			lpeg			pixman			xz
glib			libssh2			luajit			python-packaging	yaml-cpp
graphite2		libtiff			luv			python@3.13		yt-dlp
harfbuzz		libunistring		lz4			rclone			zstd
htslib			libuv			lzo			readline

==> Casks
alacritty	ghostty		kitty		monitorcontrol	quarto		rig		todoist
cyberduck	jordanbaird-ice	macdown		positron	rectangle	termius	
```

Oh My Zsh <https://ohmyz.sh/> is installed to provide additional functionality to Mac ZSH shell.
Hundreds of plugins and themes are available for ZSH. I mostly use for the enhanced tab completion and git integration.

`Rig.app` / `rig` cli is used to manage the local system wide R installation versions and/or architectures and can manage R libraries switching.
System wide R is not typically used, but typically has a minimum of the R packages `renv` and `pak` installed.

Micromamba <https://mamba.readthedocs.io/en/latest/installation/micromamba-installation.html> is a micro binary rust built version of the conda package manager. In the past I have typically used it for managing R and Python non-system wide installs and isolating package libraries.  I have been using it less and less as I have been using `renv` and virtual environments more and more. Still useful for install bioinformatics tools not available in `brew`.

kitty terminal <https://sw.kovidgoyal.net/kitty/> is used as the terminal emulator. It is a fast, feature rich terminal emulator that is highly customizable. Meow.

Termius <https://termius.com/> is a great SSH client with stored connections, credentials, and keys. It is available on all platforms and has a great mobile app.

Github Desktop <https://desktop.github.com/> is used to manage git repositories. I use the command line for most git operations, but the desktop app is useful for visualizing changes and managing repositories.

Cyberduck <https://cyberduck.io/> is used for file transfer to remote servers. It supports a wide range of protocols and is easy to use. I particularly like the editor function that allows me to edit files on the remote server in my local choice of editors.

RStudio <https://www.rstudio.com/> is used as the primary R IDE. It has a wide range of features and is highly customizable. I use it for R Markdown, Shiny, and other R projects.

VS Code <https://code.visualstudio.com/> is used as the primary alternative code editor. It is highly customizable and has a wide range of extensions available. I use it for R, Python, Markdown, and other languages.

`quarto` <https://quarto.org/> for reproducible analysis. Quarto is a markdown based document format that supports R, Python, and Julia code chunks. It is similar to R Markdown, but is language agnostic.

Paperpile <https://paperpile.com/> is used for managing references and citations. It is a great tool for organizing and citing references in manuscripts.

Aliases and functions: (See `.zshrc` dotfile.)

### Local dotfiles

## Remote system setup - Alpine HPCC

```bash
[mika0001_amc@login-ci2 mkaufman]$ tree -L 2
.
├── bin
│   ├── cellranger-8.0.1
│   ├── cellranger-8.0.1.tar.gz
│   ├── cellranger9 -> cellranger-9.0.0/cellranger
│   ├── cellranger-9.0.0
│   ├── cellranger-9.0.0.tar.gz
│   ├── micromamba
│   ├── rclone -> rclone-v1.69.0-linux-amd64/rclone
│   ├── rclone-current-linux-amd64.zip
│   ├── rclone-v1.69.0-linux-amd64
│   ├── unison
│   └── unison-fsmonitor
├── containers
│   ├── quarto-render_scrnaseq.sif
│   └── quarto_scrnaseq
├── micromamba
│   ├── envs
│   └── pkgs
├── projects
│   ├── Cittelly_scRNAseq_Jun2024
│   ├── Henry_scRNAseq_Jan2025
│   └── Pine_mSOX9_OE_scRNASeq_Dec2024
├── refs
│   ├── cellranger_probe_references
│   └── cellranger_references
└── scripts
    ├── create_loupe.sh
    ├── slurm-11086168.out
    └── smb_transfer.sh
```

### Remote dotfiles


## Project initiation

Each project starts with a folder locally in my `~/Projects` directory.

Naming convention: `Henry_scRNAseq_Jan2025`

The project folder is initialized with a `README.md` file that contains the project name and a description of the project.
Example of a `README.md` file: (See example `example_README.md`)

Project is initialized with `git init` and a `.gitignore` file is created to ignore common files and directories that are not needed in the repository.

A `renv` project is initialized with `renv::init()` to create a project specific R library. The `renv` library is used to manage the R library for the project.
#ensure pak is the default package manager

`renv` workflow is `renv::snapshot()` to save the current library state, `renv::restore()` to restore the library state, and `renv::status()` to check the library state.

## scRNAseq project organization

```bash
➜  Henry_scRNAseq_Jan2025 tree -L 3
.
├── README.md
├── analysis
│   ├── 01-scRNAseq-raw_qc.html
│   ├── 01-scRNAseq-raw_qc.qmd
│   ├── 02-scRNAseq-preprocessing.html
│   ├── 02-scRNAseq-preprocessing.qmd
│   ├── 03-scRNAseq-batch_correction.qmd
│   ├── 04-scRNAseq-clustering_annotation.qmd
│   ├── Henry_scRNAseq_Jan2025.Rproj
│   ├── outs_01-scRNAseq-raw_qc
│   │   └── so.rds
│   ├── outs_02-scRNAseq-preprocessing
│   │   └── so.rds
│   ├── outs_03-scRNAseq-batch_correction
│   ├── outs_04-scRNAseq-clustering_annotation
│   ├── renv
│   │   ├── activate.R
│   │   ├── library
│   │   ├── settings.json
│   │   └── staging
│   └── renv.lock
├── metadata
│   └── Henry and Walters scRNAseq 1-14-25.docx
├── processed_data
│   └── cellranger
│       ├── CCR_22_C10
│       ├── CCR_23_C11
│       ├── CCR_25
│       ├── CCR_40_C20
│       └── CCR_41
└── raw_data
    └── FASTQs
```

## Project workflow - scRNAseq

### git workflow

`git init`

Use Rstudio, VS Code, or Github Desktop to manage the git repository.

The typical `.gitignore` file will contain the following:

```.gitignore
# .gitignore
processed_data/
raw_data/

outs_*
*.html

.DS_Store
.Rproj.user
```

### R library management

I used `rig` to manage the R versions on my local system and switch between them.

I use `renv` to manage R libraries for each project. The general work flow for using `renv` is to initialize a new project with `renv::init()`, snapshot the library with `renv::snapshot()`, and restore the library from a lockfile with `renv::restore()`. The library is saved in the project directory and is not system wide.
You run these commands from the R console or cli launched R interpreter in the project directory. `renv::init()` creates a `renv` directory in the project directory that contains the library. It also creates a `renv.lock` file that contains the library state. `renv::snapshot()` saves the current library state to the `renv.lock` file. `renv::restore()` restores the library state from the `renv.lock` file. `renv::status()` checks the library state.

`renv` adds a .Rprofile file to the project directory that contains the line `source("renv/activate.R")`. This line is used to activate the local `renv` library when the project is opened in Rstudio, when the individual R scripts/rmd/quarto files are opened, when the R interpreter is launched in the project directory, or when the quarto cli is used to render the quarto documents.

![renv](figures/renv.png)

`renv::init()` will also optionally scan the project directory for R scripts and add the packages used in the scripts to the library. This is useful for ensuring that all the packages used in the project are in the library.

I use `pak` as the package manager for `renv`. `pak` is a fast and efficient package manager that is a drop in replacement for `install.packages()`. It is used to install packages in the `renv` library. I create a `.Renviron` file in the project directory that contains the line `RENV_CONFIG_PAK_ENABLED = TRUE` to enable `pak` as the package manager for `renv`.

### Quarto document analysis

Utilize `quarto` <https://quarto.org/> for reproducible analysis. Quarto is a markdown based document format that supports R, Python, and Julia code chunks. It is similar to R Markdown, but is language agnostic.

Ideally, each analysis step is a separate `quarto` document that is rendered to HTML or PDF. The rendered document is saved in the `analysis` directory.  
`01-scRNAseq-raw_qc.qmd | 02-scRNAseq-preprocessing.qmd | 03-scRNAseq-batch_correction.qmd | 04-scRNAseq-clustering_annotation.qmd`

### Quarto doc format

Yaml header

```yaml
---
title: "Analysis: scRNAseq Raw QC"
subtitle: "Investigator - Project Title"
author:
  name: "Michael Kaufman, PhD"
  orcid: 0000-0003-2441-5836
  email: michael.kaufman@cuanschutz.edu
  affiliation: 
    name: "University of Colorado Cancer Center"
    url: "https://medschool.cuanschutz.edu/colorado-cancer-center"
abstract:
  ""

editor: source
output: html_document
theme: cosmo

format:
  html:
    embed-resources: true
    self_contained: true
    code-fold: true
    code-summary: "[code]"
    code-overflow: wrap
    toc: false
    toc-location: left    
    grid:
      sidebar-width: 0px
      body-width: 1800px
      margin-width: 100px
      gutter-width: 10px

execute:
  warning: false
  message: false
  fig.align: "center"

params:
    ins_path: "raw_data/FASTQs"
    outs_path: "outs/01-scRNAseq-raw_qc"
    experiment: "mTC11" # mTC11 or mTC11 Lung or MKRC.2
    dims: 20
    mc.cores: 10
---
```

### Parameters

How to use parameters in the quarto document.

```r
```{r}
print(params$experiment)
"mTC11"
```
```

### Piping workflow and execution 

### Executing quarto render
Quarto can be exectued from Rstudio similar to Rmarkdown. However `quarto` is actually also a command line tool that can be executed from the terminal. This is useful for using within bash script loops or running in parallel.

**Simple example:**

```bash
quarto render 01-scRNAseq-raw_qc.qmd --to html --output-dir "outs/01-scRNAseq-raw_qc" --output "01-scRNAseq-raw_qc.html"
```

**Loop expample with passing parameters that override the yaml header:**

```bash
# render clustering and annotation
ANALYSIS="06-scRNAseq-tcell_exploration.qmd"
BASENAME=$(basename "$ANALYSIS" .qmd)

for EXPERIMENT in "mTC11" "mTC11 Lung" "MKRC.2"; do
    mkdir -p "outs/${BASENAME}"
    quarto render "$ANALYSIS" --no-clean --to html --output-dir "outs/${BASENAME}" --output "${BASENAME}_${EXPERIMENT}.html" \
        -P experiment:"${EXPERIMENT}"
done
```

### Quarto tricks

 You can embed HTML / Javascript widgets in the quarto document for interactivity.

 https://quarto.org/docs/interactive/widgets/htmlwidgets.html

https://gallery.htmlwidgets.org/

The major one that I use is the R package `DT` for interactive tables. Can have sorting, searching, filtering, and pagination. Recently I learned that you can add buttons for exporting the table to different formats like CSV straight from the html document.

#### Hide anything behind a dropdown

```markdown
<details>
<summary>Click to expand</summary>
</details>
```

### Vscode tricks

#### Plugins

- Apptainer/Singularity
- BioSyntax
- ChatGPT
- Github Copilot
- Code Spell Checker
- Dev Containers
- Docker
- Markdown All in One
- Markdown Preview Enhanced
- markdownlint
- Nextflow
- Prettier - Code formatter
- Quarto
- R
- Remote - SSH
- Remote Explorer
- Scientific Terms - Code Spell Checker
- Shiny
- Snakefmt
- Snakemake Language
- indent-rainbow
- Rainbow CSV
- themes: Catpuccin Macchiato, Synthwave '84

## Using containers in my workflow

My use case for contaienrs like Docker or Singularity is to isolate specific environments so that they are reproducible or can run anywhere. A minimal container could be something like a a version of a tool captured in a container and run on a system that does not have the tool installed. A more complex container could be a full analysis pipeline that is run on a cluster.Programs that are difficult or impossible to install on the cluster can be run from a container on the cluster.

example usage:

- run a specific version of a tool that is not installed on the system
- run a cli tool built into container as part of a larger pipeline
- build enviroment for compiling tools; then copying the tool outside the container for runnning
- run Rstudio server with pre-installed packages
- load local folder into container for analysis

### Container `renv` integration example

```dockerfile
# Start with a base image with R installed
FROM rocker/verse:latest

# Set a working directory
WORKDIR /app

# Copy renv.lock and other necessary files into the container
COPY renv.lock /app/
COPY .Renviron /app/

# Install renv and pak
RUN R -e "install.packages('renv', repos = 'https://cloud.r-project.org/')"
RUN R -e "install.packages('pak', repos = 'https://r-lib.github.io/p/pak/dev/')" \
        -e "pak::pak_setup()"

# Restore the R environment from renv.lock if it exists
RUN if [ -f /app/renv.lock ]; then R -e "renv::restore(clean = TRUE)"; fi


# Render all .qmd files in the mounted directory
CMD ["sh", "-c", "quarto render *.qmd --to html --embed-resources"]
```

On building the container the `renv.lockfile` is copied into the container and the `renv` library is restored. When then container is then run with a command to render all `.qmd` files in the mounted directory.

Building the container: `docker build -t quarto-render .`

Running with: `docker run --rm -v $(pwd):/app quarto-render` will render all `.qmd` files in the current directory using the R environment stored in the container and built with the `renv.lock` file.

## Pro / Cons of the Quarto workflow

### Pros

- reproducible analysis
- mix languages (R, Python, Julia, Bash) in the same document

### Cons

- additional complexity
- rendering can be slow

## Supporting tools

### OneDrive

Deliverables for each investigator lives here and is shared with the investigator. In addition to dated folders with the analysis outputs, there is also a powerpoint slide deck for sharing specific results during meetings and taking notes / listing action items during meetings.

I use `rclone` to transfer data directly from Alpine to the OneDrive.

## How I use AI/LLMs in my workflow

- install github copilot for vscode and rstudio
  - github education sign in
- enhanced autocompletion - vscode / Rstudio
  - free copilot through github education
- initial README / documentation generation
- converting code to functions
  - examples
- bash scripts
- adding comments to code

## Things I want to learn, incorporate, or improve

Cell caching in quarto documents to speed up rendering. This is similar to the `cache` option in Rmarkdown. It allows for caching the results of code chunks so that they do not need to be re-run every time the document is rendered. This can significantly speed up rendering time for large documents.

More interactivity in quarto documents. This could include using `htmlwidgets` or `plotly` for interactive plots, or using `shiny` for interactive applications within the quarto document.

Tabs and accordions in quarto documents. This can be useful for organizing content and making it more user-friendly. It allows for collapsing sections of the document to make it easier to navigate.

Dashboards in quarto documents. This could include using `flexdashboard` or `shinydashboard` to create interactive dashboards that display multiple plots and tables in a single document.

`uv` python version manager and pip replacement written in rust that is faster and more efficient than pip. It is similar to `micromamba` for python.

`ObservableJS` - <https://observablehq.com/> is a great platform for interactive data visualization and analysis. It is based on JavaScript and allows for easy sharing and collaboration on data analysis projects. Can be used to create widgets and interactivity in quarto documents. You can have `{ojs}` cells directly in quarto. A more advanced version would be something like `D3.js`.

Rust based tools that can be called from R or Python. There are a number of bioinformatics tools that are being developed in Rust that are faster and more efficient than their R or Python counterparts. Examples include `bio-rs` and `rust-bio`. I would like to write some scRNAseq and cancer related tools that leverage the speed and efficiency of Rust.