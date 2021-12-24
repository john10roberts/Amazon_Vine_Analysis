# Amazon_Vine_Analysis

## Overview of Project
Using the toys data set provided by the amazon vine program and using PySpark to perform the ETL process to extract the dataset, transform the data, connect to an AWS RDS instance, and load the transformed data into pgAdmin. We will use that data to determine if there is any bias for favorable reviews from participants in the vine program vs non participants. 

## Results
The dataset consits of over 4 million reviews in the toy category. In order to make the data a little easier to digest we filtered the data to only include rows with total votes greater than 20. Additionally, we filtered this list down by only focusing on the reviews that were considered helpful by dividing the # of helpful reviews by the total number of reviews and using only the results that were greater than or equal to .50. 

```
from pyspark.sql.functions import col, when, count, lit
vine_df = df
vote_count_df = vine_df.filter(col("total_votes") >= 20)
vote_count_df.show()

# Filter the new DataFrame or table created in Step 1 and create a new DataFrame or table to retrieve all the rows where the number of helpful_votes divided by total_votes is equal to or greater than 50%.
vote_count_det = vote_count_df.withColumn('percent_votes',col('helpful_votes')/col('total_votes')).alias('percent_votes').filter(col("percent_votes") >= 0.5)
vote_count_det.show()
``` 
