# Data Analysis for Professional League of Legends Scene
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
- 'dragons': This column shows the total number of dragons of a team has defeat in the entire game. The output is a non-negative integer.
- 'golddiffat15': This column shows the gold difference at 15 minutes.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
First of all, I droped the 124 columns and keep the 7 columns descibed above. Next, I transformed the outcome of the ordinal columns, 'result','position', and 'firsttower' into boolean values. Also, I created two new columns named 'game time(s)' and 'is_team'.  'game time(s)' column convert the output of 'gamelength' column into seconds. 'is_team' column returns True if the column is about team and False if not.
Here is the first five rows of the DataFrame:
|league|result|side|firsttower|firstdragon|dragons|golddiffat15|game time(s)|is_team|
|:---|:-----|:----|:----|:----|:--|:---|:----|:-----|
|LCKC|False|	Blue|	False|	False|	NaN|	391.0|	1033|	False|
|LCKC|False|	Blue|	False|	False|	NaN|	541.0|	1033|	False|
|LCKC|False|	Blue|	False|	False|	NaN|	-475.0|	1033|	False|
|LCKC|False|	Blue|	False|	False|	NaN|	-793.0|	1033|	False|
|LCKC|False|	Blue|	False|  False|	NaN|	443.0|	1033|	False|

### Univariate Analysis
