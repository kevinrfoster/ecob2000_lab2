Lab \#2
================

## in class Thursday Sept 12, 2024

## Econ B2000, MA Econometrics

## Kevin R Foster, the Colin Powell School at the City College of New York, CUNY

## Fall 2024

<img src="2labs_c.jpg" style="width:25.0%" />

Overall Goal: Using Household Pulse data, consider how outcomes of
interest vary with vaccination status. Note that we’re not saying
“caused” since that’s much tougher to demonstrate. Just find some things
that co-vary. We’ll use ggplot – you should be up to Chapter 4 in the
Healy book.

Start by writing down 3 variables (from the dataset) that you would
guess would have the strongest relationship to vax status. Then we’ll
form into groups and spend a few minutes talking through ideas to create
a group ranking of which are the strongest variables. Maybe talk about a
more subtle question: what variable do you think would be most
over-rated, that a lot of other people would think is important, but
isn’t really?

Sketch (without code, just draw pictures) some graphs which might
demonstrate those relations. It’s good to come up with ideas first, then
start trying to figure how to write code that will implement. In
micro-speak, I’m suggesting you start with indifference curves and then
work on the PPF.

The aim here is to combine both some graphs (I put examples below, which
I’d done in class) and also some statistics about means and standard
deviations within groups.

You might split the sample into groups: those who got a vaccine shot
(RECVDVACC == “yes got vaxx”), those who did not (RECVDVACC == “no did
not get vaxx”), but then you have to figure out how to deal with those
who did not answer (or did not provide an answer that matches to yes or
no) – i.e. where (RECVDVACC == “NA”) is true. What are some of the ways
that those groups differ?

Or you could split into groups based on your candidate variable(s) and
look at how much RECVDVACC changes between the groups.

Many students have issues picking a subset of the data, whether the
females or the prime-aged (with Age \>= 25 and Age \<= 55) or with
certain degree categories or whatever. Here is some pseudo-code:

``` r
# from orig_data, pick a subset
restrict1 <- (orig_data$x1 == 5) | (orig_data$x2 == "Blue")
data_new <- subset(orig_data,restrict1)
```

This outputs a new data frame with all the same variables as the
original data frame, but only for those folks with X1 value of 5 **or**
those with X2 value of Blue. The vertical line, \|, symbolizes logical
**or**; an ampersand, &, would symbolize logical **and**. Those would
give different subgroups. The **or** combines together all the 5’s along
with all the Blues; the **and** would pick out just the Blue 5’s, the
overlap. Obviously you’d want to set your own restrictions, this is just
giving you the basic framework of how to create a subset to focus on
certain observations.

Comparing two reasonable groups, what is the size of the difference in
outcome? What is the standard error of that difference measure? Using
your stats knowledge, how confident are you, that the difference is
actually there and not an artefact of sampling?

Look at the crosstabs and compute the marginal probabilities. How do
those inform? To check if you did it right, compute some of the marginal
probabilities using Bayes’ Theorem. Is your crosstab mutually exclusive
and exhaustive?

What other factors could explain the difference in outcome? Among your
list of differences in vax status, are there some potential confounders
such as age or education? What else?

How can some graphs help understand what these numbers are telling you?

What additional evidence would you look at? What conclusions could you
draw from that? How confident would you be, in the conclusions made?
What other conclusions could be drawn, from that same evidence? If you
were to try to persuade someone, imagine what evidence would be required
to persuade a person with the opposite view?

### Graph examples

``` r
require(tidyverse)
```

    ## Loading required package: tidyverse

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
require(ggplot2)

# this would be different for you
setwd("..\\..\\data\\Household Pulse Survey Phase4 Cycle2")
load("Household_Pulse_data_ph4c2.RData")

# sometimes numbers are more useful than categories

Household_Pulse_data$income_midpoint <- fct_recode(Household_Pulse_data$INCOME, 
                                                   "12500" = "HH income less than $25k",
                                                   "30000" = "HH income $25k - $34.9k",
                                                   "40000" = "HH income $35k - 49.9",
                                                   "62500" = "HH income $50k - 74.9",
                                                   "82500" = "HH income $75 - 99.9",
                                                   "125000" = "HH income $100k - 149",
                                                   "175000" = "HH income $150 - 199",
                                                   "225000" = "HH income $200k +",
                                                   NULL = "NA")
Household_Pulse_data$income_midpoint <- as.numeric(levels(Household_Pulse_data$income_midpoint))[Household_Pulse_data$income_midpoint]


Household_Pulse_data$Educ_years <- fct_recode(Household_Pulse_data$EEDUC,
                                              "8" = "less than hs",
                                              "11" = "some hs",
                                              "12" = "HS diploma",
                                              "13" = "some coll",
                                              "14" = "assoc deg",
                                              "16" = "bach deg",
                                              "18" = "adv deg")

Household_Pulse_data$Educ_years <- as.numeric(levels(Household_Pulse_data$Educ_years))[Household_Pulse_data$Educ_years]

Household_Pulse_data$Age <- 2024 - Household_Pulse_data$TBIRTH_YEAR

# this looks at just 3 categories
Household_Pulse_data$Education <- fct_recode(Household_Pulse_data$EEDUC, 
                                             "high school" = "HS diploma",
                                             "some college" = "some coll",
                                             "some college" = "assoc deg",
                                             "college grad" = "bach deg",
                                             "college grad" = "adv deg",
                                             NULL = "less than hs",
                                             NULL = "some hs")




# might want to plot a more modestly sized dataset, to start
HHP_NY <- Household_Pulse_data %>% filter(EST_ST == "New York")

# or compare NY & NJ
HHP_NY_NJ <- Household_Pulse_data %>% filter((EST_ST == "New York") | (EST_ST == "New Jersey") )


p <- ggplot(data = HHP_NY_NJ,
            mapping = aes(x = EEDUC, fill = EST_ST))
p + geom_bar()
```

![](lab2_F24_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
# compares just numbers in 2 states
p + geom_bar(position = "fill") # meh
```

![](lab2_F24_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

``` r
p + geom_bar(mapping = aes(
  y = after_stat(prop),
  group = EST_ST)) # shows proportions but stacks histograms in weird way
```

![](lab2_F24_files/figure-gfm/unnamed-chunk-2-3.png)<!-- -->

``` r
p + geom_bar(position = "dodge",
             mapping = aes(
               y = after_stat(prop),
               group = EST_ST)) # now compares the 2 histograms, might be useful 
```

![](lab2_F24_files/figure-gfm/unnamed-chunk-2-4.png)<!-- -->

``` r
# a bit nicer stuff
library(viridis)
```

    ## Loading required package: viridisLite

``` r
HHP_use <- Household_Pulse_data %>% filter( TBIRTH_YEAR > 1936 )
HHP_use <- HHP_use %>% drop_na(Education) 


HHP_use$religious_n <- fct_recode(HHP_use$SUPPORT3, 
                                              "1" = "attend church or religious ceremony never or less than 1 per year",
                                              "2" = "1 to 3 per year",
                                              "3" = "4 to 11 per year",
                                              "4" = "12+ times per year",
                                              NULL = "NA")
HHP_use$religious_n <- as.numeric(levels(HHP_use$religious_n))[HHP_use$religious_n]


HHP_graph1 <- HHP_use %>% drop_na(religious_n)

p_age_religion <- ggplot(data = HHP_graph1,
                         mapping = aes(x = Age,
                                       y = religious_n,
                                       color = Education,
                                       fill = Education))

p_age_religion + geom_smooth(aes(color=Education, fill=Education)) + 
  scale_color_viridis_d(option = "magma", end = 0.75) + 
  scale_fill_viridis_d(option = "inferno", end = 0.75) + 
  labs(x = "Age", y = "religious attendance", fill = "Education") + guides(color = "none")
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

![](lab2_F24_files/figure-gfm/unnamed-chunk-2-5.png)<!-- -->
