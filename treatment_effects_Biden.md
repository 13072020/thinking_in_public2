# Trump as control, Biden as treatment

The goal of this document is to demystify the OLS estimation of treatment effects (ATE and DiD), by showing how to obtain the same result manually.

The 2016 + 2020 US presidential elections are considered as an experiment, with the Democratic nominee change from Hillary Clinton to Joe Biden being the treatment/intervention.

This is just a numerical exercise and not a causal analysis.

- DiD: 4,184,084 votes
- ATE: 13,584,092 votes

Votes|2016|2020|% change
---|---|---|---
Republican|62,984,828|74,216,154|+17.83%
Democrat|65,853,514|81,268,924|+23.41%
Total|128,838,342|155,485,078|+20.68%

<mark>The #1 thing to take away from this is that when using OLS, DiD considers BOTH "before" AND "after" observations of the treated group to be "treated", whereas ATE only considers the "after" as treated.</mark>

# DiD (difference-in-differences) manually

.|difference
---|---
Republican gain|74,216,154 - 62,984,828 = 11,231,326
Democrat gain|81,268,924 - 65,853,514 = 15,415,410
DiD|15,415,410 - 11,231,326 = <mark>4,184,084</mark>

# ATE (and ATT) manually

We don't observe the outcome if the treated weren't treated, or if the untreated were treated (the counterfactuals, '?'):

party, election|treatment (Biden)|votes w/ treatment|votes w/o treatment|votes w - w/o (treatment effect)
---|---|---|---|---
R 2016|0|?|62,984,828|.
R 2020|0|?|74,216,154|.
D 2016|0|?|65,853,514|.
D 2020|1|81,268,924|?|.

So we fill them in with the column average:

party, election|treatment (Biden)|votes w/ treatment|votes w/o treatment|votes w - w/o (treatment effect)
---|---|---|---|---
R 2016|0|<mark>81,268,924</mark>|62,984,828|18,284,096
R 2020|0|<mark>81,268,924</mark>|74,216,154|7,052,770
D 2016|0|<mark>81,268,924</mark>|65,853,514|15,415,410
D 2020|1|81,268,924|<mark>67,684,832</mark>|13,584,092

We only have one observation of Biden running (and winning 81,268,924 votes), so, strange though it may seem, that is our best guess (in this outrageously simple framework) for the votes he would have obtained in any other year or for any other party.

67,684,832 is the expected votes without the Biden treatment. We average the outcomes over the times he didn't run. This is our estimate for the counterfactual in which the Democratic nominee in 2020 was someone other than Biden.

`(62,984,828 + 74,216,154 + 65,853,514)/3 = 67,684,832`

We have guessed/estimated counterfactuals using averages. Now, for each observation, we can take the expected votes had Biden been candidate (or votes if he actually was), and subtract actual or expected votes without this 'Biden treatment', to get the treatment effect for each observation, ie the expected change in votes by having Biden as the party nominee for that particular election.

The ATE is the average of all the values in the last column.

`(18,284,096 + 7,052,770 + 15,415,410 + 13,584,092)/4 = 13,584,092`

We can breakdown the above calculation into a weighted average of the ATT (average treatment effect on the treated, aka ATET), and the ATU (average treatment effect on the untreated):

- ATT = 13,584,092
- ATU = (18,284,096 + 7,052,770 + 15,415,410)/3 = 13,584,092
- ATE = (1/4) ATT + (3/4) ATU = 13,584,092

In this simple example, the number `13,584,092` keeps coming up, because the "votes w/ treatment" is the same for every observation, so the ATT is equal to the ATU, whose average will then also necessarily be equal.

$$\text{ATE} = \frac{1}{N} \sum_i (y_1(i) - y_0(i))$$


# DiD and ATE in R using OLS

DiD is the estimated coefficient of the interaction term $\beta_3$:

$$
\begin{align*}
votes & = \beta_0 \\
        & + \beta_1\ treatment\_did \\
        & + \beta_2\ time\_period \\
        & + \beta_3\ treatment\_did \cdot time\_period \\
        & + u
\end{align*}
$$

For DiD, also note:

- const = 62984828 = <mark>R2016</mark>
- const + time period = 62984828 + 11231326 = 74216154 = <mark>R2020</mark>
- const + treatment = 62984828 + 2868686 = 65853514 = <mark>D2016</mark>
- const + time period + treatment + interaction = 62984828 + 2868686 + 11231326 + 4184084 = 81268924 = <mark>D2020</mark>


party, election|y (outcome, votes)|treatment|time period|interaction
---|---|---|---|---
R 2016|62,984,828|0|0|0
R 2020|74,216,154|0|1|0
D 2016|65,853,514|1|0|0
D 2020|81,268,924|1|1|1


```r
# DiD
treatment_did <- c(0, 0, 1, 1)
time_period <- c(0, 1, 0, 1)
interaction <- treatment_did * time_period # this is the same as c(0, 0, 0, 1)

R2016 <- 62984828
R2020 <- 74216154
D2016 <- 65853514
D2020 <- 81268924
votes <- c(R2016, R2020, D2016, D2020)

coef(lm(votes ~ treatment_did + time_period + interaction))
#(Intercept)   treatment_did time_period interaction 
#   62984828         2868686    11231326     4184084
```

```r
# ATE
treatment_ate = c(0, 0, 0, 1)
coef(lm(votes ~ treatment_ate))
#Output:
#  (Intercept) treatment_ate
#     67684832      13584092
```

# DiD and ATE in Python using OLS

```python
# DiD
import pandas as pd
import statsmodels.api as sm

index = ['R 2016', 'R 2020', 'D 2016', 'D 2020'] # for readability, not necessary
X = pd.DataFrame({'treatment': [0, 0, 1, 1], 'time period': [0, 1, 0, 1]}, index=index)
X['const'] = 1 # will create constant column [1, 1, 1, 1]
X['interaction'] = X['treatment'] * X['time period']

# created these vote variables for readability & reusability
R2016 = 62_984_828
R2020 = 74_216_154
D2016 = 65_853_514
D2020 = 81_268_924
y = pd.DataFrame({'votes': [R2016, R2020, D2016, D2020]}, index=index)

print(X, "\n") # \n is newline
print(y, "\n")
print(sm.OLS(y, X[['const', 'treatment', 'time period', 'interaction']]).fit().params)

#        treatment  time period  const  interaction
#R 2016          0            0      1            0
#R 2020          0            1      1            0
#D 2016          1            0      1            0
#D 2020          1            1      1            1

#           votes
#R 2016  62984828
#R 2020  74216154
#D 2016  65853514
#D 2020  81268924 

#const          62984828.0
#treatment       2868686.0
#time period    11231326.0
#interaction     4184084.0
```


```python
# ATE
X['treatment_ate'] = [0, 0, 0, 1]
print(sm.OLS(y, X[['const', 'treatment_ate']]).fit().params)

#const            67684832.0
#treatment_ate    13584092.0
```


# Sources

- https://www.causalflows.com/estimating-average-treatment-effects/
- https://stats.stackexchange.com/questions/308397/why-is-average-treatment-effect-different-from-average-treatment-effect-on-the-t
- https://bookdown.org/paul/applied-causal-analysis/ate.html
- https://bookdown.org/paul/applied-causal-analysis/ate-decomposition.html
- https://bookdown.org/paul/applied-causal-analysis/independenceassumption2.html