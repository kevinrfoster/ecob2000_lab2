Lab \#2
================

## in class Thursday Sept 14, 2023

## Econ B2000, MA Econometrics

## Kevin R Foster, the Colin Powell School at the City College of New York, CUNY

## Fall 2023

<img src="2labs_c.jpg" style="width:25.0%" />

Overall Goal: Using Household Pulse data, consider how outcomes of
interest vary with vaccination status. Groups should prepare a 2-min
presentation by one of the group members about their experiment process
and results. You get 30 min to prepare.

This time, we reverse the order of steps from previous: first look at
some of the data, then step back and think about it. (Not necessarily
stating which order is best, just having you do meta-experiments on what
is best way to do experiments.)

You might split the sample into groups: those who got a vaccine shot
(RECVDVACC == “yes got vaxx”), those who did not (RECVDVACC == “no did
not get vaxx”), but then you have to figure out how to deal with those
who did not answer (or did not provide an answer that matches to yes or
no) – i.e. where (RECVDVACC == “NA”) is true. What are some of the ways
that those groups differ?

(Many students have issues picking a subset of the data, whether the
females or the prime-aged (with Age \>= 25 and Age \<= 55) or working in
certain industries or with certain degree categories. Here is some
psuedo-code:

``` r
# from orig_data, pick a subset
restrict1 <- (orig_data$x1 == 5) | (orig_data$x2 == "Blue")
data_new <- subset(orig_data,restrict1)
```

This outputs a new data frame with all the same variables as the
original data frame, but only for those folks with X1 value of 5 **or**
those with X2 value of Blue. (Note that the vertical line symbolizes
logical **or**; an ampersand would symbolize logical **and**. Those give
different subgroups.) Obviously you’d want to set your own restrictions,
this is just giving you the basic framework of how to create a subset to
focus on certain observations.)

Now consider the experiment protocol after peeking at some of the data.
Do you want to limit the comparisons further? For example, you might
want to consider whether people who are not themselves vaxxed are less
likely to get their kids vaxxed – but you shouldn’t then compare people
without kids. Think about what is a reasonable comparison set.

Comparing those two reasonable groups, what is the size of the
difference in outcome? What is the standard error of that difference
measure? Using your stats knowledge, how confident are you, that the
difference is actually there and not an artefact of sampling?

Look at the crosstabs and compute the marginal probabilities. How do
those inform? To check if you did it right, compute some of the marginal
probabilities using Bayes’ Theorem. Is your crosstab mutually exclusive
and exhaustive?

What other factors could explain the difference in outcome? Among your
list of differences in vaxx status, are there some potential confounders
such as age or education? What else?

What additional evidence would you look at? What conclusions could you
draw from that? How confident would you be, in the conclusions made?
What other conclusions could be drawn, from that same evidence? If you
were to try to persuade someone, imagine what evidence would be required
to persuade a person with the opposite view?
