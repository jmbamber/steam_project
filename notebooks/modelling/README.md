
# "There just aren't enough people playing my wizard simulator" - An analysis of Steam Sales and their impact on player numbers

[Back to home](../../)

### **Modelling**

First attempts at running a linear regression (with and without regularisation) weren't very successful, and only minor improvements were seen with other models, the best being a Random Forest Regressor.

An analysis of the results showed that the model was struggling to cope with the large amount of zeros that came out of the log transformation. Almost all of the games that had increased in player numbers were having their uplift underestimated, while there were games with no uplift being predicted as having some of the largest increases.

To try and solve this, the target variable was changed from the difference between June and July numbers, to just the June average player numbers. This resulted in a Random Forest model that seemed highly accurate, but was because the vast majority of the predictive power was coming from the player numbers in June. Recalibrating the evaluation metric to use the June figures as a baseline showed that the model was significantly weaker.

Considering the high watermark set by the baseline, even a weak model would have some value though. Also, investigation of the residuals showed that it was just a few predictions that were very strongly off and were significantly affecting the model's accuracy. All of these predictions were found to be from games that currently had large player numbers. Imposing stricter and stricter filters on the dataset found that by including only games with current player numbers of less than 3000 (which still account for over 99% of the total dataset of games) a model could be created that, although not much more accurate, was a lot more stable to cross-validation.

[Steam Modelling](steam_modelling_writeup.html)

---

Next part: [Conclusions and Next Steps](../conclusions)
