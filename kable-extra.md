---
title: "Improve your Modelsummary regression table with the kableExtra package in R"
description: "An example showing useful functions of the kableExtra package to improve your Modelsummary output in R"
keywords: "kableextra, kable, latex, modelsummary, regression, model, table, R"
draft: true
weight: 6
author: "Valerie Vossen"
authorlink: "https://nl.linkedin.com/in/valerie-vossen"
aliases:
  - /kableextra
  - /run/kableextra/
  - /run/kableextra
---


# Overview

The main purpose of the kableExtra package is to simplify the process of creating tables with custom styles and formatting in R. For instance, you can include fixed effects rows, labeling rows to indicate categories and include headers. In this building block, we will provide you with an example using some useful functions of kableExtra. Modelsummary output will be improved to a table in LaTex format ready to use for your publishable paper!

The Modelsummary output used as the starting point is the replicated Table 1 of Eiccholtz et al. (2010) from our Modelsummary building block (link). This table shows the results of a model regressing the rent of commercial office buildings on a dummy variable (1 if rated as green) and other characteristics of the buildings. More information about this paper and the table can be found in the Modelsummary building block!

After loading this Modelsummary table, the following kableExtra functions will be covered to get to a nice formatted final output!
- Output in LaTex format
- Exporting the table
- Specify the column alignment 
- Add fixed effect rows
- Add header
- Group rows

## Load packages and data

{{% codeblock %}}
```R
# Load packages
library(modelsummary)
library(dplyr)
library(fixest)
library(stringr)
library(knitr)
library(kableExtra)

# Load data 
data_url <- "https://github.com/tilburgsciencehub/website/blob/buildingblock/modelsummary/content/building-blocks/analyze-data/regressions/data_rent.Rda?raw=true"
load(url(data_url)) #data_rent is loaded now
```
{{% /codeblock %}}


## Modelsummary table 

- `models` is a list of regression 1 until 5. Please refer to the Modelsummary building block for a detailed overview and understanding of the regressions in the 'models' list.
- `cm2` and `gm2` specify respectively the variable names and statistics in the regression table.

{{% codeblock %}}
```R
cm2 = c('green_rating'    = 'Green rating (1 $=$ yes)',
        'energystar'    = 'Energystar (1 $=$ yes)',
        'leed'    = 'LEED (1 $=$ yes)',
        'size_new'    = 'Building size (millions of sq.ft)',
        'oocc_new' = 'Fraction occupied',
        'class_a' = 'A (1 $=$ yes)',
        'class_b' = 'B (1 $=$ yes)',
        'net' = 'Net contract (1 $=$ yes)',
        'age_0_10' = '$<$10 years',
        'age_10_20' = '10-20 years',
        'age_20_30' = '20-30 years',
        'age_30_40' = '30-40 years',
        'renovated' = 'Renovated (1 $=$ yes)',
        'story_medium' = 'Intermediate (1 $=$ yes)', 
        'story_high' = 'High (1 $=$ yes)', 
        'amenities' = 'Amenities (1 $=$ yes)')

gm2 <- list(
    list("raw" = "nobs", "clean" = "Sample size", "fmt" = 0),
    list("raw" = "r.squared", "clean" = "$R^2$", "fmt" = 2),
    list("raw" = "adj.r.squared", "clean" = "Adjusted $R^2$", "fmt" = 2))

msummary(models,
         vcov = "HC1",
         fmt = fmt_statistic(estimate = 3, std.error = 3),
         #stars  = c('*' = .1, '**' = 0.05, '***' = .01),
         estimate = "{estimate}",
         statistic = "[{std.error}]{stars}",
         coef_map = cm2, 
         gof_omit = 'AIC|BIC|RMSE|Within|FE',
         gof_map = gm2)

```
{{% /codeblock %}}

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;"> <thead> <tr> <th style="text-align:left;"> </th> <th style="text-align:center;">  (1) </th> <th style="text-align:center;">   (2) </th> <th style="text-align:center;">   (3) </th> <th style="text-align:center;">   (4) </th> <th style="text-align:center;">   (5) </th> </tr> </thead> <tbody> <tr> <td style="text-align:left;"> Green rating (1 = yes) </td> <td style="text-align:center;"> 0.035 </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.033 </td> <td style="text-align:center;"> 0.028 </td> <td style="text-align:center;"> </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> [0.009] </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.009] </td> <td style="text-align:center;"> [0.009] </td> <td style="text-align:center;"> </td> </tr> <tr> <td style="text-align:left;"> Energystar (1 = yes) </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.033 </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.009] </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> </tr> <tr> <td style="text-align:left;"> LEED (1 = yes) </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.052 </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.036] </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> </tr> <tr> <td style="text-align:left;"> Building size (millions of sq.ft) </td> <td style="text-align:center;"> 0.113 </td> <td style="text-align:center;"> 0.113 </td> <td style="text-align:center;"> 0.102 </td> <td style="text-align:center;"> 0.110 </td> <td style="text-align:center;"> 0.110 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> [0.019] </td> <td style="text-align:center;"> [0.019] </td> <td style="text-align:center;"> [0.019] </td> <td style="text-align:center;"> [0.021] </td> <td style="text-align:center;"> [0.021] </td> </tr> <tr> <td style="text-align:left;"> Fraction occupied </td> <td style="text-align:center;"> 0.020 </td> <td style="text-align:center;"> 0.020 </td> <td style="text-align:center;"> 0.020 </td> <td style="text-align:center;"> 0.011 </td> <td style="text-align:center;"> 0.011 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> [0.016] </td> <td style="text-align:center;"> [0.016] </td> <td style="text-align:center;"> [0.016] </td> <td style="text-align:center;"> [0.016] </td> <td style="text-align:center;"> [0.016] </td> </tr> <tr> <td style="text-align:left;"> A (1 = yes) </td> <td style="text-align:center;"> 0.231 </td> <td style="text-align:center;"> 0.231 </td> <td style="text-align:center;"> 0.192 </td> <td style="text-align:center;"> 0.173 </td> <td style="text-align:center;"> 0.173 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> [0.012] </td> <td style="text-align:center;"> [0.012] </td> <td style="text-align:center;"> [0.014] </td> <td style="text-align:center;"> [0.015] </td> <td style="text-align:center;"> [0.015] </td> </tr> <tr> <td style="text-align:left;"> B (1 = yes) </td> <td style="text-align:center;"> 0.101 </td> <td style="text-align:center;"> 0.101 </td> <td style="text-align:center;"> 0.092 </td> <td style="text-align:center;"> 0.083 </td> <td style="text-align:center;"> 0.083 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> [0.011] </td> <td style="text-align:center;"> [0.011] </td> <td style="text-align:center;"> [0.011] </td> <td style="text-align:center;"> [0.011] </td> <td style="text-align:center;"> [0.011] </td> </tr> <tr> <td style="text-align:left;"> Net contract (1 = yes) </td> <td style="text-align:center;"> -0.047 </td> <td style="text-align:center;"> -0.047 </td> <td style="text-align:center;"> -0.050 </td> <td style="text-align:center;"> -0.051 </td> <td style="text-align:center;"> -0.051 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> [0.013] </td> <td style="text-align:center;"> [0.013] </td> <td style="text-align:center;"> [0.013] </td> <td style="text-align:center;"> [0.013] </td> <td style="text-align:center;"> [0.013] </td> </tr> <tr> <td style="text-align:left;"> $&lt;$10 years </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.118 </td> <td style="text-align:center;"> 0.131 </td> <td style="text-align:center;"> 0.131 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.016] </td> <td style="text-align:center;"> [0.017] </td> <td style="text-align:center;"> [0.017] </td> </tr> <tr> <td style="text-align:left;"> 10-20 years </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.079 </td> <td style="text-align:center;"> 0.084 </td> <td style="text-align:center;"> 0.084 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.014] </td> <td style="text-align:center;"> [0.014] </td> <td style="text-align:center;"> [0.014] </td> </tr> <tr> <td style="text-align:left;"> 20-30 years </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.047 </td> <td style="text-align:center;"> 0.048 </td> <td style="text-align:center;"> 0.048 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.013] </td> <td style="text-align:center;"> [0.013] </td> <td style="text-align:center;"> [0.013] </td> </tr> <tr> <td style="text-align:left;"> 30-40 years </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.043 </td> <td style="text-align:center;"> 0.044 </td> <td style="text-align:center;"> 0.044 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.011] </td> <td style="text-align:center;"> [0.011] </td> <td style="text-align:center;"> [0.011] </td> </tr> <tr> <td style="text-align:left;"> Renovated (1 = yes) </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> -0.008 </td> <td style="text-align:center;"> -0.008 </td> <td style="text-align:center;"> -0.008 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.009] </td> <td style="text-align:center;"> [0.009] </td> <td style="text-align:center;"> [0.009] </td> </tr> <tr> <td style="text-align:left;"> Intermediate (1 = yes) </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.010 </td> <td style="text-align:center;"> 0.010 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.009] </td> <td style="text-align:center;"> [0.009] </td> </tr> <tr> <td style="text-align:left;"> High (1 = yes) </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> -0.027 </td> <td style="text-align:center;"> -0.027 </td> </tr> <tr> <td style="text-align:left;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> [0.015] </td> <td style="text-align:center;"> [0.015] </td> </tr> <tr> <td style="text-align:left;"> Amenities (1 = yes) </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> </td> <td style="text-align:center;"> 0.047 </td> <td style="text-align:center;"> 0.047 </td> </tr> <tr> <td style="text-align:left;box-shadow: 0px 1.5px"> </td> <td style="text-align:center;box-shadow: 0px 1.5px"> </td> <td style="text-align:center;box-shadow: 0px 1.5px"> </td> <td style="text-align:center;box-shadow: 0px 1.5px"> </td> <td style="text-align:center;box-shadow: 0px 1.5px"> [0.007] </td> <td style="text-align:center;box-shadow: 0px 1.5px"> [0.007] </td> </tr> <tr> <td style="text-align:left;"> Sample size </td> <td style="text-align:center;"> 8105 </td> <td style="text-align:center;"> 8105 </td> <td style="text-align:center;"> 8105 </td> <td style="text-align:center;"> 8105 </td> <td style="text-align:center;"> 8105 </td> </tr> <tr> <td style="text-align:left;"> R2 </td> <td style="text-align:center;"> 0.71 </td> <td style="text-align:center;"> 0.71 </td> <td style="text-align:center;"> 0.72 </td> <td style="text-align:center;"> 0.72 </td> <td style="text-align:center;"> 0.72 </td> </tr> <tr> <td style="text-align:left;"> Adjusted R2 </td> <td style="text-align:center;"> 0.69 </td> <td style="text-align:center;"> 0.69 </td> <td style="text-align:center;"> 0.69 </td> <td style="text-align:center;"> 0.69 </td> <td style="text-align:center;"> 0.69 </td> </tr> </tbody> </table>


## Output in LaTex format

We want the table to be outputted in LaTeX format now. For this, we set the output argument to "latex" in `msummary()`. LaTeX code is generated that can be copied and pasted directly into a LaTeX document!

{{% codeblock %}}
```R
msummary(models,     vcov = "HC1",
                     fmt = 3,
                     estimate = "{estimate}",
                     statistic = "[{std.error}]",
                     coef_map = cm2, 
                     gof_omit = 'AIC|BIC|RMSE|Within|FE',
                     gof_map = gm2,
                     output = "latex",
                   escape = FALSE
                   )
```
{{% /codeblock %}}

{{% tip %}}
When `escape = FALSE`, any special characters that are present in the output of `msummary()` will not be modified or escaped. This means that they will be printed exactly as they appear in the output, without any changes or substitutions. On the other hand, if escape = TRUE, then any special characters that are present in the output will be replaced with the appropriate LaTeX commands to render them correctly in the final document.
{{% /tip %}}


## Export the table

To save this LaTex code to a .tex file, we can use the cat() function. The file argument specifies the name of the file we want the output to be printed to: "my_table.tex". Not giving any file argument will print the output to the console. 

{{% codeblock %}}
```R
msummary(models,     vcov = "HC1",
                     fmt = 3,
                     stars  = c('*' = .1, '**' = 0.05, '***' = .01),
                     estimate = "{estimate}",
                     statistic = "[{std.error}]",
                     coef_map = cm2, 
                     gof_omit = 'AIC|BIC|RMSE|Within|FE',
                     gof_map = gm2,
                     output = "latex",
                   escape = FALSE) %>%
  cat(.,file="my_table.tex")
```
{{% /codeblock %}}

{{% tip %}}
The %>% operator is being used to pipe the output of `msummary()` into the `cat()` function. Specifically, the dot (.) is a placeholder for the output of the previous function in the pipeline, which in this case is `msummary()`.
{{% /tip %}}

image: kabletable1

## Specify the column aligment

You can specify the horizontal alignment of the columns in the table with adding the align argument in the `msummary()` function. With the following align argument, it is specified that:
- The first column with the variable names should be left-aligned (l)
- The second through fifth columns with the coefficients should be centered (c)

{{% codeblock %}}
```R
 msummary(models,     vcov = "HC1",
                     fmt = 3,
                     #stars  = c('*' = .1, '**' = 0.05, '***' = .01),
                     estimate = "{estimate}",
                     statistic = "[{std.error}]",
                     coef_map = cm2, 
                     gof_omit = 'AIC|BIC|RMSE|Within|FE',
                     gof_map = gm2,
                     #stars = NULL,
                     align="lccccc",
                     output = "latex",
                   escape = FALSE
                   )

```
{{% /codeblock %}}

## Add fixed effect rows 

In this step, we will add rows indicating the fixed effects included in each regression model.

### Creating a tibble
First, the additional rows are specified in a tibble. Within the tibble:
- There are two columns: term and "(1)", "(2)", "(3)", "(4)", "(5)". 
- The two rows specify the two kind of Fixed Effects: Location Fixed Effect & Green Building Fixed Effect. In these rows, it is specified for each regression model whether these Fixed Effects are included: "Checkmark" means included, and "XSolidBrush" means not included.

{{% codeblock %}}
```R
library(tibble)

rows <- tribble(~term,          ~"(1)", ~"(2)", ~"(3)",~"(4)", ~"(5)",
                'Location Fixed Effect', '\\Checkmark',   '\\Checkmark', '\\Checkmark', '\\Checkmark', '\\Checkmark',
                'Green Building Fixed Effect', '\\XSolidBrush',   '\\XSolidBrush', '\\XSolidBrush', '\\XSolidBrush', '\\Checkmark'
)
```
{{% /codeblock %}}

### Position of Fixed Effect rows in table
With the function `attr()`, the fixed effect rows are inserted at the right position in the table. This is row 33 and 34, which is in between the estimates and statistics. (Check the code block below.)

### add_rows
Adding the tibble with the fixed effect rows to the code producing the final table is done by including `add_rows = rows` in the `msummary()` function. Note that rows is the name of our tibble.

### row_spec
To separate the fixed effect rows of the summary statistics, a horizontal line is added below the Fixed Effect rows. This is done with the `row_spec()` function after `modelsummary()` following a pipe operator.

{{% codeblock %}}
```R
msummary(models,     vcov = "HC1",
                     fmt = 3,
                     #stars  = c('*' = .1, '**' = 0.05, '***' = .01),
                     estimate = "{estimate}",
                     statistic = "[{std.error}]",
                     coef_map = cm2, 
                     gof_omit = 'AIC|BIC|RMSE|Within|FE',
                     gof_map = gm2,
                     #stars = NULL,
                     add_rows = rows,
                     align="lccccc", 
                     output = "latex",
                   escape = FALSE
                   ) %>%
    row_spec(34, extra_latex_after = "\\midrule") %>%
    cat(., file = "my_table.tex")
```
{{% /codeblock %}}

image: kabletable2

## Add header

The `add_header_above()` function allows you to add a header row above the existing column headers of the regression table. We add this function after the `msummary()` following a pipe operator to include a title.
- The name of the header is included as a character string. 
- The first column does not need a header. Setting our header equal to 5 makes sure it spans over column 2-6. 

{{% codeblock %}}
```R
msummary(models,     vcov = "HC1",
                     fmt = 3,
                     #stars  = c('*' = .1, '**' = 0.05, '***' = .01),
                     estimate = "{estimate}",
                     statistic = "[{std.error}]",
                     coef_map = cm2, 
                     gof_omit = 'AIC|BIC|RMSE|Within|FE',
                     gof_map = gm2,
                     #stars = NULL,
                     add_rows = rows,
                     align="lccccc",
                     output = "latex",
                   escape = FALSE
                   ) %>%
    add_header_above(c(" " = 1,
                       "Dependent Variable: $log(rent)$" = 5),
                     escape = FALSE
    ) 
    cat(., file = "my_table.tex")
```
{{% /codeblock %}}

image3

## Group rows

### Add a labeling row
We can use the `pack_rows()` function to insert labeling rows. We use it to specify three categories in our variables, namely Building Class, Age and Stories. 

Within the function, we specify the name of the category and the first and last row of the rows that should be grouped together under this category. We also indicate we want the category name to be printed in italic text and not bold. 

### Indent subgroups 

Energystar and LEED are subgroups of the variable Green rating (row 1). For this, "add_intent()" is perfect to use. Instead of creating a labelling row, Energystar (row 3) and LEED (row 5) are listed under Green Rating by indenting these subgroups.

{{% codeblock %}}
```R
msummary(models,     vcov = "HC1",
                     fmt = 3,
                     #stars  = c('*' = .1, '**' = 0.05, '***' = .01),
                     estimate = "{estimate}",
                     statistic = "[{std.error}]",
                     coef_map = cm2, 
                     gof_omit = 'AIC|BIC|RMSE|Within|FE',
                     gof_map = gm2,
                     #stars = NULL,
                     add_rows = rows,
                     align="lccccc",
                     output = "latex",
                   escape = FALSE
                   ) %>%
    pack_rows("Building Class:", 11, 13, italic = TRUE, bold = FALSE) %>%
    pack_rows("Age:", 17, 24, italic = TRUE, bold = FALSE) %>%
    pack_rows("Stories:", 27, 30, italic = TRUE, bold = FALSE) %>%
    add_indent(3) %>%
    add_indent(5) %>%
    row_spec(34, extra_latex_after = "\\midrule") %>%
    cat(., file = "my_table.tex")

```
{{% /codeblock %}}

image4: final table

{{% summary %}}
In this building block, we provided you with an example on how to use some `kableExtra` functions to improve your standard Modelsummary table and have it outputted in LaTex format! The following functions are covered:
- Output in LaTex format
- Export the table
- Add fixed effect rows
- Specify the column alignment 
- Add header
- Group rows
{{% /summary %}}

