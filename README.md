# Data Analysis of Professional League of Legends Scene
Author: Takafumi Matsui
## Introduction
### Overview of this analysis
  League of Legends is a multiplayer online battle arena, often called MOBA. It is a world wide popular esports game with multiple professional league around the world. In this anallysis, I seek to analyze data and make a prediction model which will predict the result of a professional League of Legends game. The data set I worked on is a professional data set from 2022 season, which was developed by Oracle’s Elixir.

  In League of Legends, the lead at 15 minutes is one of an important indicator of which team is doing better than others. During the games, players can start vote to surrender the game after 15 minutes, which indicates that officially, the lead at 15 minuites can be a game deciding factor.

  The center question in this analysis is 'How likely the team with more gold at 15 minuets will win the game?' Every objects in this game will produce gold. Therefore, gold lead shows how well a team is performing compared to their opponents. 

### Columns description
  In the data set, there are 148992 rows × 131 columns. While I will use most of the rows, there are seven columns I will focus on. Here is the descriptions of those columns:
- 'league': This column shows in which league the game happened. There are 49 unique leagues in the data.
- 'gamelength': This column shows the length of the game in a format of mmss, where m is minutes and s is seconds.
- 'result': This column shows the result of a match. 1 indicates win and 0 indicates lose.
- 'side': This column shows the side of a player or a team of the row. There are two sides in League of Legends, 'Blue' and 'Red'.
- 'position': This column show the position of a player or displays 'team' to indicate that the column is about the entire team. 
- 'firsttower': This column shows whether a team of the row took down the first tower of the game. 1 indicates that they took the tower, 0 indicates that they didn't.
- 'golddiffat15': This column shows the gold difference at 15 minutes.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
  First of all, I droped the 124 columns and keep the 7 columns descibed above. 
  Next, I transformed the outcome of the ordinal columns, 'result','position', and 'firsttower' into boolean values. Also, I created two new columns named 'game time(m)' and 'is_team'.  'game time(m)' column convert the output of 'gamelength' column into minutes. 'is_team' column returns True if the column is about team and False if not.
  Finally, I took the rows which 'is_team' is True, to pivot the DataFrame into data about teams only and dropped all rows with null input.
Here is the first five rows of the DataFrame:
|league|result|side|firsttower|firstdragon|golddiffat15|game time(m)|is_team|
|:-----|:-----|:---|:---------|:----------|-----------:|-----------:|:------|
LCKC   | False|Blue|	    True|	     False|	      107.0|	      1033|	True|
LCKC   |	True|	Red|	   False|	      True|	     -107.0|	      1033|	True|
LCKC   | False|Blue|	   False|	     False|	    -1763.0|	      1274|	True|
LCKC   |	True|	Red|	    True|	      True|	     1763.0|	      1274|	True|
LCKC   |	True|Blue|	    True|	      True|	     1191.0|	      1212|	True|

### Univariate Analysis
Here is the histogram I permformed univariate analysis on the gold difference by the winning teams at 15 minutes in the game:
<iframe
  src="assets/gdf15.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The histogram shows that the distribution of the gold difference is nearly normal, and the mean and median are positive. At least, it is clear that the teams which win the game are more likely to win in money at 15 minutes than to lose.

### Bivariate Analysis
Here is the scatter plot I performed bivariate analysis on gold difference at 15 minutes against the game length:
<iframe
  src="assets/gdflength.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The scatter plot shows that teams with gold lead more than 10k are likely they have already closed the game at 15 minutes. However, the other parts do not have a clear trend. 

### Interesting Aggregates
Here are some interesting aggregates from the data set.
|side|firstdragon|firsttower|result|
|:---|----------:|---------:|-----:|
|Blue|       0.41|   	  0.54|	 0.52|
|Red |       0.59|	    0.46|	 0.48|

There are some side bias in each column. 
Since the dragon pit is more accessible from the red side than the blue, it has been said that it is easier to red team to capture the dradon. That tendency is shown in the table as well. On the other hand, first tower and the game is a little biased towards the blue side. The reason is not clear here. The advantage in the champion draft might be the cause, or the red side jungle being open might be the problem. Regardless of the cause, blue side team are a little likely to win the game.

## Assessment of Missingness
### NMAR Analysis
  In the given data, there is very few column which the missingness might be NMAR(Not Missing At Random). Among 104 columns with missing values, there are two patterns of missing. One of them is that they are related to the 'position' column, which means that the value is missing from individuals' rows because it is a team related value or the opposite. Another pattern is that they are related to 'league' column, especially Chinese related leagues: DCup, LDL and LPL.
  If I had to choose columns which are possibly NMAR, it would be the ban columns: 'ban1','ban2','ban3','ban4','ban5'. While many missing rows from this columns is related to Chinese leagues, there are also many other leagues missing this row. The reason might be related to the ban system of League of Legends. While the players are forced to pick a champion during the pick phase, players have the freedom to whether ban a champion or not. There are many reasons not to ban a champion during the ban phase. They might not want to reveal their pick/ban strategies in a throwaway match, or they might run out of time thinking too much.

### Missingness Dependency
  There are many missing values in the 'golddiffat15' columns. Here, I will test its dependency on other columns. I will perform permutation test over the 'league' column and the 'result' column. The test statistic is Total Variance Distance for both permutation tests and the significance level is 5%.

#### 'league' column
#### Null Hypothesis: 
Distribution of 'league' when 'golddiffat15' is missing is the same as the distribution of 'league' when 'golddiffat15' is not missing.

#### Alternate Hypothesis: 
Distribution of 'league' when 'golddiffat15' is missing is not the same as the distribution of 'league' when 'golddiffat15' is not missing.

Here is the histogram of the test results.
<iframe
  src="assets/mar1.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
The p-values was 0.026. Therefore, I reject the null hypothesis with the 5% significance level.

Conclusion: Distribution of 'league' when 'golddiffat15' is missing is not the same as the distribution of 'league' when 'golddiffat15' is not missing.

#### 'result' column
#### Null Hypothesis: 
Distribution of 'result' when 'golddiffat15' is missing is the same as the distribution of 'result' when 'golddiffat15' is not missing.

#### Alternate Hypothesis: 
Distribution of 'result' when 'golddiffat15' is missing is not the same as the distribution of 'result' when 'golddiffat15' is not missing.

Here is the histogram of the test results.
<iframe
  src="assets/mar2.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
The p-values was 1. Therefore, I failed to reject the null hypothesis with the 5% significance level.

## Hypothesis Testing
Here, I seek to find the answer of my center question; whether the team winning in golds at 15 minutes is likely to win or not. As it was shown in Interesting Aggregates, the blue side winrate is 52%. Here is my hypothesis.

#### Null Hypothesis: 
The probability that the blue side team who are winning in golds at 15 minutes is less than 52% 

#### Alternative Hypothesis: 
The probability that the blue side team who are winning in golds at 15 minutes is more than or equal to 52% 

#### Test Statistics:
Difference between the mean and 0.52.

#### Significance Level is 5%

The p-value was 0.262, which lead me to fail rejecting the null hypothesis. I couldn't find a clear relation between win rate and the gold lead at 15 minutes.

## Framing a Prediction Problem
I could not find a strong evidence during the analysis that the gold lead at 15 minutes can predict the winning team. This raise a prediction problem:
#### Is it possible to predict the game result with the information before 15 minutes?
This is a binary classification, which I seek to predict if the result is True(win) or False(lose). To find if the team win or not, there is any other better variable to predict than the result.
For model evaluation, I am going to use accuracy over f1-score becaue the output are expected to be fairly balanced, and getting False Negative or False Positive is not a fatal problem for this analysis.

## Baseline Model
My baseline model used Decision Tree Classifier with two features: a nominal feature 'side' and a continuous feature 'golddiffat15'. My baseline model is simple. Since 'side' is a categorical column with string imputs, I used OneHotEncoder to 'side' and trained the model.
The accuracy was 0.72504 which is about 72.5%. I will say that this is a good result considering I have access to only a few information that observers can get before 15 minutes. However, let's see if I can make this model better.

## Final Model
In my final model, I added multiple features and a transformer. Also, improved the classifier.

### Added Variables:

#### 'firsttower' (ordinal)
'firsttower' is an important factor to know if a team is utilizing
their gold advantage and expanding them. If a winning team take it, they can snowball the game. On the other hand, if a losing team take it, they can find chance to extend the game and get more scaling.

#### 'firstdragon' (ordinal)
'firstdragon' is added because the dragon is usually traded with
objects/kills, but dragon itself doesn't have much impact in gold difference
while it has impact on champion stats.


'firstblood' was considered but not added because it can be reflected into the gold lead compared to 'firstdragon'.

Instead, 'xpdiffat15' is added.

#### 'xpdiffat15' (continuous)
'xpdiffat15' is added because it is also a factor that does not get reflected into the gold lead, but have massive effect on champion stats


Considered adding 'firstbaron', but didn't because the baron spawn time is 
fixed in 20 min of the game. I wanted to stay with the variables that could 
possibly be measured before 15 min in the game.

'game time(m)' is also important to determine if the winning team was able to
finish the game defending their advantage, but not able to get this information
at 15 min. I considered using Binarizer to this feature, but there are too many values over 15 minutes that could be noise as shown in the bivariate analysis. Therefore, I decided not to use it.

### Added Transformer:
While OneHotEncoder still encodes all of the categorical features, StandardScaler is added to encodes the two continuous features. 

#### StandardScaler
Without any transformers, it is not clear that how good the gold lead or experience lead. Therefore, StandardScaler is added to decide how good the lead is compared to other matches.

#### DecisionTreeClassifier

## Fairness Analysis

#### Null Hypothesis: 
My model is fair. Its precision for Blue side and Red side are roughly the same, and any differences are due to random chance.

#### Alternative Hypothesis:
My model is unfair. Its precision for Blue side and Red side are not the same.

#### Test Statisitcs:
Difference between the observed precision and the precision of the permutation test.

p-value is 0.279. I failed to reject the null hypothesis at the significance level of 5%. Accordingly, my model seems to be not biased towards either side, but a fair model.
