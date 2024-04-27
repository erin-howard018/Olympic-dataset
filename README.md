# Combining Olympic and GDP data Project

![](https://a0.anyrgb.com/pngimg/1688/722/art-medals-medals-cartoon-medal-pretty-gold-medal-olympics-medal-medals-awards-olympic-medal-silver-medal-bronze-medal-prize.png)


## Table of contents
- [Introduction](#Introduction)
- [Overview](#Overview)
- [Stage 1: Ask](#Stage-1-Ask)
- [Stage 2: Prepare](#Stage-2-Prepare)
- [Stage 3: Process](#Stage-3-Process)


## Introduction 
This data project was a project that I completed as part of my course work at University. The purpose of the assignment was to undertake a data wrangling project that involved to combine two datasets in different forms, into one dataset that would allow us to gain insights and answer questions.

## Overview

**Summary of the project**
- I chose to combine a dataset on details of Olympic Medals (in .csv format) and a dataset on GDP of countries across years (in JSON format).
- Through various data wrangling techniques, I was able to combine the dataset into a final format of an Excel workbook using a year/country combination as the common variable to link the data.
- Each of the 19,986 rows of the final dataset record details of a medal won and a GDP value according to the year and country of the athlete.
- The data in this form allowed me to use Pivot tables and charts to answer three questions I had posed before my wrangling journey; firstly whether there is a trend between the medal count and average GDP across countries, whether the changing GDP across years affects the medal count for a country and lastly if a higher GDP correlates to a specific type of medal.  

The following report details my wrangling journey, including successes and failures. 

**Tools** 
- Excel
- MongoDB


## Stage 1: Ask

**Business task:**
Overall, the business task aims to gain actionable insights from the combined dataset to answer questions about the relationship between Olympic success and GDP, that would not have been able to be answered by either individual dataset alone. The potential business consequences of this could be to inform strategic decision-making, potentially in areas like funding allocation, athletic development, and economic policy.

There are three main questions that I wanted to be able to answer
1. Is there a trend between the medal count and average GDP across countries?
2. Does a country's changing GDP across years affects their medal count?
3. Does a higher GDP correlates to a specific type of medal?
    

## Stage 2: Prepare 

**First Dataset - Olympic Sports and Medals, 1896-2014**
- Data sources; A publically available dataset downloaded from Kaggle - link to data in my repository available [here](data/raw/Olympic_data.zip) or available on Kaggle [here](https://www.kaggle.com/datasets/the-guardian/olympic-games). Data was provided by the IOC Research and Reference Service and published by The Guardian's Datablog and is available to use under a [CC BY-SA 4.0 license](https://creativecommons.org/licenses/by-sa/4.0/).
- Organisation and description of files; Three .csv files; data for the Summer Olympics, data for the Winter Olympics and a country code data dictionary. The datasets describe all the medals won at the Olympics from 1896 to 2014, including medal type, sport, country, athlete etc. 
- We may have some concerns about this data in regards to ROCCC. I have faith that the data is reliable due to lack of missing values, as well as the data being collected by the official Olympic Study Centre, although it is not original as I have sourced it through a secondary source Kaggle. Although the data is comprehensive, it is not the most current only tracking up to 2014 but since the data is historical as well we will continue with this analysis to get an understanding of the relationship over time. 
- Limitations of the data are that data is seperated across three differerent .csv files, and the olympic data doesn't have country names, only country codes that correspond to the seperate .csv country code data dictionary.  

Key observations about the dataset
- There are no country names which might make it hard to understand Country codes.
- There is also the fact that there may be missing or repeated country/year pairings depending on the number of medals won for that country and that year.


**Second Dataset - Country, Regional and World GDP**
- Data sources and description; A publically available dataset downloaded from DataHub. This data comes from the WorldBank so is updated yearly with new country statistics. When I downloaded this dataset it was 30/04/2022 and it was available in JSON format in a .txt file - link to data in my repository available [here](data/raw/gdp.json.txt). The dataset on DataHub now is only available for download as .csv but you can preview this [here](https://datahub.io/core/gdp)). Data was provided by the WorldBank and is made available under the Public Domain Dedication and License v1.0 whose full text can be found [here](http://www.opendatacommons.org/licenses/pddl/1.0/).
- Data description; the JSON file describes GDP (in 2016 USD$) for a variety of countries and world regions for years between 1960 and 2016. 
- We may have some concerns about this data in regards to ROCCC. I have faith that the data is reliable being collected by the WorldBank, although it is not original as I have sourced it through a secondary source DataHub. Although the data is comprehensive, it is not the most current only tracking up to 2016 but since the data is historical as well we will continue with this analysis to get an understanding of the relationship over time.
- Limitations of the data is that all GDP is recorded in 2016 USD which is not current. 

Key observations about the dataset
- There are 11,507 documents, each relating to a single country/region and year pair. Inside this, there were contain 5 attribute-value pairs (Id, country code, country name, year and GDP). 
- I am aware there are likely to be a number of records in the GDP dataset that wouldn’t have a match in the Olympic dataset, due to the GDP dataset including a lot of regions/non-Olympic countries and also many years would be redundant as the Olympics are only every 4 years. 



## Stage 3: Process

**Steps to get Olympic Dataset to analysis-ready format**
- While there were around 31,000 rows in the summer Olympic dataset and around 6,000 in the winter, for completeness, I combined both the Summer and Winter Olympic datasets into one.  This was simple as both datasets had the same variables in the same order so I could copy paste the datasets into one Excel file. 
- I also created a new variable called season to preserve if data was from summer or winter Olympic dataset. This left me with a combined dataset available [here](data/working/combined_olympicdataset.xlsm) that has 10 variables and 36,935 rows.

**Steps to get GDP Dataset to analysis-ready format**
- In order to inspect the data, I loaded the JSON dataset into MongoDB. This initially provided a challenge as the data was stored in an array, so I had to decode it using a -jsonArray argument when using MongoImport.
- 

