# Considering Bias in Data
This repo holds the homework assignment 2 in the DATA 512: Human Centered Data Science Course.

## Project Goal
The goal of this assignment is to explore the concept of bias in data using Wikipedia articles. This assignment will consider articles on political figures from different countries. For this assignment, it is required to combine a dataset of Wikipedia articles with a dataset of country populations, and use a machine learning service called ORES to estimate the quality of each article.

It is then needed to perform an analysis of how the coverage of politicians on Wikipedia and the quality of articles about politicians varies among countries. The analysis will consist of a series of tables that show:
1.	The countries with the greatest and least coverage of politicians on Wikipedia compared to their population.
2.	The countries with the highest and lowest proportion of high quality articles about politicians.
3.	A ranking of geographic regions by articles-per-person and proportion of high quality articles.

## Data Source

### [Raw Data](https://github.com/jchoubey/data-512-homework_2/tree/main/data)

1. politicians_by_country_sept2022.csv
    List of Wikipedia articles of politicians
    The Wikipedia Category: [Politicians by nationality](https://en.wikipedia.org/wiki/Category:Politicians_by_nationality) was crawled to generate a list of Wikipedia article pages about politicians from a wide range of countries.
    
    | Columns | Description                                     |
    | ------- | ----------------------------------------------- |
    | name    | politician name (or article name on wikipedia)  |
    | url     | link to wikipedia article                       |
    | country | politician's country of practice/origin         |


2. population_by_country_2022.csv
    The population data is drawn from the world population data sheet published by the [Population Reference Bureau](https://www.prb.org/international/indicator/population/table)
    
    | Columns               | Description                                                            |
    | --------------------- | ---------------------------------------------------------------------- |
    | Geography             | countries and regions                                                  |
    | Population (millions) | population of countries & cumulative population of regions in millions |


### Some Considerations:

It is needed to be a little careful with the data. Crawling Wikipedia categories to identify relevant page subsets can result in misleading and/or duplicate category labels. Naturally, the data crawl attempted to resolve these, but not all may have been caught. Handling data inconsistencies have been documented in the [source code](https://github.com/jchoubey/data-512-homework_2/tree/main/code)
The population_by_country_2022.csv contains some rows that provide cumulative regional population counts. These rows are distinguished by having ALL CAPS values in the 'geography' field (e.g. AFRICA, OCEANIA). These rows won't match the country values in politicians_by_country.SEPT.2022.csv, but it is still needed to retain some of them so that one can report coverage and quality by region.


### [Output Data](https://github.com/jchoubey/data-512-homework_2/tree/main/output)

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

The process of the assignment has been documented in a step-by-step format within the [source code](https://github.com/jchoubey/data-512-homework_2/tree/main/code). Further details are explained below.

1. Data Acquisition: We begin with loading the data from the two csv files in pandas dataframe. We will be working with pandas through out this project.

2. Handling Data Inconsistencies: We deal with inconsistencies such as missing data and duplicate entries in both the dataframes.
    - For politician dataset, at article-country level (note that article is politician name), there are 2 articles that are duplicated. We removed duplicates for these two articles.
    - There are 48 articles that have duplicate entries, meaning each politician is associated with more that 1 country. Going through a few articles, it is not apparant if this is a defect or a true representation. We will keep these entries as they are in absence of confirmation.
    - In population dataset, we have 6 countries that have population value recorded as 0. We have listed these countries in the source code and have removed them from the dataset.

3. Basic Data Processing: There were a number of steps involved with preparing the data in desired format.
    - The population_by_country_2022.csv contains some rows that provide cumulative regional population counts. These rows are distinguished by having ALL CAPS values in the 'geography' field (e.g. AFRICA, OCEANIA). These rows won't match the country values in politicians_by_country.SEPT.2022.csv, but we still need to retain some of them so that you can report coverage and quality by region.
    - In this analysis a country can only exist in one region. The population_by_country_2022.csv actually represents regions in a hierarchical order. For the analysis we put a country in the closest (lowest in the hierarchy) region.
    - To achieve this, we have split the population data into two dataframes: country level and region level. Country level data contains all the countries, along with their lowest hierarchical region, and population. Region level data contains the 18 regions and their respective cumulative population.

4. Getting Article Quality Predictions
    - Next, we need to get the predicted quality scores for each article in the Wikipedia dataset. We're using a machine learning system called [ORES](https://www.mediawiki.org/wiki/ORES). ORES is a machine learning tool that can provide estimates of Wikipedia article quality. The article quality estimates are, from best to worst:
        - FA - Featured article
        - GA - Good article
        - B - B-class article
        - C - C-class article
        - Start - Start-class article
        - Stub - Stub-class article
    - ORES requires a specific revision ID of a specific article to be able to make a label prediction. We use the [API:Info](https://www.mediawiki.org/wiki/API:Info) request to get the most current revision ID of the article page (lastrevid).
    - **Note:** There are 7 articles for who the revision_id was not obtained. These records were dropped from the politician dataframe.
    - Putting this together, to get a Wikipedia page quality prediction from ORES for each politician’s article page you will need to read each line of politicians_by_country.SEPT.2022.csv, make a page info request to get the current page revision, and c) make an ORES request using the page title and current revision id.

5. Preparing Master Dataset
    - Both the processed politician dataset and population dataset (country only: obtained after basic data processing) were merged (inner join) on COUNTRY column to prepare a final master dataset.
    - A list of 32 countries found no match between the two files. This list was extracted and stored in wp_countries-no_match.txt
    - The remaining data was stored in wp_politicians_by_country.csv 

6. Analysis: 
    - The analysis consists of calculating total-articles-per-population (a ratio representing the number of articles per person)  and high-quality-articles-per-population (a ratio representing the number of high quality articles per person) on a country-by-country and regional basis. 
    - All of these values are to be “per capita”. Since the population file provides population in millions, we have converted it back to standard.

7. Results: The following tables data tables were obtained from the analysis (displayed in source code notebook):
    - Top 10 countries by coverage: The 10 countries with the highest total articles per capita (in descending order) .
    - Bottom 10 countries by coverage: The 10 countries with the lowest total articles per capita (in ascending order) .
    - Top 10 countries by high quality: The 10 countries with the highest high quality articles per capita (in descending order) .
    - Bottom 10 countries by high quality: The 10 countries with the lowest high quality articles per capita (in ascending order).
    - Geographic regions by total coverage: A rank ordered list of geographic regions (in descending order) by total articles per capita.
    - Geographic regions by high quality coverage: Rank ordered list of geographic regions (in descending order) by high quality articles per capita.


## Research Reflections & Implications

1. What biases did you expect to find in the data (before you started working with it), and why?
    
    Considering the data was curated from english wikipedia, I would expect more number of articles to be from english-speaking countries/regions and less so from non-english speaking ones. I also expected more articles from countries with greater access to internet (i.e. developed nations). Moreover, coupled with the above bias, I expected the number of articles to even depend on population. Thus, english speaking, developed, and largely populated countries are expected to have greater number of articles published on wikipedia than otherwise. This would also mean that this dataset would be unable to present a correct picture of the non-english speaking, under-developed or developing, and less populated countries.
    
2. What (potential) sources of bias did you discover in the course of your data processing and analysis?

    During data-processing, I encountered a 6 countries that had 0 population values in the population dataset. They are smaller countries for who capturing correct information has been a challenge. Due to this, these countries had to be dropped altogether from the dataset and thus, they could not participate in the analysis. Our analysis lost out on these countries as a result of inefficient data collection. Moreover, there were roughly 32 countries that found no match between the population and politician datasets resulting in their exclusion from the analysis. These scenarios are again accredited to the bias in the data by relying on english wikipedia for representing the world accurately. 
    
    However, a surprising find was that there were certain large, english speaking countries as well, such as USA, Canada, UK, AUS, and NZ that were not present in the politician dataset. Articles for these countries could not fetched and hence, not included in the analysis. This indicates that the bias in the data is not completely due to language or population. Perhaps the data collection was specifically targetted towards more eastern or southern countries? This can only be speculated.
    
    Moreover, during analysis, we see that the bottom 10 country list by both total number of articles and number of high quality articles comprise of all non-english speaking countries. While this is reflective of language bias, we find that the top 10 country list comprises of only a few english speakers. Top 10 also generally consists of countries with really low population, this indicates that there is a per capita bias.
    
    
3. 
