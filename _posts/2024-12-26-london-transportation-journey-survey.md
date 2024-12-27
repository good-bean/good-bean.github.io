---
layout: post
title: Data Analysis Report - London Transportation Journey Survey
categories: [Data Analysis, SQL]
excerpt: This report analyzes data provided by Transport for London (TFL) from their Rolling Origin and Destination Survey (RODS). RODS models typical passenger behaviors on the London Underground system, providing insights beyond standard usage statistics by including motivations for travel, such as commuting for work or leisure activities.
---

## Introduction

**Project Background:** This report analyzes data provided by Transport for London (TFL) from their Rolling Origin and Destination Survey (RODS). RODS models typical passenger behaviors on the London Underground system, providing insights beyond standard usage statistics by including motivations for travel, such as commuting for work or leisure activities.

**Purpose:** The objective of this analysis is to understand the volume and patterns of journeys made on the London Underground, identify the primary reasons for travel, and explore travel behaviors based on time periods and zones.

**Data source:** The data used in this analysis comes from the RODS dataset provided by TFL, focusing on daily journey counts, entry zones, time periods, and travel purposes.

## Data Preparation

* Aggregate total journeys  
* Group by zone  
* Filter for travel purpose  
* Group by time of day

## Exploratory Data Analysis (EDA)

Through this initial exploration, we aim to lay the groundwork for a more detailed analysis and ensure that we are well-prepared to address the project’s key questions. The insights gained during EDA will guide our approach to more complex statistical analyses and predictive modeling in the subsequent sections.

First we wrote a query to aggregate the sum total of journeys:

```sql
SELECT
  SUM(daily_journeys) AS total_journeys
FROM
  tfl.rods
```

This showed that there are about **4,878,330** journeys on a typical day. This total volume indicates a high level of daily activity.

Then we were curious to find the total journeys that originate from each zone. In particular, how many begin from *Zone 1*:

```sql
SELECT
  entry_zone AS origination_zone,
  SUM(daily_journeys) AS total_journeys
FROM
  tfl.rods
GROUP BY
  origination_zone
```

We saw that approximately **51.72%** of journeys start from a Zone 1 station, with **2,522,837  journeys**. The number of trips decreases as we move outward from the city center.

## Analysis

While the Exploratory Data Analysis (EDA) provided an initial understanding of the data’s structure and key trends, the focus of this report will shift to a more detailed analysis.

The questions we aimed to answer were:

* What are the peak travel periods on the London Underground?  
* What are the primary purposes of travel?  
* How do travel patterns vary by zone?  
* How do travel patterns on the London Underground vary by time of day and the purpose of the journey?  
* How do travel purposes vary across different entry zones of the London Underground?

**What are the peak travel periods on the London Underground?**

```sql
SELECT
  time_period AS time_of_day,
  SUM(daily_journeys) AS total_journeys
FROM
  tfl.rods
GROUP BY
  time_of_day
ORDER BY
  total_journeys DESC
```

**Insight:** The PM Peak period has the highest number of daily journeys (1,367,309), which makes up 28.0% of all trips. The AM Peak and Midday periods also show significant activity, with the combined total of other periods (Evening, Late, Early) accounting for 21% of all trips.

**What are the primary purposes for travel?**

```sql
SELECT
  origin_purpose AS start_point,
  SUM(daily_journeys) AS total_journeys
FROM
  tfl.rods
GROUP BY
  start_point
ORDER BY
  total_journeys DESC
```

**Insight:** The majority of journeys originate from Home (1.8 million) or Work (1.4 million), indicating that most people use the Underground for commuting. Hotel and Tourist purposes have the lowest counts, suggesting potential variations on weekends or warmer months.

**How do travel patterns vary by zone?**

```sql
SELECT
  origin_purpose AS start_point,
  destination_purpose AS end_point,
  SUM(daily_journeys) AS total_journeys
FROM
  tfl.rods
GROUP BY
  start_point,
  end_point
ORDER BY
  total_journeys DESC
```

**Insight:** The most common journey pairs are Home-Work (1.2 million) and Work-Home (1 million), supporting the primary use for commuting. A significant number of journeys have unknown purposes.

**How do travel patterns on the London Underground vary by time of day and the purpose of the journey?**

```sql
SELECT
  origin_purpose AS start_point,
  time_period AS time_of_day,
  SUM(daily_journeys) AS total_journeys
FROM
  tfl.rods
WHERE
  origin_purpose = 'Home'
  OR origin_purpose = 'Work'
GROUP BY
  start_point,
  time_of_day
ORDER BY
  start_point ASC,
  time_of_day ASC
```

**Insight:** People travel from Home and Work at the expected times; AM Peak and PM Peak, respectively. Generally, people travel from home in the morning and from work in the afternoon/evening.

**How do travel purposes vary across different entry zones of the London Underground?**

```sql
SELECT
  origin_purpose AS start_point,
  entry_zone,
  SUM(daily_journeys) AS total_journeys
FROM
  tfl.rods
GROUP BY
  start_point,
  entry_zone
ORDER BY
  entry_zone ASC,
  total_journeys DESC
```

**Insight:** Starting from Zone 1 and ending at Zone 5, the number of trips for each, Home and Work, decreases. However, in Zone 1, Work outranks Home, then beginning in Zone 2, Home outranks Work by at least 1\. All other purposes stay relatively in the same place, rank-wise.

## Results

Our objective was to uncover significant patterns and insights regarding passenger behavior on the London Underground system. By leveraging statistical techniques and predictive modeling, we aimed to answer key questions derived from our initial exploratory data analysis (EDA).

The primary purpose of travel on the Underground system is commuting to work and home. As you move from Zone 1 to Zone 5, the number of trips decreases progressively. Moreover, the data aligns with typical workday schedules, with trips originating from home and ending at work in the morning, and then reversing from work to home in the afternoon and evening.

We would recommend exploring passenger congestion patterns to see what areas of improvement could be implemented to work *with* the flow of passenger travel on a typical day. The congestion is in reference to the terminals through which passengers get to the train. Travel patterns are only a single part of the whole safety picture. 

Future analysis could include weekend and seasonal data to understand variations in travel behavior, and more granular studies on specific user demographics.