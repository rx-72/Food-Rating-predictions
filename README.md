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
  7. Discrete continuous column "n_ingredients"
  8. Ordinal Column "average rating per recipe" (prediction variable)
  9. Continous column "number of calories"
  10. Continous column "total_fat (PDV)"
  11. Continous column "sugar (PDV)"
  12. Continous column "sodium (PDV)"
  13. Continous column "protein (PDV)"
  14. Continous column "saturated_fat (PDV)"
  15. Continous column "carbohydrates (PDV)"
      
For now, we'll avoid using the nominal columns (since their values are lists which are difficult to encode, but we will cover our solution soon), and instead create a basic model using all of the remaining 12 columns in their raw form, avoiding column transformations for now unless absolutely necessary. For absolutely necessary, this means the "submitted" column which needs to be transformed into a numerical format. This is achieved by getting the difference of days from submission of the date "12/4/2023" - the submitted date. We create a function for this using FunctionTransformer() and use it in our pipeline via a ColumnTransformer(). Every other column is available numerically so we just try to use it in their raw form (hence we put remainders to "passthrough" in our inputted ColumnTransformer()). Therefore to summarize, after dropping every nominal column in the df we run a transformation on the "Submitted" column to make it numerical, and use every other numerical column in their raw form {no transformations} to create a pipeline under a specific model. My chosen model function is the RandomForestClassifier() which will run multiple decision tree classifiers on the data and average out prediction accuracy amonng multiple decision trees.

The model has been created and fitted with test data (using train_test_split to divide the data between columns used for prediction and prediction y column for both training and testing datawith a 0.75 and 0.25 data split.), so we run RMSE and the R^2/score of the predictions for training and testing sets. We have the following results below:

Training set:

  RMSE: 0.003989291158755953
  
  R^2: 0.9999840855560507


Testing set:

  RMSE: 1.2894403260466203
  
  R^2: 0.5966294280530889

From the look of these results, I would still say this basic model's performance is rather lacking but expected. Since we didn't account for depth control of the forest, we ended up greatly overfitting the predictions based on the results of R^2 between both sets. The RMSE between predictions is also signficantly larger between training and testing. (For reference, the entire point in rmse we averagely are off the mark by an entire point of the true rating, which isn't ideal for a small prediction range of 1-5.) TAs said, these results are to be expected since we only did a bare minimum model with some minor transformations and using every column as it was without doing transformations intended for trying to logically predict a rating. We also didn't try multiple hyperparameters to get the best possible randomforest parameters. In reality, we were really just trying to see if the columns in the form they currently are in would be sufficient to creating a strong model. That said, we still did perform a rather high estimate on test (for reference, expect test score personally was 0.5 or below) and our predictions do work in terms of just the training set, so now we have to feature better estimators for our model and try to balance out the overfitting. In other words, we'll be sacrificing some of prediction accuracy in training for better prediction in testing for our final model, along with logically transforming columns to our advantage (transforming columns in a way that would make sense to affecting or influencing a recipe's rating).

### Final Model

The final model is where I performed the most transformation of each column. Each said transformation was attempted to be designed in way such that it could be designed to caclulate and classify ratings correctly. Below are the specific column features transformed and added to the final model:

"Submitted": This was the only column transformed in the previous base model. I decided to retain this column since I felt dates may play a part in a rating (for example, more recent recipes may be more likely to be rated). The only major change in transformation I made was that I decided that to change days away from the date 12/4/2023 to average years away, since I felt that the RandomForest's trees would be able to round around these classifiers better and sort the ratings more accurately with usage of years and there being more similar values to compare and group in comparison to days.

"Description": I specifically looked for string descriptions containing the word "declicous". This was because I felt that logically if a description was described "delicious", higher ratings would be attracted to it. To confirm this, I tested mean differences of ratings between based df and df with only descriptions containing the word "delicious". The mean of the filtered df ended up greater hence I deemed potential in using a 1 0 column based on whether a description contained the word delicious and implemented such a column for predictions in my pipeline. One key note was that when searching "delcious" a mean returned that was much greater than the base df of ratings or the df ratings of "delcious" only. This could mean the descriptions contain multiple typo errors and the number of descriptions that contain delicous is actually much larger but there could also be similar words with that context (and we don't know for certain if they do mean delicious) so for the sake of accuracy and safety, they were ignored for the transformation in the model.

Nutrition columns ("Calories", "Sugar", "Sodium", "Protein", "Carbohydrates", "Saturated Fats", "Fats"): I put all of these columns together since they were relevant to one another and their respective transformations were similar. Effectively, I used the knowledge I gained from project 3 to run the binarizer on each of these nutrition columns with each of the threshold based on the healthy levels of each nutrition. (For example, a threshold of 700 for calories). I highly expected these transformations to be relevant since my hypothesis test on these columns in project 3 with these threshold values provided an alt hypothesis conclusion that nutrtion variables played a role in ratings, hence I designed theses 7 specific columns in a ways that were supported by that project knowledge.

"n_steps", "n_ingredients", and "minutes": These all used a specific binarizer threshold based on the idea that a preference of quickness, simpleness, and easiness was prefered. For example, I believed ratings could be depended on the idea of a perference of less steps where the less steps a rating had, the higher a rating (since it means it wasn't too complicated to make). The other columns used similar information where n_ingredients ran a threshold where too many ingredients would be too complicated and gain lower ratings (whereas lower ingredient usage would influence higher ratings), and minutes followed a similar concept (too long to make returned lower ratings and quickness to make returned higher ratings).

"tags": I specifically looked for tags lists containing the tag "easy". This unique tag was something to help differentiate easy recipes to harder ones which I believe were also a possible way to estimate ratings of a recipe (since there's a preference to use easy recipes and hence recipes tagged "easy" may gain more traction and hence higher ratings). Hence I found tags containing "easy" and label them as "1" with other rows labeled 0 (similar to hot encoding). To confirm this would be beneficial, I checked the compared the means between df ratings and df with "easy" tags ratings and found an increase so I deemed it an available column to use in this format. Note this column also contained tags such as "below 60 minutes" or "less than 10 steps" but I decided against using them since I felt it more appropiate to use the direct columns available for each since they'd likely more accurate in terms of data information (tags may be incorrectly labeled for example).

"ingredients": There were three main ingredients I looked for: "salt", "sugar", and "butter". In particular, I was thinking of recipes of baking food would be popular and hence would be higher rated. Hence, I searched for the ingredients most related to these types of foods (mainly based on cookies) and created 1 0 columns based on whether they contained such ingredients or not. I believed since bakery recipes would contain large sums of the total recipes, these columns would help determine the high amounts of rating 5 recipes in recipes. There could possible be other food types that may determine large sums of the recipes (salads for example), but I also picked these 3 columns since they ended up as a top 3 ingredients that appeared in the dataset. Henced I picked the recipe type likely most correlated between all 3 columns, considered the possibility it might dominate the majority of recipes in terms of category and connected this to the pattern that there high amounts of rating 5 on recipes. Using this knowledge as backup, I used these 3 1 0 columns transformed as new features.

The final model overall used a RandomForest once again. To optimize hyperparameters, I ran a gridsearchCV on about depths of 1 - 30 and min samples 1 - 20 between criterion and gini on 5 cross fold validations. The returned results being that optimized parameters were depth of 8, min samples were 6, and criterion was entropy. I applied these parameters to my final model and recieved the following score:

Training set:

  RMSE: 1.298524996031745
  
  R^2: 0.6071678655547775


Testing set:

  RMSE: 1.315955453958533
  
  R^2: 0.6027403800248258

In terms of just prediction of variance, this is a very miniscule improvement (only a single percentage point!). In exchange for this improvement, note that the training set score and RMSE also worsened because of this change. This is however an improvement in prediction overall as well of ratings and a better understanding of how each column affect said prediction. In other words, we still did improve in terms of trying to predict the test set, and we're able to determine better whether the columns were really relevant to our prediction column. Since we still a small improvement in the model, it might be possible to increase it further or feature engineer certain columns differently to determine truly if columns can't predict any better than this. If we still see a small improvement, it might be possible that the columns don't predict the ratings well. This is highly unlikely as there's likely some prediction reliant upon here (since we're still above 60% of variance explained) and we have multiple untested methods (such as a more larger scale hot encoding on ingredients or bag of words for description) that could possibly help explain the variance better than the features we have here. Overall, our final model was designed with attempting a better prediction of the prediction variable and it still barely improved. Hence we should explore which columns proved useful and which we should perhaps drop for the pipeline, as well as possible explore other different features. Note also, that since this is a randomforest (which isn't exactly ideal for predictions) these results do somewhat make sense (since tree test error often increase with depth increasement) hence the small improvement should somewhat be expected.

Some other methods I attempted were hotencoding and standardscaler of the respective columns (ingredients and tags plus the nutrtion columns respectively). These methods provided similar test scores to the one above (and also took much longer to run). I also tried changing up my model itself. A KNNClassifier and a decisiontree by itself ended up returning much lower test scores while a support vector model ended up getting a similar score to the forest here (though at the default parameters instead).

### Fairness Analysis
