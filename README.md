# Amazon_Vine_Analysis

## Overview of Project
Using the toys data set provided by the amazon vine program and using PySpark to perform the ETL process to extract the dataset, transform the data, connect to an AWS RDS instance, and load the transformed data into pgAdmin. We will use that data to determine if there is any bias for favorable reviews from participants in the vine program vs nonparticipants. 

## Results
The dataset consists of over four million reviews in the toy category. To make the data a little easier to digest we filtered the data to only include rows with total votes greater than twenty. Additionally, we filtered this list down by only focusing on the reviews that were considered helpful by dividing the # of helpful reviews by the total number of reviews and using only the results that were greater than or equal to .50. 

```
from pyspark.sql.functions import col, when, count, lit
vine_df = df
vote_count_df = vine_df.filter(col("total_votes") >= 20)
vote_count_df.show()

# Filter the new DataFrame or table created in Step 1 and create a new DataFrame or table to retrieve all the rows where the number of helpful_votes divided by total_votes is equal to or greater than 50%.
vote_count_det = vote_count_df.withColumn('percent_votes',col('helpful_votes')/col('total_votes')).alias('percent_votes').filter(col("percent_votes") >= 0.5)
vote_count_det.show()
```
After the filtering we were left with 63,294 records. From these records we were able to answer the following questions: 

![Vine Reviews](https://github.com/john10roberts/Amazon_Vine_Analysis/blob/main/Resources/VineReviews.png)

``` ratings_total_df = vote_count_det.groupBy("vine").agg(
    count(col("vine")).alias("Total_Reviews"),
    count(when(col("star_rating") == 5, True)).alias("Total_5_Star_Reviews"),
    (count(when(col("star_rating") == 5, True))/count(col("vine"))*100).alias("%_5_Star_To_Total")).show()
```

* How many Vine reviews and non-Vine reviews were there?

Vine reviews made up 2% (1266) of the total 63,294 reviews, Non-Vine reviews made up the other 98% (62,028)

* How many Vine reviews were 5 stars? How many non-Vine reviews were 5 stars?

There were 432 5 Star vine reviews which makes up about 1.4% of the total 5 Star Reviews. There were 29,982 5-star non-vine reviews which is the remaining 98.6% of the total 5-star reviews.

* What percentage of Vine reviews were 5 stars? What percentage of Non-Vine reviews were 5 stars?

There were 1266 total vine reviews with 432 of them 5-star this equates to a 34.12% 5-star review percentage. There were 62,028 non-vine revies with 29,982 being 5-star this equates to a 48.34% 5-star review percentage. 

## Summary
Based on the results it does not appear that vine members show bias in their rating of products as the percentage of non 5-star ratings were around 14% less than non-vine members. Vine members are often more critical than non-vine members when reviewing purchases. We could validate this by performing the same analysis on a number of different product categories. If the result of that analysis is consistent with the analysis, we have done on the toy category we could validate that there is no bias from vine members. 


