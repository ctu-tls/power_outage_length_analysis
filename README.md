# Power Outage Length Analysis Based on Type of Cause
by Claire Tu (c7tu@ucsd.edu) and Po-Hua Chen (poc008@ucsd.edu)


## Introduction

### Power Outages

Power outages affect far more than just lights turning off. They interrupt daily routines, disrupt communication, pause business operations, and bring safety risks. Whether caused by extreme weather, equipment failures, or human activity, outages impact millions of households each year and expose how dependent our modern lives are on stable electricity. As climate events intensify and energy demand increases, understanding when and why outages occur has become increasingly important. In this project, we analyze major power outages in the United States to uncover trends over time, compare different causes, and identify factors that influence outage length. By approaching the data systematically, we aim to highlight why outage patterns could matter for communities, policymakers, and anyone who relies on a reliable power system.

### Our dataset

### Dataset Description
In this project, our data came from a Purdue University dataset that contained power outage data in the continental U.S. from January 2000 to July 2016. There are 1534 rows x 56 columns in total. Each row in the dataset represents an outage event and contains information on when and where the outage occurred, the cause, duration, and the number of users affected.

We will use these data to analyze the following question: Does the length of power outage differ on natural reasons or human-caused reasons?

We will use the following columns in our analysis:

- `YEAR`: The year of the outage  
- `MONTH`: The month of the outage  
- `U.S._STATE`: The state of the outage  
- `NERC.REGION`: The geographical region assigned by the North American Electric Reliability Corporation (NERC)  
- `OUTAGE.START.DATE`: Date of the outage  
- `OUTAGE.START.TIME`: Time of the outage  
- `CLIMATE.REGION`: Climate region of the United States
- `OUTAGE.DURATION`: Duration of the outage in minutes
- `CAUSE.CATEGORY`: General cause of the outage

## Cleaning and EDA

## Assessment of Missingness
Here's what a Markdown table looks like. Note that the code for this table was generated automatically from a DataFrame, using

print(counts[['Quarter', 'Count']].head().to_markdown(index=False))
Quarter	Count
Fall 2020	3
Winter 2021	2
Spring 2021	6
Summer 2021	4
Fall 2021	55
Hypothesis Testing
