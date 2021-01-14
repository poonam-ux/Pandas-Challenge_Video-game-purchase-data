# Pandas Challenge: Video game (Heroes of Pymoli) purchase data

## Background
After landing a job as Lead Analyst for an independent gaming company, you've been assigned the task of analyzing the data for their most recent fantasy game Heroes of Pymoli.

Like many others in its genre, the game is free-to-play, but players are encouraged to purchase optional items that enhance their playing experience. As a first task, the company would like you to generate a report that breaks down the game's purchasing data into meaningful insights.

## Objective
The final report should include each of the following:

### Player Count
* Total Number of Players
# Total Number of Players
players = purchase_data.loc[:, ["Gender", "SN", "Age"]]
players = players.drop_duplicates()
player_count = players.count()[0]

# Create DataFrame
players_df = pd.DataFrame({"Total Players": [player_count]})
players_df

![]https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Total%20Players.png

