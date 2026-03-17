# predictive-performance-of-recipes-calories
This repository is for storing my final project for the dsc 80 class: Practice and Application of Data Science 

# Exploring the Relationship between Low-calorie Recipes and their Preparation Complexity 

By: Prisca He 

---

## Introduction 

For time-constrained individuals such as college students, looking for a recipe that is healthy and quick to prepare may be difficult. Video-sharing platforms, such as Youtube, TikTok and Instagram, can promote recipes that claim to provide simple, low-calorie meals. However, it is not always clear whether healthier recipes take less time or effort to prepare. Sometimes, healthier, low-calorie meals may involve more ingredients or preparation steps, which make these recipes more impractical for college students with limited time. 

__This brings me to the question: Are lower-calorie recipes simpler to prepare than other recipes?__ In order to explore this question, there are two datasets containing recipe and rating information available. The datasets are both pulled from a research paper that aimed to Generate Personalized Recipes from Historical User Preferences (Majumder et al). 

The first dataset, `recipe`, contains 83,782 rows and 12 columns, with each row being a unique recipe.

| __Column__ | __Description__ | 
|:—--|:—--|
| `name` | Recipe name | 
| `id` | Recipe ID | 
| `minutes` | Minutes to prepare recipe | 
| `contributor_id` | User ID who submitted the recipe | 
| `submitted` | Date recipe was submitted | 
| `tags` | Food.com tags for recipe | 
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” | 
| `n_steps` | Number of steps in recipe | 
| `steps` | Text for recipe steps, in order | 
| `description` | User-provided description | 
| `ingredients` | Text for recipe ingredients | 
| `n_ingredients` | Number of ingredients in recipe | 

The second dataset, `interactions`, contains 731,927 rows and 5 columns. The columns contain reviews about specific recipes in the `recipe` dataset. 

| __Column__ | __Description__ | 
| :—-- | :—-- | 
| `user_id` | User ID | 
| `recipe_id` | Recipe ID |
| `date` | Date of interaction | 
| `rating` | Rating given | 
| `review` | Review text | 

With the two datasets, `recipes` and `interactions`, __we will be looking into whether or not lower-calorie tagged recipes take less preparation time (e.g. # of ingredients, and # of recipe steps) than recipes that are not `lower-calorie` tagged.__  To carry out this exploration, we will need the following columns: `minutes`, `n_steps`, `n_ingredients`, `nutrition` (which will be collapsed into 4 different columns: `sugar`, `protein`, `carbohydrates`, and `total fat` values) and a column called `low-calorie`, containing the value 0 if the recipe does not a `low-calorie` tag, and 1 if the recipe contains a low-calorie tag. 

The `minutes`, `n_steps` and `n_ingredients` columns contain information about the length of time, the number of steps, and the number of ingredients the recipe takes to complete, which is relevant in determining if lower-calorie recipes take less preparation time. Additionally, the `sugar`, `protein`, `carbohydrates` and `total fat` values will help in determining a threshold for a recipe being ‘low-calorie’ tagged. 

Exploring whether or not lower-calorie recipes are simpler to prepare helps determine if healthier eating is realistically accessible in terms of time and effort. This kind of information will be especially valuable for people with time constraints, such as students, working professionals, or beginners, who don’t have to sacrifice time just to eat healthy.  

---

## Data Cleaning and Exploratory Data Analysis 

Before exploring the two datasets, prior data cleaning steps were done to make the data exploration efficient. 

1. Left merge the recipes dataset and interactions dataset on the recipe column ‘id’ and interactions column ‘recipe_id’.  

This creates a combined dataset where each row contains its recipe information as well as its rating and reviews it received from users on Food.com. 

Now, the merged dataset contains the following columns: 

- `name` 
- `id`
- `minutes`
- `contributor_id` 
- `submitted`
- `tags` 
- `nutrition` 
- `n_steps`
- `steps`
- `description`
- `ingredients`
- `n_ingredients`
- `user_id`
- `recipe_id`
- `date`
- `rating`
- `review`

2. Replace ratings of 0 with np.nan in the `ratings` column

A rating of 0 means the user forgot or intentionally did not rate the recipe, as ratings are typically on a scale of 1 to 5. Looking into the number of values in the rating column, ratings of 0 have the third highest amount of entries in the dataset. If we decided to keep ratings of 0 in the ratings column, this would potentially artificially lower any statistical measures computed using the ratings column. So, replacing ratings of 0 with np.nan would allow us to avoid any underestimates or bias in our ratings column. 

3. Add a column `avg_rating` that contains the average rating per recipe 

The dataset currently contains one row per recipe review, which means there can be more than one row corresponding to a unique recipe if it contains more than one review. So, adding a column to the dataset that finds the average rating per recipe allows for a more accurate representation of each recipe. 

4. Drop the `rating` column from the dataframe 

Since each recipe contains an average rating per recipe review, there is no need for the recipe’s individual ratings anymore. We can drop the `ratings` column and collapse the recipe that contains multiple with multiple reviews and ratings into one row. 

5. Split the values in the nutrition column into individual columns

The values of the `nutrition` column are initially stored as a string of a list containing six different nutrition values: calories, total fat, sugar, sodium, protein, saturated fat and carbohydrates. A lambda function was applied to the `nutrition` column to convert the string into a list, and convert all the values within the list into floats. Then, the six values were turned to six different columns, each column containing their respective nutritional value. This step of expanding the nutrition column will help with utilizing any nutritional information in our analysis later down the line. 

6. Add a column `low_calorie` that indicates if the recipe contains a low-calorie tag

The tags column of the dataframe is initially stored as a string of a list of tags (i.e labels) that Food.com has encoded for each recipe. A lambda function was applied to the column to convert the string into a list, and then checked if the ‘low-calorie’ tag was included in the list. If the tag was in the list, the recipe would have a value of 1 under the new `low_calorie` column, and 0, if not. This column would allow us to compare preparation complexities amongst low-calorie tagged recipes and non-low-calorie recipes. 

Here are all the columns of the cleaned dataframe: 

- `name` 
- `id`
- `minutes`
- `contributor_id` 
- `submitted`
- `tags` 
- `n_steps`
- `steps`
- `description`
- `ingredients`
- `n_ingredients`
- `user_id`
- `recipe_id`
- `date`
- `review`
- `avg_rating`: _new_
- `calories`
- `total fat`: _new_ 
- `sugar`: _new_
- `sodium`: _new_ 
- `protein`: _new_ 
- `saturated fat`: _new_
- `carbohydrates`: _new_
- `low_calorie`: _new_

Our cleaned dataframe ended up with 83,781 rows, and 24 columns. For our exploration, we selected the columns that are most relevant to our question exploration. Below is our final dataset, with 83,781 rows and 9 columns. 

|   minutes |   n_steps |   n_ingredients |   calories |   sugar |   protein |   carbohydrates |   total fat |   low_calorie |
|----------:|----------:|----------------:|-----------:|--------:|----------:|----------------:|------------:|--------------:|
|        50 |        11 |               7 |      386.1 |       7 |        41 |               8 |          34 |             0 |
|        55 |         6 |               8 |      377.1 |     208 |        13 |              20 |          18 |             0 |
|        45 |         7 |               9 |      326.6 |      12 |        37 |               5 |          30 |             1 |
|        45 |        11 |               9 |      577.7 |     149 |        14 |              21 |          53 |             0 |
|        25 |         8 |               9 |      386.9 |     347 |         1 |              33 |           0 |             0 |


### Univariate Analysis 

For this univariate analysis, I examined the distribution of calories amongst all the recipes in the dataset. The distribution of calories is highly right-skewed, most recipes are concentrated in the lower to moderate calorie range (100-299.9) and a long tail extending towards high-calorie dishes. While a majority of recipes are relatively lower in calories, there are a small number of extreme outliers. Those outliers are important to be accounted for when evaluating for the ‘low-calorie’ classification. 

<iframe
src="distr_cal.html"
width="800"
height="600"
frameborder="0"
></iframe>

I also examined the distribution of low-calorie tags in my dataset: 

<iframe
src="distr_cal.html"
width="800"
height="600"
frameborder="0"
></iframe>

The distribution shows that the dataset is highly imbalanced, with a large majority of recipes classified as not low-calorie and only a small proportion labeled as low-calorie. This imbalance is important to consider in subsequent analyses, as comparisons between the two groups may be influenced by the disproportionate representation of non-low-calorie recipes.

### Bivariate Analysis 

For a bivariate analysis, I examined 2 different relationships, the first one being the distribution of recipe type (low-calorie vs. non-low-calorie) based on the preparation time. 

The first graph’s distribution shows that low-calorie recipes consistently make up a smaller proportion of recipes across all preparation time categories. However, there is a slight trend where low-calorie recipes are more prevalent among shorter preparation times and become less common as preparation time increases, suggesting that lower-calorie recipes may be somewhat quicker to prepare on average.

<iframe 
src="fig2.html"
width="800"
height="600"
frameborder="0"
></iframe>

The second graph looks at the mean number of ingredients per recipe type. There is a mean difference of 1 ingredient across the two groups: 8.43 ingredients for low-calorie recipes, and 9.36 ingredients for not-low-calorie recipes. This shows that there is a slight difference with the amount of ingredients needed to prepare a lower-calorie recipe, when compared to a non-low calorie recipe. 

<iframe
src="fig3.html"
width="800"
height="600"
frameborder="0"
></iframe>

### Interesting Aggregates 

With the addition of the `low_calorie` column in the dataset, I was able to investigate the differences in central tendencies between the two groups: recipes that are not low-calorie and recipes that are low-calorie. I took the mean of the three columns, `minutes`, `n_steps` and `n_ingredients` across the two groups (low-calorie vs. not-low-calorie), which is presented in the table below: 

|   minutes |   n_steps |   n_ingredients |
|----------:|----------:|----------------:|
|   100.938 |    8.5327 |         8.43444 |
|   117.605 |   10.3926 |         9.3564  |

There is a clear difference of the 3 features amongst the two groups. Lower calorie recipes had, on average, a shorter execution time to complete their recipe (~101 vs ~118 minutes), as well as having less steps and ingredients to prepare the recipe. The consistent shift in central tendencies across these three features suggests that lower calorie recipes are simpler to prepare than non-low-calorie recipes. 

## Assessment of Missingness 

In the original merged dataset that came from `recipes` and `interactions` dataset, there are 3 columns that contain missing values. This section will look into the missing values of the dataframe. 

##MNAR Analysis 

Amongst the missing columns in the original merged dataset, I believe that the `description` column is likely MNAR. For example, the users of recipes that take many ingredients, steps and preparation to make, would feel obligated to provide a lengthier and detailed description of their recipe outside of their provided steps. Meanwhile, recipes that take only a few ingredients or steps to make, would feel less obligated to write an additional description. Meaning, “lower effort” recipes would likely have missing descriptions, making the `description` column MNAR. 

### Missingness Dependency

However, there may also be the chance that the missingness of the `description` column is dependent on other columns in the dataset.

The first dependency to investigate would be if the missingness of the `description` column is dependent on the `rating` column. Although the `rating` column does have missing values, this permutation test conducted will be on recipes with _observed_ ratings, and since missing values (np.nan) will be consistently ignored, this test is not invalidated. 

__Null Hypothesis:__ Missingness of `description` is independent of rating 

__Alternative Hypothesis:__ Missingness of `description` depends on rating 

__Test Statistic:__ The absolute difference of mean ratings in recipes with a missing description and recipes without a missing description 

__Significance Level:__ 0.05 

<iframe
src="missing_desc_bar.html"
width="800"
height="600"
frameborder="0"
></iframe>

A permutation test was run by randomly shuffling the missingness indicator for the `description` column 1000 times in order to compute the absolute difference in mean ratings with a missing description and without a missing description. 

<iframe
src="missing_desc_hist.html"
width="800"
height="600"
frameborder="0"
></iframe>

The __observed statististic__, a value of __0.175__, is represented by the red dashed line on the graph above. Since the __p_value__ of__(0.017)__ is less than 0.05, the significance level I set, I __reject__ the null hypothesis. The missingness of `description` does depend on the `rating` column.

Another missingness dependency to investigate is if the missingness of the `description` column is dependent on the `n_steps` column. 

__Null hypothesis:__ Missingness of `description` is independent of the number of steps in a recipe 

__Alternative hypothesis:__ Missingness of `description` depends on the number of steps in a recipe 

__Test Statistic:__ The absolute difference of the mean number of steps in a recipe with a missing description and recipes without a missing description

__Significance Level:__ 0.05

<iframe
src="missing_desc_steps.html"
width="800"
height="600"
frameborder="0"
></iframe>

There is less than a 1 step difference on average for recipes with description and without. The missingness indicator of the `description` column was run 1000 times again, to simulate 1000 absolute differences in mean number of recipe steps where a description was missing and ones where it was not. 

<iframe
src="missing_desc_steps_p.html"
width="800"
height="600"
frameborder="0"
></iframe>

With a p value of __0.223__, which is greater than the significance level of 0.05, we _fail to reject__ the null hypothesis. The missingness of the description column does NOT appear to depend on the number of steps that a recipe has.

## Hypothesis Testing

