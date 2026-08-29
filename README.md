# Wisconsin_TID_Analysis_2025
# Methodology

## What we analyzed

The Milwaukee Business Journal analyzed the **Wisconsin Department of Revenue's 2025 Tax Incremental District (TID) Certification Report** to identify active TIDs with the largest increases in property value and to examine how those gains are distributed across Wisconsin.

The certification report provides annual TID values for municipalities, counties, school districts, technical colleges and special districts. For each TID, it reports the property's **base value**, **current value** and **increment**.

Our primary measure is the **increment**: the difference between a TID's current equalized property value and the equalized value of the property in its base year.

In simple terms:

`Increment = Current Value − Base Value`

We use the increment to identify TIDs with the largest **absolute increases in equalized property value above their original tax base**.

The analysis does not treat increment as a measure of profitability, tax revenue, economic impact or return on public investment. Those questions require additional data. But increment remains central to how tax-increment financing works. 

When a TID is created, the property inside it has a base value; as development occurs and property values rise, the increase above that base — the increment — forms the tax base on which TID revenue is generated to pay eligible project costs. Measuring the increment therefore gives us a consistent way to see how much additional equalized property value has accumulated within each district and where that growth is concentrated across Wisconsin.

---

## 1. Start with the raw 2025 data

We imported the DOR's raw 2025 certification data into Python using pandas.

The starting dataset contains records for active TIDs and, in some cases, more than one record for the same district.

Before calculating rankings or statewide totals, we cleaned the data and established a consistent district-level unit of analysis.

---

## 2. Establish a unique identifier

We used the combination of **Municipality + TID number** as the district-level identifier.

We first tested whether each combination appeared only once in the raw data.

It did not.

The repeated combinations were not ordinary duplicate records. They represented **multi-county TIDs**, which appear in the certification report once for each county in which the district is located. The property's valuation data is divided among those county-level records.

Rather than dropping those records — which would undercount the value of multi-county districts — we treated each repeated Municipality + TID combination as one district and consolidated its underlying county records.

We also checked each Municipality + TID combination for conflicting base years to ensure that records being combined referred to the same TID.

---

## 3. Collapse multi-county TIDs

We consolidated the raw data to **one record per Municipality + TID combination**.

For each district, we:

* Summed **Current Value** across its county records.
* Summed **Base Value** across its county records.
* Summed the reported **Increment** across its county records.
* Preserved the district's **Base Year**.
* Combined county, municipal-code and municipality-type fields where multiple values were present.

This produced a clean, district-level dataset containing **1,422 active TIDs**.

We then verified that Municipality + TID was unique in the cleaned dataset.

This step is important because a multi-county TID should count as **one district**, not two or more districts simply because the state report splits its valuation across county records.

---

## 4. Validate the cleaned data

Before analyzing the districts, we ran several checks.

### Increment check

We independently calculated:

`Calculated Increment = Current Value − Base Value`

We then compared that figure with the increment reported in the source data and flagged any discrepancies.

### Missing-data check

We checked the fields required for the analysis for missing values:

* Municipality
* TID
* Base Year
* Current Value
* Base Value
* Increment

### Property-value check

We also checked for negative Current Value or Base Value observations.

Only after completing these checks did we proceed with the analysis.

---

# Measuring TID Performance

## 5. Rank TIDs by absolute increment

Our primary ranking sorts all 1,422 active TIDs by **Increment**, from largest to smallest.

We selected the top 25 for the primary ranking and retained each district's:

* County
* Municipality
* TID number
* Base year
* Base value
* Current value
* Increment

The ranking measures **absolute dollars of property-value increment**, not percentage growth. A district that increased from $10 million to $50 million has grown by 400%, but it has a $40 million increment. A district that increased from $500 million to $1 billion has grown by 100%, but has a $500 million increment and therefore ranks much higher in our analysis.

---

## 6. Measure statewide concentration

We then asked how much of Wisconsin's total TID increment is concentrated among its largest districts.

We calculated the cumulative increment for the:

* Top 10 TIDs
* Top 25 TIDs
* Top 50 TIDs
* Top 100 TIDs

We then calculated each group's share of the total statewide increment:

`Group Share = Group Increment ÷ Total Statewide Increment × 100`

This shows whether property-value increment is broadly distributed across Wisconsin's TIDs or concentrated in a relatively small number of very large districts.

---

## 7. Establish a statewide benchmark

We calculated the **median increment across all 1,422 active TIDs**.

We also calculated the median increment among the top 25 and top five TIDs.

We then compared those medians with the statewide median by calculating how many times larger the top-25 and top-five medians were than the median active TID.

We use the median because a small number of exceptionally large TIDs can heavily influence the mean. The median provides a clearer picture of the typical district.

---

## 8. Identify TIDs below their original tax base

We separately identified TIDs with **negative increments**.

A negative increment means the district's reported current equalized property value is below its base-year value.

For those districts, we calculated:

* Number of TIDs with negative increments.
* Share of all active TIDs represented by those districts.
* Median negative increment.
* Median decline in property value from the original tax base.

These districts were not treated as producing positive increment. They provide a counterpoint to the high-performing districts by showing where current property value had fallen below the original base.

---

## 9. Examine the middle of the distribution

We did not want the largest 100 districts to tell the entire story.

So we isolated **positive-increment TIDs outside the top 100** and calculated:

* Number of districts.
* Combined increment.
* Share of statewide increment.
* Median increment.
* Mean increment.

This provides a view of the broader population of active TIDs beyond the state's largest districts.

---

# Examining Age and Longevity

## 10. Identify the oldest active TIDs

The certification report's **Base Yr** field identifies the year a TID was created.

We first identified the earliest base year among active 2025 TIDs and examined all districts created in that year.

We then identified the **25 oldest active TIDs** and calculated their age as of 2025:

`Age in 2025 = 2025 − Base Year`

This gives us a measure of which districts have remained active the longest.

---

## 11. Identify high-value older TIDs

Age alone does not tell us how much property value a district has accumulated.

We therefore created a separate analysis of active TIDs with a **Base Yr before 2000**.

We counted those districts, identified the range of base years they represent and ranked them by absolute increment.

We selected the 25 pre-2000 TIDs with the largest increments.

This allows us to answer a more specific question:

> Which older active TIDs have accumulated the largest increases in property value?

We also report each district's age, base value, current value and increment.

---

# Geographic Analysis

## 12. Rank municipalities by cumulative increment

We shifted from individual TIDs to municipalities to examine where property-value increment is concentrated geographically.

Using the cleaned, one-record-per-TID dataset, we grouped districts by **County + Municipality** and calculated:

* Number of active TIDs.
* Total increment across those TIDs.

We then ranked municipalities by cumulative increment and identified the top 10.

This means a municipality can rank highly because it has several substantial TIDs, rather than simply because it contains one exceptionally large district.

---

## 13. Rank counties by cumulative increment

We also ranked Wisconsin counties by total TID increment.

For this calculation, we deliberately returned to the **original county-level records** rather than the cleaned TID-level dataset.

This is because the county split in the raw certification report is meaningful for a county-level analysis. A multi-county TID contributes its reported increment to each county's underlying record.

We grouped the raw records by county and summed the reported increment.

The county analysis therefore answers a different question from the municipality analysis:

> **Where is TID property-value increment concentrated at the county level?**

We also counted the underlying TID records represented in each county.

---

# What the Analysis Does — and Does Not — Show

The analysis is designed to measure **scale, concentration, distribution and longevity of property-value increment within Wisconsin's active TID portfolio**.

It can show:

* Which TIDs have the largest absolute increments.
* How concentrated statewide increment is among the largest districts.
* How the typical TID compares with the largest districts.
* How many active TIDs are below their original tax base.
* How much increment exists outside the largest 100 districts.
* Which older TIDs have accumulated the largest increments.
* Which municipalities and counties contain the greatest cumulative increments.

It does **not** establish:

* How much tax revenue a TID generated.
* How much a municipality spent on a TID.
* The amount of TID debt or outstanding project costs.
* The size of developer incentives.
* The return on public investment.
* How many jobs a TID created.
* How much private investment occurred.
* Whether development would have happened without TIF.
* Whether a TID caused the increase in property value.

A large increment means that the equalized property value within a TID is substantially higher than its original tax base. **It does not, by itself, prove that the TID caused that increase or that the district produced a positive fiscal or economic return.**

For that reason, we use **"increment," "property-value increase" and "property-value growth"** when describing the results rather than treating increment as synonymous with economic performance.

## In short

We started with the state's 2025 TID certification data, established **Municipality + TID** as the district-level identifier, consolidated multi-county districts, validated the financial fields and arrived at a clean dataset of **1,422 active TIDs**.

From there, we ranked districts by absolute increment and examined the results from several angles: **statewide concentration, median performance, negative increments, the middle of the distribution, district age and geographic concentration.**

The goal is to show not just **which TIDs are biggest**, but **how property-value increment is distributed across Wisconsin's TIF system and how that picture changes when we look at size, age and geography.**
