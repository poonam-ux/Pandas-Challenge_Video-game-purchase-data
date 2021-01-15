# Pandas Challenge: Video Game (Heroes of Pymoli) purchase data

## Background
After landing a job as Lead Analyst for an independent gaming company, you've been assigned the task of analyzing the data for their most recent fantasy game Heroes of Pymoli.

Like many others in its genre, the game is free-to-play, but players are encouraged to purchase optional items that enhance their playing experience. As a first task, the company would like you to generate a report that breaks down the game's purchasing data into meaningful insights.

## Objectives
The final report should include each of the following:

### Player Count
* Total Number of Players
```python
# Total number of players
players = purchase_data.loc[:, ["Gender", "SN", "Age"]]
players = players.drop_duplicates()
player_count = players.count()[0]

# Create DataFrame
players_df = pd.DataFrame({"Total Players": [player_count]})
players_df
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Total%20Players.png)

### Purchasing Analysis (Total)
* Number of Unique Items
* Average Item Price
* Total Number of Purchases
* Total Revenue
```python
# Number of Unique Items
item_count = len(purchase_data["Item ID"].unique())

# Average Purchase Price
average_item_price = purchase_data["Price"].mean()

# Total Number of Purchases
purchase_count = purchase_data["Price"].count()

# Total Revenue
total_revenue = purchase_data["Price"].sum()

# Create DataFrame
purchase_table = pd.DataFrame({
                    "Number of Unique Items": item_count,
                    "Average Item Price": [average_item_price],
                    "Number of Purchases": [purchase_count],
                    "Total Revenue": [total_revenue]
                    })
purchase_table = purchase_table.round(2)

# DataFrame formatting
purchase_table["Average Item Price"] = purchase_table["Average Item Price"].map("${:,.2f}".format)
purchase_table["Number of Purchases"] = purchase_table["Number of Purchases"].map("{:,}".format)
purchase_table["Total Revenue"] = purchase_table["Total Revenue"].map("${:,.2f}".format)
purchase_table = purchase_table.loc[:, ["Number of Unique Items", "Average Item Price", "Number of Purchases", "Total Revenue"]]
purchase_table
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Purchasing%20Analysis%20(Total).png)

### Gender Demographics
* Percentage and Count of Male Players
* Percentage and Count of Female Players
* Percentage and Count of Other / Non-Disclosed
```python
# Total count and percentage of players based on gender
gender_count = players["Gender"].value_counts()
gender_percent = gender_count / player_count

# Create DataFrame
gender_demographics = pd.DataFrame({
                        "Total Count": gender_count,
                        "Percentage of Players": gender_percent
                        })

# DataFrame formatting
gender_demographics["Percentage of Players"] = gender_demographics["Percentage of Players"].map("{:.2%}".format)
gender_demographics
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Gender%20Demographics.png)

### Purchasing Analysis (Gender)
The below each broken by gender
* Purchase Count
* Average Purchase Price
* Total Purchase Value
* Average Purchase Total per Person by Gender
```python 
# Purchase count of Male and Female players
purchase_count_gender = purchase_data.groupby(["Gender"]).count()["Price"].rename("Purchase Count")

# Average Purchase Price
avg_purchase_price = purchase_data.groupby(["Gender"]).mean()["Price"].rename("Average Purchase Price")

# Total Purchase Value
total_purchase_value = purchase_data.groupby(["Gender"]).sum()["Price"].rename("Total Purchase Value")

# Calculate Normalized Purchase
avg_purchase_per_person = total_purchase_value / gender_demographics["Total Count"]

# Create DataFrame
purchasing_gender = pd.DataFrame({
                        "Purchase Count": purchase_count_gender,
                        "Average Purchase Price": avg_purchase_price,
                        "Total Purchase Value": total_purchase_value,
                        "Avg Total Purchase per Person (Normalized Totals)": avg_purchase_per_person
                        })

# DataFrame formatting
purchasing_gender["Average Purchase Price"] = purchasing_gender["Average Purchase Price"].map("${:.2f}".format)
purchasing_gender["Total Purchase Value"] = purchasing_gender["Total Purchase Value"].map("${:,.2f}".format)
purchasing_gender["Avg Total Purchase per Person (Normalized Totals)"] = purchasing_gender["Avg Total Purchase per Person (Normalized Totals)"].map("${:.2f}".format)
purchasing_gender
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Purchasing%20Analysis%20(Gender).png)

### Age Demographics
The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)
* Total Count
* Percentage of Players
```python 
# Figure Out Minimum and Maximum Ages
# print(purchase_data["Age"].max())
# print(purchase_data["Age"].min())

#Create bins and group names for ages
age_bins = [0, 9.99, 14.99, 19.99, 24.99, 29.99, 34.99, 39.99, 999]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

# Place data series into new column inside DataFrame
players["Age Group"] = pd.cut(players["Age"], age_bins, labels=group_names)
players

# Count total players by Age category
purchase_count_age = players["Age Group"].value_counts()

# Calculate percentages by Age category 
percentage_by_age = purchase_count_age / player_count * 100

# Create DataFrame
age_demographics = pd.DataFrame({
                            "Total Count": purchase_count_age, 
                            "Percentage of Players": percentage_by_age
                            })

# DataFrame formatting
age_demographics["Percentage of Players"] = age_demographics["Percentage of Players"].map("{0:,.2f}%".format)
age_demographics = age_demographics.sort_index()
age_demographics
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Age%20Demographics.png)

### Purchasing Analysis (Age)
The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)
* Purchase Count
* Average Purchase Price
* Total Purchase Value
* Average Purchase Total per Person by Age Group
```python
#Create bins and group names for ages
age_bins = [0, 9.99, 14.99, 19.99, 24.99, 29.99, 34.99, 39.99, 999]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

# Place data series into new column inside DataFrame
purchase_data["Age Group"] = pd.cut(purchase_data["Age"], age_bins, labels=group_names)

# Calculate Count, Average Price and Total Purchase Value based on Age Group
purchase_count_age = purchase_data.groupby(["Age Group"]).count()["Price"].rename("Purchase Count")
avg_purchase_price = purchase_data.groupby(["Age Group"]).mean()["Price"].rename("Average Purchase Price")
total_purchase_age = purchase_data.groupby(["Age Group"]).sum()["Price"].rename("Total Purchase Value")
avg_purchase_per_person = total_purchase_age / age_demographics["Total Count"]

# Create DataFrame
purchasing_by_age = pd.DataFrame({
                        "Purchase Count": purchase_count_age,
                        "Average Purchase Price": avg_purchase_price,
                        "Total Purchase Value": total_purchase_age,
                        "Avg Total Purchase per Person": avg_purchase_per_person
                        })

# DataFrame formatting
purchasing_by_age["Average Purchase Price"] = purchasing_by_age["Average Purchase Price"].map("${0:,.2f}".format)
purchasing_by_age["Total Purchase Value"] = purchasing_by_age["Total Purchase Value"].map("${0:,.2f}".format)
purchasing_by_age["Avg Total Purchase per Person"] = purchasing_by_age["Avg Total Purchase per Person"].map("${0:,.2f}".format)
purchasing_by_age
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Age%20Demographics-%20Purchasing%20Analysis.png)

### Top Spenders
Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
* SN
* Purchase Count
* Average Purchase Price
* Total Purchase Value
```python
# Identify the Top 5 Spenders by Total Purchase Value and GroupBy "SN"

# Calculate "Purchase Count"
purchase_count_sn = purchase_data.groupby(["SN"]).count()["Price"].rename("Purchase Count")

# Calculate "Average Purchase Price"
avg_purchase_price = purchase_data.groupby(["SN"]).mean()["Price"].rename("Average Purchase Price")

# Calculate "Total Purchase Value"
total_purchase_value = purchase_data.groupby(["SN"]).sum()["Price"].rename("Total Purchase Value")

# Create DataFrame
spender_data = pd.DataFrame({
                        "Purchase Count": purchase_count_sn,
                        "Average Purchase Price": avg_purchase_price,
                        "Total Purchase Value": total_purchase_value
                        })

# DataFrame formatting
spender_data["Average Purchase Price"] = spender_data["Average Purchase Price"].map("${:,.2f}".format)
top_spenders = spender_data.sort_values("Total Purchase Value", ascending=False)
top_spenders["Total Purchase Value"] = top_spenders["Total Purchase Value"].map("${:,.2f}".format)
top_spenders.head(5)
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Top%20Spenders.png)

### Most Popular Items
Identify the 5 most popular items by purchase count, then list (in a table):
* Item ID
* Item Name
* Purchase Count
* Item Price
* Total Purchase Value
```python
# Retrieve the Item ID, Item Name, and Item Price columns
item_data = purchase_data[["Item ID", "Item Name", "Price"]]

# Group by Item ID and Item Name
# Calculate "Purchase Count"
item_count = item_data.groupby(["Item ID", "Item Name"]).count()["Price"].rename("Purchase Count")

# Calculate "Item Price"
item_price = item_data.groupby(["Item ID", "Item Name"]).mean()["Price"].rename("Item Price")

# Calculate "Total Purchase Value" 
total_item_purchase = item_data.groupby(["Item ID", "Item Name"]).sum()["Price"].rename("Total Purchase Value")

# Create DataFrame
item_table = pd.DataFrame({
                    "Purchase Count": item_count,
                    "Item Price": item_price,
                    "Total Purchase Value": total_item_purchase
                    })

popular_item = item_table.sort_values("Purchase Count", ascending=False)

# DataFrame Formatting
popular_item["Item Price"] = popular_item["Item Price"].map("${0:,.2f}".format)
popular_item["Total Purchase Value"] = popular_item["Total Purchase Value"].map("${0:,.2f}".format)
popular_item.head(5)
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Most%20Popular%20Items.png)

### Most Profitable Items
Identify the 5 most profitable items by total purchase value, then list (in a table):
* Item ID
* Item Name
* Purchase Count
* Item Price
* Total Purchase Value
```python
# Sorting earlier table by "Total Purchase Value"
item_table2 = item_table.sort_values("Total Purchase Value", ascending=False)

# DataFrame Formatting
item_table2["Item Price"] = item_table2["Item Price"].map("${0:,.2f}".format)
item_table2["Total Purchase Value"] = item_table2["Total Purchase Value"].map("${0:,.2f}".format)
profitable_item = item_table2[["Purchase Count", "Item Price", "Total Purchase Value"]]
profitable_item.head(5)
```
![](https://github.com/poonam-ux/Pandas-Challenge_Video-game-purchase-data/blob/main/HeroesOfPymoli/Images/Most%20Profitable%20Items.png)

## Observable Trends

* Male players are the dominating gender category with 84% count and 82.68% purchase value.
* The female count is smaller, but they spent more amount per item ( Average Purchase Price) than male players.
* Over 44% players fall under the age group “20-24” with Total Purchase Value of $ 1114 (46.8% of sales)
* “Final Critic” is the most popular and most profitable game, followed by “Oathbreaker, Last Hope of the Breaking Storm”. They both were purchased the most.
* Top spenders spent around $3.80 per game for total purchase between $15 and $19 each.
