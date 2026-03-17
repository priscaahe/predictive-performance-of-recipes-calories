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
| — | — |
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
| — | — | 
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




