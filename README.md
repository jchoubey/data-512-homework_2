# data-512-homework_2
This repo holds the homework assignment 2 in the DATA 512: Human Centered Data Science Course.

## Project Goal
The goal of this assignment is to explore the concept of bias in data using Wikipedia articles. This assignment will consider articles on political figures from different countries. For this assignment, it is required to combine a dataset of Wikipedia articles with a dataset of country populations, and use a machine learning service called ORES to estimate the quality of each article.

It is then needed to perform an analysis of how the coverage of politicians on Wikipedia and the quality of articles about politicians varies among countries. The analysis will consist of a series of tables that show:
1.	The countries with the greatest and least coverage of politicians on Wikipedia compared to their population.
2.	The countries with the highest and lowest proportion of high quality articles about politicians.
3.	A ranking of geographic regions by articles-per-person and proportion of high quality articles.

## Data Source

### Raw Data: 
https://github.com/jchoubey/data-512-homework_2/tree/main/data

1. politicians_by_country_sept2022.csv
    List of Wikipedia articles of politicians
    The Wikipedia Category: Politicians by nationality (https://en.wikipedia.org/wiki/Category:Politicians_by_nationality) was crawled to generate a list of Wikipedia article pages about politicians from a wide range of countries.
    
    | Columns | Description                                     |
    | ------- | ----------------------------------------------- |
    | name    | politician name (or article name on wikipedia)  |
    | url     | link to wikipedia article                       |
    | country | politician's country of practice/origin         |


2. population_by_country_2022.csv
    The population data is drawn from the world population data sheet published by the Population Reference Bureau. https://www.prb.org/international/indicator/population/table
    
    | Columns               | Description                                                            |
    | --------------------- | ---------------------------------------------------------------------- |
    | Geography             | countries and regions                                                  |
    | Population (millions) | population of countries & cumulative population of regions in millions |


### Some Considerations:

It is needed to be a little careful with the data. Crawling Wikipedia categories to identify relevant page subsets can result in misleading and/or duplicate category labels. Naturally, the data crawl attempted to resolve these, but not all may have been caught. Handling data inconsistencies have been documented in the source code. https://github.com/jchoubey/data-512-homework_2/tree/main/code
The population_by_country_2022.csv contains some rows that provide cumulative regional population counts. These rows are distinguished by having ALL CAPS values in the 'geography' field (e.g. AFRICA, OCEANIA). These rows won't match the country values in politicians_by_country.SEPT.2022.csv, but it is still needed to retain some of them so that one can report coverage and quality by region.


### Output Data:  
https://github.com/jchoubey/data-512-homework_2/tree/main/output

1. wp_countries-no_match.txt contains a list of 31 unmatched countries between politician and population datasets. It must be noted that 6 of these countries have population value 0 in the population dataset and therefore were dropped from the population dataset resulting in their inclusion in this list.

2. wp_politicians_by_country.csv is the final consolidated dataset utilized for analysis. 

    | Columns         | Source                                                            |
    | --------------- | ----------------------------------------------------------------- |
    | country         | join criteria between politician and population dataset           |
    | region          | population data                                                   |
    | population      | population data                                                   |
    | article_title   | politician data                                                   |
    | revision_id     | requested from [Action API](https://www.mediawiki.org/wiki/API:Info) |
    | article_quality | requested from [ORES API](https://www.mediawiki.org/wiki/ORES)       |


## Project Structure

``` 
data-512-homework_2
├── data
│   ├── politicians_by_country_sept2022.csv
│   ├── population_by_country_2022.csv
├── output
│   ├── wp_countries-no_match.txt
│   ├── wp_politicians_by_country.csv
├── code
│   ├── considering_bias_in_data_source_code.ipynb
├── LICENSE
└── README.md
```

## Process of the Assignment

The process of the assignment has been documented in a step-by-step format within the source code. Further details are explained below.

### 1. Data Acquisition
We begin with loading the data from the two csv files in pandas dataframe. We will be working with pandas through out this project.

### 2. Handling Data Inconsistencies
The data is not devoid of fallacies. We deal with inconsistencies such as missing data and duplicate entries in both the dataframes.

### 3. Basic Data Processing
    

### 4. Getting Article Quality Predictions


### 5. Preparing Master Dataset


### 6. Analysis


### 7. Results



## Research Reflections & Implications
