---
title: "Panel data"
description: "A building block about the basics of panel data"
keywords: "paneldata, panel, data, R, regression, model"
draft: false
weight: 
author: "Valerie Vossen"
authorlink: "https://nl.linkedin.com/in/valerie-vossen"
aliases:
  - /paneldata
  - /run/paneldata/
  - /run/paneldata
---

# Overview

The goal of this building block is to provide an overview of panel data and how it can be helpful in causal inference analysis. 

Panel data refers to a specific type of dataset that includes observations on multiple entities over multiple time periods. Unlike pooled cross-sectional data, which can have different cross-sectional units in each time period, panel data follows the same cross-sectional units throughout a given time period.

The growing use of panel data in policy analysis, economics and social sciences can be attributed to its advantageous features. The structure of panel data, analyzing the same cross-sectional units over time, offers several benefits over cross-sectional and pooled cross-sectional data.


- **Control for unobserved characteristics**
Having multiple observations on the same units allows for controlling unobserved characteristics of individuals, firms and other entities. This helps mitigate omitted variable bias and provides more accurate estimates of causal effects.
- **Causal inference**
The availability of multiple observations in panel data enhances the ability to draw causal inferences. Inferring causality from a single cross-section can be challenging due to potential confounding factors. Panel data allows for analyzing changes within units over time, providing stronger evidence for causal relationships.
- **Examination of dynamics**
 Panel data enables us to examine dynamic processes, such as lagged behavior or the outcomes of decision-making. By observing units over time, changes in variables over different time periods can be understood. 

The building block will contain the following content: 

- Introduction to data
- Organising panel data
- Exploring panel data
- Notation of panel data
- Analysis with panel data, step-by-step:
    1. Cross-sectional data
    2. Panel data (2 years)
    3. Panel data (more than 2 years)
- Assumptions


## The `Grunfeld` data set 

The [Grunfeld dataset](https://www.statsmodels.org/dev/datasets/generated/grunfeld.html) will be used as an example throughout this building block. It contains investment data for 11 U.S. Firms over a time period of 1935 until 1954. Thus, the data set contains 220 observations (11 firms * 20 years). The following variables are included in the data set:

| **Variables** 	| **Description** 	|
|---	|---	|
| `invest` 	| Gross investment in 1947 dollars 	|
| `value` 	| Market value as of Dec 31 in 1947 dollars 	|
| `capital` 	| Stock of plant and equipment in 1947 dollars  	|
| `firm` 	| - General Motors <br>- US Steel <br>- General Electric <br>- Chrysler <br>- Atlantic Refining <br>- IBM <br>- Union Oil  <br>- Westinghouse <br>- Goodyear <br>- Diamond Match <br>- American Steel  	|
| `year`  	| 1935-1954 	|   


### Load packages and data

Let's load the necessary packages and the `Grunfeld` data set:

{{% codeblock %}}
```R
# Load packages
library(fixest)
library(AER)
library(ggplot2)

## load Grunfeld data from the AER package
data(Grunfeld)
```
{{% /codeblock %}}


## Organising panel data

The panel data is stored in a two-dimensional way:
- The column `firm` identifies the entities under observation.
- The column `year` documents the points in time the data was collected.

The most common way to organize the data set is to store the time period of each unit together. In this case, the first 20 rows are for the first firm in our data, and each row is for each year (in combination with the first firm). The second 20 rows is for the second firm in our data, and so on. 

Also, we change the order of the columns so the first column indicates the firm and the second column indicates the year. 

{{% codeblock %}}
```R
Grunfeld <- Grunfeld[, c("firm", "year", "invest", "value", "capital")]
print(Grunfeld)
head(Grunfeld)
```
{{% /codeblock %}}

With the argument `head()`, you can print the first 6 rows of the data set. 

[screenshot]

## Exploring panel data

The following plot illustrates the gross investment over the years in the data set, for the first five firms in the `Grunfeld` data set. A subset of firms is chosen in this visualisation to avoid the plot to be too crowded. 

{{% codeblock %}}
```R
data5firms <- Grunfeld[Grunfeld$firm == "General Motors" | 
                       Grunfeld$firm == "US Steel" | 
                       Grunfeld$firm == "General Electric"  | 
                       Grunfeld$firm == "Chrysler" | 
                       Grunfeld$firm == "Atlantic Refining",
                       ]

ggplot(data5firms, 
       aes(x=year, 
           y=invest, 
          color=firm)) + 
  geom_line() + 
  labs(x="Years", 
       y="Gross investment", 
       title = "Gross investment over the years 1935-1954", 
       color= "Firms",
       caption = "Source: Grunfeld dataset")
```
{{% /codeblock %}}

[screenshot]

## Notation of panel data

Panel data typically comprises observations on `n` entities (such as firms) over `T` periods (here, years), where `T $ 2`. In the case of the Grunfeld data set, it contains informations on 11 firms (n = 11) observed across 20 years (T=20).

Each variable in the data set is denoted by a subscript consisting of i and t, representing a specific observation that combines a firm (i) with a particular year (t). For instance, an observation on the variables of interest,invest and value, is represented as 

($value_{it}$, $invest_{it}$), where `i` $=$ 1,...,`n` and `t` = 1,...,`T`

This indexing scheme allows us to distinguish and analyze the values of variables for each unique combination of a firm and a specific year. 

## Analysis with panel data

An application of panel data will be done with the `Grunfeld` data set. Our analysis is about exploring the relationship between market value and gross investment. To explore this relationship, regression with panel data will be done. We start "simple" with cross-sectional and move on to using panel data in steps. Within these steps, the advantages of panel data will become clear!

### Cross-sectional data
Initially, we focus on utilizing cross-sectional data from the year 1935. To account for the influence of capital on investment, we incorporate the variable "capital" into our regression equation.

The multiple regression model is as follows:

{{<katex>}}
invest_{i} = \beta_0 + \beta_1 value_{i} + \beta_2 capital_{i} + \epsilon_{i}
{{</katex>}}

where,
- $invest_{i}$ is the gross investment of firm `i` 
- $value_{i}$ is the market value of assets of firm `i`
- $capital_{i}$ is the stock value of plant and equipment of firm `i`
- $\epsilon_{i}$ is the error term

{{% codeblock %}}
```R
Grunfeld1935 <- Grunfeld[Grunfeld$year== '1935',]

reg1 <- lm(invest ~ value + capital, data = Grunfeld1935)
summary(reg1)
```
{{% /codeblock %}}

[screenshot summary]

The coefficient of value is 0.118 and significant on a 1% level, which means that the regression results indicate a positive relationship between the value and investment of a firm.

#### Unobserved heterogeneity bias
Using only cross-sectional data may result in omission of variables that influence investment decisions. This omission can lead to biased and incomplete findings regarding the relationship between the value and investment we are trying to estimate. 

To address this issue, one possible solution is to include more variables in the equation to control for additional factors that impact gross investment decisions for firms. However, this is not straightforward. Many factors might be unobserved and impossible to control for. 

### Panel data 
The use of panel data becomes particularly advantageous here! Panel data allows for the inclusion of additional variables that vary over time and across different entities, effectively capturing the omitted factors that can affect investment decisions. 

This approach allows us to account for two types of unobserved effects, also known as fixed effects. 

- The first type are unobserved effects that are constant over time. In this case, they represent firm-specific characteristics that persistently influence investment behavior. Think of effects like Managerial Quality and Reputation of the firm that can have an effect on their investment decisions. For example, a higher managerial quality of a firm might result in a higher investment independent of firm value. 
- The second type is unobserved effects that vary over time. These time-varying unobserved effects capture factors that change over different periods and still affect investment decisions. Examples of this type of effects in our case could be the market conditions in a certain year. A year with better market conditions might bring higher investment independent on firm value. 

#### T = 2 (data of 2 years)

Let's first consider a data set with two time periods, specifically the years 1935 and 1940. Our objective is to eliminate firm-specific effects that remain constant over time, represented by the variable $Z_{i}$. We can achieve this by taking the difference between the observations in 1940 and 1935.

The multiple regression model is as follows:

{{<katex>}}
invest_{it} = \beta_0 + \beta_1 value_{it} + \beta_2 capital_{it} + \beta_3 Z_{i} + \mu_{it}
{{</katex>}}

where:
- $invest_{it}$ is the gross investment of firm `i` in year `t`
- $value_{it}$ is the market value of assets of firm `i` in year `t`
- $capital_{it}$ is the stock value of plant and equipment of firm `i` in year `t`
- $Z_{i}$ is the fixed effect for firm `i` (capturing unobserved firm-specific characteristics that differ between firms but remain constant over time)
- $\mu_{it}$ is the error term, which includes all other unobserved factors that affect investment but are not accounted for by the independent variables or the fixed effects.

Now, let's focus on the specific equations for `t = 1935` and `t = 1940`:

The regression equation for the year `t = 1935`:
{{<katex>}}
invest_{i1935} = \beta_0 + \beta_1 value_{i1935} + \beta_2 capital_{i1935} + \beta_3 Z_{i} + \mu_{i1935}
{{</katex>}}

And the regression equation for the year `t = 1940`:
invest_{i1940} = \beta_0 + \beta_1 value_{i1940} + \beta_2 capital_{i1940} + \beta_3 Z_{i} + \mu_{i1940}
{{</katex>}}

To eliminate the firm-specific characteristics $Z_{i}$ we can regress the difference in the investment variable between 1940 and 1935 on the difference in firm value between those years. This leads us to the differenced regression equation:

{{<katex>}}
invest_{i1940} - invest_{i1935} = \beta_1 (value_{i1940} - value_{i1935}) + \beta_2 (capital_{i1940} - capital_{i1935}) + (\mu_{i1940} - \mu_{i1935})
{{</katex>}}

By taking the difference between the observations in `t = 1940` and `t = 1935`, we effectively remove any variable that is constant over time from the analysis. This differencing approach helps control for unobserved heterogeneity that is constant over time. 

The following code helps estimating this regression in R:

```R
# subset data for years 1935 and 1940 
Grunfeld1935 <- Grunfeld[Grunfeld$year== '1935',]
Grunfeld1940 <- Grunfeld[Grunfeld$year== '1940',]

# compute the differences of variables between the years 
invest_diff <- Grunfeld1940$invest - Grunfeld1935$invest
value_diff <- Grunfeld1940$value - Grunfeld1935$value
capital_diff <- Grunfeld1940$capital - Grunfeld1935$capital

# regression 
reg2 <- lm(invest_diff ~ value_diff + capital_diff)
summary(reg2)

```

[screenshot]

$\beta_1$ is the coefficient of interest, showing the relationship between firm value and investment. The estimate has a value of `0.066`, significant at a 10% level. 

#### T $\ge$ (more than 2 years)

Now, we will consider a fixed effects regression, by using the full `Grunfeld` data set (T = 20). 
With only entity fixed effects, this is the fixed effects model:

{{<katex>}}
invest_{it} = \beta_0 + \beta_1 value_{it} + \beta_2 capital_{it} + \alpha_i + \epsilon_{it}
{{</katex>}}

where,
- $invest_{it}$ is the gross investment of firm `i` in year `t`
- $value_{it}$ is the market value of assets of firm `i` in year `t`
- $capital_{it}$ is the stock value of plant and equipment of firm `i` in year `t`
- $\alpha_i$ is the fixed effect for firm `i`
- $\epsilon_{it}$ is the error term, which includes all other unobserved factors that affect investment but are not accounted for by the independent variables or the fixed effects.

Time fixed effects capture the unobserved year-specific factors that are common to all firms in that year.
Adding both time and entity fixed effects, we will come to this general fixed effects model:

{{<katex>}}
invest_{it} = \beta_0 + \beta_1 value_{it} + \beta_2 capital_{it} + \alpha_i +  \delta_t + \epsilon_{it}
{{</katex>}}

where $\delta_t$ is the fixed effect for year `t`
 
*Intuition*
The intuition behind this fixed effects regression equation is relatively straightforward. You are adding dummy variables for each year and each firm to account for unobserved factors. By including these variables, you allow the intercept to vary across different observations. 

The intercept of an observation is: `$\beta_0$ + $\alpha_i$  + $\delta_t$`. 

Let's consider the observation of firm 5 in year 6 as an example: 
($value_{56}$, $invest_{56}$), where `i` $=$ 5 and `t` = 6

This observation has the intercept `$\beta_0$ + $\alpha_5$  + $\delta_t$`. 
$\beta_0$ represents the baseline intercept, $\alpha_5$ captures the firm-specific effect for firm 5 (taking a value of 1 for firm 5 and year 6, and 0 otherwise), and $\delta_6$ represents the year-specific effect for year 6.

The following code estimates this equation in R. For a more more thorough explanation on fixed effects regression in R and the package `fixest`, check the [`fixest` building block](https://tilburgsciencehub.com/building-blocks/analyze-data/regressions/fixest/).

```R
feols(invest ~ value + capital | firm + year , data = Grunfeld)
```

## Assumptions of Fixed Effects Regression 

The focus for the assumptions lays on entity fixed effects. 

In the fixed effects model

Y_it = beta_1 + X_it + alpha_i + u_it, i = 1,...,n, t = 1,...,T, 

we assume the following: 

1. The error term u_it has conditional mean zero, that is, E(u_it|X_i1,...,X_it = 0).
2. (X_i1, X_i2,...,X_it, u_i1,...,u_it), i = 1,...,n are i.i.d. draws from their joint distribution. 
3. Large outliers are unlikely, i.e., (X_it, u_it) have nonzero finite fourth moments
4. There is no perfect collinearity. 

When there are multiple regresors, X_it is replaced by X_1,it, X_2,it,..., X_k,it. 

Summary