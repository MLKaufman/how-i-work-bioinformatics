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

Aliases and functions: (See `.zshrc` dotfile.)

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

## Project initiation

Each project starts with a folder locally in my `~/Projects` directory.

Naming convention: `Henry_scRNAseq_Jan2025`

The project folder is initialized with a `README.md` file that contains the project name and a description of the project.
Example of a `README.md` file: (See example `example_README.md`)

Project is initialized with `git init` and a `.gitignore` file is created to ignore common files and directories that are not needed in the repository.

A `renv` project is initialized with `renv::init()` to create a project specific R library. The `renv` library is used to manage the R library for the project.
#ensure pak is the default package manager

`renv` workflow is `renv::snapshot()` to save the current library state, `renv::restore()` to restore the library state, and `renv::status()` to check the library state.

## Project organization

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

Utilize `quarto` <https://quarto.org/> for reproducible analysis. Quarto is a markdown based document format that supports R, Python, and Julia code chunks. It is similar to R Markdown, but is language agnostic.

Ideally, each analysis step is a separate `quarto` document that is rendered to HTML or PDF. The rendered document is saved in the `analysis` directory.
01-scRNAseq-raw_qc.qmd | 02-scRNAseq-preprocessing.qmd | 03-scRNAseq-batch_correction.qmd | 04-scRNAseq-clustering_annotation.qmd

### git workflow

`git init`

Use Rstudio, VS Code, or Github Desktop to manage the git repository.


.gitignore
```bash
# R


### Quarto doc format

Yaml header
```yaml

```

parameters 
piping 

### Executing quarto render


## Container integration

The `renv` works well for managing the R library, but does not manage the system dependencies. To manage the system dependencies, I use Docker or Singularity containers.
The other advantage of using containers is that the analysis is reproducible across different systems and environments, locally or on a cluster like Alpine.