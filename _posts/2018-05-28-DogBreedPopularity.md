---
layout: post
title: Which Dog Breeds are the Most Popular in the United States?
tags: [AKC, Google Trends, dog, breed, ggplot, R]
---

###Introduction
Growing up, dogs were always a part of my life.  From purebred German Shepherds and labradors to various mixed breeds, I was never left wanting for a dog to play with.  Even now, my family has a nine-year-old Basset Hound named Danny, and it would be an understatement to say that Danny is a magnet for attention when we take him on walks.  Sadly, it's not a common occurrence to see a Basset Hound nowadays.  But, interestingly, one of the most common comments we hear from people who want to meet Danny is, "My family had a Basset Hound when we grew up!"  My curiosity piqued, this post is a look into dog breed popularity over time.

###Approach
Popularity (of anything) can be measured in countless ways.  For dog breeds, a good first step is to look into the [American Kennel Club (AKC)](https://www.akc.org/) records.  The AKC has a voluntary registry that [currently recognizes 190 purebred dog breeds](https://www.akc.org/press-center/articles/breeds-by-year-recognized/).  The raw AKC data are not publicly available, but the AKC does produce reports of breed popularity over time—in this case, breed popularity equates to the number of registered purebred dogs.  Issues with using the AKC registry data include:
1) **Not all dogs are purebred.**  In fact, mixed-breed dogs typically have a lower purchase price than purebreds, and many believe that mixed-breeds have better general health than purebreds.
2) **Not all owners register their purebreds with the AKC.**  While the AKC has a [detailed fee schedule](https://www.akc.org/register/information/fee-schedule/) for various types of dog and puppy litter registrations, the cheapest current cost is $30 to register a purebred dog with the AKC using the online application.
3) **Popularity can mean more than the number of dogs owned.**  Even if every purebred _and_ mixed-breed dog were registered with the AKC and therefore included in the AKC data reports, an argument can be made that dog ownership in itself does not represent popularity.  Perhaps, instead, popularity should be measured by actual interest in a particular breed over time.

Following from issue #3 above, another approach to measuring the popularity of a dog breed is to look at [Google search popularity over time](https://trends.google.com/trends/?geo=US).  This should provide a good sense of what dog breeds the U.S. population is most interested in at any given time.  Issues with using Google Trends data include:
1) **Searches are heavily influenced by the news.** For instance, I would expect that "pit bull" is a consistently-popular search term over the last few years given the prevalence of news stories highlighting pit bull biting incidents.
2) **Popularity can mean more than the population's general interest at any given time.** It may not be fair to rate a breed's popularity on the population's Google searches for a given time period, especially given #1.  After all, number of dogs registered for a particular breed shows more commitment than simple Google searches for that breed, and one could argue that commitment to a particular breed over time is a good indicator of popularity.

###Data Exploration
