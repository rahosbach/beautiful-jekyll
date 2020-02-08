---
layout: post
title: A Sankey Diagram of Magnus Carlsen's 120 Straight Classical Chess Wins
tags: [chess, Flourish, Magnus Carlsen, Sankey]
---

### Introduction
Since his becoming the top chess player in the world in 2011 and winning his first world championship in 2013, [Magnus Carlsen](https://en.wikipedia.org/wiki/Magnus_Carlsen#World_Chess_Championship_2013) has captivated the chess world.  In fact, his dominance of the game over all time controls (classical, rapid, and blitz) combined with his affable personality (especially for an elite chess player) has lead to a recent resurgence of chess popularity globally (see [this _Vice_ article](https://www.vice.com/en_us/article/aebkk5/how-magnus-carlsen-is-making-chess-cool-and-wearing-his-rivals-down), for example).  Growing up, I always had a slight interest in chess, but I spent my time playing the more "traditional" sports (at least in the United States) of baseball, tennis, and to a lesser extent, soccer and basketball.  But, what Carlsen has been able to accomplish across a chess board is nothing short of amazing, and it has caught my attention.

In this article, I aim to unveil some interesting details about a new chess record (yet another!) that Carlsen recently claimed: the longest unbeaten streak in classical chess.  It is worth mentioning that despite the fact that literally every chess game I play ends up in a decisive result (and this is because my chess skills are nothing to write home about), at the elite level a draw (_i.e._, a tie) is actually more common than a decisive victory.  This is because players at the top of the game are so strong, and they know so much chess theory, that a point is often reached in the game where neither player sees an opening to take an advantage.  At that point, one player may offer the other player a draw, and if the other player agrees, then the game ends in a draw.  Now, about Carlsen's new record...

As reported by [Reuters on January 14, 2020](https://www.reuters.com/article/us-chess-carlsen/chess-carlsen-breaks-record-for-longest-unbeaten-streak-idUSKBN1ZE0JZ), "The reigning world champion surpassed the 110 classical games undefeated set by Sergei Tiviakov in 2005 after drawing against Jorden van Foreest in the fourth round of the Wijk aan Zee tournament."  Think about that: **110 straight classical games, over a variety of tournaments and elite opponents, where Carlsen either won or the game was drawn.**  Now, Carlsen himself suggested at the time that he didn't really consider the record broken yet, because two of those 110 games were against good, but lower-level opponents than he typically plays against.  But, since that time Magnus has completed another classical chess tournament without a loss, bringing his current streak to 120 games!

### Data Source
While there are plenty of websites that will list data for Grandmaster-level chess games, I wasn't able to find one curated source of information for Carlsen's current unbeaten record.  So, to get the data I needed I used Google Sheets' IMPORTHTML() method to import tables of Carlsen's games from [365chess.com](https://www.365chess.com/).  These tables included Carlsen's opponent's name, whether Carlsen was white or black, the result of the game, the number of moves in the game, and the [Elo ratings](https://en.wikipedia.org/wiki/Elo_rating_system) for Carlsen and his opponent at the time of the game. 

### The Visualization
I've been looking for an excuse to generate a Sankey diagram for one of my posts, and this post seemed to fit the bill.  The Sankey diagram I've chosen for this post, which I created using [Flourish](https://flourish.studio/), shows the number of games Carlsen played during this 120-game streak that he played as white and black (the left column) against opponents of different Elo ratings (the middle column).  Then, the column on the right shows the number of games resulting in a win or draw based on the Elo rating of Carlsen's opponent.  As a reminder, Carlsen has held the top Elo rating in classical chess since 2011; so, at no point during this 120-game streak has anyone held a higher Elo rating than Carlsen.

<div class="flourish-embed flourish-sankey" data-src="visualisation/1358593"><script src="https://public.flourish.studio/resources/embed.js"></script></div>

### Thoughts
Over this 120-game streak, the left column shows that Carlsen has had the white pieces 50% of the time and the black pieces 50% of the time.  Because the player with the white pieces has the first move in chess, having the white pieces has traditionally been seen as an advantage.  Furthermore, the right column shows that two-thirds of the games resulted in a draw (which is not unexpected, as mentioned previously).  The visualization also shows how the percentage of wins relative to draws decreases as Carlsen's opponents' Elo ratings increase (as expected).  Against opponents having Elo ratings of 2800+, over 80% of the games were drawn!  It is worth mentioning that during this streak, the 31 games against opponents rated 2800+ were played against only three players: Fabiano Caruana (22 games), Ding Liren (6 games), and Shakhriyar Mamedyarov (3 games).  On the other hand, Carlsen clearly has no difficulty playing opponents rated below 2600, as he won all four such games he played during this streak.

Given these thoughts, I wanted to put together another couple of charts to look at:
1. The distribution of games played against each opponent, and
2. The distribution of games played by the number of moves played in the game by each player.

I generated these two simple bar charts in Google Sheets, because that is where my data collection, analysis, and filtering happened for this post (read: they're nothing fancy).  The first plot is interesting because it shows that Fabiano Caruana (the current world #2) has been Carlsen's opponent more than 1/6th of the time.  Of the 22 games played against Caruana, 12 of them occurred during the 2018 World Chess Championship, which Carlsen won in a tiebreak after the two played 12 classical games to draws.

![Bar plot showing the number of games played against different opponents during Magnus Carlsen's 120-game unbeaten streak.]({{http://rahosbach.github.io}}/img/magnus/barplot_opponents.svg)

This second plot of the distribution of the number of moves played by each player is interesting for a couple of reasons.  First, more than 70% of the games where concluded after 30-59 moves per player.  But, Carlsen also played two games during this streak that lasted for more than 100 moves per player!  The first of these games was the first game of the 2018 World Chess Championship against Fabiano Caruana, which lasted for 115 moves per player.  The second was a 131-move draw against Vidit Gujrathi in the 2019 Tata Steel Chess tournament.

![Bar plot showing the number of games played against different opponents during Magnus Carlsen's 120-game unbeaten streak.]({{http://rahosbach.github.io}}/img/magnus/barplot_moves.svg)


