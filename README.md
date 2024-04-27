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
- Each of the 19,986 rows of the final combined dataset record details of a medal won and a GDP value according to the year and country of the athlete.
- The data in this form allowed me to use Pivot tables and charts to answer three questions I had posed before my wrangling journey; firstly whether there is a trend between the medal count and average GDP across countries, whether the changing GDP across years affects the medal count for a country and lastly if a higher GDP correlates to a specific type of medal.  

The following report details my wrangling journey, including successes and failures. 

**Tools** 
- Excel; 
- MongoDB


## Stage 1: Ask

**Business task:**
Overall, the business task aims to gain actionable insights from the combined dataset to answer questions about the relationship between Olympic success and GDP, that would not have been able to be answered by either individual dataset alone. The potential business consequences of this could be to inform strategic decision-making, potentially in areas like funding allocation, athletic development, and economic policy.

There are three main questions that I wanted to be able to answer
1. Is there a trend between the medal count and average GDP across countries?
2. Does a country's changing GDP across years affects their medal count?
3. Does a higher GDP correlates to a specific type of medal?
    

## Stage 2: Prepare 


### First Dataset - Olympic Sports and Medals, 1896-2014
- **Data sources**; A publically available dataset downloaded from Kaggle - link to data in my repository available [here](data/raw/Olympic_data.zip) or available on Kaggle [here](https://www.kaggle.com/datasets/the-guardian/olympic-games). Data was provided by the IOC Research and Reference Service and published by The Guardian's Datablog and is available to use under a [CC BY-SA 4.0 license](https://creativecommons.org/licenses/by-sa/4.0/).
- **Organisation and description of files**; Three .csv files; data for the Summer Olympics, data for the Winter Olympics and a country code data dictionary. The datasets describe all the medals won at the Olympics from 1896 to 2014, including medal type, sport, country, athlete etc. 
- **Quality**; We may have some concerns about this data in regards to ROCCC. I have faith that the data is reliable due to lack of missing values, as well as the data being collected by the official Olympic Study Centre, although it is not original as I have sourced it through a secondary source Kaggle. Although the data is comprehensive, it is not the most current only tracking up to 2014 but since the data is historical as well we will continue with this analysis to get an understanding of the relationship over time. 
- **Limitations**; The data is seperated across three differerent .csv files, and the olympic data doesn't have country names, only country codes that correspond to the seperate .csv country code data dictionary.  

**Key observations about the dataset**
- There are no country names which might make it hard to understand Country codes.
- There is also the fact that there may be missing or repeated country/year pairings depending on the number of medals won for that country and that year.


### Second Dataset - Country, Regional and World GDP
- **Data sources and description**; A publically available dataset downloaded from DataHub. This data comes from the WorldBank so is updated yearly with new country statistics. When I downloaded this dataset it was 30/04/2022 and it was available in JSON format in a .txt file - link to data in my repository available [here](data/raw/gdp.json.txt). The dataset on DataHub now is only available for download as .csv but you can preview this [here](https://datahub.io/core/gdp)). Data was provided by the WorldBank and is made available under the [Public Domain Dedication and License v1.0](http://www.opendatacommons.org/licenses/pddl/1.0/).
- **Data description**; the .txt file in JSON format  describes GDP (in 2016 USD$) for a variety of countries and world regions for years between 1960 and 2016. 
- **Quality**; We may have some concerns about this data in regards to ROCCC. I have faith that the data is reliable being collected by the WorldBank, although it is not original as I have sourced it through a secondary source DataHub. Although the data is comprehensive, it is not the most current only tracking up to 2016 but since the data is historical as well we will continue with this analysis to get an understanding of the relationship over time.
- **Limitations**; The data is all GDP is recorded in 2016 USD which is not current. 

**Key observations about the dataset**
- There are 11,507 documents, each relating to a single country/region and year pair. Inside this, there were contain 5 attribute-value pairs (Id, country code, country name, year and GDP). 
- I am aware there are likely to be a number of records in the GDP dataset that wouldn’t have a match in the Olympic dataset, due to the GDP dataset including a lot of regions/non-Olympic countries and also many years would be redundant as the Olympics are only every 4 years. 



## Stage 3: Process
All of the working detailed in the section is available in [this](data/working/Wrangling_Workbook.xlsx) Excel file

### Steps to get Olympic Dataset to analysis-ready format
- While there were around 31,000 rows in the summer Olympic dataset and around 6,000 in the winter, for completeness, I combined both the Summer and Winter Olympic datasets into one.  This was simple as both datasets had the same variables in the same order so I could copy paste the datasets into one Excel file. 
- I also created a new variable called season to preserve if data was from summer or winter Olympic dataset. This left me with a combined dataset available [here](data/working/combined_olympicdataset.xlsm) that has 10 variables and 36,935 rows.
- <img width="412" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/c33de72a-9a50-4b95-9f61-46429815f59e">


### Steps to get GDP Dataset to analysis-ready format
In order to inspect the data, I loaded the JSON dataset into MongoDB. This initially provided a challenge as the data was stored in an array, so I had to decode it using a -jsonArray argument when using MongoImport.
- <img width="350" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/0502277e-001f-4a9f-a4c6-e5c441f336b2">

My next step was to import the JSON file into Excel using the 'Get Data from JSON' function on Excel.
- <img width="381" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/0c2cbaa8-cde3-4bee-b048-3e959cbd433f">


### Combining the datasets; creating unique country/year pairings

The common attribute between the two datasets is that they both have countries and years. However, each dataset often has more than one observation per country, and more than one per year.
- **PROBLEM** Obviously if I were to combine dataset on either just country or year, I would get multiple results for each value.
- **SOLUTION** To create a new variable named Country+YearCombination for each country code and year pairing in both the GDP and Medal datasets.
- <img width="461" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/71c8e0bc-ece5-4c75-a75e-ba52be253b64">
- <img width="359" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/6dd29e06-7f38-4340-b67a-a1206bbfd2a3">

My initial thinking while observing the datasets was true in that each country code/year pairing appeared just once in the GDP dataset, while many appeared multiple times in the Medal dataset.
- This made it clear to me it would be easiest to try assign a GDP value to each row in the medal dataset rather as there would be only one match, rather than the other way around. 

### Combining the datasets; ensuring country codes are the same

In a new column in the Medal dataset, I decided to use the VLOOKUP function to lookup a country/year pair in the GDP dataset and return the corresponding GDP value.
- **PROBLEM** While I had predicted some N/A values where country/year pairings don’t exist in the GDP dataset, this VLOOKUP method gave me 20,197 N/A’s and only 16,738 matches.
  + Scanning through the N’A values, I saw rows that I believed should have a match. For example, Germany, which definitely appears in both dataset had no matches.
  + Investigating why lead me to see that Germany has different country codes in each of the datasets and therefore VLOOKUP couldn’t identify them as a match.
- **SOLUTION** In order to minimise the number of N/A’s in the VLOOKUP process, I realised I need to ensure that the country codes for each country are the same.
  + To compare the country codes between the datasets, in a new sheet I used the UNIQUE function to see the unique country codes for each dataset.
    
  + Because GDP dataset has a country name column, it was easy to see what country each code corresponded to.
  + <img width="319" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/6b2ddfe6-bd74-4e33-a22c-df972feb1cbc">

  + The Medal dataset on the other hand, had no country name column so I needed a way to figure out what country each code corresponded to. I used a combination of VLOOKUP from the Dictionary dataset that came with the download and also the manual process of looking up countries of athletes for those that still isn’t have a match.
  + For example; the code ANZ had no match. One of these rows corresponds to athlete named Frank E BEAUREPAIRE who competed for Australasia (no longer a competing country in the Olympics). I was now able to manually fill in now that the ANZ code corresponded to Australasia.
  + <img width="361" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/d5e2cda9-4cd8-4616-bff0-b1a81ef238fd">


Now that I had a list of country codes and their corresponding country name for each dataset, I need to ensure they are the same for both datasets.
- I decided to compare them by using VLOOKUP to match the country codes present in Medal Dataset to those in the GDP dataset.
  + I introduced conditional formatting to compare and then highlight codes that didn’t match between the two datasets.
  + <img width="402" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/837dc9ce-8055-49d3-9a84-759ab89b0b21">
- I decided I would change the country codes in the GDP dataset to match the syntax of the Medal dataset.
  + I used find and replace to change all appearances of a specific code at once.


### Combining the datasets; VLOOKUP second attempt

After reconciling the country codes, I attempted my VLOOKUP of GDP value again.
-<img width="461" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/d7b3b0b0-4290-48c5-8bc2-a2234bbfdd68">
- I saw a bit of an improvement 19,704 matches and 17,231 N/As, but I still saw rows that I thought should have a match.


### Combining the datasets; Investigating the NA’s

To investigate if this is the best I could do, I was going to start by filtering out the country codes that we didn’t have a match for (the N/A’s) in the country code matching process. I was under the impression that these were valid N/A values, thinking that these countries weren’t present in the GDP dataset but I realised my mistake.
- **PROBLEM** As I had matched country codes by using VLOOKUP based off the names of the countries, I had failed to consider the possibility that country names could be different in the two datasets.
  + For example, my process had failed to match ‘Bahamas’ in the Medal dataset to ‘Bahamas, The’ in the GDP dataset as I had used exact matching only.
- **SOLUTION** To re-do the VLOOKUP process but using approximate match.
  + This solved some of the N/A values in the VLOOKUP when combining the two datasets, an improvement to 19,855 matches and 17,080 N/A.


### Combining the datasets; Filtering out valid N/A’s + investigating the other N/A’s
I am now fairly confident that I have sorted all the issues with matching country codes.

My next step was to filter out all the valid N/A’s from the combined dataset so I can see what N/A’s I have left.
- Valid N/A’s would be
  + Data for years before 1960 as this is the earliest year in the GDP dataset
  + Data for countries that weren’t present in the GDP dataset. These were N/A’s in the country code matching table (AHO, ANZ, BOH, BWI, EUA, EUN, FRG, GDR, IOP, PRK, RU1, RUS, TCH, TPE, URS, YUG, ZZX)
    
- By then filtering to only seeing the remaining N/A’s in the combined datasets, allowed me to see N/A’s that may be invalid from errors in my matching process or maybe they could just be missing GDP values for that country in that year.
- While we have 1,354 of these “invalid” N/A values, many are repeated so I used the unique function to see what unique country/year pairings were giving N/A values.
  +  I was left 55 unique “invalid” country/year pairings. I decided I wanted to investigate these for completeness and to ensure I have captured as much data as possible in my combined dataset.
  +  <img width="426" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/db660077-8b57-42cd-a9ba-694b41744467">
  + As suspected, most were due to not having GDP data for these years, and additionally for the Athlete for a medal being pending so not having a country.


### Final Storage Format

Now know all my genuine valid N/A values (17,049 of them), I can see that I was able to pair every row in the medal dataset that had a match in the GDP dataset.
- I have valid 19,886 valid rows, and therefore there are 19,886 in my combined dataset.
- My final dataset looks like;
- <img width="417" alt="image" src="https://github.com/erin-howard018/Olympic-dataset/assets/167825293/ab9877f3-4c24-4d1c-8282-e259dedae93e">




  




















