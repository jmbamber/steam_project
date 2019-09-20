
# "There just aren't enough people playing my wizard simulator" - An analysis of Steam Sales and their impact on player numbers

**Executive Summary**

The goal was the create a model that could accurately predict how many extra players a game gets when it is discounted on Steam. This would hopefully provide insights into how developers could make the most out of discounting their games, and could be especially valuable to small, independent developers who don't have access to their own analytics teams.

In order to maximise number of datapoints, the analysis was based around the Steam Summer Sale 2019, a large sale event running from 25th June to 9th July where approximately 3/4s of the games for sale were discounted. The variable to be predicted was the difference in average player numbers between the month before and the month after the sale (June to July).

Information on which games were discounted, and by how much, was scraped from [IsThereAnyDeal.com](https://www.isthereanydeal.com), and player number information was scraped from [SteamCharts.com](https://www.steamcharts.com). Information about the games themselves was taken from the Steam Store API (originally planned to be done by myself, before finding it already done on Kaggle).

Ultimately, by far the biggest predictor of player numbers (whether absolute or change) turned out to be past player numbers, and using these values alone is already a fairly successful prediction for future player numbers. By removing some of the most popular games from the dataset (still keeping over 99% of the sample), a Random Forest model was created that, although not especially powerful, was relatively stable.

Based on the model discount amount has only a small impact on the change in player numbers after a sale. Considering the current low accuracy though, this cannot be taken as a definitive conclusion. Some further avenues of investigation and/or ways in which the data could be collected differently were also identified, which may help make a more valuable model in the future.

Below is a summary of each stage of the project, with links to the Jupyter Notebooks containing greater detail and all code used.

**Data Collection**

The original plan was to download information on games using the Steam store API. However, I found that someone had already downloaded and cleaned a version of this data and uploaded it to [Kaggle](https://www.kaggle.com/nikdavis/steam-store-games).

This dataset was cleaned by removing free games (can't be discounted) and games released in 2019 (hopefully these games would have more stable monthly player numbers than those recently released):

[Steam API Cleaning](notebooks/steam_api_data_cleaning.ipynb)

Next, on which of these games were on sale during the Summer Sale was scraped from [IsThereAnyDeal.com](https://www.isthereanydeal.com):

[ITAD Scraping](notebooks/itad_scraping.ipynb)

Finally, [SteamCharts.com](https://www.steamcharts.com) was scraped to get information on player numbers:

[Steam Charts Scraping](notebooks/steamcharts_scraping.ipynb)


**Data Cleaning and EDA**

The three separate datasets had to be combined into one, followed by a large amount of cleaning and feature engineering. This included the creation of the target variable: the difference in average player numbers between June and July for each game (uplift).

[Steam Cleaning](notebooks/steam_data_cleaning.ipynb)


Next the EDA began. Fortunately my initial hypothesis turned out to be correct, most games had increased in average player numbers between June and July (also a higher proportion than those that increased between May and June) and the increase was higher among games that had been discounted in the Summer Sale.

However the EDA also confirmed one of my concerns: the target variable wasn't at all normally distributed. It appeared somewhat logistic and was very positively skewed. Despite the top games getting a monthly average of tens of thousands of players, only around 1% get more than 1000, while a full third average at less than 1 player. Most people playing games are concentrated on only a very few of the most popular ones.

Various transformations were applied to try and bring it to a more normal distribution, including a Fisher transformation and a Yeo-Johnson, but neither were strong enough. In the end it seemed like the best approach was to use a log transformation, but this presented a new problem: How to deal with the negative numbers in my prediction variable (i.e. those games who dropped in numbers between the month). In the end I decided to try transforming all negative numbers to 0 (i.e. "no increase in players) and add a small constant (to deal with those 0s). Further decisions would be made based on initial modelling attempts.

[Steam EDA](notebooks/steam_eda.ipynb)


**Modelling**

First attempts at running a linear regression (with and without regularisation) weren't very successful, and only minor improvements were seen with other models, the best being a Random Forest Regressor.

An analysis of the results showed that the model was struggling to cope with the large amount of 0s that came out of the log transformation. Almost all of the games that had increased in player numbers were having their uplift underestimated, while there were games with no uplift being predicted as having some of the largest increases.

To try and solve this, the target variable was changed from the difference between June and July numbers, to just the June average player numbers. This resulted in a Random Forest model that seemed highly accurate, but was because the vast majority of the predictive power was coming from the player numbers in June. Recalibrating the evaluation metric to use the June figures as a baseline showed that the model was significantly weaker. 

Considering the high watermark set by the baseline, even a weak model would have some value though. Also, investigation of the residuals showed that it was just a few predictions that were very strongly off and were significantly affecting the model's accuracy. All of these predictions were found to be from games that currently had large player numbers. Imposing stricter and stricter filters on the dataset found that by including only games with current player numbers of less than 3000 (which still account for over 99% of the total dataset of games) a model could be created that, although not much more accurate, was a lot more stable to cross-validation.

[Steam Modelling](notebooks/steam_modelling_writeup) 

### **Conclusions**

**1) This is difficult to predict**

Ultimately, predicting the uplift in player numbers resulting from a large Steam Sale appears to be very difficult. Part of this is due to the state of the industry (indeed the state of all entertainment industries), that the vast majority of players are concentrated around only a very few games. This means any variable based on player numbers will have a challenging distribution that needs to be dealt with.

None of the transformations I tried applying proved particularly successful. They were either not strong enough (Fisher, Yeo-Johnson) or couldn't cope with all the values (can't log transform negative numbers). Shifting my attention to a new variable (actual player numbers) did allow for easier transformation, but didn't help with the problem that the most extreme values had the least training examples. As such, most models ended up unstable and struggled to cope when presented with such extreme values for prediction.

**2) Discounts appear to have little impact on player numbers**

Looking at the coefficients/feature importances of the models that were made, there is a clear trend: popularity features are the biggest predictors of change in player numbers (i.e. features like current/max player numbers, number of ratings etc.). The level of discount provides very little predictive power, suggesting that it therefore has little impact.

There are a few things to be aware of here though:

1) The accuracy of the models were low, so this is not a definitive claim
2) Player numbers does not mean sales. Sales may well still significantly increase due to a discount, but if you are a small indie developer who needs a larger playerbase (e.g. for a multiplayer game), you cannot expect a discount to bring in the players needed)

**3) This is a topic worth investigating further**

Despite the lack of clear success here, there are still some avenues that I believe are worth investigating further:

Firstly, trying to gather the data again. As I had to look at historical data, I ended up using the difference between month averages. While it was true that there was a larger uplift in player numbers that usual (presumably due to the sale), the uplift would have likely been somewhat watered down by the rest of the month. The next major Steam Sale is likely to occur over Halloween. Getting player numbers through either the API or scraping immediately afterwards will hopefully result in a more prominent and consistent uplift in the player numbers.

Secondly, by cheating and only trying to predict the uplift of games that had a positive change in player numbers, it was possible to create a relatively accurate regression. Attempting to identify those games that had a positive uplift vs. those that didn't, perhaps through a classification, might allow a more accurate model to be made for the actual uplift numbers. I already had a brief, unsuccessful attempt at running classifiers, but didn't try it as thoroughly as I would like.

Finally, trying some additional models might help. More robust models (e.g. Huber) may be more stable when exposed to extreme values. And Zero-inflated models may help identify those games that did not have a positive increase in player numbers.
