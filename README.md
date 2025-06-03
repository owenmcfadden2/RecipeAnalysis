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
‘recipe_id’ - ID of recipe being reviewed
‘date’ - Date review was submitted
‘rating’ - Rating of recipe
‘review’ - Review of recipe

