---
layout: post
title: Which Dog Breeds are the Most Popular in the United States?
tags: [AKC, Google Trends, dog, breed, ggplot, bump chart, heatmap, R]
---

Growing up, dogs were always a part of my life.  From purebred German Shepherds and Labrador Retrievers to various mixed breeds, I was never left wanting for a dog to play with.  Even now, my family has a nine-year-old Basset Hound named Danny, and it would be an understatement to say that Danny is a magnet for attention when we take him on walks.  Sadly, it's not a common occurrence to see a Basset Hound nowadays.  But, interestingly, one of the most common comments we hear from people who want to meet Danny is, "My family had a Basset Hound when we grew up!"  My curiosity piqued, this post is a look into dog breed popularity over time.

### Approach
Popularity (of anything) can be measured in countless ways.  For dog breeds, a good first step is to look into the [American Kennel Club (AKC)](https://www.akc.org/) records.  The AKC has a voluntary registry that [currently recognizes 190 purebred dog breeds](https://www.akc.org/press-center/articles/breeds-by-year-recognized/).  The raw AKC data are not publicly available, but the AKC does produce reports of breed popularity over time—in this case, breed popularity equates to the number of registered purebred dogs.  Issues with using the AKC registry data include:
1. **Not all dogs are purebred.**  In fact, mixed-breed dogs typically have a lower purchase price than purebreds, and many believe that mixed-breeds have better general health than purebreds.
2. **Not all owners register their purebreds with the AKC.**  While the AKC has a [detailed fee schedule](https://www.akc.org/register/information/fee-schedule/) for various types of dog and puppy litter registrations, the cheapest current cost is $30 to register a purebred dog with the AKC using the online application.
3. **Popularity can mean more than the number of dogs owned.**  Even if every purebred _and_ mixed-breed dog were registered with the AKC and therefore included in the AKC data reports, an argument can be made that dog ownership in itself does not represent popularity.  Perhaps, instead, popularity should be measured by actual interest in a particular breed over time.

Following from issue #3 above, another approach to measuring the popularity of a dog breed is to look at [Google search popularity over time](https://trends.google.com/trends/?geo=US).  This should provide a good sense of what dog breeds the U.S. population is most interested in at any given time.  Issues with using Google Trends data include:
1. **Searches are heavily influenced by the news.** For instance, I would expect that "Pit Bull" is a consistently-popular search term over the last few years given the prevalence of news stories highlighting pit bull biting incidents.  (It's worth noting that "Pit Bull" is not actually an AKC-recognized breed, but the term is used colloquially to refer to Bull Terriers, American Staffordshire Terriers, and Staffordshire Bull Terriers.)
2. **Popularity can mean more than the population's general interest at any given time.** It may not be fair to rate a breed's popularity on the population's Google searches for a given time period, especially given #1.  After all, number of dogs registered for a particular breed shows more commitment than simple Google searches for that breed, and one could argue that commitment to a particular breed over time is a good indicator of popularity.

### Data Exploration
#### AKC Data
The AKC data came from the [_Most Popular Dog Breeds - Full Ranking List_ webpage](https://www.akc.org/expert-advice/news/most-popular-dog-breeds-full-ranking-list/) (published on March 28, 2018) that provides the national popularity ranking of each AKC dog breed from 2013-2017, and a [_Reader's Digest_ webpage](https://www.rd.com/culture/most-popular-dog-breeds-in-every-state/) (published on March 21, 2018) that indicates the top three breeds by state (based on AKC data).

The national rankings data contain all 190+ dog breeds that the AKC currently recognizes.  To make the data more digestible, I decided to use a bump chart (inspired by Dominik Koch's [blog post on bump charts](https://dominikkoch.github.io/Bump-Chart/)) to show how the rankings of the top 25 dog breeds changed from 2013 to 2017:
![Top 25 dog breeds by year from 2013 to 2017 according to national AKC data]({{http://rahosbach.github.io}}/img/dog_breed_popularity/National_Rank_Top25.png)

There are some interesting trends that appear in this bump chart:
1. Labrador Retrievers, German Shepherds, and Golden Retrievers, respectively, are the most popular dog breeds for all years.
2. French Bulldogs moved up seven places from 2013 to 2017 (11th to 4th) and Pembroke Welsh Corgis moved up nine places (from 24th to 15th).  While both of these breeds are cute and small (important given the recent trends in urbanization), perhaps the most important key factors for their rising popularity are celebrities and social media.  For instance, Queen Elizabeth II is famous for owning Welsh Corgis and there are [at least two corgis currently on Instagram with over 200,000 followers](https://www.thedailybeast.com/the-secrets-of-instagrams-most-famous-dogs).  Similarly, the likes of Reese Witherspoon, Lady Gaga, and Hugh Jackman have all shared their affection for French Bulldogs on social media, and the recent advertising trend of including Frenchies hasn't hurt their popularity either. 
3. Shih Tzus moved down five places from 2013 to 2017 (15th to 20th), and Boxers and Doberman Pinschers each moved down four places (from 7th to 11th and from 12th to 16th, respectively).
4. Chihuahuas fell out of the top 25 in 2015 after being 22nd and 24th in popularity in 2013 and 2014, respectively.
5. Bernese Mountain Dogs reached 25th in popularity in 2017 after being out of the top 25 for the previous 4 years.

The state rankings data only provide the top three most popular dog breeds for each state.  (Note that second- and third-most-popular breeds were not provided for Kentucky, and no data were provided for Louisiana.)  Therefore, I did not need to trim down the data in this case.  Instead, I plotted all of the data as a heatmap:
![Top 3 dog breeds by state according to AKC data]({{http://rahosbach.github.io}}/img/dog_breed_popularity/State_Rank.png)

Interesting takeaways from this heatmap representation of the data include:
1. The top three breeds for each of the 50 states are represented by only seven dog breeds.
2. Not surprisingly (based on the national rankings data shown above), Labrador Retrievers, German Shepherds, and Golden Retrievers are most popular at the state level.  In fact, for 30 states (60%) the top three breeds are represented by some combination of Labrador Retrievers, German Shepherds, and Golden Retrievers.
3. Beagles are part of the top 3 for 11 states (22%), and they are the most popular breed in Kentucky (the second- and third-place breeds were not provided for Kentucky).
4. Surprisingly, Rottweilers are the third most popular breed in Hawaii according to the AKC, and Hawaii is also the only state where French Bulldogs are the most popular breed.

Before wrapping up this section on the AKC data, I must give a shout-out for my fellow Basset Hound lovers out there.  According to the AKC, Basset Hounds moved from the 39th most popular breed in 2013 to the 42nd most popular breed by 2017.  They may not be first in terms of AKC registrations, but as my Basset Danny would tell me (with much aplomb): "That just means we're more unique."

#### Google Trends Data
The [Google Trends](https://trends.google.com/trends/?geo=US) Web interface allows one to compare the Google search popularity for up to five different dog breeds at a time going back to 2004.  As previously mentioned, these data are far more likely to be significantly influenced by current events than the annual AKC registration data.  Given that, I would expect "Pit Bull" to be a very popular search term.  But in addition to Pit Bulls, let's also take a look at some of the other breeds that stood out from the AKC data: Beagles, French Bulldogs, Chihuahuas, and Pembroke Welsh Corgis:

<script type="text/javascript" src="https://ssl.gstatic.com/trends_nrtr/1435_RC11/embed_loader.js"></script> <script type="text/javascript"> trends.embed.renderExploreWidget("TIMESERIES", {"comparisonItem":[{"keyword":"/m/0h5xg","geo":"US","time":"2004-01-01 2018-05-30"},{"keyword":"/m/01dj7","geo":"US","time":"2004-01-01 2018-05-30"},{"keyword":"/m/038wt3","geo":"US","time":"2004-01-01 2018-05-30"},{"keyword":"/m/0khhs","geo":"US","time":"2004-01-01 2018-05-30"},{"keyword":"/m/02kh2h","geo":"US","time":"2004-01-01 2018-05-30"}],"category":0,"property":""}, {"exploreQuery":"date=all&geo=US&q=%2Fm%2F0h5xg,%2Fm%2F01dj7,%2Fm%2F038wt3,%2Fm%2F0khhs,%2Fm%2F02kh2h","guestPath":"https://trends.google.com:443/trends/embed/"}); </script>

Key takeaways from the Google Trends data are:
1. As expected, the search term "Pit Bull" has been widely popular dating back to at least 2004.  Setting aside the politics of Pit Bull biting incidents, it's no surprise that many people have searched for information on the breed given the news coverage Pit Bulls receive.
2. Searches for Chihuahuas peaked in October 2008 and have since been in decline.
3. Beagles received a huge spike in Google search popularity in February 2008, which corresponds to [the first time a Beagle had won Best in Show at the Westminster Dog Show](http://www.stltoday.com/lifestyles/pets/look-back-uno-the-belleville-beagle-becomes-the-top-dog/collection_78e46775-1ddf-503a-a740-b489fbdf2008.html).
4. Both French Bulldogs and Pembroke Welsh Corgis are seeing increasing Google search popularity over time, which is to be expected due to their recent "celebrity" status.

Finally, let's take a look at how the top three most popular breeds according to AKC registrations compare to Pit Bulls, the most popular breed we've seen according to Google Trends data:

<script type="text/javascript" src="https://ssl.gstatic.com/trends_nrtr/1435_RC11/embed_loader.js"></script> <script type="text/javascript"> trends.embed.renderExploreWidget("TIMESERIES", {"comparisonItem":[{"keyword":"/m/0h5xg","geo":"US","time":"2004-01-01 2018-05-30"},{"keyword":"/m/0km3f","geo":"US","time":"2004-01-01 2018-05-30"},{"keyword":"/m/0km5c","geo":"US","time":"2004-01-01 2018-05-30"},{"keyword":"/m/01t032","geo":"US","time":"2004-01-01 2018-05-30"}],"category":0,"property":""}, {"exploreQuery":"date=all&geo=US&q=%2Fm%2F0h5xg,%2Fm%2F0km3f,%2Fm%2F0km5c,%2Fm%2F01t032","guestPath":"https://trends.google.com:443/trends/embed/"}); </script>

From this plot it's clear to see that starting in 2009, Pit Bulls are more popular than Labrador Retrievers, German Shepherds, and Golden Retrievers in terms of Google searches.  This is in spite of Staffordshire Bull Terriers and American Staffordshire Terriers (common AKC breeds labeled as Pit Bulls) ranking 79th and 76th, respectively, according to the AKC registration data.

### Conclusion
The purpose of this post was to gain some insight into which dog breeds are the most popular in America.  While my data exploration did not produce a clear, absolute answer as to which dog breed reigns supreme, I did gain some insight into which set of breeds may be considered more popular than others.  It's pretty clear that Labrador Retrievers, German Shepherds, and Golden Retrievers are the most popular breeds in terms of AKC registrations.  However, these are not necessarily the most popular breeds in our pop culture, as shown by the dominance of the term "Pit Bull" in Google searches.  Additionally, one can make a strong argument that a large proportion of dogs are mixed-breed and/or were purchased from shelters.  Such dogs are not accounted for in the AKC data.  If it were possible to obtain dog adoption data from shelters around the U.S., that would help to shed light on this issue.  Reinforced here is the lesson that popularity can be defined and measured in many different ways.  Therefore, one must be diligent when quantifying comparisons of popularity to be crystal clear regarding the data sources and popularity metrics used to obtain results.

