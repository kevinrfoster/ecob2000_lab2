Lab \#2
================

## in class Thursday Sept 17, 2026

## Econ B2000, MA Econometrics

## Kevin R Foster, the Colin Powell School at the City College of New York, CUNY

## Fall 2026

<img src="2labs_c.jpg" style="width:25.0%" />

Overall Goal: Using Household Pulse data, consider how partnering (being
married or similar) varies with outcomes of interest. Note that we’re
not saying “caused” since that’s much tougher to demonstrate. Just find
some things that co-vary. We’ll use ggplot.

Start by writing down 3 variables (from the dataset) that you would
guess would have the strongest relationship to partnering Then we’ll
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

You might split the sample into groups, perhaps pick just people with
advanced degrees or maybe you’re particularly interested in Hispanic
women, or people in NY, or some other group.

Many students have issues picking a subset of the data, whether the
females or the prime-aged (with Age \>= 25 and Age \<= 55) or with
certain degree categories or whatever. Here is some pseudo-code:

``` r
# from orig_data, pick a subset

restrict1 <- (orig_data$x1 == 5) | (orig_data$x2 == "Blue")
data_new <- subset(orig_data,restrict1)

# alt with tidyverse
library(tidyverse)
data_new <- orig_data %>% filter((x1 == 5) | (x2 == "Blue"))
```

This outputs a new data frame with all the same variables as the
original data frame, but only for those folks with X1 value of 5 **or**
those with X2 value of Blue. The vertical line, `|`, symbolizes logical
**or**; an ampersand, `&`, would symbolize logical **and**. Those would
give different subgroups. The **or** combines together all the 5’s along
with all the Blues; the **and** would pick out just the Blue 5’s, the
overlap. Obviously you’d want to set your own restrictions, this is just
giving you the basic framework of how to create a subset to focus on
certain observations.

I show two methods, either with `subset` or with the `tidyverse`
library. The tidyverse code is cleaner but takes a bit more experience
to use.

Comparing two reasonable groups, what is the size of the difference in
outcome? What is the standard error of that difference measure? Using
your stats knowledge, how confident are you, that the difference is
actually there and not an artefact of sampling?

Look at the crosstabs and compute the marginal probabilities. How do
those inform? To check if you did it right, compute some of the marginal
probabilities using Bayes’ Theorem. Is your crosstab mutually exclusive
and exhaustive?

What other factors could explain the difference in outcome? Among your
list of differences in partnering, are there some potential confounders
such as age or education? What else?

How can some graphs help understand what these numbers are telling you?

What additional evidence would you look at? What conclusions could you
draw from that? How confident would you be, in the conclusions made?
What other conclusions could be drawn, from that same evidence? If you
were to try to persuade someone, imagine what evidence would be required
to persuade a person with the opposite view?

### Graph examples

``` r
library(tidyverse)
library(ggplot2)

# this would be different for you
setwd("..")
setwd("HPS_2020-24")

load("d_HHP2020_24.Rdata")

setwd("..")
setwd("ecob2000_lab2")
```

Note a few features of the data. I’ve created two income measures:
`income_midpoint` and `income_midpoint_factor`. The first is treated as
a number so it’s easy to find the average value. But perhaps too easy,
that might be misleading! The second is treated as a label. See the
differences in `summary`.

``` r
summary(d_HHP2020_24$income_midpoint)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
    ##   12500   40000   82500   95461  125000  225000  187771

``` r
summary(d_HHP2020_24$income_midpoint_factor)
```

    ##  12500  30000  40000  62500  82500 125000 175000 225000   NA's 
    ##  85405  67970  85421 134183 112727 145006  73407  92900 187771

People are asked to report their household income as being within a
range and I’ve created labels and numbers at the midpoint of that range.
(Well, not precisely since the upper range is “200,000 and above” which
I’ve coded as `225,000`. Obviously not the midpoint.)

The ranges are:

Less than 25,000  
25,000 - 34,999  
35,000 - 49,999  
50,000 - 74,999  
75,000 - 99,999  
100,000 - 149,999  
150,000 - 199,999 200,000 and above

Also for this exercise note that this is household income, so if two
people get married then if they’re both working their household income
will be larger than when they were single and living in separate
households.

I will show you some simple graphs to give a baseline for you to start.

First step, the whole dataset is almost a million observations so that’s
a bit large for some purposes. Here are some smaller versions,

``` r
# might want to plot a more modestly sized dataset, to start
HHP_NY <- d_HHP2020_24 %>% filter(State == "New York")

# or compare NY & NJ
HHP_NY_NJ <- d_HHP2020_24 %>% filter((State == "New York") | (State == "New Jersey") )
```

Here are some simple graphs.

``` r
p <- ggplot(data = HHP_NY_NJ,
            mapping = aes(x = Education, fill = State))
p + geom_bar()
```

![](lab2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Welp, that’s a graph. That’s where most of us start – not quite what we
wanted. This just gives the count of numbers of respondents in various
education categories but the number is not what we really want. And we
certainly don’t want the two numbers stacked.

``` r
p + geom_bar(position = "fill") # meh
```

![](lab2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

That graph is perhaps a bit more helpful – although not perhaps in
answering the question you wanted. This shows the fraction of each
education category who are living in the 2 states, so you can see that
NY has more than 50% of the people with less than a high school
education, while having fewer than 50% of the people with exactly a high
school diploma. But it’s still not all that great. That’s probably not
the proportion that we’re interested in.

``` r
p + geom_bar(mapping = aes(
  y = after_stat(prop),
  group = State)) 
```

![](lab2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

This finally shows proportions by state, but stacks histograms in a
weird way.

``` r
p + geom_bar(position = "dodge",
             mapping = aes(
               y = after_stat(prop),
               group = State)) # now compares the 2 histograms, might be useful 
```

![](lab2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

That’s a bit better – now this looks at the proportions, in each state,
who have different educational qualifications. In NY, slightly more than
30% of respondents have an advanced degree while in NJ it’s slightly
less than 30%. It’s tougher to see for the lower-educated groups but NY
also has a bit higher fraction there too. So NY has a higher fraction of
people in the extremes (either a little or a lot of education) while NJ
has more in the middle. Which is useful as a reminder that if we look at
the averages, we might miss that.

One reason I use NY and NJ is because you probably know people who have
moved between those states. It’s easy to fall into traps if you forget
that lots of people move around.

Those graphs aren’t about partnering, which I’d said we’d consider. Here
I create a dummy variable (note that this survey doesn’t have a response
for ‘living together’) for if a person is married or was once married
(answers that they divorced, separated, or were widowed).

``` r
d_HHP2020_24$partnered <- (d_HHP2020_24$Mar_Stat == "Married") | 
                      (d_HHP2020_24$Mar_Stat == "widowed") | 
                      (d_HHP2020_24$Mar_Stat == "divorced") |
                      (d_HHP2020_24$Mar_Stat == "separated")
# if ever partnered
xtabs( ~ Mar_Stat + partnered, data = d_HHP2020_24) # just to check
```

    ##            partnered
    ## Mar_Stat     FALSE   TRUE
    ##   Married        0 556611
    ##   widowed        0  54162
    ##   divorced       0 152705
    ##   separated      0  17850
    ##   never     195037      0

We’ll start by cutting the data into particular age groups, focusing on
people 18 - 45. Note that we want to be finicky about distinguishing `<`
from `<=` like in a math class. For age, that’s easy to do by listing
non-integers since the data always has age as integer.

``` r
HHP_NY_NJ_under45 <- HHP_NY_NJ %>% filter(Age < 45)

HHP_NY_NJ_under45$Age_groups <- cut(HHP_NY_NJ_under45$Age,
                            breaks = c(-Inf, 24.5, 29.5, 34.5, 39.5, Inf),
                            labels = c("under 25","25 to 29","30 to 34","35 to 39", "40 to 44"))

# check that it looks right:
HHP_NY_NJ_under45 %>% group_by(Age_groups) %>% summarize(mn = mean(Age))
```

    ## # A tibble: 5 × 2
    ##   Age_groups    mn
    ##   <fct>      <dbl>
    ## 1 under 25    21.8
    ## 2 25 to 29    27.2
    ## 3 30 to 34    32.1
    ## 4 35 to 39    37.1
    ## 5 40 to 44    42.0

Here’s a first try at graphing,

``` r
p_MS_Age <- ggplot(data = HHP_NY_NJ_under45,
            mapping = aes(x = Age_groups, fill = Mar_Stat))

p_MS_Age + geom_bar(position = "stack", stat = "count")  
```

![](lab2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

But the different numbers in different age groups means it’s not as easy
to read. Therefore try to make fractions of the age group. I’ll do this
in two stages to make it easier to see the steps – and also to check
if/when something goes wrong. First step is to calculate the fractions,

``` r
frac_MS_byAge <- HHP_NY_NJ_under45 %>% 
  group_by(Age_groups, Mar_Stat) %>% 
  summarize( n = n() ) %>% 
  mutate(freq_in_group = n / sum(n) )
```

    ## `summarise()` has grouped output by 'Age_groups'. You can override using the
    ## `.groups` argument.

Take a look at the output to see if that looks sensible. Then graph,

``` r
p_frac_MS_Age <- ggplot(data = frac_MS_byAge, 
                        mapping = aes(x = Age_groups, 
                                      y = freq_in_group * 100, # make a percentage
                                      fill = Mar_Stat))

p_frac_MS_Age + geom_bar(stat = "identity")  
```

![](lab2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

Try to make some more useful graphs! Work with your group both to
generate ideas but also to critique, to decide that some ideas didn’t
really work out.
