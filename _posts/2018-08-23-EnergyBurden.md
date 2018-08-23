---
layout: post
title: How Does Energy Burden Affect Low-Income Populations in the U.S.?
tags: [energy burden, lollipop chart, low income, map, R]
---

I recently attended the American Council for an Energy-Efficient Economy (ACEEE) [Summer Study on Energy Efficiency in Buildings conference](http://aceee.org/conferences/2018/ssb).  This year, ACEEE incorporated a topic panel-Energy Efficiency and Equity: Addressing the Underserved-that revolved around efforts to reach underserved households with energy efficiency.  And after attending a couple of this panel's talks, I was introduced to this concept of energy burden.

Energy burden is generally defined as the fraction of income a household spends on energy services (_e.g._, electricity and gas).  In an ideal world, a household's energy burden would be quite low; however, a quick Google search for "energy burden" reveals numerous studies indicating that low-income and underserved populations tend to spend significantly more of their precious income on energy services

In this post, I will explore energy burden in the United States at the State level, and then drill down to see how energy burden differs by income level and home ownership type (_i.e._, owner or renter).

### Data and Methodology
In looking for energy burden data, I came across the [US Department of Energy's Low-Income Energy Affordability Data (LEAD) Tool](https://catalog.data.gov/dataset/clean-energy-for-low-income-communities-accelerator-energy-data-profiles-2fffb).  This "tool" is actually a set of interactive spreadsheets that allow the user to drill all the way down to the city level (by region) and explore number of households at different income levels, number of homeowners vs. renters, average monthly energy expense, annual income, and energy burden.  

The State-level data set I used for this analysis is for the year 2015 and can be downloaded [here](https://openei.org/doe-opendata/dataset/9dcd443b-c0e5-4d4a-b764-5a8ff0c2c92b/resource/7f188556-abeb-4e37-a0a1-5c2eb45ea72b/download/stateall2015.xlsb) (as of August 22, 2018).  While it would be nice to have data for multiple years, or at least for a more recent year, this particular data set contains everything we need to look into energy burden for 2015.  Additionally, because these data are contained in an analysis spreadsheet, I was able to reproduce in large part the original LEAD analysis.  (The only difference is that the LEAD tool forces users to analyze the data by building age, number of units, or heating fuel, which I ignored for this anslysis.  Therefore, the LEAD analysis incorporated intermediate weightings to account for those subsets which I did not include.)

The following code loads the R libraries I will use for this analysis, reads in the raw data set as a .csv file (I manually converted the "STATE" worksheet in the original Excel spreadsheet into a .csv), and performs the initial data munging.  Everything is commented to explain what each step does.

```R
library(tidyverse)
library(ggthemes)
library(openintro)
library(fiftystater)

# Read in and munge data to get it in a usable form
data <- read_csv('state_all_2015_energyburden.csv') %>% # read in the data
  select(STATE,
         `OWNER 0-30%`:`RENTER E_1`) %>% # select only the needed columns
  left_join(read_csv('energyburden_state_mapping.csv'),
            by = "STATE") %>% # merge in the state mappings from numbers to abbreviations
  select(-STATE) %>% # delete the original column containing state numbers
  select(STATE_NAME,
         `OWNER 0-30%`:`RENTER 100%+`,
         `OWNER H 0-30%`:`RENTER E 100%+`,
         `OWNER H 0-30%_1`:`RENTER E_1`) %>% # select only the needed columns
  rename(`OWNER E_TOT` = `OWNER E_1`,
         `RENTER E_TOT` = `RENTER E_1`) %>% # rename columns for future use
  gather(Variable,
         value,
         `OWNER 0-30%`:`RENTER E_TOT`) %>% # convert the data from wide to long
  mutate(median_income = ifelse(grepl("0-30%", Variable),
                                "0-30%",
                                ifelse(grepl("30-50%", Variable),
                                       "30-50%",
                                       ifelse(grepl("50-80%", Variable),
                                              "50-80%",
                                              ifelse(grepl("80-100%", Variable),
                                                     "80-100%",
                                                     ifelse(grepl("100%+", Variable),
                                                            "100%+",
                                                            "All"))))), # create column for median income
         Variable_Name = str_trim(str_split(Variable,
                                            "[0-9]+",
                                            simplify=TRUE)[,1]),
         Variable_Name_Appended = ifelse(grepl("_1", Variable),
                                         "_1",
                                         ""),
         Variable_2 = str_trim(paste0(Variable_Name,
                                      Variable_Name_Appended))) %>% # create variable name without median income
  select(state = STATE_NAME,
         median_income,
         variable = Variable_2,
         value) %>% # select and rename needed columns
  mutate(state = tolower(abbr2state(state))) %>% # convert state abbreviations to full names
  filter(median_income != "All") # ignore a median_income level that is not useful

# make median_income a factor and reorder the levels
data$median_income <- factor(data$median_income,
                                levels = c("0-30%", "30-50%", "50-80%",
                                           "80-100%", "100%+"))
# make state a factor for reodering in future plots
data$state <- as.factor(data$state)
```

### Analysis
The analyses conducted in the plots below largely replicate the analyses from the original LEAD spreadsheet (located in the "CHARTS" worksheet), with the aforementioned caveats.  In this part of the analysis I've created four separate data frames that calculate energy burden by State, income level, and ownership type; State and income level; State and ownership type; and State, respectively.  All of the aggregations were based on the share of households represented in each subgroup.

```R
# Process data to properly calculate energy burden at State, income, and type level
# This methodology is based largely on the analysis provided in the original 
# data source's spreadsheet
state_income_type_results <- data %>%
  group_by(state, median_income) %>%
  summarise(housing_units_owner = sum(value[variable == "OWNER"]),
            housing_units_renter = sum(value[variable == "RENTER"]),
            annual_income_owner = (sum(value[variable == "OWNER H_1"]) /
                                     sum(value[variable == "OWNER H"])),
            annual_income_renter = (sum(value[variable == "RENTER H_1"]) /
                                      sum(value[variable == "RENTER H"])),
            monthly_energy_expense_owner = (sum(value[variable == "OWNER E_1"]) /
                                              sum(value[variable == "OWNER E"])),
            monthly_energy_expense_renter = (sum(value[variable == "RENTER E_1"]) /
                                               sum(value[variable == "RENTER E"]))) %>% 
  mutate(energy_burden_owner = (12 * monthly_energy_expense_owner /
                                  annual_income_owner),
         energy_burden_renter = (12 * monthly_energy_expense_renter /
                                   annual_income_renter)) %>% 
  gather(variable, value, housing_units_owner:energy_burden_renter) %>% 
  mutate(owner_renter = ifelse(grepl("owner", variable), "Owner", "Renter"),
         variable = gsub("_renter|_owner", "", variable)) %>% 
  spread(variable, value)

# Calculate energy burden as function of State and income only
# Weight energy_burden by number of households per income group in that State
state_income_results <- state_income_type_results %>%
  group_by(state, median_income) %>% 
  summarise(annual_income = (sum(annual_income * housing_units /
                                   sum(housing_units))),
            monthly_energy_expense = (sum(monthly_energy_expense * housing_units /
                                            sum(housing_units))),
            energy_burden = (monthly_energy_expense * 12 /
                               annual_income))

# Calculate energy burden as function of State and type level only
# Weight energy_burden by number of households per ownership type in that State
state_type_results <- state_income_type_results %>%
  group_by(state, owner_renter) %>% 
  summarise(annual_income = (sum(annual_income * housing_units /
                                   sum(housing_units))),
            monthly_energy_expense = (sum(monthly_energy_expense * housing_units /
                                            sum(housing_units))),
            energy_burden = (monthly_energy_expense * 12 /
                               annual_income))

# Calculate energy burden as function of income level only
# Weight energy_burden by number of households in that income group
income_results <- state_income_type_results %>%
  group_by(median_income) %>% 
  summarise(annual_income = (sum(annual_income * housing_units /
                                   sum(housing_units))),
            monthly_energy_expense = (sum(monthly_energy_expense * housing_units /
                                            sum(housing_units))),
            energy_burden = (monthly_energy_expense * 12 /
                               annual_income))

# Calculate energy burden as function of State only
# Weight energy_burden by number of households in that State
state_results <- state_income_type_results %>%
  group_by(state) %>% 
  summarise(annual_income = (sum(annual_income * housing_units /
                                   sum(housing_units))),
            monthly_energy_expense = (sum(monthly_energy_expense * housing_units /
                                            sum(housing_units))),
            energy_burden = (monthly_energy_expense * 12 /
                               annual_income))
```

### Results
We'll first look at the average energy burden at the State level, regardless of income or ownership type.  The plot below shows that Maine had the highest average energy burden in the US in 2015, with nearly 6% of the average household's annual income going toward energy services.  In contrast, California has the lowest average energy burden at just under 2%.

![Average energy burden by state in 2015 for all income groups and property ownership types.]({{http://rahosbach.github.io}}/img/energy_burden/energy_burden_by_state_2015.svg)

Next we'll take a look at energy burden across the US, accounting for both household income level and ownership type.  Note that a household's income level (labelled along the right side of the maps) is binned as a fraction of the median annual income of all households in that area.  For example, if an area's median annual income is \$100,000, then households with an annual income up to \$30,000 would be included in the 0-30% income bin.

![Average energy burden by state, income group, and ownership type in 2015.]({{http://rahosbach.github.io}}/img/energy_burden/energy_burden_by_ownership_income_2015.svg)

This plot makes it clear to see that it is in fact the low-income populations in the US that are experiencing the highest energy burden (see the top row of maps).  In the worst cases, such as the states in New England, owners making no more than 30% of their area's median income are paying upwards of 30% of their annual income on energy services, on average!  This likely speaks to the energy intensity of basic necessities such as refrigeration, heating and cooling, and lighting consuming a large fraction of a low-income household's income, as opposed to the relatively minimal additional energy cost (as a fraction of household income) of more discretionary items that higher-income households are more likely to own.  For households having annual incomes of at least 80% of their area's median annual income, regardless of ownership type, energy burden looks to be a minimal issue (when aggregated at the State level).

It's worth noting that owners tend to have higher energy burdens than renters.  This may be due to owners likely having more discretionary "toys" at their homes, compared to renters.

Finally, based on the results from the previous plot, let's look at the national-average energy burden as a function of income level.  The plot below verifies that not only do the lowest-income populations have the highest energy burden, but those making 0-30% of their area's median income actually have more than double the energy burden of those making at least 30% of their area's median income!

![Average energy burden by income group for 2015.]({{http://rahosbach.github.io}}/img/energy_burden/energy_burden_by_income_2015.svg)

### Conclusions
The data in the 2015 LEAD dataset seem to indicate that low-income populations do tend to spend more of their income on energy services than higher-income populations.  This is the type of issue that continues to keep lower-income populations from being able to escape the chain of poverty.  Certain energy services are very necessary, including lighting, refrigeration, and heating and air conditioning.  It is likely that such services are what contribute the most to low-income population's high energy burdens.  Moving forward, targeting low-income populations with properly-run efficiency incentives and programs could help to alleviate this issue.

### Appendix
This post is partially motivated by an online course I am taking entitled, "Introduction to R for Journalists: How to find great stories in data."  Part of the requirements for this course is to generate a document using R Markdown and publish it.  This post was originally created in R Markdown (see [this HTML file](https://github.com/rahosbach/rahosbach.github.io/blob/master/_data/energyburden.html) that was generated from the original R Markdown); however, I had issues getting an R Markdown document to render properly on a Jekyll-based blog.  Therefore, I converted this post to simple markdown language for getting this post up. 
