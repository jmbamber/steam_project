
# "There just aren't enough people playing my wizard simulator" - An analysis of Steam Sales and their impact on player numbers

[Back to home](../../)

### **Conclusions and Next Steps**

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
