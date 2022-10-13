1. wp_countries-no_match.txt contains a list of 31 unmatched countries between politician and population datasets. It must be noted that 6 of these countries have population value 0 in the population dataset and therefore were dropped from the population dataset resulting in their inclusion in this list.

2. wp_politicians_by_country.csv is the final consolidated dataset utilized for analysis. 

    | Columns         | Source                                                            |
    | --------------- | ----------------------------------------------------------------- |
    | country         | join criteria between politician and population dataset           |
    | region          | population data                                                   |
    | population      | population data                                                   |
    | article_title   | politician data                                                   |
    | revision_id     | requested from Action API https://www.mediawiki.org/wiki/API:Info |
    | article_quality | requested from ORES API https://www.mediawiki.org/wiki/ORES       |