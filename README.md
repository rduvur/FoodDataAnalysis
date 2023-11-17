DSC80 Project 3 Fall 2023

by Rohan Duvur (rduvur@ucsd.edu) and Kotaro Murata (kmurata@ucsd.edu)

## Introduction

The internet has been an invaluable tool in introducing the cuisines and cooking techniques of communities to individuals all over the world. We found it fascinating to think on how much influence online recipes may have had on our diets. In this page, we seek to explore how nutritional trends have changed over the years. Have discoveries in health studies affected the kinds of recipes that are being uploaded online? Is it possible for the average person to easily find healthy dishes and prepare them in a reasonable amount of time? There's a widely held belief that cooking healthy food is time consuming and costly for working individuals to make on a daily basis. Ultimately, the question we want to provide some insight on is whether or not its true recipes with less calories typically end up requiring more time to make.

In this project we'll be exploring data scraped from the cooking website food.com (orginally scraped from the follwing study https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf). The study provided 2 csv files. One we've titled "food", and the other named "reviews"

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

#### Reviews

|Column|Description|
|---|---|
|`'user_id'`	|User ID|
|`'recipe_id'`	|Recipe ID|
|`'date'`	|Date of interaction|
|`'rating'`	|Rating given|
|`'review'`	|Review text|