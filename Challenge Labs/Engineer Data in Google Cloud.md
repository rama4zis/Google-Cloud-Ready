# 1. Clean your training data

1.  Navigate to **BigQuery** in the console. 
2.  Go to Query Editor.
3.  Run this Query:
```
CREATE OR REPLACE TABLE 
  taxirides.taxi_training_data_724 AS
SELECT
  ( tolls_amount + fare_amount ) AS fare_amount_566,
  pickup_datetime,
  pickup_longitude AS pickuplon,
  pickup_latitude AS pickuplat,
  dropoff_longitude AS dropofflon,
  dropoff_latitude AS dropofflat,
  passenger_count AS passengers
FROM
  taxirides.historical_taxi_rides_raw
WHERE
  RAND() < 0.001
  AND trip_distance > 2 -- Get from your require task
  AND fare_amount >= 3.0 -- Get from your require task
  AND pickup_longitude > -78
  AND pickup_longitude < -70
  AND dropoff_longitude > -78
  AND dropoff_longitude < -70
  AND pickup_latitude > 37
  AND pickup_latitude < 45 
  AND dropoff_latitude > 37
  AND dropoff_latitude < 45
  AND passenger_count > 2 -- Get from your require task
```

# 2. Create a BigQuery ML model

Run this Query:
```
CREATE OR REPLACE MODEL
  taxirides.fare_model_440
TRANSFORM(
  * EXCEPT(pickup_datetime),
  ST_Distance(ST_GeogPoint(pickuplon, pickuplat),
  ST_GeogPoint(dropofflon, dropofflat)) AS euclidean,
  CAST(EXTRACT(DAYOFWEEK FROM pickup_datetime) AS STRING) AS dayofweek,
  CAST(EXTRACT(HOUR FROM pickup_datetime) AS STRING) AS hourofday
)
OPTIONS(
  input_label_cols=['fare_amount_566'],
  model_type='linear_reg'
) AS
SELECT
  *
FROM
  taxirides.taxi_training_data_724
```

```
SELECT
  SQRT(mean_squared_error) AS rmse
FROM
  ML.EVALUATE(MODEL taxirides.fare_model_440, (
    WITH taxitrips AS (
      SELECT *, 
        ST_Distance(ST_GeogPoint(pickuplon, pickuplat),
        ST_GeogPoint(dropofflon, dropofflat)) AS euclidean,
        FROM taxirides.taxi_training_data_724
    )
    SELECT *
    FROM taxitrips
  )
)
```

# 3. Perform a batch prediction on new data

Run this Query:
```
CREATE OR REPLACE TABLE
  taxirides.2015_fare_amount_predictions AS
SELECT *
FROM ML.PREDICT(MODEL taxirides.fare_model_440, (
  SELECT *,
    FROM taxirides.report_prediction_data
  )
)

```