
# "There just aren't enough people playing my wizard simulator" - An analysis of Steam Sales and their impact on player numbers

[Back to home](../../)

### **Data Collection**

The original plan was to download information on games using the Steam store API. However, I found that someone had already downloaded and cleaned a version of this data and uploaded it to [Kaggle](https://www.kaggle.com/nikdavis/steam-store-games).

This dataset was cleaned by removing free games (can't be discounted) and games released in 2019 (hopefully these games would have more stable monthly player numbers than those recently released):

[Steam API Cleaning](steam_api_data_cleaning.ipynb)

Next, on which of these games were on sale during the Summer Sale was scraped from [IsThereAnyDeal.com](https://www.isthereanydeal.com):

[ITAD Scraping](itad_scraping.ipynb)

Finally, [SteamCharts.com](https://www.steamcharts.com) was scraped to get information on player numbers:

[Steam Charts Scraping](steamcharts_scraping.ipynb)

---

Next part: [Data Cleaning and EDA](../data_cleaning_and_eda)
