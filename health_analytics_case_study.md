# Health-Analytics-Mini-Case-Study


## Mini Case Study is from Danny Ma's 8 Week SQL Challenge


### Case Objective

We’ve received an urgent request from the General Manager of Analytics at Health Co requesting our assistance with their analysis of the health.user_logs dataset.

The Health Co analytics team have shared with us their SQL script - they unfortunately ran into a few bugs that they couldn’t fix!

We’ve been asked to quickly debug their SQL script and use the resulting query outputs to quickly answer a few questions that the GM has requested for a board meeting about their active users.


### Prior to answering the questions, SQL best practices encourage understanding the dataset and its fields.

```sql
SELECT * 
FROM health.user_logs
LIMIT 5;
```
This dataset includes 6 columns which are named as id, log_date, measure, measure_value, systolic, and diastolic.


<!--  tables -->
|id | log_date | measure | measure_value | systolic| diastolic |
|---|----------|---------|---------------|---------|-----------|
|fa28f948a740320ad56b81a24744c8b81df119fa|2020-11-15|weight|46.03959|null|null|
|1a7366eef15512d8f38133e7ce9778bce5b4a21e|2020-10-10|blood_glucose|97|0|0|
|bd7eece38fb4ec71b3282d60080d296c4cf6ad5e|2020-10-18|blood_glucose|120|0|0|
|0f7b13f3f0512e6546b8d2c0d56e564a2408536a|2020-10-17|blood_glucose|232|0|0|
|d14df0c8c1a5f172476b2a1b1f53cf23c6992027|2020-10-15|blood_pressure|140|140|113|


#### 1. How many unique users exist in the logs dataset?

```sql

SELECT COUNT(DISTINCT id) AS unique_users
FROM health.user_logs;
```

|unique_users|
|------------|
|    554     |


### For questions 2-8 we created a temporary table

```sql

DROP TABLE IF EXISTS user_measure_count;
CREATE TEMP TABLE user_measure_count AS
SELECT
    id,
    COUNT(*) AS measure_count,
    COUNT(DISTINCT measure) as unique_measures
  FROM health.user_logs
  GROUP BY 1;

SELECT *
FROM user_measure_count
LIMIT 5;
```

|id| measure_count|unique_measures|
|--|-------------|---------------|
|004beb6711843b214e80d73df57a3680fdf9700a|3|2|
|007fe1259a4283a991e1f2835ddcdedacf78dde9|1|1|
|008dd1dc1728bb0b420188963905d259c5533149|1|1|
|00ae4fa0241952312d518cee728a387bf156f514|4|1|
|0115244529929acd03b01315cff7eabfb9f126af|1|1|



#### 2. How many total measurements do we have per user on average?

```sql
SELECT ROUND(AVG(measure_count)) AS total_measures
FROM user_measure_count;
```

|avg_total|
|---------|
|   79    |


#### 3. What about the median number of measurements per user?

```sql

SELECT PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY measure_count) AS median_count
FROM user_measure_count;
```

|median_count|
|------------|
|     2      |


#### 4. How many users have 3 or more measurements?

```sql
SELECT COUNT(*)
FROM user_measure_count
WHERE measure_count >= 3;
```

|count|
|-----|
| 209 |


#### 5. How many users have 1,000 or more measurements?

```sql

SELECT COUNT(*)
FROM user_measure_count 
WHERE measure_count >= 1000;
```

|count|
|-----|
|  5  |

#### 6. Have logged blood glucose measurements?

```sql
SELECT COUNT(DISTINCT id)
FROM health.user_logs
WHERE measure = 'blood_glucose';
```

| count |
|-------|
|  325  |

#### 7. Have at least 2 types of measurements?

```sql
SELECT COUNT(*)
FROM user_measure_count
WHERE unique_measures >=2;
```

| count |
|-------|
|  204  |


#### 8. Have all 3 measures - blood glucose, weight and blood pressure?

```sql
SELECT COUNT(*)
FROM user_measure_count
WHERE unique_measures = 3;
```
| count |
|-------|
|   50  |


#### 9.  What is the median systolic/diastolic blood pressure values?

```sql
SELECT
  PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY systolic) AS median_systolic,
  PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY diastolic) AS median_diastolic
FROM health.user_logs
WHERE measure = 'blood_pressure';
```

|median_systolic|median_diastolic|
|---------------|----------------|
|      126      |       79       |
