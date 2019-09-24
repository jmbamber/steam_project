
# "There just aren't enough people playing my wizard simulator" - An analysis of Steam Sales and their impact on player numbers

[Back to home](../../)

### **Data Cleaning and EDA**

The three separate datasets had to be combined into one, followed by a large amount of cleaning and feature engineering. This included the creation of the target variable: the difference in average player numbers between June and July for each game (uplift).

[Steam Cleaning](steam_data_cleaning.html)


Next the EDA began. Fortunately my initial hypothesis turned out to be correct, most games had increased in average player numbers between June and July (also a higher proportion than those that increased between May and June) and the increase was higher among games that had been discounted in the Summer Sale.

However the EDA also confirmed one of my concerns: the target variable wasn't at all normally distributed. It appeared somewhat logistic and was very positively skewed. Despite the top games getting a monthly average of tens of thousands of players, only around 1% get more than 1000. Most people playing games are concentrated on just a few of the most popular ones.

Various transformations were applied to try and bring it to a more normal distribution, but the best approach seemed to be using a log transformation. This presented a new problem: How to deal with the negative numbers in my prediction variable (i.e. those games who dropped in numbers between the months). I decided to start by transforming all negative numbers to 0 (i.e. "no increase in players) and add a small constant (to deal with those zeros). Further decisions would be made based on initial modelling attempts.

[Steam EDA](notebooks/steam_eda.html)

---

Next part: [Modelling](../modelling)
