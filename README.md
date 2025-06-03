# Analysis of Recipe Ratings in Relation to Protein Content
By: Owen McFadden and David Goldstein

## Introduction
For our project, we wanted to dive into how protein content relates to the rating of different recipes. Protein is one of the most important parts of our diets, contributing to everything our body does, and yet a lot of the time is overlooked by many people. It’s beyond important for human development and maintenance that you get your recommended daily protein intake.

To begin, here is a brief introduction of the datasets we’re working with, “RAW_recipes.csv” and “interactions.csv”
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
For our **univariate analysis**, we looked into the distribution of the rating column. Below, you’ll see a bar chart displaying the distribution of ratings, and is skewed heavily left, which indicates that most of the ratings in this dataset are on the higher side (as you can see by hovering, almost 170,000 of our 233,779 reviews gave a rating of 5/5)
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
AAAA

## Hypothesis Testing
**Null Hypothesis**: Foods with high protein are rated the same as foods without high protein  
**Alternate Hypothesis**: Foods with higher protein are rated higher  
For our test statistic, we chose to use AAA and we chose a significance level of α = 0.05

## Framing a Prediction Problem
AAAA

## Baseline Model
AAAA

## Final Model
AAAA

## Fairness Analysis
AAAA
