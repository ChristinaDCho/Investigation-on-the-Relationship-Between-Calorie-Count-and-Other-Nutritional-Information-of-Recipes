# Investigation on the Relationship Between Calorie Count and Other Nutritional Information of Recipes
By Christina Cho and Hanna Serykava

# Overview

This was the final project for the class DSC 80 at UCSD. It analyzes a dataset containing information on various recipes on food.com.

---

# Introduction

While the calorie count of a recipe is not the same thing as the overall healthiness of a recipe, calorie count is still nonetheless a useful tool for making healthy food choices. Particularly, making healthy food choices can be difficult as consumers make incorrect assumptions about the health of a food item from its labeling with terms such as “low sugar,” “low fat,” “low carb,” “low sodium,” etc.[^1] This project aims to analyze **which factors affect the calorie count of recipes**, and see if we can **predict the calorie count of a recipe from its nutritional information**.

The datasets we are using to solve this question come from food.com, which is a website that hosts a large number of recipes that can be rated by users. We are working with two csv files: 
1. RAW_recipes.csv consisting of 83782 rows and 13 columns
2. RAW_interactions.csv consisting of 731927 rows and 5 columns

The first dataset, called `recipes`, has 83782 rows and 10 columns and has various information on each recipe. The relevant columns and their description is below.

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

The second dataset, called `reviews`, has 731927 rows and 13 columns, and has information on each user rating of the recipes.

|column|description|
|---|---|
|`'user id'`|ID of the user who submittted the review |
|`'recipe id'`|ID of the recipe |
|`'date'`|Date the review was published |
|`'rating'`|User-submitted rating |
|`'review'`|Review text |

[^1]: Steffen Jahn, Ossama Elshiewy, Tim Döring, Yasemin Boztug,
Truthful yet misleading: Consumer response to ‘low fat’ food with high sugar content,
Food Quality and Preference, Volume 109, 2023, 104900, ISSN 0950-3293, https://www.sciencedirect.com/science/article/abs/pii/S0950329323000940

---

# Data Cleaning and Exploratory Data Analysis

1. We merged the  RAW_recipes.csv and RAW_interactions.csv files using a left merge on the id and recipe_id columns to be able to work with both files simultaneously.

2. We replaced ratings of 0 with NaN, under the assumption they are not valid ratings, as ratings can only be from 1 to 5.

3. We decided to extract the data from the nutrition column which was list in a string form and we transformed the values into other columns: calories, total_fat_pdv, sugar_pdv, sodium_pdv, protein_pdv, sat_fat_pdv, carb_pdv and converted each of these into floats.

4. We replaced missing values in columns calories, sugar_pdv, total_fat_pdv and rating in order to ensure data accuracy.

5. Then, we calculated the average rating of each recipe.

6. After cleaning, our dataset recipes_rating has 234429 rows and 26 columns. The relevant columns and the type of data they contain is below.

## Result

This is the first few rows of our cleaned dataframe. For readability, only the relevant columns have been selected.

| name                                 |     id |   n_ingredients |   rating |   avg_rating |   calories |   total_fat_pdv |   sugar_pdv |   sodium_pdv |   protein_pdv |   sat_fat_pdv |   carb_pdv |
|:-------------------------------------|-------:|----------------:|---------:|-------------:|-----------:|----------------:|------------:|-------------:|--------------:|--------------:|-----------:|
| 1 brownies in the world    best ever | 333281 |               9 |        4 |            4 |      138.4 |              10 |          50 |            3 |             3 |            19 |          6 |
| 1 in canada chocolate chip cookies   | 453467 |              11 |        5 |            5 |      595.1 |              46 |         211 |           22 |            13 |            51 |         26 |
| 412 broccoli casserole               | 306168 |               9 |        5 |            5 |      194.8 |              20 |           6 |           32 |            22 |            36 |          3 |
| 412 broccoli casserole               | 306168 |               9 |        5 |            5 |      194.8 |              20 |           6 |           32 |            22 |            36 |          3 |
| 412 broccoli casserole               | 306168 |               9 |        5 |            5 |      194.8 |              20 |           6 |           32 |  

## Univariate Analysis

We wanted to check the distribution of calories in the dataset. However, it was an extremely skewed distribution with most of the data being between 0-1000, but certain data points going all the way up to around 30k, making the histogram unreadable. In order to rectify this, we created a subset of the data, called `under_2000` that contains only the data for recipes that have 2000 calories or under. We chose 2000 as the cutoff as most of the data points were within that range, and 2000 is also the recommended daily calories for the average man according to the FDA.

<iframe
  src="assets/calories_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As you can see, even within the subsection of recipes that are 2000 calories or less, the calorie distribution is very skewed to the right. Most of the data seems to be between 0 and 500 calories.

## Bivariate Analysis

Next, we wanted to see the difference in the distribution of calories for recipes that could be labeled "low sugar" vs. "low fat." However, the data itself does not label the recipes as either of those terms, so we had to manually label them.

The criteria set by the FDA for low fat foods requires that for every 100 calories, the food has 3 or less grams of fat. The criteria for low sugar is that sugar PDV must be 5 or less. 

1. First, we created a function that takes in nutrition as PDV and returns it as a percentage of the number of calories.
2. Then we created a column, `total_fat_p_cal` that has the total fat as a percentage of the number of calories with that function.
3. Then we created two columns, `low_fat` and `low sugar` that have boolean values indicating whether the recipe is low fat or low sugar respectively.

<iframe
  src="assets/box.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

While the box plots look similar, the variance for the calories of the low sugar recipes seems to be higher than the variance of the calories of the low fat recipes. Additionally, the low sugar recipes seem to be higher in calorie overall than the low fat recipes.

## Interesting Aggregates

We also created a pivot table to show the average calorie count of recipes for each rating, as that might show us a relationship for the calorie count of recipes and the rating. We thought that higher calorie recipes might be rated more highly, as they would be tastier.

|          |     1.0 |     2.0 |     3.0 |     4.0 |     5.0 |
|:---------|--------:|--------:|--------:|--------:|--------:|
| calories | 486.595 | 446.598 | 425.791 | 405.047 | 415.213 |

Interestingly, the pivot table shows the opposite trend of what we thought, as the average calorie count of recipes goes down as the rating goes up.

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

We are planning to test whether rating column depends on:
1. avg_rating (a column rating may depend on if the missing values appear more frequently in recipes with low or high average rating)
2. 'minutes'
3. 'sodium_pdv’
4. 'sat_fat_pdv'

`avg_rating`

**Null Hypothesis**: The missingness of ratings does not depend on average ratings.

**Alternate Hypothesis**: The missingness of ratings does depend on average ratings.

**Test Statistic**: K-S Stat

**Significance Level**: 0.05

Because our p-value is 0.0143, which is less than the significance level of 0.05, we can reject the null hypothesis. Missngness in `rating` may be dependent on avg_rating, making it MAR.

`minutes`

**Null Hypothesis**: The missingness of ratings does not depend on minutes of recipe.

**Alternate Hypothesis**: The missingness of ratings does depend on minutes of recipe.

**Test Statistic**: K-S Stat

**Significance Level**: 0.05

Because our p-value is 0.0004, which is less than the significance level of 0.05, we can reject the null hypothesis. Missngness in `rating` may be dependent on minutes, making it MAR.

`sodium_pdv`

**Null Hypothesis**: The missingness of ratings does not depend on sodium (PDV).

**Alternate Hypothesis**: The missingness of ratings does depend on sodium (PDV).

**Test Statistic**: K-S Stat

**Significance Level**: 0.05

Because our p-value is 0.3748, which is greater than the significance level of 0.05, we cannot reject the null hypothesis. Missngness in `rating` may be independent of sodium (PDV).

`sat_fat_pdv`

**Null Hypothesis**: The missingness of ratings does not depend on saturated fat (PDV).

**Alternate Hypothesis**: The missingness of ratings does depend on saturated fat (PDV).

**Test Statistic**: K-S Stat

**Significance Level**: 0.05

Because our p-value is 0.1914, which is greater than the significance level of 0.05, we cannot reject the null hypothesis. Missngness in `rating` may be independent of saturated fat (PDV).

### Missingness exploration graphs: 

#### Distribution of avg_rating for Missing vs. Non-Missing Ratings:

<iframe
  src="assets/Distribution of avg_rating for Missing vs. Non-Missing Ratings.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Majority of recipes are situated close to 5.0 average rating. There appears to be a slight increase in missing ratings for recipes close to 5.0 average ratings. (Suggestion: some users could decide not to leave their positive rating when there are already plenty of positive ratings)


#### Distribution of minutes for Missing vs. Non-Missing Ratings

<iframe
  src="assets/Distribution of minutes for Missing vs. Non-Missing Ratings.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


#### Distribution of minutes for Missing vs. Non-Missing Ratings

<iframe
  src="assets/Distribution of avg_rating for Missing vs. Non-Missing Ratings.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Majority of recipes take less than 100 minutes to finish. There appears to be a slight increase in missing ratings for recipes that take more than 100 minutes. (Suggestion: some users could decide not to finish/abandon the recipe, therefore, decide not to leave their rating)


#### Distribution of sodium_pdv for Missing vs. Non-Missing Ratings

<iframe
  src="assets/Distribution of sodium_pdv for Missing vs. Non-Missing Ratings.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The proportion of missing ratings appears to be roughly proportional to the total count across sodium levels (both are skewed right).


#### Distribution of sat_fat_pdv for Missing vs. Non-Missing Ratings

<iframe
  src="assets/Distribution of sat_fat_pdv for Missing vs. Non-Missing Ratings.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The proportion of missing ratings appears to be roughly proportional to the total count across saturated fat levels (both are skewed right).

---

# Hypothesis Testing

As stated in the introduction, we are trying to find the relationship between various nutrition facts of an item of food and its calorie count. Therefore, it would be useful to empirically examine the difference in calorie count of one of the most widely used terms of “low sugar.” Are the foods that qualify as “low sugar” truly lower calorie than the foods that are not?

We chose to do a permutation test as we are comparing two separate groups with different labels. We proposed that foods that are low sugar are indeed lower in calories than foods that are not low sugar, as sugar itself is quite high in calories. Additionally, foods that are low in sugar will encompass low calorie foods such as vegetable dishes.

We chose to set our test statistic as the difference of means between the calorie count of the recipes labeled “low sugar” and not labeled “low sugar” because the two distributions both have the same shape and are roughly normal, so the mean calorie count will be a fair representation of the calorie count of both groups.

**Null Hypothesis**: The mean calorie count of the recipes labeled as "low sugar" is the same as the mean calorie count of recipes that are not labeled as "low sugar."

**Alternate Hypothesis**: The mean calorie count of the recipes labeled as "low sugar" is less than than the mean calorie count of recipes that are not labeled as "low sugar."

<iframe src="assets/perm_test_hist.html" width=800 height=600 frameBorder=0></iframe>

Using the test statistic of difference of means between the calorie count of the recipes labeled “low sugar” and not labeled “low sugar,” carrying out the permutation test resulted in a p-value of 0.0, which is less than our significance level of 0.05. Therefore, we can reject the null hypothesis and conclude that the mean calorie count of recipes labeled as low sugar may be less than the mean calorie count of recipes that are not labeled as low sugar.

---

# Framing a Prediction Problem

The prediction problem is predicting the calories of recipes on food.com using the other information we know about each recipe. It is a regression analysis. We chose the calorie count as the response variable because it is an important general tool that indicates healthier foods. The methods we used to evaluate the model were R^2 and RMSE because these are the two most relevant metrics used for linear regression models, allowing us to see both the closeness of the data points with each other and also how accurate the predicted line is.

---

# Baseline Model

We are using a linear regression model as our baseline. The features we are using are the logs of the columns “sugar PDV” and “total fat PDV,” which are both quantitative. We chose to take the log of both columns because they have a skewed distribution, and taking the log accounts for that. The bulk of the data for both are between 0 and 100, but there are data points that go up to 30k for sugar and 3000 for total fat.

The model had an R^2 value of 0.0656 and an RMSE of 563.783. As the R^2 is fairly low and the RMSE is fairly high, we do not believe the current model is good. 

---

# Final Model

### Added features:

#### n_ingredients (Number of ingredients)

Reasoning: number of ingredients in the recipe is often correlated with the calorie count of the resulting dish. Higher number of ingredients can correlate with higher calorie content of the recipe. This helps our model to better grasp the calorie variations based on the number of ingredients.

#### Minutes (Cooking time)

Reasoning: longer cooking time means more elaborate recipes which can correlate with higher calorie count. This helps our model to determine the complexity of the recipe, which indirectly helps it determine calorie count with higher accuracy.

#### log transformation of sugar_pdv

Reasoning: the original sugar_pdv was highly skewed, meaning few recipes had a very high sugar count while others’ sugar content was much lower. By taking the natural log transformation we were able to normalize the distribution of the sugar_pdv, which simplifies the process of capturing the linear relationship between the calorie count and sugar_pdv for our model. By doing this, we stabilized our variance and significantly improved our model’s accuracy (we have more normal distribution, therefore our model could learn better).

#### Standardization of features we used in our model

Reasoning: features with larger values (ex: values in minutes are generally higher than values in n_ingredients) could dominate the model, making it harder for the model to capture the weight of each feature on the calorie count (The standardization has especially high impact on the Ridge Regression, making it less biased against the large scale values)


### Modeling algorithm:

We have checked the performance of Ridge Regression, Random Forest and Lasso Regression (Lasso Regression had slightly worse predictions in terms of R² and RMSE (while Ridge applies L2 regularization, helping to prevent overfitting) and Random Forest had higher  RMSE than Ridge and much slower performance).

We chose the Ridge Regression. We needed to find the optimal alpha out of [1, 10, 50, 100, 500] for our Ridge Regression to balance bias and variance. We used GridSearchCV to select the best value of our hyperparameter from the range of values  [1, 10, 50, 100, 500] and performed cross-validation (cv=3) (we used neg_root_mean_squared_error for scoring). 

#### Baseline’s model performance: 

1. R² =0.06555081885812675
2. RMSE = 563.7830302958045

#### Final Model performance: 

1. R² =0.9377
2. RMSE = 147.01

The R² of the final model is much greater than the R² of the baseline model, meaning that the final model explains higher variance in calorie prediction.

The RMSE of the final model is much lower than the RMSE of the baseline model, meaning that the final model’s predictions are closer to the actual calorie counts.

<iframe src="assets/ridge.png" width=800 height=600 frameBorder=0></iframe>

---

# Fairness Analysis

Group X: recipes with <= 10 ingredients (simpler recipes)
Group Y: recipes with > 10 ingredients (more complex recipes)

We are trying to determine whether the final model predicts calorie count better for one of these two groups. We are evaluating the regression model, therefore, we decided to use RMSE (Root Mean Squared Error). We use a permutation test to determine whether the difference between observed RMSE between Group X and Group Y is significant.

**Null Hypothesis**: Our model is fair. RMSE for small and large numbers of ingredients is roughly the same, and any differences are due to random chance.

**Alternative Hypothesis**: Our model is unfair. RMSE for small numbers of ingredients is significantly higher than for large numbers of ingredients.

We chose the absolute difference in RMSE of group X and group Y. We set significance level = 0.05. Therefore, we reject the null hypothesis if we get the p-value lower than 0.05.

Our output: RMSE Difference: 6.429792108726588

P-value: 0.536

Conclusion: Fail to reject H₀ (Our model is fair). No strong evidence of bias; any difference in precision is likely due to random chance.

<iframe src="assets/perm.png" width=800 height=600 frameBorder=0></iframe>
