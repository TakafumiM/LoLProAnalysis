# Data Analysis of Professional League of Legends Scene
Author: Takafumi Matsui
## Introduction
### Overview of this analysis
  League of Legends is a multiplayer online battle arena, often called MOBA. It is a world wide popular esports game with multiple professional league around the world. In this anallysis, I seek to analyze data and make a prediction model which will predict the result of a professional League of Legends game. The data set I worked on is a professional data set from 2022 season, which was developed by Oracle’s Elixir.

  In League of Legends, the lead at 15 minutes is one of an important indicator of which team is doing better than others. During the games, players can start vote to surrender the game after 15 minutes, which indicates that officially, the lead at 15 minuites can be a game deciding factor.

  The center question in this analysis is 'How likely the team with more gold at 15 minuets will win the game?' Every objects in this game will produce gold. Therefore, gold lead shows how well a team is performing compared to their opponents. 

### Columns description
  In the data set, there are 148992 rows × 131 columns. While I will use most of the rows, there are eight columns I will focus on. Here is the descriptions of those columns:
- 'league': This column shows in which league the game happened. There are 49 unique leagues in the data.
- 'gamelength': This column shows the length of the game in a format of mmss, where m is minutes and s is seconds.
- 'result': This column shows the result of a match. 1 indicates win and 0 indicates lose.
- 'side': This column shows the side of a player or a team of the row. There are two sides in League of Legends, 'Blue' and 'Red'.
- 'position': This column show the position of a player or displays 'team' to indicate that the column is about the entire team. 
- 'firsttower': This column shows whether a team of the row took down the first tower of the game. 1 indicates that they took the tower, 0 indicates that they didn't.
- 'golddiffat15': This column shows the gold difference at 15 minutes.
- 'xpdiffat15': This column shows the experience difference at 15 minutes.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
  First of all, I droped the 124 columns and keep the 7 columns descibed above. 
  Next, I transformed the outcome of the ordinal columns, 'result','position', and 'firsttower' into boolean values. Also, I created two new columns named 'game time(m)' and 'is_team'.  'game time(m)' column convert the output of 'gamelength' column into minutes. 'is_team' column returns True if the column is about team and False if not.
  Finally, I took the rows which 'is_team' is True, to pivot the DataFrame into data about teams only and dropped all rows with null input.
Here is the first five rows of the DataFrame:
| league   | result   | side   | firsttower   | firstdragon   |   golddiffat15 |   game time(m) | is_team   |   xpdiffat15 |
|:---------|:---------|:-------|:-------------|:--------------|---------------:|---------------:|:----------|-------------:|
| LCKC     | False    | Blue   | True         | False         |            107 |        17.2167 | True      |        -1617 |
| LCKC     | True     | Red    | False        | True          |           -107 |        17.2167 | True      |         1617 |
| LCKC     | False    | Blue   | False        | False         |          -1763 |        21.2333 | True      |         -906 |
| LCKC     | True     | Red    | True         | True          |           1763 |        21.2333 | True      |          906 |
| LCKC     | True     | Blue   | True         | True          |           1191 |        20.2    | True      |         2298 |

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
| side   |   firstdragon |   firsttower |   result |
|:-------|--------------:|-------------:|---------:|
| Blue   |      0.406247 |     0.541946 | 0.522695 |
| Red    |      0.593564 |     0.458054 | 0.477116 |

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

Here is the obseved distribution of 'league' when 'golddiffat15' is missing and not missing.

| league     |   gdf_missing = False |   gdf_missing = True |
|:-----------|----------------------:|---------------------:|
| CBLOL      |            0.022931   |           0          |
| CBLOLA     |            0.0203831  |           0          |
| CDF        |            0.00688874 |           0          |
| CT         |            0.00245352 |           0          |
| DCup       |            0          |           0.042331   |
| DDH        |            0.0197226  |           0          |
| EBL        |            0.0174578  |           0          |
| EL         |            0.0127395  |           0          |
| ESLOL      |            0.0228367  |           0          |
| EUM        |            0.0251958  |           0          |
| GL         |            0.0164197  |           0          |
| GLL        |            0.0191564  |           0          |
| HC         |            0.0152873  |           0          |
| HM         |            0.014438   |           0          |
| IC         |            0.00707747 |           0          |
| LAS        |            0.0214212  |           0          |
| LCK        |            0.0440691  |           0          |
| LCKC       |            0.0371803  |           0          |
| LCL        |            0.00150986 |           0          |
| LCO        |            0.0200057  |           0          |
| LCS        |            0.0288761  |           0          |
| LCSA       |            0.0509578  |           0          |
| LDL        |            0          |           0.517867   |
| LEC        |            0.022931   |           0          |
| LFL        |            0.0233085  |           0          |
| LFL2       |            0.0227423  |           0          |
| LHE        |            0.022931   |           0          |
| LJL        |            0.0201944  |           0          |
| LJLA       |            0.00358592 |           0          |
| LLA        |            0.0176465  |           0          |
| LMF        |            0.0301029  |           0          |
| LPL        |            0          |           0.432106   |
| LPLOL      |            0.0197226  |           0          |
| LVP SL     |            0.0231198  |           0          |
| MSI        |            0.00754931 |           0          |
| NEXO       |            0.0182127  |           0          |
| NLC        |            0.0361423  |           0          |
| PCS        |            0.0255733  |           0          |
| PGC        |            0.0530339  |           0          |
| PGN        |            0.0140606  |           0          |
| PRM        |            0.0346324  |           0          |
| SL (LATAM) |            0.0155704  |           0          |
| TAL        |            0.0193451  |           0          |
| TCL        |            0.020855   |           0          |
| UL         |            0.0230254  |           0          |
| UPL        |            0.0388789  |           0          |
| VCS        |            0.0304803  |           0          |
| VL         |            0.0160423  |           0          |
| WLDs       |            0.0133057  |           0.00769654 |

Here is the histogram of the emprirical distribution of the test TVD.
<iframe
  src="assets/mar1.html"
  width="800"
  height="400"
  frameborder="0"
></iframe>
The p-values was 0.026. Therefore, I reject the null hypothesis with the 5% significance level.

Conclusion: There was not enough evidence to show that distribution of 'league' when 'golddiffat15' is missing is not the same as the distribution of 'league' when 'golddiffat15' is not missing.

#### 'result' column
#### Null Hypothesis: 
Distribution of 'result' when 'golddiffat15' is missing is the same as the distribution of 'result' when 'golddiffat15' is not missing.

#### Alternate Hypothesis: 
Distribution of 'result' when 'golddiffat15' is missing is not the same as the distribution of 'result' when 'golddiffat15' is not missing.

Distribution of 'result' when 'golddiffat15' is missing is not the same as the distribution of 'result' when 'golddiffat15' is not missing.

| result   |   gdf_missing = False |   gdf_missing = True |
|:---------|----------------------:|---------------------:|
| False    |              0.500094 |                  0.5 |
| True     |              0.499906 |                  0.5 |

Here is the histogram of the emprirical distribution of the test TVD.
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
My baseline model used Decision Tree Classifier two features: a nominal feature 'side' and a continuous feature 'golddiffat15'. My baseline model is simple. Since 'side' is a categorical column with string imputs, I used OneHotEncoder to 'side' and trained the model.
The accuracy was 0.7342 which is about 73.4%. I will say that this is a good result considering I have access to only a few information that observers can get before 15 minutes. However, let's see if I can make this model better.

## Final Model
In my final model, I added multiple features and a transformer.

### Added Variables:

#### 'firsttower' (ordinal)
'firsttower' is an important factor to know if a team is utilizing
their gold advantage and expanding them. If a winning team take it, they can snowball the game. On the other hand, if a losing team take it, they can find chance to extend the game and get more scaling.

#### 'firstdragon' (ordinal)
'firstdragon' is added because the dragon is usually traded with objects/kills, but dragon itself doesn't have much impact in gold difference while it has impact on champion stats.

'firstblood' was considered but not added because it can be reflected into the gold lead compared to 'firstdragon'.

Instead, 'xpdiffat15' is added.

#### 'xpdiffat15' (continuous)
'xpdiffat15' is added because it is also a factor that does not get reflected into the gold lead, but have massive effect on champion stats


Considered adding 'firstbaron', which is a feature which indicates if I team took the first baron or not, but didn't because the baron spawn time is fixed in 20 min of the game. I wanted to stay with the variables that could possibly be measured before 15 min in the game.

'game time(m)' is also important to determine if the winning team was able to finish the game defending their advantage, but this information is not available at 15 min. I considered using Binarizer to this feature, but there are too many values over 15 minutes that could be noise as shown in the bivariate analysis. Therefore, I decided not to use it.

### Added Transformer:
While OneHotEncoder still encodes all of the categorical features, StandardScaler is added to encodes the two continuous features. 

#### StandardScaler
Without any transformers, it is not clear that how good the gold lead or experience lead is. Therefore, StandardScaler is added to decide how good the lead is compared to other matches.

The accuracy of the new model was 0.7427. The accuracy did improve, but not significantly.

### Best Hyperparameters
To further improve the model, here I will find the best hyperparameter to tune the model. The hyperparameters I test on is max_depth and criterion. 




## Fairness Analysis
Finally, I am going to check if my model is fair among the sides. In other words,
#### Is my model biased towards either blue or red side?

Here, I chose blue side as group X and red side as group Y. 

#### Null Hypothesis: 
My model is fair. Its precision for Blue side and Red side are roughly the same, and any differences are due to random chance.

#### Alternative Hypothesis:
My model is unfair. Its precision for Blue side and Red side are not the same.

#### Test Statisitcs:
Difference between the observed precision and the precision of the permutation test.

p-value is 0.194. I failed to reject the null hypothesis at the significance level of 5%. Accordingly, there is not enough evidence that my model is biased towards either side.
