---
title: "Managing Climber Impact: Indian Creek Raptor Closure Violations"
date: 2023-06-05
img_path: /images/creek_raptors/
---
# Introduction
Each year from mid/late February to late August the Bureau of Land Management (BLM) announces a voluntary closure of certain Indian Creek climbing crags in order to protect raptors as they nest and raise their young until they fledge. These types of closures are relatively common in popular climbing areas such as Trout Creek, Pinnacles, Zion and Yosemite. The purpose of these closures is to give these uninhibited space to build their nests and raise their young. The peregrine falcon is of particular interest, originally endangered by use of [DDT](https://en.wikipedia.org/wiki/DDT) pesticide which diminished fertility and thinned eggshells. In 1975 the population hit a low of [324](https://www.sciencenews.org/article/50-years-ago-ddt-pushed-peregrine-falcons-edge-extinction) nesting pairs, and as of 2020 the North American population exceeds [72,000](http://datazone.birdlife.org/species/factsheet/peregrine-falcon-falco-peregrinus/text). Golden eagles are another raptor of concern. Though never listed as endangered, it is a protected species that is impacted by human activity. While the status of these raptors is not critical, there still exists a cogent basis to co-exist respectfully with these animals.

![Eagles](eagles.png)
*Peregrine Falcon (left) and Golden Eagle (right)  [[Source 1](https://www.allaboutbirds.org/guide/Peregrine_Falcon/id)] [[Source 2](https://birdfact.com/articles/how-big-are-golden-eagles)]*

As with any conservation effort, there is much debate with regard to the impact of the effort and the effect on recreational access. Some areas like Yosemite have the funding to [check for raptor presence throughout the season](https://www.outsideonline.com/outdoor-adventure/climbing/climbers-changing-outdated-raptor-closures-peregrine-falcon/), and open access quickly as the birds move on. Other areas such as Indian Creek likely do not have the funding for such efforts, and as a result institute blanket bans over a largely pre-determined time period. In the case of the Indian Creek closures, the bans are actually considered voluntary, and are in large part self-regulated by the community. While there are plenty of people who will argue online about the merits of this and that, I will be  apathetic to these arguments and focus on the data analysis. I've also avoided referencing usernames, as I do not want to attract negative attention to anyone. If one really wants to find people out it is not difficult as the information is public. If one really does not want to be found out, Mountain Project has a setting that makes ticks private.

# Purpose
The purpose of this analysis is to answer the basic **when, where, who** of these raptor closure violations in order to better focus conservation efforts.
1. **When** - When are these violations occurring? When is it critical to have stewards in the creek educating climbers about the closures?
2. **Where** - Which crags and routes are these violations occurring on? Which areas should have improved signage?
3. **Who** - What kind of climber is violating these closures? What strategies can be changed to better inform these kinds of climbers?

In general I will plot these relationships of interest in three ways. First the raw number of violations, second the raw number of ticks, and third some normalized ratio of the two that attempts to decouple potentially obfuscating relationships. In this way we can see which elements are responsible for a high raw number of violations, and which elements are responsible for a high number of violations in proportion to their overall use.

# Data Structure
This data is scraped from publicly available mountain project tick data.

For each route we know:
* Route Name
* Route Location
* Route Grade

For each tick we know:
* Tick User Reference
* Tick Date

My data actually contains quite a bit more information (Some of which can be seen utilized in my [previous IC conservation post](https://brayden-l.github.io/posts/Creak-Traffic/)), but I have opted to keep things simple to focus on relevant information.

I collected raptor closure details from [blog posts](https://www.blm.gov/press-release?combine=raptor&term_node_tid_depth%5B%5D=27&field_release_date_value=1) on the BLM website.
{% include creek_raptors/raptor_closure_table.html %}

It seems that raptor closures began in 2018. Raptor closure season has not yet ended for 2023 so I am omitting the current year. This gives us a range of five years of data from 2018-2022.

# A Note on Data Bias
Since this data is pulled entirely from Mountain Project, there is quite a bit of reporting bias one should be aware of.
* There are likely many violators who do not make their violation known through ticking.
* Mountain Project is an incomplete listing of crags and routes, which means that some violations may not be able to be recorded even if they otherwise would be.
* Mountain Project users are more likely to be experienced or serious climbers, so this data likely under-represents new climbers.
* The climber that meticulously ticks their climbs is a particular type of climber with common behaviour that may be correlated with their likelihood to violate the infraction.

As such we must take this analysis with a grain of salt, as it provides only a partial and biased view of the issue. While it is imperfect, it is at least a well structured perspective on the issue.

# When
## Year to Year
We begin with yearly variation as our first element of interest.
{% include creek_raptors/year_viol.html %}
{% include creek_raptors/year_tot.html %}
{% include creek_raptors/year_norm.html %}

We can see that 2018, 2020, and 2022 were comparatively good years. 2019 and especially 2021 were bad years. When accounting for overall use I do not see any additional insight.

### 2018
* 2018 was a good year.
* This was the first year of the raptor closure effort. It is possible that stewards and representatives were effective in getting the word out. Though it would be similarly convincing to suggest that the first year of an awareness campaign should be weak as it takes time for news to spread.
* Only 3 walls were closed this year compared to the growing list of closed areas in subsequent years.
* Two of the three closed walls (Cat and Reservoir) have parking off the main road and near the beef basin intersection. It is possible that fear of community and official judgment keeps violations at these front and center crags to a minimum.

### 2019
* 2019 was a bad year.
* It is possible the community learned more broadly that the closures were voluntary.
* 75% of these violations occurred during the high season of March/April/May.
* A whopping 53% are attributed to the 2nd Meat Wall alone. The 2nd Meat Wall was added to the closure list this year, but so were other walls and they did not carry such volume. Perhaps the closure of this wall was poorly communicated.

### 2020
* 2020 was a good year.
* Indian Creek access was limited significantly due to the covid-19 pandemic. There were enforcement personnel patrolling the area urging non-local recreationists to leave. The highest level of covid uncertainty occurred during the closure period, so people were less likely to travel anyway.
* Interestingly, the normalized rate was low even accounting for the reduced usage. I suggest this may be due to increased enforcement presence reducing would-be violations. Basically, people already feel like they are doing something wrong by being there, many would not want to take additional risky action.

### 2021
* 2021 was a notably bad year.
* In this year 73% of violations occurred in the high season. This rises to 98% if you count February.
* 48% of violations occurred at Selfish Wall this year.
* At Selfish Wall, the route Hand Solo (5.9, 66x violations) accounts for 12% of all violations alone. That and 5 other Selfish Wall routes such The Ooze (5.10-, 27x), Breakfast Social (5.10-, 22x), Tokyo Drift (5.11-, 15x), Tag Team (5.10-, 14x), Turd Blossom (5.10, 14x) make up 30% of all violations. It is notable that this wall has a high concentration of high quality less difficult routes. These are desirable and accessible climbs so it is expected that more people would be willing to violate the closure to climb these. The shocking volume here suggests that it is possible that word has gotten out amongst organized groups such as school groups or climbing event organizers who desire a concentration of accessible moderate routes.
* Selfish Wall is a ways off the main road, so there is less social and enforcement pressure.

### 2022
* 2022 was a good year.
* The distribution among crags was quite balanced, with no real outliers. It is the same for routes.
* The improvement from 2021 to 2022 is impressive. I do not know for sure, but I believe this was the first year that the Mountain Project Indian Creek area moderators added the Raptor Closure tags to the climbing areas themselves. Either the increased visibility of the closure truly reduced violations, or people were less willing to record their violations with a tick right under a big sign that says not to do just that!

## Month to Month
We would like to inspect month to month variation to help pin down which part of the closure season is most pertinent. This helps to determine when steward presence is most important.
{% include creek_raptors/month_viol.html %}
{% include creek_raptors/month_tot.html %}
{% include creek_raptors/month_norm.html %}

* We can see that the high season of March/April/May accounts for 70% of violations, as we would expect.
* It is notable to me that February has so many violations. This may be due to it being the first month of the closure, so awareness may be low.
* We can see that when normalized, the out-of-season months maintain a much higher "violation ratio". This suggests that climbers during this period are more willing to violate the closure. An obvious reason for this is reduced steward and enforcement presence, in addition to reduced social pressure with less overall climbers.

I took a deeper look at which crags held the bulk of violations for each month across all 2018-2022 years:
* February - Cat Wall, The Wall
* March - Selfish Wall, 2nd Meat Wall, Cat Wall
* April - Selfish Wall, 2nd Meat Wall, Cat Wall
* May - Selfish Wall, 2nd Meat Wall, Fin Wall
* June - Reservoir Wall, Cliffs of Insanity, Cat Wall
* July - Reservoir Wall
* August - Reservoir Wall, Cat Wall

It seems that Cat wall is consistently popular across the season. Reservoir Wall makes an appearance as it gets warmer. Selfish Wall and 2nd Meat Wall are popular at high season.

# Where
## By Crag
We can inspect violations by crag to help identify which areas are consistently impacted. In this way additional signage or steward presence may be allocated to reduce violations in these areas.
{% include creek_raptors/crag_viol.html %}
{% include creek_raptors/crag_tot.html %}
{% include creek_raptors/crag_norm.html %}

Cat Wall and Reservoir Wall we would expect to be so high, as they are hugely popular crags. Selfish Wall and to a lesser extent 2nd Meat Wall are standouts, as they receive many violations given their lower popularity. Crags such as Suburbia, Slug Wall, Shock and Awe and especially Echoes Wall are notable in that a comparatively larger portion of their ticks occur during the closure season.

## By Route
Knowing which specific routes are offenders allows for targeted additional messaging on Mountain Project to urge would-be climbers of the route to respect the closures.
{% include creek_raptors/route_viol.html %}
{% include creek_raptors/route_tot.html %}
{% include creek_raptors/route_norm.html %}

The clear take away here is that moderate routes like Hand Solo, The Ooze and Breakfast Social are particular offenders. Hand Solo is a real outlier here.

# Who
## By Grade
Knowing what grade range accounts for violations helps to classify what kind of climber is willing to violate the closures.
{% include creek_raptors/grade_viol.html %}
{% include creek_raptors/grade_tot.html %}
{% include creek_raptors/grade_norm.html %}

A 5.10 grade at the creek is both common and accessible, so it is sensible that it accounts for many of the violations. Interestingly, when normalized by overall ticks, it seems that harder climbers are more willing to violate the closures.

## Violator Profile
The 1,070 ticks that represent violations were created by 325 individuals with the following distribution:
{% include creek_raptors/violator_value_count.html %}
Note that the one "individual" with 61 violations is actually a basket of all private ticks.

Of the 325 violators, 310 violated the closure in 1/5 year only, 14 in 2/5 years, and none greater than that.

I've hand collected the following data on the top 20 violators.

{% include creek_raptors/violator_table.html %}

A cursory glance suggests that this group of high violation individuals tend to be older, more experienced and more advanced climbers.

Note that for perceived ability I took a quick look at their ticks and declared them advanced if they have attempted many 5.12 leads, intermediate if attempted many 5.11 leads, and beginner if only <=5.10 leads. It is also important to note that this data is collected as of the current day and does not represent their attributes at the time of their violation. This means an alternative conclusion is possible, that a violator is likely to be someone who *goes onto* be a more experienced and advanced climber.

There is certainly more room to do better profile analysis on individuals who violate the closure. This would require a lot more data scraping and analytical work.

# Conclusions
Based on the analysis done here, I have the following conclusory comments.

* 2021 was a bad year, and 2022 was a good year. It is critical to understand what happened in each of these years that led to their respective outcomes.
* The high season has higher volume of violations as anticipated. February is surprisingly high. Perhaps better awareness efforts are required at the start. Off season in general tends to attract more violations as a share of overall ticks, which is likely due to reduced steward presence.
* Cat Wall and Reservoir wall are expectedly high volume. Selfish Wall and to a lesser extend 2nd Meat Wall are particular standouts as a share of their overall ticks. I believe this is due to their selection of high quality moderate routes, and their relative remoteness.
* It may be helpful to add additional messaging to specific high traffic routes on Mountain Project. Routes like Hand Solo are shockingly high contributors.
* It seems that raw share of violations can be attributed to a larger group of less experienced climbers, but that high volume and repeat violators are more likely to be from a smaller group of experienced climbers. It is important not to assume that lack of compliance is due to simple unawareness, you must also impress the importance of the issue on experienced recreationists.
* There exists an interesting relationship here, whereby increasing communication of the closures also reduces the visibility of the violations. I see this most likely in the case of area or route specific closure warnings, which would dissuade users from recording their violations on Mountain Project. In taking action to solve the problem, one might actually make it more difficult to gauge the status of the problem.
