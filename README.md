DSC80 Project 3 Fall 2023

by Rohan Duvur (rduvur@ucsd.edu) and Kotaro Murata (kmurata@ucsd.edu)

## Introduction

The internet has been an invaluable tool in introducing the cuisines and cooking techniques of communities to individuals all over the world. We found it fascinating to think on how much influence online recipes may have had on our diets and in turn our health. In this page, we seek to explore how nutritional trends have changed over the years. Have discoveries in health studies affected the kinds of recipes that are being uploaded online? Is it possible for the average person to easily find healthy dishes and prepare them in a reasonable amount of time? There's a widely held belief that cooking healthy food is time consuming and costly for working individuals to make on a daily basis. 

Ultimately, the question we want to provide some insight on is **whether or not its true recipes with less calories typically end up requiring more time to make**.

In this project we'll be exploring data scraped from the cooking website food.com (orginally scraped for the follwing study https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf). The study provided 2 csv files. One we've titled "food", and the other named "reviews"

#### Food 

|Column	                 |Description|
|---                     |---        |
|`'name'	`            |Recipe name|
|`'id'`	                 |Recipe ID|
|`'minutes'`	         |Minutes to prepare recipe|
|`'contributor_id'`	     |User ID who submitted this recipe|
|`'submitted'`	            | Date recipe was submitted|
|`'tags'`	              |Food.com tags for recipe|
|`'nutrition'`	          |Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein    (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”|
|`'n_steps'`	          |Number of steps in recipe|
|`'steps'`	              |Text for recipe steps, in order|
|`'description'`	     | User-provided description|

The food dataframe contains 83,782 recipes

#### Reviews

|Column|Description|
|---|---|
|`'user_id'`	|User ID|
|`'recipe_id'`	|Recipe ID|
|`'date'`	|Date of interaction|
|`'rating'`	|Rating given|
|`'review'`	|Review text|

Reviews has 731,927 ratings, each with the recipe_id of the dish being reviewed.

For the purposes of this study, we are especially interested in the minutes and nutrition columns. As they will be key determiners in classifying which meals are healthy and how fast it takes to make them.

## Cleaning Data and Exploratory Data Analysis

### Data Cleaning

We went through the following steps in order to obtain our cleaned dataframes.

#### cleaned_data

1. First thing's first, lets make a dataframe where the reviews are matched with their respective recipes. We can do this by doing a left merge of "food" with "reviews" on the     recipe_id column. 

2. Replace all 0-star ratings with np.nan. When this data was scraped, it filled in reviews that did not provide a star rating with a review of 0. Under most rating scales, a review of 0 stars is not possible. 

3. Currently the nutrition column is a string type. Separated by commas are the nutrition facts for a single serving of the recipe. We want to split this column into separate columns for each metric and convert them to floats. 

4. The 'food' df had a date submitted column. We want to eventually use the years that recipes were posted to track how they evolved over the years. To do this, we extracted the year from the string by converting them to a pandas datetime object.

5. Drop columns like description, contributor_id, steps, tags, date, ingredients, rating, avg_rating, etc.

#### clean_food

We found a considerable amount of troll / outlier recipes posted to the website which skewed our data (hot cocoa with 45,000 calories per serving). We wanted to make a separate dataframe that only included recipes and ignored these 'weird' recipes by using the following filters:

1. Could be made in under 5 hours (300 minutes)

2. No more than 4,000 calories

This is more in-line with something an individual with limited free-time would be cooking for themselves.

This is what our dataframes ended up looking like in the end:

| name                                 |     id |   minutes |   n_steps |   n_ingredients |   year |   calories (#) |
|:-------------------------------------|-------:|----------:|----------:|----------------:|-------:|---------------:|
| 1 brownies in the world    best ever | 333281 |        40 |        10 |               9 |   2008 |          138.4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |        12 |              11 |   2012 |          595.1 |
| 412 broccoli casserole               | 306168 |        40 |         6 |               9 |   2008 |          194.8 |
| 412 broccoli casserole               | 306168 |        40 |         6 |               9 |   2009 |          194.8 |
| 412 broccoli casserole               | 306168 |        40 |         6 |               9 |   2013 |          194.8 |

cleaned_data has 234,429 reviews matched to recipes

clean_food has 80,079 recipes that are within our requirements

### Univariate Analysis

We first visualized the distribution of calories in the recipes posted to Food.com

<iframe src="assets/Calorie-Distribution.html" width=800 height=600 frameBorder=0></iframe>

We immediately noticed a right skew in the plot. Most recipes being posted to the site are living in that 'snack-ish' 150-169.9 calorie range. The mean calories are being pulled to the right by outliers.

Taking a look at the # of ingredients distribution:

<iframe src="assets/ingredients.html" width=800 height=600 frameBorder=0></iframe>

We see a similar skew in the distribution. The most common # of ingredients seems to be 8 for the average meal on food.com.

### Bivariate Analysis

Probably the most important relationship to plot for our question is that between Calories and the Minutes columns. We sampled 1000 elements from our dataframe and put them on a scatterplot.

<iframe src="assets/calories_vs_minutes.html" width=800 height=600 frameBorder=0></iframe>

Just from looking at the plot, there doesn't seem to be a very apparent correlation between calories and the amount of time it takes to make a dish. We see a lot of points on the right that are very high in calories but barely take any time to make. There is a large cluster focused around foods from 0 to 500 calories with prep times ranging from 0 to 100 minutes. An example of this subsection with one possible set of random data is provided below for your convenience:

<img src="assets/Screen Shot 2023-11-15 at 12.27.33 AM.png" alt="image" width="950" height="auto">

This leads us to believe there isn't much time to be saved by choosing to make high or low calorie meals as opposed to the other.

Studies showing the linkage between high-calorie diets and obesity have been mainstream for a while now. Let's examine whether this has had an effect on the recipes being posted to food.com by looking at the proportion of low-calorie recipes being posted each year. We'll define a low calorie food to be less than 400 calories. 

<iframe src="assets/low_cal_prop.html" width=800 height=600 frameBorder=0></iframe>

Interestingly, it seems that the proportion of low calorie foods appears to be steadily decreasing over the course of a decade. This goes against our impression that recipes would follow the spread in awareness of nutritional pitfalls.

To examine further the increase in calories over the years, we constructed a box and whisker plot by grouping recipes by the years they were submitted. 

<iframe src="assets/calories_per_year.html" width=800 height=600 frameBorder=0></iframe>

Notice how the median number of calories went up from ~287 calories to ~315 calories. Q3 jumped from 459 to 510. Seems , there is a shift to higher calorie foods being shared on food.com over the last decade.

### Interesting Aggregates

Things like staturated fats are known to raise blood cholesterol levels as well as increase risk of heart disease. We've already made many visual plots. Let's first examine how minutes effects all of our nutrition with a pivot table:

|   minutes |   carbohydrates (PDV) |   protein (PDV) |   saturated fat (PDV) |   sodium (PDV) |   sugar (PDV) |   total fat (PDV) |
|----------:|----------------------:|----------------:|----------------------:|---------------:|--------------:|------------------:|
|        30 |                     8 |            23   |                    20 |             15 |          19   |              20   |
|        31 |                     8 |            16   |                    17 |             14 |          25   |              15   |
|        32 |                     8 |            13   |                    20 |             13 |          26   |              18   |
|        33 |                     9 |            13   |                    18 |             12 |          34.5 |              16.5 |
|        34 |                    10 |            14.5 |                    18 |             13 |          34.5 |              18.5 |

Here's the data visualized:

<iframe src="assets/median_nutrients_vs_minutes.html" width=800 height=600 frameBorder=0></iframe>

Something that stands out is the height of successive peaks for a given nutrient. See how often the peaks are taller towards the higher minute values. Another cool thing to see is the synchronization between peaks for different nutrients. For example, places that have high values for sugar also have high values for things like saturated fats and total fat.

## Assignment of Missingness

With the .isnull() method we took a look at which columns were interesting to gauge missingness of.

The output is pasted below:

|    | column name         |   number of nulls |
|---:|:--------------------|------------------:|
|  0 | name                |                 1 |
|  1 | id                  |                 0 |
|  2 | minutes             |                 0 |
|  3 | contributor_id      |                 0 |
|  4 | tags                |                 0 |
|  5 | n_steps             |                 0 |
|  6 | steps               |                 0 |
|  7 | description         |               114 |
|  8 | ingredients         |                 0 |
|  9 | n_ingredients       |                 0 |
| 10 | user_id             |                 1 |
| 11 | recipe_id           |                 1 |
| 12 | date                |                 1 |
| 13 | rating              |             15036 |
| 14 | review              |                58 |
| 15 | avg_rating          |              2777 |
| 16 | year                |                 0 |
| 17 | calories (#)        |                 0 |
| 18 | total fat (PDV)     |                 0 |
| 19 | sugar (PDV)         |                 0 |
| 20 | sodium (PDV)        |                 0 |
| 21 | protein (PDV)       |                 0 |
| 22 | saturated fat (PDV) |                 0 |
| 23 | carbohydrates (PDV) |                 0 |
| 24 | is_low_cal          |                 0 |


From looking at the list of variables and the number of missing values, we see the most prevelent in the columns are description, rating, review, and avg_rating. For a value to be not missing at random (NMAR), the chance that it is missing depends on the actual missing value. To see if these elements are NMAR or MAR, we will conduct permutations to see if the missingness is dependent on the element itself or other elements in the data. 

Description 

The missingness of description could be due to the nature of the recipe and it's complexity. When a recipes is very simple, the contributer may feel that it is not necessary to put a description. Which could mean that this data is missing at random (MAR). To prove this, we could compare the recipes that have a shorter cook time and find the missingness for each and run permutations to see if this still holds.

Rating

When cleaning the data, we replaced all 0 values as np.nan. This means that there are a lot of people who didn't bother rating the recipe they posted a review for. The reasons for this could be that the recipe is so short that it isn't warranted.

**We decided on conducting analysis of rating's missingness for this study**

We created a new boolean column for our data frame called rating_missing. In our tests, we shuffle this column and group by it to see if the missingness of rating really does have an effect on the distributions of other columns.

1. For our first test to verify dependence, we looked at minutes

    **Null Hypothesis** : The distribution of 'minutes' when 'rating' is missing is the same as the distribution of 'minutes' when 'rating' is not missing

    **Alternative Hypothesis** : The distribution of 'minutes' when 'rating' is missing is not the same as the distribution of 'minutes' when 'rating' is not missing.

    Observed Statistics : The absolute difference between minutes means of these two distributions.

    Here is the distribution plot for these two distributions: 

    <iframe src="assets/rate_dist.html" width=800 height=600 frameBorder=0></iframe>

    After permuting the rating_missing column and getting a test stat 1,000 times, we arrive at the following plot of our data:

    <iframe src="assets/permuted_statistic.html" width=800 height=600 frameBorder=0></iframe>

    We got a p-value of 0.119 for this test. This is greater than our siginificance level of 0.05. Therefore, we can reject the null and say that **the missingess of rating is dependent on the value in the minutes column.** Rating appears to MAR since it is dependent on minutes based on this test.

2. Our second test involves looking at the missingness of rating compared to the values of protein (PDV).

    **Null Hypothesis** : The distribution of 'protein (PDV)' when 'rating' is missing is the same as the distribution of 'minutes' when 'rating' is not missing

    **Alternative Hypothesis** : The distribution of 'protein (PDV)' when 'rating' is missing is not the same as the distribution of 'protein (PDV)' when 'rating' is not missing.

    Observed Statistics : The absolute difference between protein (PDV) means of these two distributions.

    This plot describes the distribution of protein in relation to rating's missingness

    <iframe src="assets/protein_rating.html" width=800 height=600 frameBorder=0></iframe>

    Again, we permute the rating_missing column and get the absolute difference between the mean protein in the missing rating group and mean protein where rating is not missing.

    This is what we got:

    <iframe src="assets/protein_permute_stat.html" width=800 height=600 frameBorder=0></iframe>

    We got a p-value of 0.0 for this test. This is less than our siginificance level of 0.05. Therefore, we fail to reject the null and say that **the missingess of rating is NOT dependent on the value in the protein column.**

## Hypothesis Testing

Back to our overarching question

#### Hypotheses:

**Null: Recipes with low calories take the same amount of time to prepare as recipes with high calorie counts.**

**Alternative: Recipes with low number of calories take less time to prepare than high-calorie dishes.**

We will use the clean data frame to conduct our test.

is_low_cal: True if the dish has fewer than 400 calories. False if otherwise.

We chose a one sided test to get an answer to see if people are correct in saying they "don't have the time" to make healthy dishes.

Our test statistic is the difference in mean between the avg minutes for low calorie dishes and high calorie dishes.

Significance level: 0.05

Observed stat (-78.99738283601863)

<iframe src="assets/Hypoth.html" width=800 height=600 frameBorder=0></iframe>

We got a p-val of 0.0. This means that under the conditions of the null (low vs high calorie distinction doesn't matter), it is incredibly unlikely we will ever see a case where low calorie meals take as long or longer than high calorie dishes. In this case, we **reject the null** and conclude that there may be an time incentive to eat healthy.
 

### Thank you



















 
















