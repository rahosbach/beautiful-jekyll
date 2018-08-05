---
layout: post
title: Are California Wildfires Getting Worse?
tags: [California, linear regression, R, scatterplot, time series, wildfire]
---

Every year if seems California is inundated with hundreds of wildfires.  This year, of course, has been no different.  According to [CalFire](http://www.fire.ca.gov/current_incidents), there are 24 wildfires burning as I write this, and only five of those are currently at 100% containment.  The current most devastating fires are the [Carr Fire](http://www.fire.ca.gov/current_incidents/incidentdetails/Index/2164) (over 145,000 acres and only 41% contained, started July 23rd) near Redding and the Ferguson Fire (over 81,000 acres and only 35% contained, started July 13th) near Yosemite.  The effects of all of these fires are horrendous: Loss of life and property, injuries, mass evacuations, fine particulate matter in the air leading to respiratory issues, etc., etc.

For this post, I wanted to take a look at historical wildfires in California to see if there are any trends in the number of fires and/or the size of the fires over time.

### Data
The U.S. Geological Survey (USGS) has posted historical [Federal wildland fire occurrence data from 1980 to 2016 for the entire U.S.](https://wildfire.cr.usgs.gov/firehistory/data.html)  While these data will not capture the latest fires from the past couple years nor any information on the damage caused by the fires (_e.g._, structures destroyed, lives lost, or damage costs), they should provide a sense of what has been happening over the past 35 years in terms of wildfires. 

CalFire also provides [annual fire season reports](http://cdfdata.fire.ca.gov/incidents/incidents_statsevents) that go into more detail on only the California wildfires for a given year.  However, after viewing these reports and the data therein, I started observing some discrepancies between reports on the number of fires, land area burned, and other metrics for a few given years.  Therefore, I decided to stick with the national data from the USGS for this analysis.

### Methodology
To read in the data, analyze the data, and create visualizations for this post I used the [foreign](https://cran.r-project.org/web/packages/foreign/foreign.pdf) and [tidyverse](https://www.tidyverse.org/) libraries in R.  In this case, the USGS posts the data as a .dbf file that R cannot natively read; therefore, I had to use the foreign library to read in the data for analysis.

I approached this analysis by looking at the differences between decades, because I figured that year-over-year differences may be too noisy to really extract anything meaningful.  Plus, if there are any significant changes to be seen in wildfire activity, the time scale would be on the order of decades, not years.  In addition, because I wanted to see changes over time, I only analyzed fires that were started in California and that had a reported start date.

### Results
As a first step, I wanted to see how the cumulative fires started and acres burned over time compared for each decade: 1980s, 1990s, 2000s, and 2010s (so far).  As you can see from the first plot below, this decade (labelled "2010") has actually started off with fewer wildfires than any of the previous three decades.  I found that a bit surprising.  Then again, the wildfires have been prolific this year and last year, which these data do not capture.  You may also notice from this plot that 1987 (about day number 2,750) was a particularly bad year in terms of the number of wildfires that occurred.  So much so, that from that point on in the decade the 1980s had as many or more wildires than the 2000s.  So, it would appear then that California endured more wildfires in the 1980s and 1990s than in the 2000s, and, if the current trend would hold, even the 2010s.

![Cumulative California wildires by decade for 1980-2016.]({{http://rahosbach.github.io}}/img/california_wildfires/cumulative_fires_by_decade.png)

In the next plot we take a look at cumulative acres burned by decade.  Here we can plainly see that the 2000s saw far more California acres burned than the 1980s or 1990s.  Even this decade is trending higher than the 1980s and 1990s in terms of cumulative burned acreage.  Given that the trend here appears to be somewhat reversed from what we saw in the previous plot, I suspect if we took a look at average acreage burned per wildfire over time, we would see an increasing trend.

![Cumulative acres burned due to California wildires by decade for 1980-2016.]({{http://rahosbach.github.io}}/img/california_wildfires/cumulative_acres_by_decade.png)

The scatterplot below has a point for every year from 1980 to 2016 showing the average acreage burned per wildfire for that year.  Additionally, I have provided a best-fit linear regression line showing a loose (adjusted R-squared value = 0.25) positive trend to the data of about six added acres per fire per year.

![Average acres burned per wildfire for each year in 1980-2016 with best-fit linear regression line.]({{http://rahosbach.github.io}}/img/california_wildfires/average_acres_per_wildfire.png)

### Conclusions
I am no expert when it comes to wildfires, but I know that simply looking at the number of reported wildfires and the number of acres burned doesn't tell the whole story.  Nevertheless, from the Federal USGS data we have for California wildfires that started between 1980 and 2016, it would appear that the size of the fires (in terms of acres burned) is slowly trending upward over time.  Given increased urbanization in California and the sprawling of cities that has occurred over the past couple decades, I suspect we would find a similar positive trend if we looked into the total monetary damages of fires over time.  For a subsequent post I may try to harmonize some of the discrepancies in the CalFire annual reports to check on that.
