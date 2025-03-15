# Nutrition of Recipes on food.com
By Christina Cho and Hanna Serykava

# Overview

This was the final project for the class DSC 80 at UCSD. It analyzes a dataset containing information on various recipes on food.com.

---

# Introduction

While the calorie count of a recipe is not the same thing as the overall healthiness of a recipe, calorie count is still nonetheless a useful tool for making healthy food choices. Particularly, making healthy food choices can be difficult as consumers make incorrect assumptions about the health of a food item from its labeling with terms such as “low sugar,” “low fat,” “low carb,” “low sodium,” etc. (Jahn et. al 2023) This project aims to analyze which factors affect the calorie count of recipes, and see if we can predict the calorie count of a recipe from its nutritional information.

The datasets we are using to solve this question come from food.com, which is a website that hosts a large number of recipes that can be rated by users. 
We are working with two csv files: 
1. RAW_recipes.csv consisting of 83782 rows and 13 columns
2. RAW_interactions.csv consisting of 731927 rows and 5 columns
The first dataset, called recipes, has 83782 rows and 10 columns and has various information on each recipe. The relevant columns and their description is below.

|column|description|
|---|---|
|`'name'`|Name of the recipe |
|`'id'`|ID of the recipe |
|`'minutes'`|Amount of minutes that the recipe takes to prepare and make |
|`'nutrition'`|Nutrition information for the recipe, given as a string of a list of numbers indicating the calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV); PDV stands for “percentage of daily value”|
|`'n_steps'`|Number of steps in the recipe |
|`'steps'`|Text for recipe steps, in order |
|`'description'`|The description of the recipe |
|`'ingredients'`|List of the ingredients of the recipe |
|`'n_ingredients'`|Number of ingredients in the recipe |

The second dataset, called reviews, has 731927 rows and 13 columns, and has information on each user rating of the recipes.

|column|description|
|---|---|
|`'user id'`|ID of the user who submittted the review |
|`'recipe id'`|ID of the recipe |
|`'date'`|Date the review was published |
|`'rating'`|User-submitted rating |
|`'review'`|Review text |

---

# Data Cleaning and Exploratory Data Analysis

1. We merged the  RAW_recipes.csv and RAW_interactions.csv files using a left join on the id and recipe_id columns to be able to work with both files simultaneously.

2. We replaced ratings of 0 with NaN, under the assumption they are not valid ratings, as ratings can only be from 1 to 5.

3. We decided to extract the data from the nutrition column which was list in a string form and we transformed the values  into other columns: calories, total_fat_pdv, sugar_pdv, sodium_pdv, protein_pdv, sat_fat_pdv, carb_pdv.

4. We replaced missing values in columns calories, sugar_pdv, total_fat_pdv and rating in order to ensure data accuracy.

5. After cleaning, our dataset recipes_rating has 234429 rows and 26 columns. The relevant columns and the type of data they contain is below.

## Result

|column|description|
|---|---|
|`'name'`|object |
|`'id'`|object |
|`'n_ingredients'`|int64 |
|`'rating'`|int64 |
|`'avg_rating'`|float |
|`'calories'`|float |
|`'total_fat_pdv'`|float |
|`'sugar_pdv'`|float |
|`'sodium_pdv'`|float |
|`'protein_pdv'`|float |
|`'sat_fat_pdv'`|float |
|`'carb_pdv'`|float |



## Univariate Analysis

## Bivariate Analysis

## Interesting Aggregates

---

# Assessment of Missingness

## NMAR Analysis

We believe that the “rating” column could be “NMAR”. It’s hard to find the reason for missingness of the “rating” column in other columns as missingness of data in “rating” column may be dependent on the data in “rating” column itself.

Possible reasonings for “NMAR”:
1. Some users may decide not to leave a rating if they didn't like the recipe but don’t want to lowly rate the recipe in case they made something wrong (complicated recipe for example).
2. Some users may decide not to leave a ranking if they liked the recipe but it was too simple/quick for them to decide to write a rating.
3. Some users may tend to only rate recipes they enjoyed, choosing not to leave a rating when they disliked a recipe.

Additional data that could explain the missingness of the data in the rating column:
1. Did those users read the recipe? Did they read the recipe and not rate it? Did they cook the recipe? Did they open the ranking section but didn’t submit the rating? How often do people leave ratings in general?
2. Checking the correlation between missingness in rating and reviews, which could be caused by users skipping both rating and review because they had forgotten or had no opinion (Causing the data missingness to be “MAR”)
3. Checking whether rating missingness is correlated with users only rating the recipes with low or high “avg_rating”, meaning that users are more inclined to leave a review if they see a certain “avg_rating” (Causing the data missingness to be “MAR”).

## Missingness Dependency

---

# Hypothesis Testing

As stated in the introduction, we are trying to find the relationship between various nutrition facts of an item of food and its calorie count. Therefore, it would be useful to empirically examine the difference in calorie count of one of the most widely used terms of “low sugar.” Are the foods that qualify as “low sugar” truly lower calorie than the foods that are not?

We chose to do a permutation test as we are comparing two separate groups with different labels. We proposed that foods that are low sugar are indeed lower in calories than foods that are not low sugar, as sugar itself is quite high in calories. Additionally, foods that are low in sugar will encompass low calorie foods such as vegetable dishes.

We chose to set our test statistic as the difference of means between the calorie count of the recipes labeled “low sugar” and not labeled “low sugar” because the two distributions both have the same shape and are roughly normal, so the mean calorie count will be a fair representation of the calorie count of both groups.

Null Hypothesis: The mean calorie count of the recipes labeled as "low sugar" is the same as the mean calorie count of recipes that are not labeled as "low sugar."

Alternate Hypothesis: The mean calorie count of the recipes labeled as "low sugar" is less than than the mean calorie count of recipes that are not labeled as "low sugar."

Using the test statistic of difference of means between the calorie count of the recipes labeled “low sugar” and not labeled “low sugar,” carrying out the permutation test resulted in a p-value of 0.0, which is less than our significance level of 0.05. Therefore, we can reject the null hypothesis and conclude that the mean calorie count of recipes labeled as low sugar may be less than the mean calorie count of recipes that are not labeled as low sugar.

<iframe src="hypothesis.html" width=800 height=600 frameBorder=0></iframe>

---

# Framing a Prediction Problem

The prediction problem is predicting the calories of recipes on food.com using the other information we know about each recipe. It is a regression analysis. We chose the calorie count as the response variable because it is an important general tool that indicates healthier foods. The methods we used to evaluate the model were R^2 and RMSE because these are the two most relevant metrics used for linear regression models, allowing us to see both the closeness of the data points with each other and also how accurate the predicted line is.

---

# Baseline Model

We are using a linear regression model as our baseline. The features we are using are the logs of the columns “sugar PDV” and “total fat PDV,” which are both quantitative. We chose to take the log of both columns because they have a skewed distribution, and taking the log accounts for that. The bulk of the data for both are between 0 and 100, but there are data points that go up to 30k for sugar and 3000 for total fat.

The model had an R^2 value of 0.0656 and an RMSE of 563.783. As the R^2 is fairly low and the RMSE is fairly high, we do not believe the current model is good. 

---

# Final Model

---

# Fairness Analysis

