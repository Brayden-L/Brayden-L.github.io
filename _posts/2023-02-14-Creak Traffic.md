---
title: "Managing Climber Impact: Identifying High Traffic Routes at Indian Creek"
date: 2023-02-27
img_path: /images/creek_traffic/
---
This analysis utilizes my climbing analytic tool Giza, check it out!

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://giza-levyb.streamlit.app/)
# Introduction
As climbing becomes more popular, conservation becomes more important. These efforts are critical in reducing impact on the natural resources of rock climbing features, to protect access, and to preserve the "primitive character" of outdoor recreation. If the community embraces self-policing of agreed upon values, then more restrictive enforcement can be avoided. This concept is already well ingrained in the climbing community, as seen by the proliferation of organizations such as The [**Access Fund**](https://www.accessfund.org/) and [**Friends of Indian Creek**](https://friendsofindiancreek.wordpress.com/). Failure to adhere to acceptable behavior can give rise to controversial actions by official land managers such as Yosemite's recent move to [**permit big wall climbs**](https://www.accessfund.org/news-and-events/news/yosemite-wilderness-climbing-permits-what-you-need-to-know). In short, it is up to the community.

Indian Creek is managed by the BLM, who is known for a comparatively hands off approach compared to other government land managers. There is a huge amount of Native American Ute & Pueblo rock art and dwelling ruins, much of it [**uncatalogued and unprotected**](https://www.sltrib.com/news/2021/12/20/how-do-you-keep-climbers/). The land itself is delicate, with sensitive vegetation and cryptobiotic soil. Most importantly, the area has gained much [**popularity**](https://d1w9vyym276tvm.cloudfront.net/assets/AF_IC_2021_Sum_Report.pdf) in recent years. With minimal government oversight, much to protect, and increasing land use, this revered area is particularly well suited for self-policing conservation efforts.

# Purpose
The area of conservation that this report focuses on is protecting the literal resource of the climbs themselves. Admittedly, this is a climber-centric concern, but an important one none-the-less as the community should strive to preserve these natural resources for future generations of climbers.

![Old IHC](IHC.png)
*Incredible Hand Crack: crisp in 1988 and worn down in 2013 [[Source 1](https://mountainproject.com/assets/photos/climb/107061635_medium_1494162775.jpg?cache=1665108869)] [[Source 2](https://mountainproject.com/assets/photos/climb/108120064_medium_1494264171.jpg?cache=1665713398)]*

The sandstone of Indian Creek, while more robust that many other sandstones, is still susceptible to wear and tear. This manifests mostly in terms of abrasion as sharp corners are rounded, cracks are widened, cam lobes dig divots, ropes dig grooves, and dark varnish is rubbed down to reveal the softer light rock behind. These impacts are often referred to as being "loved to death".

![Route Damage](Route_Damage.png)
*Examples of corner rounding, cam divots, and rope grooves [[Source 1](https://mountainproject.com/assets/photos/climb/115323483_large_1537137503.jpg?cache=1675089281)] [[Source 2](https://mountainproject.com/assets/photos/climb/106082599_medium_1558472626.jpg?cache=1634401367)]*

These actions are not just negligence of leave-no-trace principles, but can change the very character of the climb by widening the crack and altering the difficulty. This is not to say that climbing these routes should be overtly disallowed, but use should be balanced with a respect for the resource.

This is where things get tricky. What constitutes a worthy use of the resource depends on who you ask. This is as difficult of a social issue as any other resource problem. Ultimately, the point is not to stop anyone from climbing anything, but to push the community to consider their impact and self-police their behavior to adhere to a communal ethic that protects our climbing resources. **The desired outcome of this writeup is a notice of some sort on high traffic climbs, similar to those seen for peregrine falcons or wet rock. This analysis will construct a priority list of climbs that are most apt to receive such a notice.**

# A Note on Opinions of Style
If you are a climber you know that perception of “ethic” and “style” varies drastically from person to person. Debates on what is considered acceptable or worthy can rage as hot as the campfires that host them. This is particularly prickly when talking about responsible land use and self-policing. In order to make explicit quantifiable conclusions, it is necessary to draw a line in the sand. I outline these “axioms” in order to provide a fun data analysis challenge that extends further than simply ranking by number of ticks. I would be the first to tell you that they lack the complexity to most responsibly capture an inclusive and fair foundation.

# Metrics of Interest
With all of the above considered, I propose two minimally controversial foundational statements:
* Each individual has an equal right to the resource.
* A lead attempt without weighting the rope is the goal.

From this we can construct some concepts of what constitutes "responsible" use of a high traffic resource. This is more controversial.
* A climber should not repeatedly top rope high traffic climbs.
* A climber should not repeat high traffic climbs once a clean lead attempt is achieved.

With these basic concepts set, we know that we are looking for climbs that have a:
* High number of ticks, or notable increase in the number of ticks over time.
* High ratio of top rope attempts vs. lead attempts despite the climb being safe.
* High number of climbers returning to the climb after a clean lead send.

# The Dataset
This dataset was created from the publicly available ticks for each climb on [**Mountain Project**](https://www.mountainproject.com/). There are **1541 routes** in the Indian Creek area as of 1/31/2023. All metrics were calculated with Python Pandas.

## Dataset Incompleteness
It is important to note that this dataset represents a drastically small subsection of the actual use of the climb.

Most importantly, far from everyone who climbs a route will tick it on Mountain Project. Even then, the ticks data is of questionable quality.
* Some people may tick some but not all attempts.
* People may provide false information.
* Some users opt to keep their ticks private which make for unusable entries.

However, this is the most exhaustive dataset available for the area. It is useful as a sizeable sample of the total population.

## Dataset Bias
Given that this data is self reported, it falls prey to common sampling bias of self reported data. It:
* Over-represents experienced climbers who are ardent about recording their activities and under-represents climbers who have only a light interest in the sport.
* Over-represents favorable results like successful onsights, under-represents unfavorable results like fell/hungs on easier climbs.

# Analysis
This analysis will begin by investigating a handful of metrics, and selecting a subset of outlier routes within that particular metric. Afterwards, a combination metric can be formulated to identify additional routes that did not qualify from individual metrics, but in combination are notable, as well as provide overall rank for the final list.
## Number of Ticks
Number of ticks provides a good starting point by which to judge a route. It is a simple representation of magnitude of use. Furthermore, it represents the number of entries for which the other metrics are based on. If the number of ticks is low, then the climb is likely not often climbed. With too few entries, we cannot be confident that our additional metrics are accurate representations.

From a basic histogram, we quickly get a good idea of the disparity of traffic amongst the route population.
{% include creak_traffic/NumTicks_Hist.html %}
With such extreme outliers, this is better represented as a percentile plot.
{% include creak_traffic/NumTicks_Perc.html %}

We can see from this percentile plot just how many routes have so few ticks. **30% of climbs have three or less ticks, and 50% of climbs have a dozen or less ticks!** This gives a hint as to how concentrated use might be with so many documented routes left untouched.

### Highly Trafficked Routes
It is intuitive that a small share of routes constitute much of the traffic. The [**80/20**](https://en.wikipedia.org/wiki/Pareto_principle) Rule suggests that 80% of consequences come from 20% of causes known as the vital few. In this context that would mean that 80% of traffic comes from 20% of routes. Let’s see if that is true.

{% include creak_traffic/NumTicks_PercResp.html %}

It is almost serendipitous that **20% of climbs are responsible for about 83% of traffic.** This comes out to about 300 climbs.

Furthermore, just **30 routes are responsible for 30% of traffic.**

{% include creak_traffic/NumTicks_NMost.html %}
{% include creak_traffic/NumTicks_Top30.html %}

Anyone who has climbed at Indian Creek probably recognize the majority of these names, and anyone can tell you that the wear is noticeable on most of them. These routes make up a solid base for our priority list.

<details open>
<summary><b>Number of Ticks Table</b></summary>
{% include creak_traffic/NumTicks_Table.html %}
</details>
<br><br>
### Highly Trafficked Walls
If community outreach is to be done in person, then focusing effort on high traffic crags is preferable. We can simply sum all  For the most part it is not very surprising. It certainly cements the reputation of Supercrack / Donnelly / Battle of the Bulge as a powerhouse valley.

{% include creak_traffic/NumTicks_Walls.html %}

It is even better to normalize by the amount of routes available at the wall. This list is a much more accurate depiction of crowdedness. Notably, it ranks the Six Shooter towers very high, as most people are only there to climb the single classic on the formation. It also ranks some notorious favorite walls like Cat and Reservoir quite low, as they have plentiful routes to handle the traffic. If you climb often in the Creek, you'll probably find this plot largely accurate.

{% include creak_traffic/NumTicksNorm_Walls.html %}


## Settling on a Minimum Number of Ticks
All other traffic metrics are “deeper” investigations into tick trends, and depend on the number of ticks to obtain data validity. With too few ticks, the additional metrics become meaningless. This, for example, can be manifested in a high number of perfect 0’s or 1’s for a metric that is a ratio from 0 to 1. I’ve found through investigation of the various metric’s histograms at different cutoffs, that a value of 30 is a good compromise that minimizes these errant values while keeping as many entries in the population as possible. Though, the point of the study is to discover notably high traffic climbs. 30 ticks is only approximately the 60th percentile. A value of 100 feels more appropriate at the 80th percentile. For this reason, **I would suggest a minimum cutoff of 30 ticks, and a typical cutoff of 100 ticks.**

{% include creak_traffic/Tick_Cutoff_None.html %}
{% include creak_traffic/Tick_Cutoff_30.html %}
{% include creak_traffic/Tick_Cutoff_100.html %}

## Tick Velocity
Ranking by overall number of ticks is a great start, but fails to normalize for any time related factor. For example, a climb may have been established later or even omitted from the mountain project database for some time. A climb such as this may actually experience heavy traffic but would not get picked up when ranking by number of ticks alone. The goal of this metric is to catch routes that are experiencing a burst in traffic within a recent time frame.

Since climbing is heavily seasonal, it makes the most sense to partition by year to suppress the noise of that natural seasonal cycle.

Let's take a look at ticks over the years for a select subset of routes. This is every 10th route as ordered by highest sum ticks.
{% include creak_traffic/YoY_Sum_Ticks.html %}
Regardless of overall popularity, all routes tend to gain ticks year over year. It is likely that Mountain Project use as well as the propensity of a given climber to tick after climbing are increasing. It is also likely that climbing overall and climbing at Indian Creek specifically are becoming more popular.

Let's investigate this deeper on all routes with >100 ticks.
{% include creak_traffic/YoY_Sum_Ticks_Box.html %}
{% include creak_traffic/Mean_YoY_Sum_Ticks_Box.html %}

2020 experienced the first negative median difference since recording began. This is most likely a result of covid and related fears of exposure limiting travel. 2021 was subsequently a breakout year as covid concerns leveled out, and outdoor recreation gained boosted popularity. The jury is still out on 2022, it may turn out to be a relative return to "normal" expected growth or a short pull-back from the previous year extreme and thus a continuation of more extreme exponential growth. Based on the box plots, it seems that YoY change was particularly extreme in both directions through the covid years. It will be interesting to see how this manifests in the coming years.

Let's see if we can identify specific routes that seem to be experiencing a consistent increase in popularity, outside of what may be considered a normal velocity. We normalize by number of ticks once again in order to decouple raw popularity. We also only consider routes with more than 100 ticks, and consider only the last 5 years so to prioritize a recent time frame.
{% include creak_traffic/Norm_Average5yr_Tick_Velocity.html %}

So we get 0.052 as an upper fence. That makes a fine cutoff.
<details open>
<summary><b>Tick Velocity Table</b></summary>
{% include creak_traffic/Tick_Velo_Table.html %}
</details>
<br><br>
It is starkly apparent to me that these routes tend to be quite short for Indian Creek standard, typically in the 45-60ft range compared to a more typical 80-100ft. A shorter climb is usually more approachable, so it is sensible that as climbs like these are discovered by a wider audience that they gain popular sentiment.

These routes are important because they are likely not heavily damaged yet, though their rising popularity puts them at risk. This table also allows us to recognizes Habitado, Trick or Treat, and Sinbad Wall as crags that seem to be experiencing a surge in popularity.

## Heavily Top Roped Climbs
Lead ratio is a simple metric that represents the proportion of ticks that claim a lead attempt vs. a top rope or follow attempt. A lead ratio of 0.5 suggests that in a typical climbing team of two, one partner led and one followed. While this is expected for multi-pitch climbs, for a crag you are likely to get groups where many people are going for a lead attempt or many people would like to top rope a route. In general, a lead ratio of 0.5 is the expected center and suggests that a route is receiving "balanced" traffic.

A high lead ratio designates that the climb is most often led. More difficult climbs typically have a higher lead ratio as someone attempting such a difficult route likely wants the lead tick. An alternative reason for a high lead ratio is that the climb is inconvenient to top rope due to wandering nature or high steepness. A final explanation is the climb is very moderate so even beginner climbers feel comfortable leading.

A low lead ratio designates that the climb is most often top roped. If one’s perspective of a clean ethic is to lead attempt a route until a clean send is achieved and move on, then repeatedly top roping a climb is the contrary. That is not to say that there are some good reasons to top rope a climb instead of lead it. The route may be inherently dangerous to lead due to poor protection or rock. Additionally, the route may be too difficult for the climber, but the climber would like to challenge themselves without incurring the danger and difficulty of leading.

Let's take a look at the histogram for lead ratio.

{% include creak_traffic/Tick_Cutoff_100_2.html %}

A natural cutoff would be to look at outlier values below the lower fence, but I’m going to relax that cutoff slightly 0.4 to cast a slightly wider net.

<details open>
<summary><b>Heavily Rop Roped Table</b></summary>
{% include creak_traffic/LeadRatio_Table.html %}
</details>
<br><br>
Interestingly, every one of these routes with notable exceptions of “Fingers In A Lightsocket” and “Binge and Purge” are able to be top roped from a neighboring easier route. None of these routes are notably dangerous, with the exception of “Fingers in A Lightsocket” which is known for ripping cams at the crux and even cleaving a fingertip (look up at your own risk). It would be unwise to encourage people to lead a climb with particular danger involved, it is probably best not to make a comment.

Any of these routes would make good candidates for the list, but I would give particular attention to “Pringles”, “Naked and Dead Variation”, and “Inflictor” as their lead ratios are such outliers.

## Repeat Send Metrics

Typically, people will attempt a route until they obtain a clean send, then move onto other climbs. That is, unless the climb is just so good or accessible that people keep coming back to it. This can be encapsulated in a “repeat send” metric. For an already popular climb, this can add quite a bit of traffic. Under the previously defined style axioms, in order to maximize inclusiveness, a climber should be able to attempt a climb as many times as they would like. For that reason, we only count repeated attempts after the first clean send.

A simple sum of all repeat sends ends up looking almost identical to the list of total number of ticks. The metric is best normalized by the total number of ticks. This will provide us with the ratio of ticks that are repeat sends.

{% include creak_traffic/RepeatSends_Hist.html %}

With an upper fence at ~0.42, I'm again going to relax the cutoff to 0.33.

<details open>
<summary><b>Repeat Sender Table</b></summary>
{% include creak_traffic/RepeatSender_Table.html %}
</details>
<br><br>

The routes here tend to be of a more moderate grade. This is largely because we are detecting repeat *sends*, not repeat *attempts*. This measure then under-represents more difficult climbs that many people have sent, then failed to send on repeat attempts. Additionally, climbs 5.9 and under are relatively uncommon at Indian Creek. These climbs are likely to be repeated by climbers of all skill levels due to their accessibility.

These routes may be less popular than those ranked by raw number of ticks, but they represent a particualarly important opportunity. Namely, they receive high traffic due to repeated use by the same people. We hold true that everyone has an equal claim to the resource, so if a route is just wildly popular and a heap of people are each responsibly claiming their use, there is not much to be done without getting more restrictive. In this case, use can be drastically reduced by targeting the small group of repeat users.

## Combination Metric
The simplest way to normalize among these different factors is to sum the [**Z (Standard) Score**](https://en.wikipedia.org/wiki/Standard_score), which is simply how many standard deviations from the mean the value is. If one believes one factor is more important than the others, you can weight it by a scalar. Based on my own bias, I weight the metrics as follows:

C<sub>Score</sub> = 0.4 \* Z<sub>Num Ticks</sub> + 0.3 \* Z<sub>Tick Velo</sub> + 0.15 \* Z<sub>Lead Ratio</sub> + 0.15 \* Z<sub>Repeat Sender</sub>

In what may seem a silly thing to do, the cumulative score can be calculated as a Z-score as well. This re-casts these numbers from an arbitrary scale to a more meaningful frame of reference.

{% include creak_traffic/Cum_Hist.html %}

At a glance, I would qualify any route outside the upper fence at ~1.75 as "critical" and any route in the 0.35-1.75 range as "notable".

The following table is ordered by cumulative metric with the worst offender at the top. The cells are shaded red so to help identify which metrics contributed the most to the cumulative value.

<details open>
<summary><b>Combination Metric Table</b></summary>
{% include creak_traffic/Conc_Table.html %}
</details>
<br><br>

Please remember, if these tables are insufficient and you'd like to dive deeper into these metrics for all routes at Indian Creek, check out my [**Giza Analytical App**](https://giza-levyb.streamlit.app/).

# Conclusion
Which routes to prioritize depends on the goals of those carrying out the work.

Routes with a high number of ticks are outright highly trafficked routes. A small selection of climbs are responsible for a drastic majority of traffic. I believe there is a solid argument that these climbs be left as "sacrificial" in nature. This is a similar justification as used to cut a trail in order to reduce uncontrolled trampling. Perhaps extreme over-use on a small set of already damaged routes is better than spreading that use to yet un-impacted routes.

Routes with a high average tick velocity are either "up and coming" or already popular routes that are experiencing an even greater acceleration of use. These are good candidates to prioritize because they are likely on track to become (more) damaged in the future, and may not have been loved to death yet.

Routes with a low lead ratio are heavily top roped. There is a potential basic ethics argument that this is irresponsible use. At the end of the day, I think it is not a good idea for a conservation coalition to impose ethic on others. This type of cultural evolution is best done on a personal basis. Critically, it is irresponsible to encourage leading on potentially dangerous routes. Top roping routes above ones pay grade is a great challenge, and often an important part of improving. All the same, we could do less huge groups running top-rope laps all day on a trade route. Perhaps reduction of this behavior is best tackled from a perspective that isn't conservation and more consideration for fellow recreationists. Outliers here tend to be hard routes that are convenient to top-rope from the same anchors as nearby easier climbs. These climbs are likely not as big of a concern as a route that experiences a lower lead ratio and high traffic. Such a list looks very similar to the list of routes with high number of ticks. I theorize that this kind of behavior is not accurately captured by this data, in large part because of self-reporting bias. Namely, that people who are more likely to top-rope are less likely to record ticks for that attempt. This concern, in combination with the aforementioned dubious grounds for even criticizing this behavior leads me to suggest ignoring this factor all-together.

Routes with a high repeat send metric are also up for debate. I expected outliers in this metric to be a more diverse set of "underground favorites". As it turns out, the outliers tend to be favorites mostly for their lower difficulty, shorter length, and overall approachability. These climbs are important for beginners to cut their teeth on. There is a familiar argument to be made that these climbs may be best left as sacrificial in nature. Approachable routes will always be popular.
<br><br><br><br>
I'm perpetually inspired by the simple effectiveness of the state owned land pit toilet warnings: "PLEASE DO NOT put trash in toilets, it is extremely difficult to remove."

I've attempted to craft my own warning message for the purpose of this analysis.

**"Indian Creek sandstone is irreperably damaged little by little when climbed. Consider climbing popular routes such as this just a few times to minimize impact and preserve these beloved lines for future generations of climbers."**

Thank you for reading.
![Creek Landscape](Creek Landscape.jpg)
*Indian Creek At Dusk From The Base Of South Six Shooter*