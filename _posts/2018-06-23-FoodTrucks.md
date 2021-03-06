---
layout: post
title: How Safe are Food Trucks in Chico, CA?
tags: [Chico, food truck, Pareto chart, Tableau]
---

A couple months ago, my wife, sister, brother-in-law, and I were discussing what we'd like to go get for dinner that night.  After some ideas were tossed around that didn't gain much traction, I proposed the idea of grabbing food at the nearby taco truck.  My wife and I had eaten at this taco truck once before, and it was quite tasty.  But, to my surprise, my brother-in-law quickly vetoed the idea, saying that he doesn't eat at food trucks because he can't trust them to be sanitary.  Of course, this suggestion of his became perfect fodder for a blog post.

### Background
To be quite honest, this same issue has been tackled numerous times before.  Everyone knows that your average food truck produces really tasty—and generally quite affordable—food.  But, is it really a good idea to eat at these mobile food vendors?

For this post I wanted to hone in on Chico, CA, but it's worth mentioning some of the existing literature that has looked into this topic.  The results are varied, but here are some highlights:
1. The Institute of Justice analyzed more than 260,000 food inspection reports from restaurants, food trucks, and the like across seven major U.S. cities and published [a report in 2014](http://ij.org/wp-content/uploads/2015/03/street-eats-safe-eats.pdf).  They found that in every city they analyzed, **food trucks did as well or better than their brick-and-mortar restaurant counterparts**.  In six of the seven cities, food trucks were found to have less sanitation violations than restaurants at a statistically significant level.
2. An [article from the LA Times in 2016](http://www.latimes.com/local/california/la-me-food-trucks-20160518-snap-story.html) reported that **about 27% of food trucks received an inspection grade less than A over the past two years** (for comparison, only 5% of brick-and-mortar restaurants received less than an A).  Furthermore, more than 4% of food trucks inspected that year were forced to close (though, many were allowed to reopen upon reinspection).
3. An [April 2017 article in the Maine Law Review written by Crystal T. Williams](https://digitalcommons.mainelaw.maine.edu/cgi/viewcontent.cgi?referer=https://scholar.google.com/&httpsredir=1&article=1123&context=mlr) delves into the **broad landscape of food truck regulations** across the U.S., with some cities having strict regulations, some having very few regulations, and some in a period of regulatory transition.
4. An [August 2010 article from ConsumerSafety.org](https://www.consumersafety.org/news/food/are-food-trucks-worth-the-risk/) takes a more pragmatic approach to addressing the risk of food trucks.  While the article details some reasons why certain food trucks may be less sanitary than a brick-and-mortar restaurant (_e.g._, lack of space), the ultimate conclusion is that we should use common sense when deciding whether or not to purchase food from a given food truck.  Does the truck have its inspection label clearly displayed? Did the truck receive a good grade?  Do the food preparers wear gloves? etc. etc.

### Methodology
Why did I go into the perspectives regarding food trucks in a general sense?  Well, it's because Butte County does not provide online food inspection reports for food trucks (as far as I can tell).  However, Butte County does publish online food inspection reports for brick-and-mortar establishments.

After scraping food inspection reports from the [Butte County Public Health Department website](http://www.buttecounty.net/publichealth/EnvironmentalHealth/ConsumerProtection/FoodInspectionReports.aspx) (using R), I had food inspection reports from 2016 to June 2018 for 15 cities in Butte County (including Chico) and over 1,200 separate food establishments (including restaurants, convenience stores, bakeries, and the like).  None of these establishments are food trucks, but at the very least I obtained some interesting data to dig through.

### Results (for brick-and-mortar establishments)
Take a look at the following storyboard, which covers Butte County as a whole, and then drills down to look at how Chico establishments have fared over the last couple years.  Note that all of the slides are interactive, meaning you can hover over data points to see the actual values.

<iframe src="https://public.tableau.com/views/ButteCountyFoodInspectionReports/Story1?:showVizHome=no&:embed=true" width="650" height="860"></iframe>

### Conclusion
Well, I'm sorry to say that the short answer to our original question is that we simply do not know how food trucks in Chico, CA fare compared to their brick-and-mortar restaurant counterparts.  However, we've been able to take a look into the food inspection results for the brick-and-mortar establishments in Butte County as a whole, and Chico more specifically.  It's revealing to me how many well-known (and well-loved) Chico food establishments have received numerous minor (and sometimes major) violations over the last couple of years.  Should we really expect that the average food truck in Chico would perform worse than the results we've seen in the storyboard above?
