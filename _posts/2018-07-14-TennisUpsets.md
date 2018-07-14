---
layout: post
title: Are there More Upsets in the ATP or WTA?
tags: [ATP, jupyter, python, tennis, time series, WTA]
---

I've been a pretty serious tennis fan since high school.  Back then, Andre Agassi and Pete Sampras were still battling it out, along with Justine Henin-Hardenne (now Justine Henin) and Kim Clijsters.  It was great fun to watch.

Nowadays, I still follow tennis throughout the season, but I mainly just watch the four major tournaments.  For the last number of years it has seemed to me that the men's tennis matches (ATP Tour) were generally pretty unsurprising: The top-ranked player in the duel was usually the winner.  However, on the women's side (WTA Tour) it seems to me that no single player (or set of players) can remain atop the rankings chart and win out consistently against a variety of opponents (Serena Williams is the notable exception here).

So, this post is all about figuring out if there are more "upsets" (_i.e._, the lower-ranked player wins) on the WTA Tour compared to the ATP Tour.  My suspicion is that there should be more upsets on the WTA Tour.

### Approach
My approach to this problem is simple: Look at historical matches and calculate the fraction of matches in a given year that are upsets, where an upset is defined as the winner's ATP/WTA ranking being at least some value below the loser's ranking.  For example, one might define an upset as any time the lower-ranked player wins (_i.e._, the difference between the winner's rank and loser's rank is at least 1).  Alternatively—and perhaps more accurately—one could define an upset as any time the difference between the winner's rank and loser's rank is at least 5.  In this example, if the 3rd-ranked player beat the 1st-ranked player, that _would not_ be upset; however, if the 6th-ranked player beat the 1st-ranked player, that _would_ be an upset.

Note that, by definition, a "higher-ranked" player should have a lower ranking value.  That is, the best player in the world has a #1 ranking, whereas the 25th-best player in the world has a #25 ranking.  

We will take a look at the fraction of matches in a given year that result in an upset, and we'll compare the ATP Tour and the WTA Tour side-by-side so that we can observe any clear differences.  We will also break out these data by surface type, to see if any particular surface (clay, grass, or hard) generally results in more upsets (I've ignored carpet here, as not many tournaments are played on carpet).  My thinking here is that on grass courts, for instance, big servers typically have an advantage.  For the past decade or so, we haven't seen any of the really big servers on the men's side reach the #1 world ranking; but, on grass courts these big servers may have enough advantage to consistently beat higher-ranked players.  Conversely, clay courts tend to neutralize big serves, and I would therefore expect that big servers may consistently lose to lower-ranked players in clay-court matches.

Finally, the data have been filtered for this analysis according to the following:
- Tournament types include Master's Series and Grand Slams only.
- Only matches for which the winner's world ranking and loser's world ranking are provided are used.
- Only matches played on clay, grass, or hard courts are used.

### Data
Thankfully, a guy by the name of [Jeff Sackmann](https://github.com/JeffSackmann) has collected data on professional tennis matches dating back to 1968!  The data files—one file per year—are freely available on his GitHub repositories: [tennis_atp](https://github.com/JeffSackmann/tennis_atp) and [tennis_wta](https://github.com/JeffSackmann/tennis_wta).

The annual data files contain everything we need for this analysis:
- ATP (men's) or WTA (women's) Tour
- Date of the match
- Court surface the match was played on
- Winner and loser world rankings

### Results
To generate the results for this post I used the [Matplotlib](https://matplotlib.org/) library in Python 3.  The full analysis, including creation of the plots, was completed in a [Jupyter](http://jupyter.org/) notebook.  [An interactive version of the notebook can be viewed here](https://mybinder.org/v2/gh/rahosbach/rahosbach.github.io/master?filepath=%2F_includes%2FATP_vs_WTA_Upsets.ipynb), but I provide a discussion of the key results below.

When originally looking at the results, the year-by-year data showed quite a bit of variance.  Therefore, I went ahead and used a moving average of three years for plotting the fraction of matches resulting in an upset. (Note that the time frame for the moving average can be adjusted in the notebook's interactive plot.)  A three-year moving average kept some of the variance, but allowed for a clearer look into the trends in upsets by Tour (ATP or WTA), surface type, and year.

The three sets of plots below show the same data, except that the definition of an upset is different in each:
1. The winner's rank was **at least five ranks lower** than the loser's rank.
2. The winner's rank was **at least 10 ranks lower** than the loser's rank.
3. The winner's rank was **at least 25 ranks lower** than the loser's rank.

![3-Year rolling average of fraction of matches resulting in an upset (winner is ranked at least 5 spots lower than the loser) for the ATP and WTA tours by year (1968-2018) and surface type.]({{http://rahosbach.github.io}}/img/atp_vs_wta_upsets/atpvswta_rolling3_threshold5.png)
![3-Year rolling average of fraction of matches resulting in an upset (winner is ranked at least 10 spots lower than the loser) for the ATP and WTA tours by year (1968-2018) and surface type.]({{http://rahosbach.github.io}}/img/atp_vs_wta_upsets/atpvswta_rolling3_threshold20.png)
![3-Year rolling average of fraction of matches resulting in an upset (winner is ranked at least 25 spots lower than the loser) for the ATP and WTA tours by year (1968-2018) and surface type.]({{http://rahosbach.github.io}}/img/atp_vs_wta_upsets/atpvswta_rolling3_threshold25.png)

The data start a bit later for the WTA in these plots due to not having all the required data for the earlier years (_e.g._, not having rankings for both players).  As expected, the fraction of matches resulting in an upset decreases as the required difference in rankings for an upset increases.  But, there are quite a few unexpected findings here for me:
1. The fraction of matches resulting in an upset is roughly equivalent for all surfaces over time.
2. **The fraction of matches resulting in an upset is quite similar between the ATP and the WTA** (on the order of 25%-30% of matches).
3. I never expected that 25% or more matches would result in the winner being ranked at least five spots below the loser.  (I thought the fraction would be much lower, especially for the ATP Tour.)
4. About 15% of matches result in an upset where the winner is ranked at least 25 spots below the loser.  In this case, grass appears to result in slightly more upsets than the other surfaces for the ATP Tour.
5. The fraction of upsets appeared to have peaked around the year 2000 for the ATP Tour, and has been in decline since.  Conversely, the fraction of matches resulting in an upset on the WTA Tour has been rising over the time period of these data.  This may explain why, anecdotally, I thought that more WTA matches resulted in upsets.

### Conclusions
According to this analysis, the resounding answer to this post's motivating question is: **Neither the ATP Tour nor the WTA Tour appear to have more upsets at the Master's Series and Grand Slam level.**  That said, it would appear that the number of upsets is trending upward for the WTA, whereas the trend is downward for the ATP Tour.  Hopefully, Mr. Sackmann will continue aggregating tennis data, and this can be revisited in a couple years to see if anything has changed.

Again, the [interactive analysis notebook](https://mybinder.org/v2/gh/rahosbach/rahosbach.github.io/master?filepath=%2F_includes%2FATP_vs_WTA_Upsets.ipynb) is published and available for your perusal.
