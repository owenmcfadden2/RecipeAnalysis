# Analysis of Recipe Ratings in Relation to Protein Content
By: Owen McFadden and David Goldstein

## Introduction
For our project, we wanted to dive into how protein content relates to the rating of different recipes. Protein is an absolutely essential part of our daily diet, helping to build and repair our muscles, supports immune function and is a key contributor in the production of enzymes and hormones. Many people don't consume enough protein, especially adults as they get older, and this could be due to the fact that **maybe high-protein recipes simply don't taste as good**. To investigate this, we'll work with two dataframes taken off of the internet.

To begin, here is a brief introduction of the datasets we’re working with, “RAW_recipes.csv” and “interactions.csv”:  
* RAW_recipes has 83782 rows each of which represents a recipe, and 12 columns:
  * ‘name’ -  Name of recipe
  * ‘id’ - ID of recipe
  * ‘minutes’ - Minutes to prepare recipe
  * ‘contributor_id’ - ID of user who submitted the recipe
  * ‘submitted’ - Date recipe was submitted
  * ‘tags’ - 
  * ‘nutrition’ - Nutritional information of recipe
  * ‘n_steps’ - Number of steps to prepare recipe
  * ‘steps’ - Steps to prepare recipe
  * ‘description’ - Description of recipe
  * ‘ingredients’ - Ingredients needed to prepare the recipe
  * ‘n_ingredients’ - Number of ingredients

* Interactions has 731927 rows and 5 columns:
  * ‘user_id’ - ID of user who reviewed recipe
  * ‘recipe_id’ - ID of recipe being reviewed
  * ‘date’ - Date review was submitted
  * ‘rating’ - Rating of recipe
  * ‘review’ - Review of recipe

Looking at the two datasets, the two columns we are going to be most interested in are rating (an integer 1-5, 1 indicating worst and 5 indicating best), and nutrition, which contains nutritional information on the recipe in the following format:
* [calories (#), total fat (% daily value), sugar (% daily value), sodium (% daily value), protein (% daily value), saturated fat (% daily value), carbohydrates (% daily value)]

## Data Cleaning and Exploratory Data Analysis
Our next step was to merge the two datasets and then clean the merged dataframe
1. We performed an inner merge between the two dataframes on the id and recipe_id columns (since they represent the same value), and only keeps rows present in both original dataframes. Our final merged dataframe has 234428 rows (before cleaning) and 17 columns (all 17 described above)
2. We noticed that there were some missing values to handle  
 a. There was one row which was both missing a name and recipe_id, so we dropped this row entirely  
 b. Many rows had a rating of 0.0, which doesn’t make sense on a standard 1-5 rating scale, so we replaced all instances with NaN values    
3. We added a new column labeled ‘protein (% daily value)’ which dissects the ‘nutrition’ column and is the recipes protein content in the percentage of daily value (out of 50 grams)  
4. Many of these protein (% daily values) made no sense (some being as low as 0% and others as high as 3000%), so we only kept rows whose protein content was less than 250% (a generous high estimate, as some recipes could have a ton of protein)
5. Lastly, we added a column called 'protein_bin' which is bins of possible protein contents as follows: [0-50%], [50-100%], [100-150%], [150-200%] and [200-250%] 
After merging, cleaning and adding columns, our final dataset has 233779 rows and 19 columns, and the first 5 rows are shown below for reference:  

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                | nutrition                              |   n_steps | steps                                     | description                            | ingredients                          |   n_ingredients |   user_id |   recipe_id | date       |   rating | review                                |   protein (% daily value) | protein_bin |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:-------------------------------------|:----------------------------------------|----------:|:------------------------------------------|:----------------------------------------|:-------------------------------------|----------------:|----------:|------------:|:-----------|---------:|:----------------------------------------|--------------------------:|:------------|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', ...]          | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] |        10 | ['heat the oven to 350f and ...]          | these are the most; chocolatey...       | ['bittersweet chocolate', ...]        |               9 |    386585 |      333281 | 2008-11-19 |        4 | These were pretty good...              |                         3 | 0-50%       |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', ...]          | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] |        12 | ['pre-heat oven the 350 degrees...]      | this is the recipe that...              | ['white sugar', ...]                  |              11 |    424680 |      453467 | 2012-01-26 |        5 | Originally I was gonna...              |                        13 | 0-50%       |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', ...]          | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0] |         6 | ['preheat oven to 350 degrees'...]       | since there are already 411...          | ['frozen broccoli cuts', ...]         |               9 |     29782 |      306168 | 2008-12-31 |        5 | This was one of the best broccoli...   |                        22 | 0-50%       |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', ...]          | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0] |         6 | ['preheat oven to 350 degrees'...]       | since there are already 411...          | ['frozen broccoli cuts', ...]         |               9 | 1196280   |      306168 | 2009-04-13 |        5 | I made this for...                     |                        22 | 0-50%       |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', ...]          | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0] |         6 | ['preheat oven to 350 degrees'...]       | since there are already 411...          | ['frozen broccoli cuts', ...]         |               9 |    768828 |      306168 | 2013-08-02 |        5 | Loved this...                          |                        22 | 0-50%       |  

### Univariate and Bivariate Analysis
For our **univariate analysis**, we looked into the distribution of the rating column. Below, you’ll see a bar chart displaying the distribution of ratings, and is skewed heavily left, which indicates that most of the ratings in this dataset are on the higher side (as you can see by hovering, almost 170,000 of our 233,779 reviews gave a rating of 5/5).
<div style="margin-bottom: -175px;">
  <iframe
    src="assets/univariate.html"
    width="800"
    height="600"
    frameborder="0"
  ></iframe>
</div>

For our **bivariate analysis**, we looked at average rating among recipes with different protein content. We used the protein_bin column to bin the proteins and averaged the rating of recipes within those bins. As you can see, there is no visible disparity, but we'll have to continue digging deeper on the issue.
<div style="margin-bottom: -175px;">
  <iframe
    src="assets/bivariate.html"
    width="800"
    height="600"
    frameborder="0"
  ></iframe>
</div>

### Aggregate Analysis
For our **pivot table** we wanted to see how protein content (protein_bin) is related to not only rating, but also minutes, n_ingredients, and n_steps. To do this we created a pivot table indexed by protein_bin and showing the averages of minutes, n_ingredients, n_steps and rating within those protein bins.  

| protein_bin   |   minutes |   n_ingredients |   n_steps |   rating |
|:--------------|----------:|----------------:|----------:|---------:|
| 0–50%         |  106.525  |         8.60376 |   9.46058 |  4.68554 |
| 50–100%       |   91.4503 |        10.5683  |  11.4433  |  4.66648 |
| 100–150%      |  143.887  |        10.5773  |  12.7276  |  4.64887 |
| 150–200%      |  203.723  |        10.0575  |  12.491   |  4.6134  |
| 200–250%      |  256.662  |         8.22914 |  14.0014  |  4.64763 |  

## Assessment of Missingness
### NMAR Analysis
As a reminder, for a column to be NMAR, it means that the chance that the value is missing depends on the actual missing value itself, not other columns. Looking at our data, one column that has a lot (57 rows) of missing entries is the review column (the other missing data can be seen in the table below). Description and name also had some missing data, but there is no explainable reason why description or name being missing would be dependent on the value itself. Review was a little more interesting to think about, as it could be NMAR in the sense that reviews could be skewed to only have the extreme reviews, the very positive and very negative reviews. This is because for someone to leave a review they had to have had a strong feeling towards the recipe (people who thought it was just okay probably wouldn't waste their time with a review).

|             |   missing_count |
|:------------|----------------:|
| name        |               1 |
| description |             114 |
| review      |              57 |  

### Missingness Dependency
For our analysis of missingness, we wanted to see if the missingness of the description column was dependent on other columns, specifically rating and minutes. We did this by performing permutation tests, with a significance level of α = 0.05 and our test statistic was the absolute difference in means: average rating where description **is missing** - average rating where description **is not missing**.

#### Missingness Test #1 - Minutes
Does **descriptions** missingness depend on **minutes**? **No** - our permutation test (100 shuffles) yielded a p-value of 0.34, since this is greater than our α, we don't have any evidence to prove that the missingness of description depends on minutes. The plot below is a histogram of our simulated absolute difference in means, and the black line is the observed absolute difference in means (42.275).

<div style="margin-bottom: -175px;">
  <iframe
    src="assets/minutes_miss.html"
    width="800"
    height="600"
    frameborder="0"
  ></iframe>
</div>

#### Missingness Test #2 - Rating
Does **descriptions** missingness depend on **rating**? **Yes** - our permutation test (100 shuffles) yielded a p-value of 0.05, since this is less than our α, it is extremely unlikely that this is due to chance alone. The plot below is a histogram of our simulated absolute difference in means, and the black line is the observed absolute difference in means (0.2307).

<div style="margin-bottom: -175px;">
  <iframe
    src="assets/rating_miss.html"
    width="800"
    height="600"
    frameborder="0"
  ></iframe>
</div>

## Hypothesis Testing
As we originally said, we wanted to investigate whether higher protein content would lead to a lower rating, and to do this we ran a permutation test:  
**Null Hypothesis**: Foods with high protein (>= 50% protein daily value) are rated the same as foods without high protein    
**Alternate Hypothesis**: Foods with high protein (>= 50% protein daily value) are rated lower than foods without high protein  
**Test Statistic**: Mean rating of foods with high protein (>= 50% protein daily value)  
**Significance Level**: α = 0.05  
We chose to run a hypothesis test since it doesn't make any assumptions about the distribution of ratings, and we're simply asking **How unusual is is our observed test statistic if protein has no effect on rating?** 

### Running the permutation test:  
**Step 1**: Find the observed test statistic: 4.662 (mean rating of foods with high protein)  
**Step 2**: Randomly shuffle values 1000 times to disconnect any possible association between rating and protein content. For each of these shuffles we computed the mean of high protein recipes (randomly shuffled) and stored it as **simulated data**  
**Step 3**: Compute our p-value given this distribution of simulated data under the null hypothesis  
**Step 4**: Make our decision  

Our computed p-value is **0.00** (essentially zero) and since this is less than our significance level **0.05**, we **reject the null hypothesis**, that foods with high protein are rated the same as foods without high protein. As you can see in the histogram below, all of our simulated test statistics are greater than the observed test statistic (black line).  

<div style="margin-bottom: -175px;">
  <iframe
    src="assets/protein_hist.html"
    width="800"
    height="600"
    frameborder="0"
  ></iframe>
</div>

## Framing a Prediction Problem
For our prediction problem, we are forming a **classification** problem to predict the rating (on a discrete 1-5 scale) of a recipe based on characteristics that are known before the review is submitted. Our response variable is **rating**, so that given all of the recipes features (like number of steps, number of ingredients, etc.) we could predict whether the recipe is "good" (rated highly). We're going to use **RMSE** to evaluate the effectivness of our model along with **R-squared**.

## Baseline Model
For our **baseline model**, we used two features:  
1. **has_description** (nominal): A column we added for this model, indicating whether or not a recipe has a written description
2. **n_ingredients** (quantitative): Number of ingredients in the recipe

Importantly for both our base and our final model, we sampled from our overall dataframe so our models wouldn't take forever to run. We sampled a random 50,000 recipes and based our model on those. Otherwise, the code would take too long to run.

We trained a random forest classification model using **pipeline** and default variables, by **one-hot encoding** has_description and leaving n_ingredients as is.  

**Model Performance**:

Training RMSE: 1.471

Test RMSE: 1.485

Since our **RMSE** is fairly similar, it's clear we're not overfitting our data. 

Training R-squared: 0.723

Test R-squared: 0.725

Since our **R-squard** is fairly high, it seems we're not doing that bad of a job predicting data

## Final Model
For our **final model**, we used five features:
1. **has_description** (nominal): as described above
2. **n_ingredients** (quantitative): as described above
3. **n_steps** (quantitative): Number of steps in the recipe
4. **minutes** (quantitative): Number of minutes recipe takes to make
5. **review_length** (quantitative): A new column we added for this model, indicating the length of a review

Our 3 new features in this model were **n_steps**, **minutes**, and **review_length**. We chose these specifically as we think they could relate to the rating of a recipe. For n_steps and minutes, if a recipe takes longer and is more complex, in theory a recipe could be better. For review_length, a long review could be a strong indicator of strong emotions in regards to the recipe, positive or negative.

We had to **transform** the data as follows:
* has_description: one-hot encoding
* n_ingredients: standardized using StandardScaler
* n_steps: standardized using StandardScaler
* minutes: transformed using QuantileTransformer
* review_length: transformed using QuantileTransformer

We continued to use **RandomForestClassifier** since rating isn't continuous. To select the best **hyperparameters**, we chose to use **GridSearchCV** to find the optimal max_depth and optimal number of trees. We searched over the range from 1 to 10, taking steps of 1, and the best depth was **7**. **GridSearchCV** also determined that the best number of trees to use is **100** out of a list of 100, 150, and 200.

**Model Performance**: we are now going to use **R-squard** to evaluate the performance of our model

Training R-square: 0.723

Test R-squared: 0.731

This is a **tiny improvement** over our baseline model...This is likely because of several factore: 1. **GridSearchCV** really didn't do all that much to optimize our hyperparameters. The default amount of trees is 100 and it found 100 to be the best number and the difference in depth of 7 and 3 is really quite small. Furthermore, it could be because our extra parameters really aren't that correlated with rating and we got lucky picking two good ones for the base model.

## Fairness Analysis
* **Group X**: called group_a, this is the group of recipes that take at least 45 minutes
* **Group Y**: called group_b, this is the group of recipes that take less than 45 minutes
* **evaluation metric**: we dicided to use accuracy_score to see how good our model is at predicting rating
* **null hypothesis**: when the model is broken up into groups X and Y seperately, the difference in accuracy is not unusual.
* **alternative hypothesis**: there's an unusual difference in accuracy between the two groups X and Y
* **test statistic**: absolute difference in accuracy
* **ignificance level**: α = 0.05
* **p**: 0.0
  
**Conclusion**: We reject the Null, there is a difference in accuracy between the two groups and our model isn't exactly fair depending on how many minutes it takes to make the recipe.

