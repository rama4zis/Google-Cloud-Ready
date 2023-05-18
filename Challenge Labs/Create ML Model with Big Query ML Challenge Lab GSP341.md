# Task 1. Create a dataset to store your machine learning models

Cloud Shell
```
bq mk austin
```

# Task 2. Create a forecasting BigQuery machine learning model

Navigate to **BigQuery UI** > **Query Editor**

```
CREATE OR REPLACE MODEL austin.location_model
OPTIONS
  (model_type='linear_reg', labels=['duration_minutes']) AS
SELECT
    start_station_name,
    EXTRACT(HOUR FROM start_time) AS locationstart_hour,
    EXTRACT(DAYOFWEEK FROM start_time) AS day_of_week,
    duration_minutes,
FROM
    `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
JOIN
    `bigquery-public-data.austin_bikeshare.bikeshare_stations` AS stations
ON
    trips.start_station_name = stations.name
WHERE
    EXTRACT(YEAR FROM start_time) = 2016
    AND duration_minutes > 0
```
*Note: This task maybe ask different year, please change it like the task description.*

# Task 3. Create the second machine learning model

```
CREATE OR REPLACE MODEL austin.subscriber_model
OPTIONS
  (model_type='linear_reg', labels=['duration_minutes']) AS
SELECT
  start_station_name,
  EXTRACT(HOUR FROM start_time) AS start_hour,
  subscriber_type,
  duration_minutes
FROM `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
WHERE 
  EXTRACT(YEAR FROM start_time) = 2016
```
*Note: This task maybe ask different year, please change it like the task description.*

# Task 4. Evaluate the two machine learning models

Query 1
```
-- Evaluation metrics for location_model
SELECT
  SQRT(mean_squared_error) AS rmse,
  mean_absolute_error
FROM
  ML.EVALUATE(MODEL austin.location_model, (
  SELECT
    start_station_name,
    EXTRACT(HOUR FROM start_time) AS locationstart_hour,
    EXTRACT(DAYOFWEEK FROM start_time) AS day_of_week,
    duration_minutes,
  FROM
    `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
  JOIN
    `bigquery-public-data.austin_bikeshare.bikeshare_stations` AS stations
  ON
    trips.start_station_name = stations.name
  WHERE
    EXTRACT(YEAR FROM start_time) = 2018
  )
)
```

Query 2
```
-- Evaluation metrics for subscriber_model
SELECT
  SQRT(mean_squared_error) AS rmse,
  mean_absolute_error
FROM
  ML.EVALUATE(MODEL austin.subscriber_model, (
  SELECT
    start_station_name,
    EXTRACT(HOUR FROM start_time) AS start_hour,
    subscriber_type,
    duration_minutes
  FROM
    `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
  WHERE
    EXTRACT(YEAR FROM start_time) = 2018
  )
)
```

*Note: This task maybe ask different year, please change it like the task description.*



# Task 5. Use the subscriber type machine learning model to predict average trip durations

Query 1
```
SELECT
  start_station_name,
  COUNT(*) AS trips
FROM
  `bigquery-public-data.austin_bikeshare.bikeshare_trips`
WHERE
  EXTRACT(YEAR FROM start_time) = 2018
GROUP BY
  start_station_name
ORDER BY
  trips DESC
```

Query 2
```
SELECT AVG(predicted_duration_minutes) AS average_predicted_trip_length
FROM ML.predict(MODEL austin.subscriber_model, (
SELECT
    start_station_name,
    EXTRACT(HOUR FROM start_time) AS start_hour,
    subscriber_type,
    duration_minutes
FROM
  `bigquery-public-data.austin_bikeshare.bikeshare_trips`
WHERE
  EXTRACT(YEAR FROM start_time) = 2019
  AND subscriber_type = 'Single Trip'
  AND start_station_name = '21st & Speedway @PCL'))
```

*Note: This task maybe ask different year, please change it like the task description.*