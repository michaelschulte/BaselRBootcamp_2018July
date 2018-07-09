Statistics
================
BaselRBootcamp July 2018<br/><a href='https://therbootcamp.github.io'>www.therbootcamp.com</a><br/><a href='https://twitter.com/therbootcamp'>@therbootcamp</a>

<figure>
<center>
<img src="https://github.com/therbootcamp/therbootcamp.github.io/blob/master/_sessions/_image/lynda_com_stats.jpg?raw=true" alt="Trulli" style="width:70%">
<figcaption>
<https://cdn.lynda.com>
</figcaption>
</figure>
Overview
--------

In this practical you'll do basic statistics in R. By the end of this practical you will know how to:

1.  Calculate basic descriptive statistics using `mean()`, `median()`, `table()`.
2.  Conduct 1 and 2 sample hypothesis tests with `t.test()`, `cor.test()`
3.  Calculate regression analyses with `glm()` and `lm()`
4.  Explore statistical objects with `names()`, `summary()`, `print()`, `predict()`
5.  Use sampling functions such as `rnorm()` to conduct simulations

Packages
--------

| Package     | Installation                    |
|:------------|:--------------------------------|
| `tidyverse` | `install.packages("tidyverse")` |
| `lubridate` | `install.packages("lubridate")` |
| `broom`     | `install.packages("broom")`     |
| `rsq`       | `install.packages("rsq")`       |

### Glossary

*Descriptive Statistics*

| Function                   | Description                  |
|:---------------------------|:-----------------------------|
| `table()`                  | Frequency table              |
| `mean(), median(), mode()` | Measures of central tendency |
| `sd(), range(), var()`     | Measures of variability      |
| `max(), min()`             | Extreme values               |
| `summary()`                | Several summary statistics   |

*Statistical Tests*

| Function        | Hypothesis Test                           |
|:----------------|:------------------------------------------|
| `t.test()`      | One and two sample t-test                 |
| `cor.test()`    | Correlation test                          |
| `glm()`, `lm()` | Generalized linear model and linear model |

*Sampling Functions*

| Function   | Description                                    | Additional Help |
|:-----------|:-----------------------------------------------|:----------------|
| `sample()` | Draw a random sample of values from a vector   | `?sample`       |
| `rnorm()`  | Draw random values from a Normal distribution  | `?rnorm()`      |
| `runif`    | Draw random values from a Uniform distribution | `?runif()`      |

### Examples

``` r
# -----------------------------------------------
# Examples of hypothesis tests on the diamonds
# ------------------------------------------------
library(tidyverse)
library(broom)
library(rsq)

# First few rows of the diamonds data

diamonds

# -----
# Descriptive statistics
# -----

mean(diamonds$carat)   # What is the mean carat?
median(diamonds$price)   # What is the median price?
max(diamonds$depth)    # What is the maximum depth?
table(diamonds$color)    # How many observations for each color?

# -----
# 1-sample hypothesis test
# -----

# Q: Is the mean carat of diamonds different from .50?

htest_A <- t.test(x = diamonds$carat,     # The data
                  alternative = "two.sided",  # Two-sided test
                  mu = 0.5)                   # The null hyopthesis

htest_A            # Print result
names(htest_A)     # See all attributes in object
htest_A$statistic  # Get just the test statistic
htest_A$p.value    # Get the p-value
htest_A$conf.int   # Get a confidence interval

# -----
# 2-sample hypothesis test
# -----

# Q: Is there a difference in the carats of color = E and color = I diamonds?

htest_B <- t.test(formula = carat ~ color,     # DV ~ IV
                   alternative = "two.sided",  # Two-sided test
                   data = diamonds,         # The data
                   subset = color %in% c("E", "I")) # Compare Diet 1 and Diet 2

htest_B  # Print result

# -----
# Correlation test
# ------

# Q: Is there a correlation between carat and price?

htest_C <- cor.test(formula = ~ carat + price,
                    data = diamonds)

htest_C

# A: Yes. r = 0.92, t(53938) = 551.51, p < .001

# -----
# Regression
# ------

# Q: Create regression equation predicting price by carat, depth, table, and x

price_glm <- glm(formula = price ~ carat + depth + table + x,
                  data = diamonds)

# Print coefficients
price_glm$coefficients

# Tidy version
tidy(price_glm)

# Extract R-Squared
rsq(price_glm)

# -----
# Simulation
# ------

# 100 random samples from a normal distribution with mean = 0, sd = 1
samp_A <- rnorm(n = 100, mean = 0, sd = 1)

# 100 random samples from a Uniform distribution with bounds at 0, 10
samp_B <- runif(n = 100, min = 0, max = 10)

# Calculate descriptives
mean(samp_A)
sd(samp_A)

mean(samp_B)
sd(samp_B)

# Combine samples (plus tw new ones) in a tibble

my_sim <- tibble(A = samp_A,
                 B = samp_B,
                 C = rnorm(n = 100, mean = 0, sd = 1),
                 error = rnorm(n = 100, mean = 5, sd = 10))

# Add y, a linear function of A and B to my_sim
my_sim <- my_sim %>%
  mutate(y = 3 * A -8 * B + error)

# Regress y on A, B and C
my_glm <- glm(y ~ A + B + C,
              data = my_sim)

# Look at results!
tidy(my_glm)
```

Tasks
=====

### Datasets

<table>
<colgroup>
<col width="8%" />
<col width="9%" />
<col width="11%" />
<col width="59%" />
<col width="11%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">File</th>
<th align="left">Rows</th>
<th align="left">Columns</th>
<th align="left">Description</th>
<th align="left">Source</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><code>kc_house.csv</code></td>
<td align="left">21613</td>
<td align="left">21</td>
<td align="left">House sale prices for King County between May 2014 and May 2015.</td>
<td align="left"><a href="https://www.kaggle.com/harlfoxem/housesalesprediction/home">Kaggle: House Sales Prediction</a></td>
</tr>
</tbody>
</table>

### A - Getting setup

1.  Open your R project. It should already have the folders `0_Data` and `1_Code`. Make sure that all of the data files listed above are contained in the folder

2.  Open a new R script and save it as a new file called `statistics_practical.R` in the `2_Code` folder. At the top of the script, using comments, write your name and the date. The, load the set of packages listed above with `library()`.

3.  For this practical, we'll use the `kc_house_data.csv` data. This dataset contains house sale prices for King County, Washington. It includes homes sold between May 2014 and May 2015. Using the following template, load the data into R and store it as a new object called `kc_house`.

``` r
kc_house <- read_csv(file = "XX/XXX")
```

1.  Using `print()`, `summary()`, `head()` and `skim()`, explore the data to make sure it was loaded correctly.

### B - Descriptive statistics

1.  What was the mean price (`price`) of all houses?

2.  What was the median price (`price`) of all houses?

3.  What was the standard deviation price (`price`) of all houses?

4.  What were the minimum and maximum prices? Try using `min()`, `max()`, and `range()`

5.  How many houses were on the waterfront (`waterfront`) and how many were not? (Hint: use `table()`)

### C - T tests with t.test()

1.  Do houses on the waterfront sell for more than those not on the waterfront? Answer this by creating an object called `water_htest` which contains the result of the appropriate t-test using `t.test()`. Use the following template

``` r
water_htest <- t.test(formula = XX ~ XX,
                      data = XX)
```

1.  Print your `water_htest` object to see summary results

2.  Apply the `summary()` function to `water_htest`. Do you see anything new or important?

3.  Look at the names of the `water_htest` with `names()`. What are the named elements of the object?

4.  Using the `$` operator, print the exact test statistic of the t-test.

5.  Again using the `$` operator, print the exact p-value of the test.

6.  Use the `tidy()` function to return a dataframe containing the main results of the test.

7.  Do houses on the waterfront also tend to have more (or less) living space (`sqft_living`)? Answer this by conducting the appropriate t-test and going through the same steps you did in the previous question

### D - Correlation tests with cor.test()

1.  Do newer houses tend to sell for more than older houses? Answer this by creating an object called `time_htest` which contains the result of the appropriate correlation test using `cor.test()`. Use the following template:

``` r
# Note: cor.terst() is the only function (we know of)
#  that uses formulas in the strange format
#   formula = ~ X + Y instead of formula = Y ~ X

time_htest <- cor.test(formula = ~ XX + XX,
                       data = XX)
```

1.  Print your `time_htest` object to see summary results

2.  Apply the `summary()` function to `time_htest`. Do you see anything new or important?

3.  Look at the names of the `time_htest` with `names()`. What are the named elements of the object?

4.  Using the `$` operator, print the exact correlation of the test.

5.  Again using the `$` operator, print the exact p-value of the test.

6.  Use the `tidy()` function to return a dataframe containing the main results of the test.

7.  Do houses in in better overall condition `condition` tend to sell at higher prices than those in worse condition? Answer this by conducting the appropriate correlation test and going through the same steps you did in the previous question.

### E - Linear Regression with lm() and glm()

1.  In the previous question, you used a correlation test to see if newer houses tend to sell for more than older houses. Now let's repeat the same analysis using a regression analysis with `lm()`. Do to this, use the following template:

``` r
time_lm <- lm(formula = XX ~ XX,
                 data = XX)
```

1.  Print your `time_lm` object to see the main results.

2.  Use the `tidy()` function (from the `broom` package) to return a dataframe containing the main results of the test.

3.  How does the p-value of this test compare to what you got in the previous question when you used `cor.test()`?

4.  Which of the variables `bedrooms`, `bathrooms`, and `sqft_living` predict housing price? Answer this by conducting the appropriate regression analysis using `lm()` and assigning the result to an object `price_lm`. Use the following template:

``` r
price_lm <- lm(formula = XX ~ XX + XX + XX,
                 data = XX)
```

1.  Print your `price_glm` object to see the main results.

2.  Apply the `summary()` function to `price_glm`. Do you see anything new or important?

3.  Look at the names of the `price_glm` with `names()`. What are the named elements of the object?

4.  Using the `$` operator, print a vector of the estimated coefficients of the analysis.

5.  Use the `tidy()` function (from the `broom` package) to return a dataframe containing the main results of the test.

6.  Use the `rsq()` function (from the `rsq` package) to obtain the R-squared value. Does this match what you saw in your previous outputs?

7.  Using the following template, create a new model called `everything_lm` that predicts housing prices based on *all* predictors in `kc_house` *except* for `id` and `date` (`id` is meaningless and date shouldn't matter). Use the following template. Note that to include all predictors, we'll use the `formula = y ~.` shortcut. We'll also remove id and date from the dataset using `select()` before running the analysis.

``` r
everything_lm <- lm(formula = XX ~.,
                    data = kc_house %>% 
                             select(-id, -date))
```

1.  Print your `everything_lm` object to see the main results.

2.  Apply the `summary()` function to `everything_lm`. Do you see anything new or important?

3.  Using the `$` operator, print a vector of the estimated coefficients of the analysis. Are the beta values for bedrooms, bathrooms, and sqft\_living different from what you got in your previous analysis `price_lm`?

4.  Use the `tidy()` function to return a dataframe containing the main results of the test.

5.  Use the `rsq()` function (from the `rsq` package) to obtain the R-squared value. How does this R-squared value compare to what you got in your previous regression `price_lm`?

6.  How well did the `everything_lm` model fit the actual housing prices? We can answer this by calculating the average difference between the fitted values and the true values directly. Using the following template, make this calculation. What do you find is the mean absolute difference between the fitted housing prices and the true prices?

``` r
# True housing prices
prices_true <- kc_house$XX

# Model fits (fitted.values)
prices_fitted <- everything_lm$XX

# Calculate absolute error between fitted and true values
abs_error <- abs(XX - XX)

# Calculate mean absolute error
mae <- mean(XX)

# Print the result!
mae
```

19.. Using the following template, create a scatterplot showing the relationship between the fitted values of the `everything_lm` object and the true prices.

``` r
# Create dataframe containing fitted and true prices
prices_results <- tibble(truth = XX,
                         fitted = XX)

# Create scatterplot
ggplot(data = prices_results,
       aes(x = fitted, y = truth)) + 
  geom_point(alpha = .1) +
  geom_smooth(method = 'lm') +
  labs(title = "Fitted versus Predicted housing prices",
       subtitle = paste0("Mean Absolute Error = ", mae),
       caption = "Source: King County housing price Kaggle dataset",
       x = "Fitted housing price values",
       y = 'True values') + 
  theme_minimal()
```

### F - Logistic regression with glm(family = 'binomial')

1.  In the next set of analyses, we'll use logistic regression to predict whether or not a house will sell for over $1,000,000. First, we'll need to create a new binary variable in the `kc_house` called `million` that is 1 when the price is over 1 million, and 0 when it is not. Run the following code to create this new variable

``` r
# Create a new binary variable called million that
#  indicates when houses sell for more than 1 million

# Note: 1e6 is a shortcut for 1000000

kc_house <- kc_house %>%
  mutate(million = price > 1e6)
```

1.  Using the following template, use the `glm()` function to conduct a logistic regression to see which of the variables `bedrooms`, `bathrooms`, `floors`, `waterfront`, `yr_built` predict whether or not a house will sell for over 1 Million. Be sure to include the argument `family = 'binomial'` to tell `glm()` that we are conducting a logistic regression analysis.

``` r
# Logistic regression analysis predicting which houses will sell for
#   more than 1 Million

million_glm <- glm(formula = XX ~ XX + XX + XX + XX + XX,
                   data = kc_house,
                   family = "XX")
```

1.  Print your `million_glm` object to see the main results.

2.  Apply the `summary()` function to `million_glm`.

3.  Using the `$` operator, print a vector of the estimated beta values (coefficients) of the analysis.

4.  Use the `tidy()` function to return a dataframe containing the main results of the test.

5.  You can get the fitted probability predictions that each house will sell for more than 1 Million by accessing the vector `million_glm$fitted.values`. Using this vector, calculate the average probability that houses will sell for more than 1 Million (hint: just take the mean!)

6.  Using the following code, create a data frame showing the relationship between the fitted probability that a house sells for over 1 Million and the true probability:

``` r
# Just run it!

million_fit <- tibble(pred_million = million_glm$fitted.values,
                      true_million = kc_house$million) %>%
                mutate(fitted_cut = cut(pred_million, breaks = seq(0, 1, .1))) %>%
                group_by(fitted_cut) %>%
                summarise(true_prob = mean(true_million))

million_fit
```

1.  Now plot the results using the following code:

``` r
# Just run it!

ggplot(million_fit, 
       aes(x = fitted_cut, y = true_prob, col = as.numeric(fitted_cut))) + 
  geom_point(size = 2) +
  labs(x = "Fitted Probability",
       y = "True Probability",
       title = "Predicting the probability of a 1 Million house",
       subtitle = "Using logistic regression with glm(family = 'binomial')") +
  scale_y_continuous(limits = c(0, 1)) +
  guides(col = FALSE)
```

### G - Transforming variables

1.  In most real world datasets, distributions of housing prices are heavily skewed, where most houses sell for a small to moderate amount, and a small number of houses sell for much more. Is this the case in the `kc_house` dataset? Find out by creating a histogram of `price` with the following code:

``` r
# Just run it!

# Create a histogram of housing prices
ggplot(data = kc_house,
       aes(x = log(price), y = ..density..)) +
  geom_histogram(col = "white") + 
  geom_density(col = "red") + 
  theme_minimal() +
  labs(title = "Housing prices (original)")
```

1.  If you find the data were heavily skewed, try looking at a histogram of the log transformed housing prices using the `log()` function

2.  Using the following code, add a new variable called `price_l` to `kc_house` that contains log--transformed prices.

``` r
# Add price_l, log-transformed price, to kc_house
kc_house <- kc_house %>%
  mutate(price_l = log(price))
```

1.  Now that you've created `price_l`, try repeating your previous regression analyses. Do you get the same results? If not, which results do you trust more?

### H - Using predict() to predict new values

1.  Your friend Donald just bought a house whose building records have been lost. She wants to know what year her house was likely built. Help Donald figure out when his house was built by conducting the appropriate regression analysis, and then using the specifics of his house to predict the year that his house was built using the `predict()` function. You know that his house is on the waterfront, has 3 bedrooms, 2 floors and has a condition of 4. The following block of code may help you!

``` r
# Create regression model predicting year (yr_built)
year_lm <- lm(formula = XX ~ XX + XX + XX + XX,
              data = XX)

# Define Donald's House
DonaldsHouse <- tibble(waterfront = X,
                       bedrooms = X,
                       floors = X,
                       condition = X)

# Predict the hear of donald's house
predict(object = year_lm, 
        newdata = DonaldsHouse)
```

### X - Advanced

1.. Do houses on the waterfront tend to have smaller lots than those not on the waterfront? Do this once using a correlation test with `cor.test()` and once using `lm()`. Do your results differ?

1.  Which variables in the dataset significantly predict the grade of a house? Answer this with a regression analysis.

2.  Is there an interaction between waterfront and year built (`yr_built`) on price? In other words, is the relationship between year built and price the same for homes on the waterfront and homes not on the waterfront? Answer this by conducting the appropriate regression analysis and interpret the results. Note: to include an interaction in a model, use the formula `y ~ X1 * X2` instead of `y ~ X1 + X2`.

3.  How well can a regression analysis based on houses sold in 2014 predict housing costs in the 2015? To answer this we'll create a regression model based on houses sold in 2014 and then use that model to predict the prices of houses sold in 2015. To start, we'll need to create two new dataframes `kc_house_2014` and `kc_house_2015`. Run the code in the following chunk to do this.

``` r
# Just run it!

# Add year_sold to kc_house

kc_house <- kc_house %>%
  mutate(year_sold = year(ymd(date)))

# Create kc_house_2014, 
#    Only houses sold in 2014
kc_house_2014 <- kc_house %>%
  filter(year_sold == 2014)

# Create kc_house_2015, 
#     Only houses sold in 2015
kc_house_2015 <- kc_house %>%
  filter(year_sold == 2015)
```

1.  Create a regression model called `price_2014_lm` that models housing prices in 2014 using only the `kc_house_2014` data. Note that you may wish to exclude some variables such as `id`, `date`, `million` and `year_sold`. You may also wish to log--transform the price data!

2.  Create a regression model called `price_2015_lm` that models housing prices in 2015 using only the `kc_house_2015` data. Include the same exclusion and transformation criterion you used in the previous question.

3.  Explore and compare the two models, do they look very similar? Very different?

4.  Calculate the mean absolute error of each model applied to the dataset in which they were created. That is, what was the mean absolute fitted error of each model?

5.  Using `predict()`, predict the 2015 housing data based on the `price_2014_lm` model.

6.  Calculate the mean absolute error of the `price_2014_lm` model when applied to the 2015 data. How different was the mean error compared to the mean fitting error of the `price_2015_lm` model?

### Generating random samples from distributions

1.  You can easily generate random samples from statistical distributions in R. To see all of them, run `?distributions`. For example, to generate samples from the well known Normal distribution, you can use `rnorm()`. Look at the help menu for `rnorm()` to see its arguments.

2.  Let's explore the `rnorm()` function. Using `rnorm()`, create a new object `samp_100` which is 100 samples from a Normal distribution with mean 10 and standard deviation 5. Print the object to see what the elements look like. What should the mean and standard deviation of this sample? be? Test it by evaluating its mean and standard deviation directly using the appropriate functions. Then, do a one-sample t-test on this sample against the null hypothesis that the true mean is 10 What are the results? Use the following code template to help!

``` r
# Generate 100 samples from a Normal distribution with mean = 100 and sd = 5
samp_100 <- rnorm(n = XX, mean = XX, sd = XX)

# Print result
samp_100

# Calcultae sample mean and standard deviation.
mean(XX)
sd(XX)

t.test(x = XX,   # Vector of values
       mu = XX)  # Mean under null hypothesis
```

1.  Repeat the previous block of code several times and look at how the output changes. How do the p-values change when you keep drawing random samples?

2.  Change the mean under the null hypothesis to 15 instead of 10 and run the code again several times. What happens to the p-values?

3.  Look at the code below. As you can see, I generate 4 variables x1, x2, x3 and noise. I then create a dependent variable y that is a function of these variables. Finally, I conduct a regression analysis. *Before you run the code*, what do you expect the value of the `coefficients` of the regression equation will be? Test your prediction by running the code and exploring the `my_lm` object.

``` r
# Generate independent variables
x1 <- rnorm(n = 100, mean = 10, sd = 1)
x2 <- rnorm(n = 100, mean = 20, sd = 10)
x3 <- rnorm(n = 100, mean = -5, sd = 5)

# Generate noise
noise <- rnorm(n = 100, mean = 0, sd = 1)

# Create dependent variable
y <- 3 * x1 + 2 * x2 - 5 * x3 + 100 + noise

# Combine all into a tibble
my_data <- tibble(x1, x2, x3, y)

# Calculate my_lm
my_lm <- lm(formula = y ~ x1 + x2 + x3,
              data = my_data)
```

1.  Adjust the code above so that the coefficients for the regression equation will be (close to) `(Intercept) = -50`, `x1 = -3`, `x2 = 10`, `x3 = 15`

Additional Resources
--------------------

-   For more advanced mixed level ANOVAs with random effects, consult the `afex` and `lmer` packages.

-   To do Bayesian versions of common hypothesis tests, try using the `BayesFactor` package. [BayesFactor Guide Link](https://cran.r-project.org/web/packages/BayesFactor/vignettes/manual.html)