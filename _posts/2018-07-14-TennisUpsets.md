---
layout: post
title: Are there More Upsets in the ATP or WTA?
tags: [ATP, jupyter, python, tennis, time series, WTA]
---

I've been a pretty serious tennis fan since high school.  Back then, Andre Agassi and Pete Sampras were still battling it out, along with Justine Henin-Hardenne (now Justine Henin) and Kim Clijsters.  It was great fun to watch.

Nowadays, I still follow tennis throughout the season, but I mainly just watch the four major tournaments.  For the last number of years it has seemed to me that the men's tennis matches (ATP Tour) were generally pretty unsurprising: The top-ranked player in the duel was usually the winner.  However, on the women's side (WTA Tour) it seems to me that no single player (or set of players) can remain atop the rankings chart and win out consistently against a variety of opponents (Serena Williams is the notable exception here).

So, this post is all about figuring out if there are more "upsets" (_i.e._, the lower-ranked player wins) on the WTA Tour compared to the ATP Tour.  My suspicion is that there should be more upsets on the WTA Tour.

### Data
Thankfully, a guy by the name of [Jeff Sackmann](https://github.com/JeffSackmann) has collected data on professional tennis matches dating back to 1968!  The data files—one file per year—are freely available on his GitHub repositories: [tennis_atp](https://github.com/JeffSackmann/tennis_atp) and [tennis_wta](https://github.com/JeffSackmann/tennis_wta).

{% include ATP_vs_WTA_Upsets.html %}
