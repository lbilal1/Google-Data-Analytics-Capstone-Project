-- Creating a table for storing trip data from multiple datasets
CREATE TABLE cyclistic_trip (
    ride_id              VARCHAR(50),       -- Unique identifier for each ride
    rideable_type        VARCHAR(20),       -- Type of bike used (e.g., docked bike, electric bike)
    started_at           TIMESTAMP,         -- Timestamp of when the ride started
    ended_at             TIMESTAMP,         -- Timestamp of when the ride ended
    start_station_name   VARCHAR(100),      -- Name of the station where the ride started
    end_station_name     VARCHAR(100),      -- Name of the station where the ride ended
    member_casual        VARCHAR(50)        -- User type (member or casual)
);

-- Inserting data from multiple monthly datasets into the cyclistic_trip table
INSERT INTO cyclistic_trip
SELECT * FROM cyclistic_trip_202207
UNION ALL
SELECT * FROM cyclistic_trip_202208
UNION ALL
SELECT * FROM cyclistic_trip_202209
UNION ALL
SELECT * FROM cyclistic_trip_202210
UNION ALL
SELECT * FROM cyclistic_trip_202211
UNION ALL
SELECT * FROM cyclistic_trip_202212
UNION ALL
SELECT * FROM cyclistic_trip_202301
UNION ALL
SELECT * FROM cyclistic_trip_202302
UNION ALL
SELECT * FROM cyclistic_trip_202303
UNION ALL
SELECT * FROM cyclistic_trip_202304
UNION ALL
SELECT * FROM cyclistic_trip_202305
UNION ALL
SELECT * FROM cyclistic_trip_202306;

-- Selecting all records from the cyclistic_trip table
SELECT * FROM public.cyclistic_trip;

-- DATA CLEANING
-- Removing unnecessary columns for analysis
ALTER TABLE public.cyclistic_trip
DROP COLUMN start_station_id,
DROP COLUMN end_station_id,
DROP COLUMN start_lat,
DROP COLUMN start_lng,
DROP COLUMN end_lat,
DROP COLUMN end_lng;

-- Checking for duplicate ride IDs
SELECT DISTINCT ride_id FROM public.cyclistic_trip;

SELECT ride_id, COUNT(*) AS trip_count
FROM public.cyclistic_trip
GROUP BY ride_id
HAVING COUNT(*) > 1;

-- Checking for NULL values in critical columns
SELECT *
FROM public.cyclistic_trip
WHERE started_at IS NULL OR ended_at IS NULL;

SELECT *
FROM public.cyclistic_trip
WHERE start_station_name IS NULL OR end_station_name IS NULL;

-- Removing records with NULL station names
DELETE FROM public.cyclistic_trip
WHERE start_station_name IS NULL OR end_station_name IS NULL;

-- Checking for distinct rideable types and member types
SELECT DISTINCT rideable_type FROM public.cyclistic_trip;
SELECT DISTINCT member_casual FROM public.cyclistic_trip;

-- Checking distinct station names
SELECT 
    COUNT(DISTINCT start_station_name) AS unique_start_stations,
    COUNT(DISTINCT end_station_name) AS unique_end_stations
FROM public.cyclistic_trip;

SELECT DISTINCT start_station_name FROM public.cyclistic_trip;
SELECT DISTINCT end_station_name FROM public.cyclistic_trip;

-- DATA MANIPULATION
-- Adding new columns for analysis
ALTER TABLE public.cyclistic_trip
ADD COLUMN ride_length INTERVAL,
ADD COLUMN ride_min NUMERIC,
ADD COLUMN time_of_day NUMERIC,
ADD COLUMN day_of_week TEXT,
ADD COLUMN ride_year_month TEXT;

-- Calculating ride length and ride minutes
UPDATE public.cyclistic_trip
SET ride_length = AGE(ended_at, started_at),
    ride_min = TRUNC(EXTRACT(EPOCH FROM ride_length) / 60),
    time_of_day = EXTRACT(hour FROM started_at),
    day_of_week = TO_CHAR(started_at, 'Dy'),
    ride_year_month = TO_CHAR(started_at, 'YYYY-MM');

-- Exploring total ride minutes and lengths
SELECT 
    MIN(ride_min) AS min_ride_min,
    MAX(ride_min) AS max_ride_min,
    AVG(ride_min) AS avg_ride_min
FROM public.cyclistic_trip;

SELECT 
    MIN(ride_length) AS min_ride_length,
    MAX(ride_length) AS max_ride_length,
    AVG(ride_length) AS avg_ride_length
FROM public.cyclistic_trip;

-- Removing invalid entries
DELETE FROM public.cyclistic_trip
WHERE ended_at < started_at OR ride_min < 1;

-- ANALYSIS
-- Count of rides by user type
SELECT member_casual, COUNT(ride_id) AS ride_count
FROM public.cyclistic_trip
GROUP BY member_casual;

-- Average ride length for users
SELECT member_casual, 
    AVG(ride_length) AS avg_ride_length, 
    ROUND(AVG(ride_min), 2) AS avg_ride_min
FROM public.cyclistic_trip
GROUP BY member_casual;

-- Types of rides per user type
SELECT rideable_type, member_casual,
    COUNT(ride_id) AS trips
FROM public.cyclistic_trip
GROUP BY rideable_type, member_casual
ORDER BY trips DESC;

-- Checking details about docked bikes by day of week and time of day
SELECT day_of_week, COUNT(*) AS trips
FROM public.cyclistic_trip
WHERE rideable_type = 'docked_bike'
GROUP BY day_of_week;

SELECT time_of_day, COUNT(*) AS trips
FROM public.cyclistic_trip
WHERE rideable_type = 'docked_bike'
GROUP BY time_of_day;

-- Number of rides by user type and day of week
SELECT member_casual, day_of_week,
    COUNT(ride_id) AS trips
FROM public.cyclistic_trip
GROUP BY member_casual, day_of_week
ORDER BY member_casual, trips DESC;

-- Average ride length for users by day of week
SELECT member_casual, day_of_week,
    AVG(ride_length) AS avg_ride_length, 
    ROUND(AVG(ride_min), 2) AS avg_ride_min
FROM public.cyclistic_trip
GROUP BY member_casual, day_of_week
ORDER BY member_casual, avg_ride_min DESC;

-- Number of trips per time of day
SELECT member_casual, time_of_day,
    COUNT(ride_id) AS trips
FROM public.cyclistic_trip
GROUP BY member_casual, time_of_day
ORDER BY member_casual, trips DESC;

-- Number of trips per month
SELECT member_casual, ride_year_month,
    COUNT(ride_id) AS trips
FROM public.cyclistic_trip 
GROUP BY member_casual, ride_year_month
ORDER BY member_casual, trips DESC;

-- Number of rides per start station (top 20)
SELECT start_station_name, COUNT(ride_id) AS trips
FROM public.cyclistic_trip
WHERE member_casual = 'member'
GROUP BY start_station_name
ORDER BY trips DESC
LIMIT 20;

SELECT start_station_name, COUNT(ride_id) AS trips
FROM public.cyclistic_trip
WHERE member_casual = 'casual'
GROUP BY start_station_name
ORDER BY trips DESC
LIMIT 20;

-- Top 10 end stations per rides
SELECT end_station_name, COUNT(ride_id) AS trips
FROM public.cyclistic_trip
WHERE member_casual = 'casual'
GROUP BY end_station_name
ORDER BY trips DESC
LIMIT 10;

SELECT end_station_name, COUNT(ride_id) AS trips
FROM public.cyclistic_trip
WHERE member_casual = 'member'
GROUP BY end_station_name
ORDER BY trips DESC
LIMIT 10;
