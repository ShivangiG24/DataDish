# DataDish 👩🏽‍🍳

# Abstract ✍️
This is an in-depth analysis of publicly-available data sourced from food.com, conducted by Shivangi Gupta and Shreya Sudan for DSC 80 (The Practice and Application of Data Science) at UC San Diego. It encompasses a series of rigorous analytical procedures, including data cleansing, exploratory data analysis, data visualization, and hypothesis testing.

# Introduction 😇

Hey! We chose the Recipes dataset because we are both passionate about cooking and felt slightly nostalgiac about working with the Great British Bake Off in DSC 10 last spring. It was a wonderful choice, to be honest, since we had a fun time reading through very creative recipe names, instructions, and reviews.

The Recipes dataframe has 83782 rows × 12 columns and the Interactions dataframe has 731927 rows × 5 columns. Note that the bulk of the project utilizes a merged version of both of these dataframes, resulting in a dataframe that has 83782 rows × 13 columns.

Throughout this project, we focused mainly on the "average rating" (the rating given to a recipe on average by all reviewers of that recipe) and "minutes" (the amount of time it takes to prepare the given recipe) columns, as we wanted to learn more about the correlation between those two. At times, we also utilized a categorical version of the cooking time which we extracted from the "tags" column (contains Food.com tags for the given recipe) by using a helper function. 

The research question which guided our data exploration was "how do average rating of recipes vary with relation to cooking times both numerically and categorically?"


Here are the components of our project:

1. Introduction
    - Pre-Processing Recipes and Interactions
3. Cleaning and EDA
    - Univariate Analysis
    - Bivariate Analysis
    - Interesting Aggregate
4. Assessment of Missingness
    - NMAR Analysis
    - Missingness Dependency
6. Hypothesis Testing

A glimpse at the original dataframes: 
`print(interactions.head().to_markdown(index=False))`
`print(recipes.head().to_markdown(index=False))`


## Pre-Processing Recipes and Interactions 

1. Loaded in both csv files and read them. 
2. Left-merged on recipes and replaced all 0 ratings with np.NaN because these were recipes that were possibly not reviewed yet, which is intrinsically different than a recipe being given a 0-star rating. 
3. Then, we found the average rating per recipe as a Series by calculating the mean rating after grouping by the recipe_id and using the transform function.
4. We added the average rating as a column to the recipes dataframe.

`print(recipes.head().to_markdown(index=False))`

# Cleaning and EDA 📊

1. Checked the datatypes of columns within the Recipes dataframe to make sure they matched what they should be logically (ie 'minutes' should be an int). 
2. When we were examining the datatypes closely, we noticed that some columns appeared to look like lists but were actually strings. For these such columns ('ingredients', 'tags', 'nutrition'), we removed the opening and closing parenthesis as well as split by string to ensure the inherent data remained the same and now fit the proper format. Note that for the nutrition column specifically, we separated each index within a singular list and created new columns in the dataframe to match each index with the value it represented. For example, calories was the first element, total fat was the second, and so on.
3. As part of our EDA, we checked for NaN values in the columns and found that 'name' had 1 missing value, 'description' had 70 missing values, and our new 'average_rating' column now had 1002 missing values (which used to be 0's).
4. We then proceeded to conduct further EDA and create the respective plots that were asked of us. Scroll down to see more markdown cells explaining what we did for this!

## Univariate Analysis
For our univariate analysis, we thought that it'd be interesting to look at average rating of recipes and the cooking times as categorical data. 
1. Empirical Distribution of the Average Ratings: For our first plot, we created a histogram to observe the empirical distribution of average ratings. Most of our datapoints (about 97%) received an average rating ranging between 4 and 5 resulting in a histogram that is left-skewed. This was expected because we noticed during our EDA that the mean of the 'average_rating' column was approximately 4.669 and the median was 4.857.
2. Counts of Cooking Times as Categorical Data: To extract cooking times, as categorical data, we observed that the 'tags' column in the DataFrame recipes had a string value in the format 'xx-minutes-or-less' or 'xx-hours-or-less'. The defined function extract_time(lst) takes in a list of strings and extracts the minutes. Note that this isn't the actual cooking time in minutes, but rather 4 different categories which entail the upper bound of cooking times for that recipe. For example, the "60-minutes-or-less" category is specifically for recipes that are between 30 and 60 minutes. After assigning a new column 'cooking time (minutes)' with these extracted values, we created our second plot-bar graph of cooking times as categorical data. With the bar chart, we were able to discern that the most recipes, specifically 25406 recipes which is equivalent to 32.0504% of the entire dataset, fell under the "60-minutes-or-less" column.

## Bivariate Analysis

1. Average Rating vs. Minutes of Recipes: We examined the relationship between the 'minutes' column of recipes, which represents how many minutes a recipe takes to make, and their 'average_rating'. We noticed that there was one far-right outlier in the data which was a recipe that recieved a rating of 5 and somehow takes over a million minutes to make. Because of this outlier, our x-axis units was in the millions although most of the data was under 100,000 minutes (and perhaps even much, much lower in the thousands or tens of thousands). We recognize that if we were analyzing the correlation between these two variables more closely, it might be beneficial to drop the extremely large outlier to produce more meaningful plots and insights but since we aren't doing so, we thought it was best to keep the original data intact.
2. Average Rating by Cooking Time: We decided to make an overlaid box plot and histogram that examined the relationship between cooking time, 60-minutes-or-less, and the average rating of these recipes. Note that this cooking time is not categorical as we filtered out recipes based on the "minutes" column which has the specific cooking times of each recipe. The box plots here look eerily similar, suggesting that regardless of whether or not the recipe took under or above 60 minutes, the recipes shared similar distributions of average ratings. If you look closely at the histogram, you'll notice that the blue distribution (recipes that fit the 60-minutes-or-less criteria) closely follows the red distribution (recipes that are above 60-minutes). 

## Interesting Aggregate

We chose to make a pivot table that showed the distribution of recipes under the 60-minutes-or-less category and everything above 60 minutes. We were able to support our previous bivariate analysis which showed that the distributions of these two groups is highly similar, seen by the similarity of the central measures of tendency. This interesting finding led to us wanting to explore the relationship between cooking times and average rating further, which gave way to our hypothesis testing later on!

# Assessment of Missingness 🕵️

## NMAR Analysis

We propose that the 'description' column in recipes is not **not missing at random** (NMAR). The description of each recipe draws in users on the website. We found out that the same contributors who did not add a description were more likely to not add a description again. Thus, the **missing values** of the description column itself tells us about the disposition of the contributor and is indicative to the likelihood of their actions.


Another argument for the column's missing values to be NMAR could be the contributor's fluency in the English language or their expertise in cooking. One who is only a novice at cooking or English might not describe their recipe with fervent passion (which btw is how we approached this project!), due to certain limitations such as not feeling comfortable or confident with going into a lot of detail about the recipe. Information on the contributor's native language or their proficiency or familiarity wih cooking, would determine whether the missing values are actually **missing at random** (MAR).

## Missingness Dependency

We decided to analyze the missingness dependency of the average_rating column at a 5% significance level, given that it's a standard research practice, based on these categorical columns:

1. 'is 60-minutes-or-less': 
    - **Null Hypothesis**: The missing values in average_rating column are not missing at random (NMAR) and do not depend on cooking times
    - **Alternative Hypothesis**: The missing values in average_rating column are missing at random and depend on cooking times.
    When conducting our permutation test for missingness, we chose the **total variation distance (tvd)** as the test statistic because we were working with categorical data i.e., 'is 60-minutes-or-less' column. The observed tvd is approximately 0.018 and is shown on the histogram below. With a p-value of 0.2, we **fail to reject the null** hypothesis because we can see that our observed_tvd lies almost in the center of the randomized permutative distribution, meaning that it is possible for us to see a result such as the one we got in the data.
    
    
2. 'contributor ID':
    - **Null Hypothesis**: The missing values in average_rating column are not missing at random (NMAR) and do not depend on contributor ID
    - **Alternative Hypothesis**: The missing values in average_rating column are missing at random and depend on contributor ID.
    When conducting our permutation test for missingness, we chose the **total variation distance (tvd)** as the test statistic because we were working with categorical data i.e., 'contributor ID' column. The observed tvd is approximately 0.289 and is shown on the histogram below. With a p-value of 0.034, we **reject the null** hypothesis  because we can see that our observed_tvd lies at the far right of the randomized permutative distribution, meaning that it is not likely for us to see a result such as the one we got in the data.

# Hypothesis Testing 🥘🤤

**Null Hypothesis**: Average rating of recipes with a cooking time of 60 minutes and under are drawn from the same, continuous distribution as the average rating of recipes with a cooking time of greater than 60 minutes.

**Alternative Hypothesis**: Recipes with a cooking time of 60 minutes and under do not have the same average rating of recipes with a cooking time of greater than 60 minutes, implying that these two groups of recipes (differentiated by cooking times) do not come from the same distribution.

**Test Statistic & Significance Level**: Our test statistic is the k-s statistic. We chose this because we noticed that the measure of central spread i.e., medians and means of the two distributions were highly similar (observable in our overlaid box plot and pivot table above) which implied that we couldn't use a statistic such as the difference in means nor tvd as we weren't dealing with categorical data. For the significance level, we chose to do 5% as it's very standard in conducting scientific research experiments.

**P-value and Conclusion**: Our p-value from both the shorter ks_2samp method (which uses the built-in function) was 0.767 and our p-value after performing the permutation test was 0.572. While these may seem different, they both lead to us failing to reject our null hypothesis as they are above the significance level of 0.05. This means that we cannot conclude that the average rating of recipes with a cooking time of 60 minutes and under are drawn from the same, continuous distribution as the average rating of recipes with a cooking time of greater than 60 minutes.
