# Food-Rating-predictions

### Framing the Problem

  In my previous project, I determined whether certain nutrient factors of a recipe may play a role in being significant to how a recipe is rated. I concluded that specific nutrition variables did prove to be correlated to how ratings of a recipe was given. To follow up this result, my prediction question now will be to predict the average rang that a recipe may recieve on the food website, using its available columns and my self created nutrition variable columns. In particular, the rating that a recipe may recieve lies along a value range between 1-5. Most of these ratings are highly focused around the integer variables (more than 75% of the average ratings are an integer of [1, 2, 3, 4, 5]) Because of this, the prediction that I will commit to will be a multiclass classifer (for 1, 2, 3, 4, and 5) due to the high data focus around integer values mainly. The response variable will be specifically the column "average_rating_per_recipe", which contains the average rating a recipe recieves out of all of its reviews. Note this column still contains some decimal values despite the high focus in on integer ratings, so these values will be rounded to the clostest integer. The metric I plan to use will be the correlation or R^2, since I plan to care more about the accurate prediction of a rating in this project than how each column will contribute to predictions of the average rating. Finally, I look into just about every column to build my model, including submission date, number of steps, number of ingredients, description, tags, etc. Certain columns will still be dropped due to the lack of relevance to the ratings (for example,"id"), and in terms of availability, since each recipe information is grabbed directly from a free public website, I assume that I'm available to all columns of the data (since each is available publicly off the website I would pull from a dataset).

### Baseline Model 
