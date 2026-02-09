# Quarto Cheatsheet

Quarto is a multi-language, next-generation version of R Markdown. It supports R, Python, Julia, and Observable JavaScript. Quarto is a standalone executable that can render documents, presentations, websites, and more.

## Installation
There are many ways to get up and running with Quarto:
- **Download the Quarto CLI:** [quarto.org/docs/get-started/](https://quarto.org/docs/get-started/)
- **RStudio:** Quarto is integrated into RStudio (version 2022.07.1 and later).
- **VS Code:** Install the Quarto extension from the marketplace.
- **Homebrew (macOS):**
```bash
brew install quarto
```

## Command Line Interface (CLI)
| Command | Description |
| :--- | :--- |
| `quarto render file.qmd` | Render a document to the default format |
| `quarto preview file.qmd` | Live preview (re-renders on save) |
| `quarto render --to pdf` | Render specifically to PDF |
| `quarto create-project` | Initialize a new Quarto project |

## Useful YAML Options
```yaml
---
title: "Spatial Transcriptomics Analysis - 01 - Quality Control"
subtitle: "PI - Project Name"
author: 
  name: "Michael Kaufman, PhD"
  orcid: 0000-0003-2441-5836
  email: michael.kaufman@cuanschutz.edu
  affiliation: 
    name: "University of Colorado Cancer Center"
    url: "https://medschool.cuanschutz.edu/colorado-cancer-center"
abstract: ""

editor: source # Use 'visual' or 'source' mode
theme: cosmo # Standard themes (cosmo, flatly, journal, lumen, etc.)
number-sections: false
link-citations: true
link-external-references: true
 
format:
  html:
    title-block-banner: "report_assets/analysis-logo.png" # Custom banner for the header
    embed-resources: true # Embed images/scripts into a single HTML file
    self-contained: true # Legacy version of embed-resources
    highlight-style: pygments # Syntax highlighting style
    code-fold: true # Fold code by default
    code-summary: "[code]" # Custom text for the fold button
    code-overflow: wrap # Wrap long code lines
    toc: true # Enable Table of Contents
    toc-depth: 2 # Show top two levels in TOC
    toc-location: right # TOC position (left, right, body)
    css: report_assets/custom-toc.css
    grid: # Fine-grained layout control
      sidebar-width: 300px
      body-width: 1600px
      margin-width: 300px
      gutter-width: 10px

execute:
  warning: false # Hide R/Python warnings in output
  message: false # Hide R/Python messages in output
  fig-align: "center" # Global figure alignment
  output-line-length-limit: 80 # Truncate long terminal output

params: # Parameters for templated reports (access via params$name)
  species: "human"
  contrast: "CDK1i-Vehicle"
  treatment_bam: "../processed_data/bams/CDK1i_1_sorted.bam"
  control_bam: "../processed_data/bams/Vehicle_1_sorted.bam"
  gtf: "../ref/Homo_sapiens.GRCh38.104.gtf.gz"

---
```

## Tips & Tricks
### 1. Code Folding & Linking
In bioinformatics, code blocks can be long. Use `code-fold: true` to keep it clean.
To link code back to documentation:
```yaml
format:
  html:
    code-link: true
```

### 2. Tabsets
Useful for showing multiple versions of a plot (e.g., different normalization methods):
```markdown
::: {.panel-tabset}
## Plot A
(plot code here)

## Plot B
(plot code here)
:::
```

### 3. Callout Blocks
There are five types: `note`, `tip`, `warning`, `caution`, and `important`.

::: {.callout-note}
**Note:** This is a basic note.
:::

::: {.callout-tip}
**Tip:** Use callouts to highlight important parts of your workflow.
:::

::: {.callout-warning}
**Warning:** Check your data before proceeding.
:::

::: {.callout-caution collapse="true"}
## Expand to see more
This callout is collapsed by default.
:::

#### HTML `<details>` Method
You can also use standard HTML for custom collapsible sections:
```html
<details>
<summary>Click to expand</summary>
This is hidden content that works in most Markdown renderers.
</details>
```

### 4. Code Annotations
Add comments to code that users can hover over:
```python
import pandas as pd
df = pd.read_csv("data.csv") # <1>
df.head() # <2>
```
1. Read the data
2. Display the first few rows

### 5. Margin Content
Place text or figures in the margin:
```markdown
::: {.column-margin}
This content will appear in the margin next to the main text.
:::
```

### 6. Layouts (Figures side-by-side)
```markdown
::: {layout-nw="[[1,1]]"}
![Figure 1](fig1.png)

![Figure 2](fig2.png)
:::
```

## Runtimes & Environments
### 1. Mixing R and Python (The Knitr Engine)
For mixing languages, **Knitr is the recommended engine**. It uses the `reticulate` package to maintain a single Python session alongside your R session.

- **Setup:** Just include both types of code blocks. Quarto will use Knitr by default.
- **Data Sharing:**
  - In **Python**, access R objects via the `r` object: `r.my_r_df`
  - In **R**, access Python objects via the `py` object: `py$my_python_df`

- **Configuring the Python Runtime:**
  - By default, `reticulate` will look for a `.venv` or `venv` in your project root.
  - To force a specific environment in your code:
    ```r
    library(reticulate)
    use_virtualenv("./.venv") # Use a specific venv
    # or
    use_condaenv("my-env") # Use a specific conda env
    ```
  - **Environment Variable:** You can also set `RETICULATE_PYTHON` in an `.Renviron` file in your project root:
    ```bash
    RETICULATE_PYTHON=".venv/bin/python"
    ```

#### Example Workflow
```r
#| label: r-data-prep
library(tidyverse)
r_data <- mtcars %>% filter(mpg > 20)
```

```python
#| label: python-analysis
import pandas as pd
# Access the R dataframe 'r_data'
df = r.r_data 
df['new_col'] = df['hp'] * 2
```

```r
#| label: r-plot
# Access the modified Python dataframe 'df'
library(ggplot2)
ggplot(py$df, aes(x=mpg, y=new_col)) + geom_point()
```

### 2. Virtual Environments
Quarto is smart about detecting local environments:
- **Python:** It automatically looks for a `.venv` or `env/` folder in your project root. To use a specific python, set `QUARTO_PYTHON` in your `.bashrc` or `.zshrc`.
- **R:** If a `renv.lock` file is present, Quarto will use that project's specific library.

### 3. Notebooks vs. QMD
- **Render a Notebook:** `quarto render analysis.ipynb` (No conversion needed!)
- **Convert QMD <-> IPYNB:**
  - `quarto convert analysis.qmd` -> `analysis.ipynb`
  - `quarto convert analysis.ipynb` -> `analysis.qmd`

## Presentations (Slides)
Quarto can create high-quality presentations in HTML (`revealjs`), PDF (`beamer`), or PowerPoint (`pptx`).

### 1. Basic YAML for Revealjs
```yaml
format:
  revealjs:
    theme: sky
    transition: fade
    incremental: true # Lists appear one by one
    slide-number: true
    chalkboard: true # Enable drawing on slides
```

### 2. Creating Slides
- `# Section Title`: Creates a major transition slide.
- `## Slide Title`: Starts a new slide with a title.
- `---`: Starts a new slide without a title.

### 3. Special Features
- **Fragments:** Use the `.fragment` class to reveal elements one by one.
  ```markdown
  ::: {.fragment}
  This appears later.
  :::
  ```
- **Speaker Notes:**
  ```markdown
  ::: {.notes}
  Invisible to the audience, only visible in speaker view (press 's').
  :::
  ```
- **Columns:**
  ```markdown
  ::: {.columns}
  ::: {.column width="50%"}
  Left content
  :::
  ::: {.column width="50%"}
  Right content
  :::
  :::
  ```

## Including Content (Partial Documents)
You can embed other `.qmd` or `.md` files into a master document. This is useful for splitting up large bioinformatics reports into chapters or methods/results sections.

### 1. Simple Include (Shortcode)
The most common way is using the `include` shortcode:
```markdown
{{< include _methods.qmd >}}
```
*Note: Use an underscore `_` prefix for included files to tell Quarto NOT to render them as standalone documents.*

### 2. Render Included Files with Code Execution
By default, included files are treated as static text. To have Quarto execute and render code chunks in the included file, use the `render` option:
```markdown
{{< include _methods.qmd render="true" >}}
```

### 3. Embedding Computational Results
If you want to embed a specific figure or result from another `.qmd` or `.ipynb` (including its code and output), use the `embed` shortcode:
```markdown
{{< embed analysis.qmd#fig-pca >}}
```
*Unlike `include`, which just pastes text, `embed` pulls the rendered output and its context from the source file.*

### 4. Knit Child (Knitr Legacy)
If you are strictly using the Knitr engine, you can use the code chunk option:
```r
#| child: "_methods.qmd"
```

## Useful Lua Filter Snippet
Create a file named `uppercase.lua` to transform text:
```lua
function Strong(el)
  return pandoc.SmallCaps(el.content)
end
```
Then use it in YAML:
```yaml
filters:
  - uppercase.lua
```

## Bioinformatics Specific Formats
### Citations (BibTeX)
```yaml
bibliography: references.bib
```
Usage: `As shown by @smith2023...`

### Cross-Referencing
- Figures: `![Caption](path.png){#fig-label}` -> See @fig-label.
- Tables: `| Col |...| {#tbl-label}` -> See @tbl-label.

## Freeze (Caching)
Prevent Quarto from re-running time-consuming bioinfo pipelines every time you render:

Globally in YAML:
```yaml
execute:
  freeze: auto  # re-render only when source changes
```

Code chunk level:
```r
#| freeze: true
# Time-consuming code here
```
