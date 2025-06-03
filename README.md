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

## Data Cleaning
Our next step was to merge the two datasets and then clean the merged dataframe
1. We performed an inner merge between the two dataframes on the id and recipe_id columns (since they represent the same value), and only keeps rows present in both original dataframes. Our final merged dataframe has 234429 rows and 17 columns (all 17 described above)
2. We noticed that there were some missing values to handle
 - a. There was one row which was both missing a name and recipe_id, so we dropped this row entirely.
 - b. Many rows had a rating of 0.0, which doesn’t make sense on a standard 1-5 rating scale, so we replaced all instances with NaN values
 - c. We added a new column labeled ‘protein (% daily value)’ which dissects the ‘nutrition’ column and is the recipes protein content in the percentage of daily value (out of 50 grams)
 - d. Many of these protein (% daily values) made no sense (some being as high as 3000%), so we only kept rows whose protein content was 250% (a generous high estimate, as some recipes could have a ton of protein)
3. A
4. A
