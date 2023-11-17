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

We found a considerable amount of troll/ outlier recipes posted to the website which skewed our data (hot cocoa with 45,000 calories per serving). We wanted to make a separate dataframe that only included recipes and ignored these 'weird' recipes by using the following filters:

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

There doesn't seem to be a very apparent correlation between calories and the amount of time it takes to make a dish. There is a large cluster focused around foods from 0 to 500 calories with prep times ranging from 0 to 100 minutes. An example of this subsection with one possible set of random data is provided below for your convenience:

<iframe src="assets/Screen Shot 2023-11-15 at 12.27.33 AM.png" width=800 height=600 frameBorder=0></iframe>

 This leads us to believe there isn't much time to be saved by choosing to make high or low calorie meals as opposed to the other.

 Studies showing the linkage between high-calorie diets and obesity have been mainstream for a while now. Let's examine whether this has had an effect on the recipes being posted to food.com by looking at the proportion of low-calorie recipes being posted each year. We'll define a low calorie food to be less than 400 calories. 

<iframe src="assets/low_cal_prop.html" width=800 height=600 frameBorder=0></iframe>

Interestingly, it seems that the proportion of low calorie foods appears to be steadily decreasing over the course of a decade. This goes against our impression that recipes would follow the spread in awareness of nutritional pitfalls.

To examine further the increase in calories over the years, we constructed a box and whisker plot by grouping recipes by the years they were submitted. 

<iframe src="assets/calories_per_year.html" width=800 height=600 frameBorder=0></iframe>

Notice how the median number of calories went up from ~287 calories to ~315 calories. Q3 jumped from 459 to 510. Seems , there is a shift to higher calorie foods being shared on food.com over the last decade.




 
















