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

We will take a look at the fraction of matches in a given year that result in an upset, and we'll compare the ATP Tour and the WTA Tour side-by-side so that we can observe any clear differences.  We will also break out these data by surface type, to see if any particular surface (clay, grass, or hard) generally results in more upsets.  My thinking here is that on grass courts, for instance, big servers typically have an advantage.  For the past decade or so, we haven't seen any of the really big servers on the men's side reach the #1 world ranking; but, on grass courts these big servers may have enough advantage to consistently beat higher-ranked players.  Conversely, clay courts tend to neutralize big serves, and I would therefore expect that big servers may consistently lose to lower-ranked players in clay-court matches.

### Data
Thankfully, a guy by the name of [Jeff Sackmann](https://github.com/JeffSackmann) has collected data on professional tennis matches dating back to 1968!  The data files—one file per year—are freely available on his GitHub repositories: [tennis_atp](https://github.com/JeffSackmann/tennis_atp) and [tennis_wta](https://github.com/JeffSackmann/tennis_wta).

The annual data files contain everything we need for this analysis:
- ATP (men's) or WTA (women's) Tour
- Date of the match
- Court surface the match was played on
- Winner and loser world rankings

### Results

