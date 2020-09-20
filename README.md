Lab \#2
================

## in class Monday Sept 21, 2020

## Econ B2000, MA Econometrics

## Kevin R Foster, the Colin Powell School at the City College of New York, CUNY

## Fall 2020

![](2labs.jpg)

Overall Goal: Using PUMS data, consider how outcomes of interest vary
with college major choice. Groups should prepare a 1-min presentation by
one of the group members about their experiment process and results. You
get 45 min to prepare.

This time, we reverse the order of steps from previous: first look at
some of the data, then step back and think about it. (Not necessarily
stating which order is best, just having you do meta-experiments on what
is best way to do experiments.)

Load the ACS PUMS data and look at which workers, with different college
majors, are in industries that turned out to be at high risk for
unemployment due to Covid – the retail sector, arts, entertainment,
accommodation and restaurants. Here’s some code that will create a dummy
variable for workers in those sectors.

``` r
 acs2017_ny$IND_number <- as.numeric(levels(acs2017_ny$IND))[acs2017_ny$IND]
 acs2017_ny$Covid_risk <- ((acs2017_ny$IND_number > 4600) & acs2017_ny$IND_number < 6000) | ((acs2017_ny$IND_number > 8500) & (acs2017_ny$IND_number < 8700))
```

For now don’t get lost in looking at every major but maybe pick a couple
of majors to compare (for example, Econ & Psych, but now I’ve stolen
that idea so pick a different one).

How do the two groups differ, in who chooses that major? Are there
differences in gender, race/ethnicity, ancestry, immigration status?
What other outcomes differ – for example, neighborhood?

Now consider the experiment protocol after peeking at some of the data.
You’re implicitly comparing a subgroup, of those people who finished a
4-year degree and reported information. What additional restrictions
might you impose? Should you include retired people? People not in the
labor force?

Looking at that subgroup, what is the size of the difference in outcome?
What is the standard error of that difference measure? Using your stats
knowledge, how confident are you, that the difference is actually there
and not an artefact of sampling?

Look at the crosstabs and compute the marginal probabilities. How do
those inform? To check if you did it right, compute some of the marginal
probabilities using Bayes’ Theorem. Is your crosstab mutually exclusive
and exhaustive?

What other factors could explain the difference in outcome? Among your
list of differences in who chooses the major, are there some potential
confounders? Can you look at other educational attainments?

What additional evidence would you look at? What conclusions could you
draw from that? How confident would you be, in the conclusions made?
What other conclusions could be drawn, from that same evidence? If you
were to try to persuade someone, imagine what evidence would be required
to persuade a person with the opposite view?

I’ve included a file that gives the details of the IND coding, if you
want to get deeper into the weeds of this analysis.
