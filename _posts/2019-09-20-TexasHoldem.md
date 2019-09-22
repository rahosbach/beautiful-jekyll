---
layout: post
title: What Affect does the Number of Players have on Texas Holdem Results?
tags: [heatmap, line plot, poker, Seaborn, Texas Holdem]
---

### Introduction
For some time now I've pondered the ease or difficulty in developing a poker simulator in Python.  Well, a little over a week ago I decided to try my hand (no pun intended) at creating a program that would simulate a hand of [Texas Holdem](https://en.wikipedia.org/wiki/Texas_hold_%27em) for a given number of players.

Texas Holdem is a well-defined poker variation with a basic structure: 
1. Each player is dealt two "hole" cards that are hidden from all other players.
2. Five community cards are dealt in three stages (betting happens at each stage).
3. Each player makes the best possible five-card hand from their two hole cards and the five community cards (poker hand descriptions and hierarchy are described [here](https://www.cardplayer.com/rules-of-poker/hand-rankings)).

In considering the hole cards, a general rule of thumb is that pairs, suited high cards (_e.g._, A-K or A-Q), and even off-suit A-K are the best to play with.  In contrast, a 2-7 off-suit is the worst (no flush opportunity and no potential to connect the two cards in a straight).  However, what I really wanted to know is how does the win percentage associated with a particular set of hole cards vary based on the number of players in the hand.  My hypothesis is that the win percentage will be inversely proportional to the number of players (_i.e._, increasing the number of players decreases the hand's win percentage), perhaps in an exponential fashion.

### Methodology and Assumptions
The simulator I created does not take into account any betting.  Instead, it assumes that all players remain in the hand until the very end.  In this way, the resulting win percentages should be conservative, because in an actual hand of Texas Holdem there generally only remain two or three players by the time the final (fifth) community card is dealt.  Therefore, in a real-world setting the eventual competition at the end of the hand would be lower.

I created the simulator using [Python 3](https://www.python.org/) in a [Jupyter notebook](https://jupyter.org/).  The code for the simulator only relies on Python's standard libraries (specifically, `collections`, `itertools`, `operator`, and `random`).  In addition, I did make use of Python's `multiprocessing` library and the [joblib](https://joblib.readthedocs.io/) library to achieve faster run times using parallel processing.  Finally, to analyze and plot the final results I relied on [Pandas](https://pandas.pydata.org/) and [Seaborn](https://seaborn.pydata.org/), respectively.

In order to estimate the win percentages for sets of hole cards (as well as eventual winning five-card hands), I used the [Monte Carlo method](https://en.wikipedia.org/wiki/Monte_Carlo_method).  Specifically, I simulated 100,000 hands each for 2-11 players (_i.e._, 100,000 hands for two players, 100,000 hands for three players, etc.).

### Results
Using parallelization, running 100,000 hands for each of 2-11 players took about 36 minutes to complete on my laptop (a basic, four-core machine).

The heatmap below allows an easy comparison of the win+draw percentage as a function of a player's hole cards and the total number of players in the hand. (A draw is when two or more players have final five-card hands that are equivalent; these players would split the pot.)  In this case, I aggregated hole cards into a small collection of categories that I wanted to compare: pairs, suited connectors (_i.e._, two cards of the same suit that are only one rank away from each other, like 9-10 of spades), suited (_i.e._, two cards of the same suit that are not connected), connectors (_i.e._, two cards that are only one rank away from each other, not suited), and A-through-4 high cards (_i.e._, hands that are not suited and are not connected). (Note that a 3-high card is technically a connected hand: 3-2.)  This results in a reasonable number of categories to compare, as opposed to trying to compare [all possible combinations](https://www.tightpoker.com/poker_hands.html) of hole cards.

![Heatmap of win+draw percentage as a function of starting hole cards and number of players in the hand for Texas Holdem.]({{http://rahosbach.github.io}}/img/texas_holdem/heatmap_hole_cards.svg)

The next chart is a line plot of the exact same data that were plotted in the previous heatmap.  This line plot is included in order to see _how_ the win+draw percentage declines as the number of players increases (_i.e._, linearly, exponentially, etc.).

![Line plot of win+draw percentage as a function of starting hole cards and number of players in the hand for Texas Holdem.]({{http://rahosbach.github.io}}/img/texas_holdem/lineplot_hole_cards.svg)

The next heatmap allows one to compare the win+draw percentage as a function of a player's final five-card hand and the total number of players in the hand.

![Heatmap of win+draw percentage as a function of final five-card hand and number of players in the hand for Texas Holdem.]({{http://rahosbach.github.io}}/img/texas_holdem/heatmap_final_hand.svg)

### Concluding Thoughts
As expected, increasing the number of players in the hand reduces a given set of hole card's (or final five card hand's) win+draw percentage.  From the line plot, it appears that this reduction is exponential in nature.  Here are a few other observations from the heatmaps:

**Hole Cards**
1. It would appear that A-high and K-high hole cards tend to have a higher win+draw percentage than suited connector, suited, or connector hole cards.
2. With only two players, having a pair, suited connectors, suited, A-high, K-high, or Q-high hole cards gives a greater than 50% chance of winning or drawing the hand.  But, once there are three players, only a pair has a 50% chance of winning or drawing the hand.
3. Once you get to four players, a pair only has a 39% percent chance of winning or drawing.

**Final Hands**
1. Straight flushes will essentially always win or draw the hand.  Even with 10 players in the hand, a straight flush wins or draws more than 90% of the time. (Note that straight flushes do not commonly arise in games of Texas Holdem.  Straight flushes only occurred in 0.03% of the hands in this simulation.  Therefore, given the nature of this Monte Carlo analysis, the win+draw percentages are based on relatively few instances of players achieving a straight flush.)
2. With only two players, 2-pair has a 67% chance of winning or drawing, with 3-of-a-kind providing a 76% chance.  However, with three players in the hand, 2-pair only has a 48% chance of winning or drawing.
3. With only two players, a high card has an 18% chance of winning or drawing the hand.  However, with four or more players, the win+draw percentage for high cards drops to 1% and lower.
4. A straight is the lowest hand that has at least a 50% chance of winning or drawing the hand, no matter how many players are playing.
