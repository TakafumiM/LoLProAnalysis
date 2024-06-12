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
|:-----|:-----|:---|:---------|:----------|:-----------|:-----------|:------|
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
|:---|:----------|:---------|:-----|
|Blue|       0.41|   	  0.54|	 0.52|
|Red |       0.59|	    0.46|	 0.48|
There are some side bias in each column. 
Since the dragon pit is more accessible from the red side than the blue, it has been said that it is easier to red team to capture the dradon. That tendency is shown in the table as well. On the other hand, first tower and the game is a little biased towards the blue side. The reason is not clear here. The advantage in the champion draft might be the cause, or the red side jungle being open might be the problem. Regardless of the cause, blue side team are a little likely to win the game.

## Assessment of Missingness
In the given data, there is very few column which the missingness might be NMAR(Not Missing At Random). Among 104 columns with missing values, most of them are related to the 'position' column, which means that the value is missing from individuals' rows because it is a team related value or the opposite. 
