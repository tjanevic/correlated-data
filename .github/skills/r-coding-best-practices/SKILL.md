---
name: r-coding-best-practices
description: Use whenever writing, reviewing, or debugging R code
---

## Overview

Modern R development guide (tidyverse 1.1+, R 4.3+). Follow these patterns for all R code in this project.

## Core Principles

1. Native pipe `|>` not magrittr `%>%`
2. Profile before optimizing — use `profvis` and `bench`
3. snake_case for all names — variables are nouns, functions are verbs
4. tidyverse style guide throughout

## Must-Use Patterns

### Pipe
```r
# Always
data |> filter(year >= 2020) |> summarise(mean_value = mean(value))
# Never
data %>% filter(year >= 2020)
```

### Joins (dplyr 1.1+)
```r
# Always — join_by() syntax
transactions |> inner_join(companies, by = join_by(company == id))
transactions |> inner_join(companies, join_by(company == id, year >= since))

# Never — character vector syntax
inner_join(x, y, by = c("a" = "b"))
```

### Grouping (dplyr 1.1+)
```r
# Always — .by for per-operation grouping (returns ungrouped automatically)
data |> summarise(total = sum(revenue), .by = c(company, year))

# Avoid — persistent group_by unless you explicitly need it
data |> group_by(category) |> summarise(mean(value)) |> ungroup()
```

### Function Arguments with Data Masking
```r
# Embrace {{}} for forwarding function arguments
my_summary <- function(data, group_var, summary_var) {
  data |> group_by({{ group_var }}) |> summarise(mean_val = mean({{ summary_var }}))
}

# .data[[]] for character vectors
for (var in names(mtcars)) mtcars |> count(.data[[var]]) |> print()
```

### purrr (1.0+)
```r
# Always — map() |> list_rbind()
models <- splits |> map(\(s) train_model(s)) |> list_rbind()

# Never — superseded map_dfr()
map_dfr(splits, train_model)
```

### Parallel Processing
```r
library(mirai)
daemons(4)
results <- large_list |> map(in_parallel(expensive_fn))
daemons(0)
# Only use in_parallel() for CPU-intensive ops — overhead kills fast functions
```

## OOP System Selection

| Need | Use |
|------|-----|
| Vector-like objects (data frame columns) | **vctrs** |
| General objects with validation | **S7** |
| Simple classes, max compatibility | **S3** |
| Bioconductor ecosystem | **S4** |

### S7 example
```r
Range <- new_class("Range",
  properties = list(start = class_double, end = class_double),
  validator = function(self) {
    if (self@end < self@start) "@end must be >= @start"
  }
)
```

## Performance Workflow

1. Profile first: `profvis({ your_code() })`
2. Benchmark alternatives: `bench::mark(approach1 = ..., approach2 = ...)`
3. For large data (>1GB): consider `data.table`
4. Never grow objects in loops — pre-allocate or use `map()`

## Common Mistakes

- Using `%>%` instead of `|>`
- `sapply()` instead of typed `map_dbl()` / `map_chr()`
- `group_by() |> summarise() |> ungroup()` instead of `.by =`
- `by = c("a" = "b")` joins instead of `join_by()`
- `map_dfr()` instead of `map() |> list_rbind()`
- Growing results with `result <- c(result, x)` in loops
- Mixing `{{}}` and `.data[[]]` unnecessarily — pick one pattern per function

## Quick Reference

| Old pattern | Modern replacement |
|-------------|-------------------|
| `data %>% f()` | `data |> f()` |
| `by = c("a" = "b")` | `by = join_by(a == b)` |
| `group_by() |> summarise() |> ungroup()` | `summarise(.by = x)` |
| `map_dfr(x, f)` | `map(x, f) |> list_rbind()` |
| `sapply(x, f)` | `map_dbl(x, f)` / `map_chr(x, f)` |
| `grepl(p, text)` | `str_detect(text, p)` |
| `gsub(p, r, text)` | `str_replace_all(text, p, r)` |
| `aggregate(x ~ y, data, mean)` | `summarise(data, mean(x), .by = y)` |
