# Standards: R

This document describes our best practices for R.

## Linted style

For many of our best practices for R, we use [`lintr`'s default settings]. The exception is an
extended maximum line length of 100 characters. For our default config, you can copy the [example
`.lintr`] file to your repo to get these.

## Additional practices

Not all of our practices are defined by linting. Here are our additional standards and
recommendations.

### Tidyverse

Prefer tidyverse, unless there is a performance need (but avoid optimising it early!).

### Line breaks

Aim to only have a single operation per line. Use `magrittr` piping to break complex operations
across multiple lines.

### Variables

Use snake case `snake_case`.

Give variables meaningful names. Name functions with a verb.

Use left assignment (`<-`), not right assignment (`->`).

Avoid variable reassignment. Use `magrittr` piping (`%>%`) and recompose complex piping into
separate functions. For example:

```R
complex_steps_one <- function(input) {
  input %>%
    # complex code
}

complex_steps_two <- function(input) {
  input %>%
    # complex code
}

my_data %>%
  complex_steps_one() %>%
  complex_steps_two()
```

Use `local()` or functions to reduce namespace pollution and to make it clear where variables are
used. Prefer `local()` for projects and functions for packages. Using `local()` for example:

```R
my_data <- fetch_data()

# For reducing namespace pollution
local({
  processed_data_for_chart <- my_data %>%
    # processing
  
  plot <- my_data %>%
    # plot

  ggsave("plot.png", plot) 
})

# For reducing namespace pollution and making intermediate-only variables more explicit.
processed_data <- local({
  step1 <- my_data %>%
    # lots of processing
  
  step2 <- my_data %>%
    # lots more processing
  
  # ...

  return(stepN)
})
```

### Documentation

Use code comments to explain logic (but avoid redundancy).

Use `roxygen` to document functions.

Use [RStudio-style code folding sections] for longer workflows

### Imports and exports

Use `roxygen` to define document imports and exports (avoid using NAMESPACE directly).

Use `z_imports.R` to define the imports for the package (avoiding `@import` on each function).

Avoid importing rarely used functions by using the double colon `::` to select definitions from
another package.

Avoid exporting all functions from a package by using `roxygen` to document the exported functions.

### Repositories

Give the repository an appropriate name:

- If it's a one-shot output: `YYYYMM_project_title`
- If it's a regular output: `project_title`
- If it's a reusable package, designed to be imported elsewhere: `crea<name>` (for example `creahia`)

Use our output directory structure. For regular outputs, it may be appropriate to have an additional
subfolder under `data` to group each analysis.

```dir
data/
  |- cache/       # a cache of files downloaded by the scripts
  |- diagnostics/ # diagnostics that aren't part of the output
  |- input/       # manually downloaded input files
  \- output/      # the outputs for the analysis
```

[RStudio-style code folding sections]: https://support.posit.co/hc/en-us/articles/200484568-Code-Folding-and-Sections-in-the-RStudio-IDE
[`lintr`'s default settings]: https://lintr.r-lib.org/reference/default_linters.html
[example `.lintr`]: examples/.lintr
