# Food-Rating-predictions

### Framing the Problem

  In my previous project, I determined whether certain nutrient factors of a recipe may play a role in being significant to how a recipe is rated. I concluded that specific nutrition variables did prove to be correlated to how ratings of a recipe was given. To follow up this result, my prediction question now will be to predict the average rang that a recipe may recieve on the food website, using its available columns and my self created nutrition variable columns. In particular, the rating that a recipe may recieve lies along a value range between 1-5. Most of these ratings are highly focused around the integer variables (more than 75% of the average ratings are an integer of [1, 2, 3, 4, 5]) Because of this, the prediction that I will commit to will be a multiclass classifer (for 1, 2, 3, 4, and 5) due to the high data focus around integer values mainly. The response variable will be specifically the column "average_rating_per_recipe", which contains the average rating a recipe recieves out of all of its reviews. Note this column still contains some decimal values despite the high focus in on integer ratings, so these values will be rounded to the clostest integer. The metric I plan to use will be the correlation or R^2, since I plan to care more about the accurate prediction of a rating in this project than how each column will contribute to predictions of the average rating. Finally, I look into just about every column to build my model, including submission date, number of steps, number of ingredients, description, tags, etc. Certain columns will still be dropped due to the lack of relevance to the ratings (for example,"id"), and in terms of availability, since each recipe information is grabbed directly from a free public website, I assume that I'm available to all columns of the data (since each is available publicly off the website I would pull from a dataset).

### Baseline Model 

  After dropping the deemed irrelvant columns ("name", "id", "contributor_id", "steps" for deemed irrelevant to ratings, and "nutrition" since we're already extracted the we wanted to columns), we have the following column features:
  1. Discrete continuous column "Minutes"
  2. Discrete continuous column "Submitted"
  3. Nominal Column "tags"
  4. Discrete continuous column "n_steps"
  5. Nominal Column "description"
  6. Nominal Column "ingredients"
  7. Discrete continuous column n_ingredients
  8. Ordinal Column "average rating per recipe" (prediction variable)
  9. Continous column "number of calories"
  10. Continous column "total_fat (PDV)"
  11. Continous column "sugar (PDV)"
  12. Continous column "sodium (PDV)"
  13. Continous column "protein (PDV)"
  14. Continous column "saturated_fat (PDV)"
  15. Continous column "carbohydrates (PDV)"
      
For now, we'll avoid using the nominal columns (since their values are lists which are difficult to encode, but we will cover our solution soon), and instead create a basic model using all of the remaining 12 columns in their raw form, avoiding column transformations for now unless absolutely necessary. For absolutely necessary, this means the "submitted" column which needs to be transformed into a numerical format. This is achieved by getting the difference of days from submission of the date "12/4/2023" - the submitted date. We create a function for this using FunctionTransformer() and use it in our pipeline via a ColumnTransformer(). Every other column is available numerically so we just try to use it in their raw form (hence we put remainders to "passthrough" in our inputted ColumnTransformer()). Therefore to summarize, after dropping every nominal column in the df we run a transformation on the "Submitted" column to make it numerical, and use every other numerical column in their raw form {no transformations} to create a pipeline under a specific model. My chosen model function is the RandomForestClassifier() which will run multiple decision tree classifiers on the data and average out prediction accuracy as well as attempt to limit overfitting. 

The model has been created and fitted with test data (using train_test_split to divide the data between columns used for prediction and prediction y column for both training and testing datawith a 0.75 and 0.25 data split.), so we run RMSE and the R^2/score of the predictions for training and testing sets.
