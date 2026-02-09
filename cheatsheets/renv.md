# renv Cheatsheet

`renv` is a dependency management package for R that helps create reproducible environments.

![renv workflow diagram](https://rstudio.github.io/renv/articles/renv.png)

## Getting Started
| Command | Description |
| :--- | :--- |
| `renv::init()` | Initialize a new project-local environment. Sets up library, `lockfile`, and `.Rprofile`. |
| `renv::status()` | Report issues or differences between the project library and the lockfile. |
| `renv::snapshot()` | Save the state of the project library to the `renv.lock` file. |
| `renv::restore()` | Restore the project library to the state recorded in `renv.lock`. |

## Bioconductor

I like to set Bioconductor as a default repository in my projects to force to use Bioconductor packages as first choice.:

```r
renv::install("BiocManager")
options(repos = BiocManager::repositories())
renv::snapshot()
```

## Package Management
| Command | Description |
| :--- | :--- |
| `renv::install("pkg")` | Install a package into the project library (supports CRAN, GitHub, Bioconductor). |
| `renv::install("user/repo")` | Install from GitHub. |
| `renv::install("bioc::Package")` | Install from Bioconductor. |
| `renv::install("pkg@1.2.3")` | Install a specific version of a package. |
| `renv::record("pkg@1.2.3")` | Record a specific version in the lockfile without installing it. |
| `renv::remove("pkg")` | Remove a package from the project library. |
| `renv::update()` | Update packages in the project library to their latest versions. |
| `renv::history()` | View a history of snapshots taken in the project. |

## Maintenance & Cleanup
| Command | Description |
| :--- | :--- |
| `renv::clean()` | Remove unused packages from the project library. |
| `renv::diagnostics()` | Print diagnostic information about the project and environment. |
| `renv::rebuild()` | Reinstall all packages in the project (useful when switching R versions). |
| `renv::purge("pkg")` | Remove a package from the global `renv` cache. |

## Collaboration Workflow
1. **Developer A**: `renv::init()` -> Work -> `renv::snapshot()` -> Commit `renv.lock`, `.Rprofile`, `renv/activate.R`.
2. **Developer B**: Clone Repo -> Open R -> `renv::restore()` automatically downloads correct versions.

## Important Files
- `renv.lock`: The source of truth for package versions. **Commit this to Git.**
- `.Rprofile`: Used to activate `renv` when starting R in the project. **Commit this to Git.**
- `renv/`: Contains internal state. **Usually partially gitignored**, but `renv/activate.R` should be committed.
- `renv/library/`: The actual R packages. **Do NOT commit this to Git.**
