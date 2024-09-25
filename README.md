# Analyzing Mobile Game Data Using Pandas

### Project Goals

The main goal of my project is to demonstrate the skills I have gained throughout the Python course as part of the Professional Diploma in Data Analytics and Finnish Language from Aalto EE. I selected the idea of this project from a [collection of test assignments for data analytics](https://pavelbukhtik.notion.site/Product-Analyst-Data-Analyst-a5f7bea5a0064960bbdb7a3ee1e46e7) (NB: It's in Russian). The country codes and names data were sourced from the [ISO-3166 dataset](https://github.com/lukes/ISO-3166-Countries-with-Regional-Codes/tree/master/all).

### Data Analysis Task

The task is to analyze mobile game data, which includes information about players such as their level, country, platform, registration date, and last login date. This analysis is expected to provide insight into the game's audience and player behavior.

### Questions

1) For each platform, determine the top 5 countries by the number of players.
2) What is the maximum lifetime of players in the game?
3) How many players are above level 20?
4) Calculate the average player level for each country.

### Dataset Description

Dataset source: [Google Sheets Link](https://docs.google.com/spreadsheets/d/1bb0mBd9OGSdH08POkWgcYlK9a2YPQq1Dd2XyikUdeeI/edit#gid=1478114822).

The initial table contains the following columns:

* country - the player's country
* pl - the player's level
* plf - the platform
* reg - the player's registration date
* ts - the time of the player's last login to the game
* user - the player's ID

## Data Preparation

Upon reviewing the dataset, I identified several issues:

* Unclear column names
* Redundant user IDs
* Non-human-readable date format
* Non-human-readable country codes

To address these issues and improve data quality, I:

* Renamed columns
* Converted date formats
* Handled redundant user IDs
* Added country names

Detailed descriptions and the process are provided in the Jupyter Notebook file. Here you can see a snippet from the initial dataset alongside the result after the data preparation process. This comparison highlights the improvements made, such as clearer column names, readable date formats, simplified user IDs, and mapped country names.

Before:

![pic1](https://github.com/user-attachments/assets/b3e26682-4bf4-4ce1-bcad-4f9bb67d8bdc)

After:

![pic2](https://github.com/user-attachments/assets/b216f4a7-fb27-41e4-ab3c-de6886fff1a4)

## Exploring and Analysing the Data

Now that my dataset is clean and prepared, I can begin to answer the questions I listed earlier. But first, let's look at some basic statistics to better understand the data.

The dataset contains 997 unique players with an average level of 13, from 100 different countries. With this initial exploration, I now have a better understanding of the scope of the data, and I can proceed with the more detailed analysis.

### Top 5 Countries by Number of Players

To identify the top 5 countries with the most players for each platform, I grouped the dataset by platform and country, and counted the number of players for each combination. Then I sorted the results by platform and player count, ensuring the counts are in descending order within each platform. Finally, I extracted the top 5 countries by player count for each platform.

The result shows the top countries that contribute the most players on each platform.

```Python
# Groupping the dataset by platform and country and counting the number of players
country_counts = df_final.groupby(['platform', 'country_name']).size().reset_index(name='player_count')

# Sorting the results by platform and player count in descending order
country_counts_sorted = country_counts.sort_values(by=['platform', 'player_count'], ascending=[True, False])

# Extracting top 5
top_countries_per_platform = country_counts_sorted.groupby('platform').head(5).reset_index(drop=True)
```
### Maximum Lifetime of Players in the Game

To calculate the maximum lifetime of players in the game, I subtracted the registration date (reg_date) from the last login date (last_login). This gave me the lifetime (in days) of each player, representing how long they've been active in the game. Then I found the player with the longest lifetime.

```Python
# Converting 'last_login' and 'reg_date' columns to datetime format
df_final['last_login'] = pd.to_datetime(df_final['last_login'])
df_final['reg_date'] = pd.to_datetime(df_final['reg_date'])

# Calculating player lifetime as the difference between 'last_login' and 'reg_date' in days
df_final['lifetime'] = (df_final['last_login']-df_final['reg_date']).dt.days

# Converting the 'lifetime' to integers for clarity
df_final.loc[:,'lifetime'] = df_final['lifetime'].astype('int')

# Calculating the maximum lifetime
max_lifetime = df_final['lifetime'].max()

print("The answer is", max_lifetime, "days")
```
The answer is 891 days.

### How Many Players Are Above Level 20?

To determine how many players are above level 20, I filtered the dataset for those whose level is above 20 and then counted the unique players.

```Python
# Filtering the dataset to count players with a level greater than 20
players_20 = df_final[df_final['level'] > 20]['user_id'].nunique()
print("Number of players above level 20:",players_20)
```
Out of 997 unique players, only 249 (25%) have successfully reached a level higher than 20. Although this shows an interesting pattern in player progression, further analysis would be required to fully understand the factors influencing player advancement — something that is beyond the scope of this project.

### Average Player Level for Each Country

To calculate the average player level for each country, I grouped the dataset by country_name and calculated the average level for each country. I then sorted the countries in descending order by their average player level.

```Python
# Grouping by 'country_name' and calculating the average player level
country_avglevel = df_final.groupby(['country_name'])['level'].mean().reset_index(name='avg_level')

# Sorting in descending order
country_avglevel_sorted = country_avglevel.sort_values(by=['avg_level'], ascending=[False])
country_avglevel_sorted.reset_index(drop=True)

# Displaying the top 10 countries by average player level
top_countries_players_level = country_avglevel_sorted.head(10)
top_countries_players_level
```
This gives us the top 10 countries with the highest average player level. The result could be interesting for understanding which countries have more advanced players. However, further analysis may be needed to determine the underlying factors behind these trends.

## Conclusion

In this project, I explored and analyzed mobile game data using Pandas. By cleaning and organizing the dataset, I was able to answer key questions about player distribution and behavior. The analysis offered valuable insights and provided a solid foundation for understanding the game's player base and engagement trends.

Throughout the analysis, I applied several skills I learned in the Python course, including working with data types, manipulating strings, using loops, and working with lists. This project not only expanded my knowledge, but also highlighted the practical application of these concepts in real-world data analysis.
