# Model-waning-of-vaccine-efficany-following-Swedish-data

I'm trying to model the impact of waning of vaccine efficacy, observed in the Swedish data in [1] on european vaccination data from ECDC [2].

The goal is to get an estimate of the % of the population per NUTS region who are protected from symptomatic COVID-19 after vaccination.

The code is in the notebook, I've done this for Poland, but there's data for all European countries, just change the country code and check 
which regions have a denominator.

Model
- Group data by Region, VaccineType and WeekDelayGroup (as in the paper's intervals)
- For each group repeat 1000 times with different seeds:
  - Fit Normal(mu, sigma) so that it's centered around mean efficacy for a given group and 95% CI fits the group's CI - treat this distribution as population vaccine efficacy prob. distribution
  - Draw p ~ Normal(mu, sigma) for every person who got a second dose in the given group's week - treat p as personal vaccine efficacy probability
  - Draw success for each person from Bernoulli(p) count successes and treat them as the number of people from this group whose vaccine remains effective
- Get the median value from the repetitions, add J&J vaccinated with the single dose, normalize by ECDC's denominator column for each region
- Plot the values on the map - per region using Eurostats NUTS json data.

Caveats:
- This does not corrected for already distributed third doses because ECDC does not include such data (would be nice tho).
- The Swedish study does not include data for J&J vaccine, so I'm assuming it's 100% efficient without vaning, that's a mistake - as a result 
we're getting an over-estimate of vaccine efficacy. I'd welcome a PR which fixes that and provides some estimates for J&J from other papers.
- I'm assuming first doses have zero efficacy, because it's hard to track how many ppl who received first-doses are counted for second doses and when.
- I'm assuming there's zero efficacy of second dose in the first 14 days, I'd welcome a PR that changes that to some reasonable estimate with a citation.
- Some countries have other vaccines than Pfizer/Moderna/AZ/J&J, we're ignoring them now - treating as zero efficacy, I'd welcome a PR that fixes that.

[1] https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3949410

[2] https://www.ecdc.europa.eu/en/publications-data/data-covid-19-vaccination-eu-eea
