---
title: "Multiple Linear Regression For Predicting Route Quality"
date: 2023-03-15
img_path: /images/Linear_Regression_Route_Quality/
---
# Introduction
Let's see how good of a model we can build to predict rock climbing route quality.

I've scraped all non-boulder routes from [Mountain Project](https://www.mountainproject.com/), amounting to **30,738 routes**, for the following locations:

Bishop Area, Black Canyon of the Gunnison, Eldorado Canyon, High Sierra, Index, Indian Creek, Joshua Tree, Lake Tahoe, Leavenworth, Mammoth Lakes, Moab Area, New River Gorge, Red River Gorge, Red Rock, Rifle, Rumney, Smith Rock, Squamish, Ten Sleep, The Needles (CA), Tuolomne, Vedauwoo, Yosemite, Zion.

We will be using a multiple linear regression model. While we care about our prediction accuracy (R-Squared), we will be prioritizing inferential power. Though we will attempt and qualify more complex implimentation, we may opt to not include such measures so to maintain a simple interpretability of the model.

# Selecting a Response Variable
Creating a meaningful summary metric for quality given a series of votes is not an uncommon problem to have. The first thing that typically comes to mind is to use a simple arithmetic mean, which isn't a bad idea, but it does not do a very good job of accounting for a difference in number of votes. An item with 1x5star review will be ranked higher than an item with 999x5star reviews and 1x1star review. A statisticians perspective says to consider your pool of votes to be a sampled subset of your true population, which can be used to estimate the true population value. It depends, like most things in statistics, on the number of samples. It is important to note that we are not saying items with low vote counts are inherently worse, but we are less certain of their true state. By using this concept we build a list that is our proposed "best guess" estimate of the ranking.

Before we select a model, we should be aware of what information is available to us. I have access *only* to the average rating and number of ratings. I do not have access to the individual ratings.

The [Wilson Score Interval](https://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval) is a favorite among binomial ranking systems such as Reddit's upvote/downvote system. It can be [modified](https://stackoverflow.com/questions/19613023/wilsons-confidence-interval-for-5-star-rating) to work with more than two options. Unfortunately because my data set does not have the individual rankings, I cannot make use of this method. Besides, there has to be a more relevant method for a star ranking system like we have here.

Companies like Amazon and IMBD surely care about ranking items with a star system. It is possible they are using a more complicated method overall, but the typical basis seems to be the Bayesian Average [Ref](https://www.algolia.com/doc/guides/managing-results/must-do/custom-ranking/how-to/bayesian-average/) [Ref](https://arpitbhayani.me/blogs/bayesian-average). In short, it assumes the default rating for an item is the mean of the entire set, this is **'m'**. Then as votes are added that value is adjusted accordingly. How many votes are required to be confident of the true value is **'C'**, which is based on the number of votes. This is more nuanced to select. Some people use the mean, some use the 25th percentile. We have the required components to construct this new metric.

![Bayes Average](Bayes.png)
*Bayes Average Equation [[Source]](https://www.algolia.com/doc/guides/managing-results/must-do/custom-ranking/how-to/bayesian-average/)*

We just need to select 'C'. A percentile plot shows our rather skewed distribution nicely.
{% include linear_regression_routes/Num_Ratings_Perc.html %}

This is where we make a rather large decision. A common standard is 25th percentile, which here is 2. However, I simply do not believe that a route rating with less than 5 votes is anywhere near accurate. Additionally, we will be using metrics calculated from additional user submitted data for each route, and routes with low rating votes are likely to have minimal or even missing data for these metrics. If we remove all entries with <5 votes, we will be removing **44%!* of the data. This is a huge hit. It will also make our model worse at predicting these lesser known routes. We will however, be minimizing the amount of unconfident noisy data in our model. To keep our model simple, it is a good idea to limit our scope and focus on items we have meaningful data for. Further attunements to that end can come later.

Removing these values and taking C to be the 25th percentile yields a value of 9. This is reasonable.

Calculating the Bayesian Stars is very simple.

```python
m = df['Avg Stars'].mean()
c = df['Num Star Ratings'].quantile(0.25)
df['Bayesian Stars'] = ((df['Avg Stars'] * df['Num Star Ratings']) + (c*m)) / (df['Num Star Ratings'] + c)
```
Let's check out what this transformation has done to our rating distribution.
{% include linear_regression_routes/Avg_Vs_Baye_Hist.html %}

So we removed all routes with missing "Avg Stars" and "Num Star Ratings". We also removed all routes with <5 "Num Star Ratings". This reduced our data set from 30,738 to 16,202, a reduction of 47%.

A typical shorthand explanation of the star system for climbs is as follows:
* 0-1 : likely junk, not worth your time.
* 1-2 : A fine climb, worth your time but likely not remarkable.
* 2-3 : An area classic, likely very good.
* 3-4 : An all time classic, likely excellent.

# Initial EDA of Predictors
Start by checking the head and seeing what we are working with.
{% include linear_regression_routes/Table_Head.html %}

## Predictor Description

**Route ID** - Int. Primary key. Unique. Extracted from URL.

**Route** - String. Name of route. high cardinality but not necessarily unique. Likely not a particularly useful predictor, but an important human-interpretable reference for talking about any specific entry.

**Location** - String. Series of sub-locations listed from from "leaf" to "root". Useful to create categorical groupings of entries in close proximity.

**Area Latitude** - Float. Latitude of terminal sub-location. Useful for plotting geo-spatial data. Likely high correlation with leaf of location string.

**Area Longitude** - Float. Longitude of terminal sub-location. Same comment as for latitude.

**Route Type** - Binary Categorical String. Style of climbing protection offered on route. Either "Trad" or "Sport".

**Rating** - Categorical String. Designates consensus difficulty of climb. Uses [Yosemite Decimal System](https://every-last-rock.com/yosemite-decimal-system/). Designates consensus difficulty of climb.

**Risk** - Categorical String. Designates lack of protection on route, and consequence for a fall. Uses movie theater style ratings. If a fall occurs, "PG13" suggests injury, "R" suggests serious injury or maiming, "X" suggests death.

**Length** - Float. Length of route measured in feet.

**Pitches** - Int. Number of [pitches](https://climbingready.com/pitches-rock-climbing/) the route is typically segmented into. Likely high correlation with "Length".

**SP/MP** - Binary Categorical String. Designates whether the climb is a single pitch ("SP"), or multiple pitches ("MP"). Likely high correlation with "Length" and "Pitches". An additional useful predictor as the difference in style and surrounding predictor data quality vary drastically between the two groups.

**Num Ticks** - Int. Number of user submitted ticks. A tick is a record of a climbers attempt on a route, including the date, style, success outcome, and comment. A measure of popularity and data integrity of metrics derived from tick data.

**Num Tickers** - Int. Number of users who submitted ticks. Since a user can submit multiple ticks for the same route, this provides an indirect measure of how many people return to a route. Likely high correlation with "Num Ticks"

**Lead Ratio** - Float. Ratio of ticks that claim to have [led](https://www.rei.com/learn/expert-advice/lead-climbing-basics.html) the route as opposed to top-rope or follow. A measure of many potential things: "Scary-ness" of route, difficulty to set up top-rope, inconvenience of following etc.

**OS Ratio** - Float. Ratio of ticks that claim to [onsighted](https://www.sportrock.com/post/on-sight-vs-flash-vs-redpoint#:~:text=This%20is%20when%20climbers%2C%20without,route%20on%20their%20first%20attempt.) the route as opposed to a fell/hung or redpoint. A secondary measure of difficulty, or "tricky-ness".

**Repeat Sender Ratio** - Float. Average number of times a climber who has redpointed or onsighted the route has returned for another clean send. this includes the first redpoint so there is a minimum value of 1. A measure of popularity.

**Mean Attempt To RP** - Float. Average number of attempts to achieve a redpoint. This includes the redpoint attempt itself so there is a minimum value of 1. A measure of difficulty to "work" a route clean.

## Pairplot
I've omitted all categorical predictors apart from rating for visual clarity.
![PairPlot](PairPlot.png)

# Data Cleaning

## Route ID
This is a primary key. Every row has a unique value and there are no null values.

If we remove some columns for model training and want to join them back later for analysis we will want this. Let's set it to our index.

## Route
There are no null values here. For fun, here is a list of route names that repeat more than 3 times in the data set. If you're developing a new route, maybe pick a different name. Redacted routes likely had offensive names. Unknown and Unnamed are effectively null values, but they do mean slightly different things.
<details>
<summary><b>Name Count Table</b></summary>
{% include linear_regression_routes/Name_Count_Table.html %}
</details>


This is useful for meaningful analysis once our model is trained, but is not useful for training the model. We will drop this column for now.

## Location
There are no null values.

This column is useful to group nearby climbs together. It is currently a string, but it can easily be turned into a list. Each step in the sub-location tree is meaningful information. For example the fact that a climb is located in California vs. Utah is useful information. As is the fact that a climb exists in the upper or lower valley of Yosemite specifically. It is easy to imagine that a particular wall or area is high or low quality. This information is particularly useful because it may indirectly measure qualities that we otherwise do not have a good measure of, for example rock quality or accessability.

In general though, this feature is superceded by longitude and latitude. Lon/Lat provide extremely similar information in a simpler to understand and compute form factor. For this reason, we will drop this location column to prevent unwanted collinearity.

## Area Latitude and Longitude
There are no null values. No values look to be illogical.

The locations are restricted to those areas I've selected when constructing the original data set.

{% include linear_regression_routes/Loc_Map.html %}

## Route Type
There are no null values.

In this dataset we are only considering roped climbing, so trad and sport. This dataset is 53% trad and 47% sport, a pretty even proportion.

Because there are only two groups, we can easily binary encode this data.

## Rating
I've got 38 null rating values. These look to be alpine ice/snow routes with no traditional route grade. These can soundly be dropped as they are significantly out of character to the bulk of the dataset.

There are two subtle variations on Yosemite Decimal System. One uses 4 letters, \[a,b,c,d] to partition number grades 5.10 and above. The other uses three signs \[-, (none), +] to partition number grades 5.10 and above. Any collection of data is going to have a mix of these two systems.

To homogenize to the sign grade system, letter grades are bundled. So 5.10- might be composed of 5.10a, 5.10 composed of 5.10b and 5.10c, and 5.10+ composed of 5.10d. No rounding occurs but we are biasing the groups to the middle (5.10) grade, and we are losing some resolution.

To homogenize to the (more popular) letter grade system, you need to round each sign grade up or down. This can be done by hand if someone has climbed every route, which is unlikely. You could round all values down, all values up, or take two random half samples and round one half up and one half down. The half rounding technique is preferred since it introduces minimal bias. The difference between one grade is not such a massive concern.

82% of the ratings are letter type. For this reason, and to avoid the bias and loss of resolution of the sign system leads us to select homogenizing to the letter grade system. 

{% include linear_regression_routes/Rating_Hist.html %}

There are less hard routes. This is in large part because developing a harder route takes more effort, and less people are capable of climbing at that grade.

It is also interesting to point out the discrepency on the transition from 'd' to 'a' (ex: 5.10d to 5.11a). It is commonly believed that climbers prefer an 'a' climb over a 'd' climb, primarily for ego related reasons. As such it is often hypothesized that establishing routes at an 'a' grade is preferred if it is close, so that more people will want to climb the route. It is interesting to see that this looks to be the case until the grades are so hard (>5.13d) that ego chasing probably isn't necessary.

## Risk
Most routes (92%) have a null risk value. There is however meaningful information in that null value, namely it designates that the route is overall safe. This can be represented in character with the rest of the scale in the movie theater analogy as a value of "G".

These values can then be ordinally encoded as follows: {'G':1, 'PG13':2, 'R':3, 'X':4}

## Length
This feature is a bigger one to deal with. It is certainly useful and interesting information, but it has the worst integrity of any of the features. We are going to have to do some real work to get it into good shape.
### Value Anomaly Handling
We start as we always do with a histogram. I've omitted the highest 16 outlier values for a better view.
{% include linear_regression_routes/Length_Hist.html %}

Simply inspecting the highest and lowest 30 values tells us a lot.

On the low end we have 4 routes with negative values, which are reasonable values if turned positive, so that is what we will do.

There are 6 entries with less than 10. Upon inspection it looks like whoever input the data, put the pitch count in this field instead of length. We will assign these NaN. There are a fair bit of genuinely short routes, we'll keep these.

As for high values, we are looking out for two things.

The first is outliers which were likely erroneously reported. This may be because the length is too high given the number of pitches, or the number

The second is ridge traverses. Ridge traverse length values do not mesh well with our intended use of route length. Whereas a typical route length defines the vertical distance gained during the climb, a ridge traverse counts horizontal length as well. While this is a fine metric for comparing ridge traverses, these values tend to be much higher than normal length values, ie. in the 10,000's vs. 100's. It is best to omit these routes in order to improve our dataset, at the expense of losing inference power related to those routes. In this case, it is worth it.

There is no clear cut tag that designates a ridge traverse. Sorting by Length/Pitches turns out to be an excellent way to identify them. This has the added bonus of being a good method to find illogical outliers of length given various pitch values. For context, a typical pitch maxes out around 120 feet. It may be as high as 130ft if it requires an 80m rope. A pitch may be even longer if the pitch is part of a multipitch route, up to around 240ft. We'll add a reasonable ~10% buffer and cut it off at 260ft.

This yields 187 results, 135 of which are immediately recognized as ridge traverses due to their alpine tag. This leaves 52 routes of unknown status. Are they ridge traverses not in an alpine environment? Is the length wrong? Is the number of pitches wrong? We can go one by one and decide, but a systematic solution is likely to be incorrect. With so few values we will not miss out on too much information, so let's just drop them.

### Missing Value Handling
Length has a 10% missing value rate or ~1,200 entries.

The most common simple way to impute likely values is with some summary statistic (mean, median, geometric mean) computed from nearby routes. We can use the Location datapoint to simply group values together. It is important that we control for how many pitches are in the route. We do not want to base a single pitch length off of multipitch values. So we are grouping routes with the same number of pitches in the same area.

What if there are no comparable routes? We could subsequently search within one greater tier of location. Let's keep things simple for now and say if there are <10 comparable routes, we will use median for that number of pitches from the full dataset.

For the local imputed length, we will be using the geometric mean as a middle ground between median and arithmetic mean. Median is better used for a larger sample size in the >35 range. Since only 3/1205 groups are >25 this is not a very good choice. We use geometric mean as it punishes extreme values better than arithmetic mean.

We can see how the length rises with pitch count. It is clear that as number of entries goes down, the noise rises.
{% include linear_regression_routes/Global_Med_Length_Plot.html %}

It may be smart to bin all routes with greater than say 15 pitches into one group. There is a particularly prickly outcome in our current setup where if we have a NaN value with 34 pitches and no comparable local routes, then we don't even have a global value to fill it with! This may also be easily solved by selecting for a nearby "pitches" value if it is not available.

I'm curious how length/pitch evolves as pitches goes up. An interesting note is that at 3-5 pitches there is a clear bias to just round to 100ft/pitch.
{% include linear_regression_routes/Global_Med_Length_Pitch_Plot.html %}

Doing this rather complex method of imputation requires some Pandas-fu. Particularly because we are imputing via a different method based on factors given by groupby operations. Obtaining those index subsets based on groupby output is not straight forward. It is worth the extra effort however because we are doing our best to maximize the valid information in this imputation.

## Pitches
There are no null values.

There are 2 entries with a illogical values, namely a negative pitch count. One may be a reasonable positive value at 5 but the other would be 56. We'll drop these entries to be safe.

The following histogram ignores Pitches = 1 for visibility as most routes are single pitch.
{% include linear_regression_routes/Pitches_Hist.html %}

This histogram is sensible as there are simply less tall walls, and developing longer routes requires more effort and typically has a smaller audience.

Pitches and Length have a correlation of 0.96. If we calculate VIF, we see that Length and Pitches have a value of 11.76 and 12.10 respectively. This is well above a value of 5. It is a good idea to drop this column to remove this collinearity.

## SP/MP
There are no null values.

87% of the entries are single pitch, and 13% are multi pitch.

Because there are only two groups, we can easily binary encode this data.

## Num Ticks
There are a handful of null values which need to be assigned a value of 0.

Number of ticks represents how many user ticks were submitted for the climb. This represents what you would expect, a measure of popularity. Importantly, it also represents the "power" of many other metrics that are calculated from the tick data. If number of ticks is low, then the confidence that our calculated metric are an accurate representation is low.

{% include linear_regression_routes/NumTicks_Hist.html %}

## Num Tickers
Num Ticks and Num Tickers have a correlation of 1.00. The VIF for Num Ticks and Num Tickers is 107.52 and 107.62 respectively. This is well above a value of 5. We will drop this column.

## Lead Ratio
0.8% of entries are null. We will perform a simple imputation with the total median value of 0.735. The high count for 0.00 and 1.00 are due to low number of ticks for that entry and is likely based on only a single valid data point.

{% include linear_regression_routes/LeadRatio_Hist.html %}

## OS Ratio
3.7% of entries are null. We will perform a simple imputation with the total median value of 0.693. The high count for 0.00 and 1.00 are due to low number of ticks for that entry and is likely based on only a single valid data point.

{% include linear_regression_routes/OSRatio_Hist.html %}

## Repeat Sender Ratio
5.4% of entries are null. We will perform a simple imputation using a value of 1.0 instead of any average or median. The high count for 1.00 is due to low number of ticks for that entry and is likely based on only a single valid data point.

I think it is fun to note that many of the routes with a high value here are typically routes in the Bishop Area with a low number of ticks where Marty Lewis has maintained an impeccable record of his own ascents.

{% include linear_regression_routes/RepeatSender_Hist.html %}

The high count of "1" makes this plot hard to interpret, let's try plotting with those values ignored.

{% include linear_regression_routes/RepeatSender_1omit_Hist.html %}

## Mean Attempts to RP
33.2% of entries are null. We will perform a simple imputation with the total median value of 1.17. The high count for 1.00 is due to low number of ticks for that entry and is likely based on only a single valid data point.

{% include linear_regression_routes/MeanAttemptsToRP_Hist.html %}

The high count of "1" makes this plot hard to interpret, let's try plotting with those values ignored.

{% include linear_regression_routes/MeanAttemptsToRP_1omit_Hist.html %}

# Checking For Predictor Collinearity
There are two methods to determine collinearity. The first is a simple correlation heatmap.
![Corr Heatmap](Corr_Heatmap.png)

The second is a Variance Inflation Factor (VIF) table.
{% include linear_regression_routes/VIF_Table.html %}
If you read the previous section you know we were already looking at Pitches and Num Tickers to remove because we presumed they had high collinearity. This is certainly the case since both the correlation and VIF are very high. If we remove these values we get a much more tame correlation table. VIF factors change only very slightly, and all remain below the typical val=5 cutoff. This includes Rating and OS Ratio which has a reasonably high collinearity but not enough to be concerning.
![Corr Heatmap Amended](Corr_Heatmap_Amended.png)

Some fun additional observations:
* Trad climbs are likely to be longer, riskier, led less, and onsighted more. Makes sense given the fact that you are placing your own, potentially less dependable, gear.
* Number of ticks decreases as the rating gets harder. This is because less people can climb the harder grades.
* OS Ratio drops heavily as rating increases, this makes sense as it is harder to onsight more difficult routes.
* Mean attempts to rp increases as num ticks increases. This is sensible as someone is likely more dedicated to obtain a clean send on a more popular route.
* A climb with a higher lead ratio is likely harder, longer and less risky. As ratings get harder, most people opt for a lead attempt for the "send".

# Initial Linear Fit
We will start by running a very basic multiple linear fit. Every variable is included, all just first order. We are not yet considering transformations or interactions.

## Initial Fit Statistics
{% include linear_regression_routes/LM_Init_Summary_Table1.html %}
{% include linear_regression_routes/LM_Init_Summary_Table2.html %}

## Does A Relationship Exist For Any Predictor?
With an **F-statistic = 939.2** and a **P(F-statistic) = 0.00**, we can be certain that there exists some relationship in this model.

## What is the Overall Strength?
**Our R-squared = 0.431**. This is not very high, but it is not abysmal.

We also have an **RMSE of 0.357**, which is right on 9% of the range of our response variable range, and ~13% of our mean.

## Which Predictors Are Associated With the Response?
All predictors have a near zero P-Value, except for OS Ratio. Confidence intervals all look quite good except again for OS Ratio which includes a correlation value of 0 in it's confidence interval. This may well be due to the collinearity with Rating, and we should consider removing it from our predictors.

# Initial Fit Diagnostics
We need to check some diagnostics to see if some of our important assumptions are being met.

## Error Normality
Error looks solidly normal via the Residual QQ plot. Our Prob(Omnibus) and Prob(Jarque-Bera) being near zero also provides us solid confidence that our error is normal.
![QQ Residual](QQ_Residual.png)

## Non-Linear Residuals
Our linearity of residuals is ok. The only concerning element is the over-estimation at the high end of the fitted values greater than about 4. Perhaps with some outlier removal and some model tuning we can get this to a better place.
![Resid Vs. Fitted Val](Resid_FittedVal.png)

## Homoscedaity
Our P-Value for our **Breusch-Pagan Lagrange Multiplier Test is near zero (Less than 1E-182)**. We can be confident of homoscedaity.

## Correlation of Error Terms
Our **Durbin Watson Test Value is 1.584**, which is 2.0>x>1.0 putting us in a good spot. There is no obvious correlation of error terms in the Residuals Vs. Fitted Values Plot.

# Outlier Handling
We use a combination of an influence plot, cook's distance, leverage plot and studentized residuals to identify outliers.
![Influence Plot](Influence_Plot.png)

If we take a look at the entries that have high cook's distance, we see that they typically have outliers in Mean Attempts to RP and Repeat Sender Ratio. These values are problematic when a single user ticks many attempts or redpoints, and no one else does to bring the average to a reasonable value. On further inspection it again seems to be Marty Lewis throwing a wrench into things with his meticulous ticking on otherwise niche routes.

If we use 4/n as our critical Cook's Distance we get 678 entries with concerning values. Another standard is 3*Cooks_Mean which results in 399 concerning values. I believe ~400 entries out of ~16,000 is a reasonable number of outliers given my understanding of the data. I am going to remove these outliers.

With the outliers removed, few of the previously spoken of metrics change much. Notably the **R-Squared rises to 0.477**.

Now for a new influence plot, which looks much less concerning.
![Influence Plot Outliers Removed](Influence_Plot_OutRem.png)

# Interactions
We should consider interaction terms between predictors. The following have a P-Value worth looking at.
<details>
<summary><b>Interaction P-Values</b></summary>
{% include linear_regression_routes/Interaction_Table.html %}
</details>

Implementing all of these interactions brings our **R-Squared value to 0.567**.

To allow for ease of interpretability, we will ignore interactions for now.

# Non-Linear Transformations on Predictors
Typically we consider residual plots for each individual predictor, and if the plot shows a non-linear trend, we can select a transformation to improve the fit. The partial regression plots do not reveal any clear transformation.

![Partial Regression Grid](Partial_Regression_Grid.png)

I attempted some intuitive transformations such as a log10 on length and num ticks, but with no benefit.

# What is the Strength of Association?
We can gauge association by taking a look at the coefficients. Note that these are the coefficients **given that all other predictors remain constant**.
{% include linear_regression_routes/Coef_Table.html %}
We can make the following rough conclusions:
* A climb being multipitch instead of single pitch raises the stars by ~0.1144. This suggests that all things being equal, climbers enjoy a climb more if it is a multi pitch. This may be a side effect of the fact that a multi-pitch has more opportunities to "wow" the climber. Often times a multi pitch route needs only a few good pitches to convince someone of its quality.
* If the length is missing then this reduces the stars by ~0.047. Routes with no length are more likely to be climbs that fewer people care about, and therefore are of lower quality.
* Risk has a relatively light coefficient. For each increase in risk intensity, the stars reduce by 0.0205. This suggests that climbers prefer a climb that is safe with good protection. It seems that climbers that particularly enjoy run-out or dangerous routes are likely a minority.
* The difference between an onsight ratio of 0 and 1 is a reduction in stars by 0.0197. This is actually counter to what I would have thought. I had predicted that routes that are easier to onsight are more likely to be accepted as a classic. What I think confounds this effect is that onsight ratio is correlated with rating, in that harder routes are harder to onsight. Since rating holds a higher coefficient, this value get's dragged along with it.
* The area longitude coefficient suggests that as you move East, the stars decrease by 0.0011 per degree. Most people agree that there exists quality climbing at a higher volume in the west versus the east. This is likely biased by my west coast centric selection of areas.
* For every foot of length, the stars increase by 0.0002. This means roughly for every additional average 100ft multi-pitch pitch, the stars increase by 0.02. This lines up well with a common perception that epic ultra-long routes are of high quality.
* For every additional tick, the stars increase by 0.004. Since tick counts can reach the 100's quite easily and even to 1000's for very popular routes, this predictor is without a doubt one of the most important. 
* The area latitude coefficient suggests that as you move North, the stars increase by 0.0075 per degree. This suggests that climbing is more quality up north. A possible explanation I can think of is that there is more granite up north. There is a typical perception that granite is a preferred rock type.
* For each increase in rating, the stars increase by 0.065. Consider then 0.26 stars between each number grade 5.10 and up. A likely explanation here is that harder routes simply offer higher quality climbing, this is a common sentiment in the vice versa at lower grades where some may shrug that "the climb didn't offer much interesting'. Additionally, a climber must try much harder on a difficult climb, so likely becomes more attached.
* For each additional mean attempt to redpoint, the stars increase by 0.0958. I would attribute it to the same "attachment" effect as for rating. A high mean attempt to redpoint suggests a "tricky" route, providing an even deeper attachment and engagement to the climber.
* As for repeat sender ratio, if the average redpointer returns for 1 additional redpoints compared to the average redpointer not returning, the stars increase by 0.1634. This is sensible as this metric measures "fan favorite" status.
* The difference between a lead ratio of 0 and 1 is an increase in 0.1634 stars. This suggests that climbs that most people lead are higher quality. I understand that lead ratio typically increases as rating increases, and so believe this to be dragged by the heavy rating effect in much the same way that OS Ratio is.
* A climb being trad instead of sport raises the stars by ~0.1901. This suggests that all things being equal, climbers enjoy a climb more if it goes on natural gear.

So a quality climb is likely a difficult yet safe long trad multi-pitch in the northern section of the west coast. Sounds like a modern Yosemite route to me.

# A Simpler More Focused Model on Just Single Pitch Entries
## Model Changes
There are major issues with data when it comes to multi-pitch entries. Since there is now way to add pitch by pitch resolution for any of the metrics, a lot of important information is lost. We may find a more consistent and reasonable model if we focus on single pitch entries.

This looks to be at least partially true as an identical model on the pruned data set accomplishes an R-Squared of 0.467 compared to the previous 0.431. The impact is less than I would have thought.

Additionally, I played around with removing various predictors to see how large of an impact they had on our R-Squared. I pruned predictors with minimal impact, and was left with just: Rating, Num Ticks, Length, Is Trad, and Risk. Metrics such as Mean Attempts to RP and Repeat Sender Ratio are problematic in that they are susceptible to outliers if number of ticks is low. I think I need to revamp these metrics to account for such effects to lower the noise. Metrics such as OS Ratio and Lead Ratio are less susceptible to such issues, but their inclusion simply does not improve our model by much.

I experimented with interactions and transformations on these predictors and found none with meaningful with promise. All diagnostics are good. We accomplish an R-Squared of 0.455.

The coefficients in comparison are as follows:
* Rating unchanged at 0.065
* Num Ticks unchanged at 0.0004
* Length 0.0002 to 0.0004
* Is Trad 0.1901 to 0.1294
* Risk -0.0205 to -0.0759

So Length becomes twice as impactful, Is Trad becomes ~35% less impactful, and Risk becomes nearly four times as impactful.

Removing about 90 particularly bad outliers brings our R-Squared to 0.471.

Our residuals look a big better too.
![QQ_Residual_SP](QQ_Residual_SP.png)
![Resid Vs. Fitted Val SP](Resid_FittedVal_SP.png)

## Partial Regression Plots
We can inspect the relationship of a predictor on the response variable *conditional* on other predictors with partial regression plots.

![Partial Regression Grid SP](Partial_Regression_Grid_SP.png)

Much of this is as expected. It is interesting to note that it seems both num ticks and length tend to over estimate at high values. This is likely the source of our poor overall residual performance at high bayesian star values.

## Regression Diagnostic Plots
We can inspect the behaviour of these predictors further with regression diagnostic plots. I will use num ticks as an example.

![Num_Ticks_Reg_Diag](Num_Ticks_Reg_Diag.png)

One thing that sticks out to me is that the variance reduces significantly as number of ticks increases.

I inspected the regression diagnostic plots for the other predictors and did not find any smoking guns.

# Major Issues With Model
I am pleased that we pass all of our typical diagnostic criterion, and can feel confident making the critical assumptions required for this model. However, the most obvious issue with this model is that the R-Squared value is just not great. This model is insufficient in explaining the variance in the response variable.

We likely do not have sufficient meaningful predictors to account for the variance. Some ideas are:
* Rock type.
* Sun exposure.
* Difficulty attributes, ie. sustained or cruxy.
* Style attributes, ie. techy, overhung, slab.
* Feature attributes, ie. flake, handcrack, slopers.
* Unique-ness or novelty.
* Aesthetic qualities.
* Accessibility, approach etc.
* Variation of movement.
* Proximity to other nearby quality routes.

Then of course there is the likely case that a multiple linear regression model is not the best model for the job! This is all but guaranteed. An example of a weakness this model has is that it cannot account for an XOR condition. For example if quality routes were in three of 4 opposite corners of the map, we would end up with a particularly poor fit. There is a slew of different models we can try, and we will!