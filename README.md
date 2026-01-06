# Teen Club Monthly Report
Use this to compile the monthly teen club report.

## Active in care male and female
```sql
/* Active in care 10 -19 */
USE openmrs_warehouse;

SET @endDate = "2024-01-31";

call create_last_teen2_club_outcome_at_facility(@endDate);
SELECT 
    location, op.gender, COUNT(*) AS 'active in care 10 - 19'
FROM
    last_teen_club_facility_outcome ltcfo
        LEFT JOIN
    omrs_patient op ON op.patient_id = ltcfo.pat
WHERE
    state IN ('Patient transfer in' , 'First time initiation')
        AND FLOOR((DATEDIFF(@endDate, op.birthdate) / 365.25)) BETWEEN 10 AND 19
GROUP BY location , op.gender;
```
## Number of teens in the district male and female
```sql
/* Teens who are HIV positive between 10 -19 */
USE openmrs_warehouse;

SET @endDate = '2023-11-30';

CALL create_last_art2_outcome_at_facility(@endDate);

SELECT 
    location, op.gender, COUNT(pat) AS 'HIV positive between 10 -19'
FROM
    last_facility_outcome lfo
        LEFT JOIN
    omrs_patient op ON op.patient_id = lfo.pat
WHERE
    state = 'on antiretrovirals'
        AND FLOOR((DATEDIFF(@endDate, op.birthdate) / 365.25)) BETWEEN 10 AND 19
GROUP BY location , op.gender;
```

## Viral Load Supression
```sql
/*VL suppression */
/* Numerator */
USE openmrs_warehouse;

/* Space to cover 1 year 3 months between start date and end date */
SET @startDate = '2022-08-01';
SET @endDate = "2023-11-30";
SET @location = 'neno district hospital';


call create_last_teen_club_outcome_at_facility(@endDate,@location);

SELECT 
    COUNT(*)
FROM
    last_teen_club_facility_outcome ltcf
        LEFT JOIN
    (SELECT 
        patient_id,
            visit_date,
            ldl,
            less_than_limit,
            viral_load_result,
            other_results
    FROM
        mw_art_viral_load mavl
    JOIN (SELECT 
        patient_id AS p_id, MAX(visit_date) AS last_visit_date
    FROM
        mw_art_viral_load
    WHERE
        visit_date <= @endDate
    GROUP BY patient_id) x ON x.p_id = mavl.patient_id) mavl1 ON mavl1.patient_id = ltcf.pat
WHERE
    mavl1.visit_date BETWEEN @startDate AND @endDate
        AND location = @location
        AND (mavl1.ldl = 'True'
        OR mavl1.less_than_limit <= 1000
        OR mavl1.viral_load_result <= 1000)
        AND mavl1.other_results IS NULL;


/* Denominator */
USE openmrs_warehouse;

/* Space to cover 1 year 3 months between start date and end date */
SET @startDate = '2022-08-01';
SET @endDate = "2023-11-30";
SET @location = 'neno district hospital';


call create_last_teen_club_outcome_at_facility(@endDate,@location);

SELECT 
    COUNT(*)
FROM
    last_teen_club_facility_outcome ltcf
        LEFT JOIN
    (SELECT 
        patient_id,
            visit_date,
            ldl,
            less_than_limit,
            viral_load_result,
            other_results
    FROM
        mw_art_viral_load mavl
    JOIN (SELECT 
        patient_id AS p_id, MAX(visit_date) AS last_visit_date
    FROM
        mw_art_viral_load
    WHERE
        visit_date <= @endDate
    GROUP BY patient_id) x ON x.p_id = mavl.patient_id) mavl1 ON mavl1.patient_id = ltcf.pat
WHERE
    mavl1.visit_date BETWEEN @startDate AND @endDate
        AND (mavl1.ldl IS NOT NULL
        OR mavl1.less_than_limit IS NOT NULL
        OR mavl1.viral_load_result IS NOT NULL)
        AND mavl1.other_results IS NULL;
```

## Active above 19 male and female
```sql
/* Active in care 20 - 22 */
USE openmrs_warehouse;

SET @endDate = "2024-01-31";

call create_last_teen2_club_outcome_at_facility(@endDate);
SELECT 
    location, op.gender, COUNT(*) AS 'active in care 20 - 22'
FROM
    last_teen_club_facility_outcome ltcfo
        LEFT JOIN
    omrs_patient op ON op.patient_id = ltcfo.pat
WHERE
    state IN ('Patient transfer in' , 'First time initiation')
        AND FLOOR((DATEDIFF(@endDate, op.birthdate) / 365.25)) BETWEEN 20 AND 22
GROUP BY location , op.gender;
```
## Active below 10 male and female
```sql
/* Active in care below 10 */

USE openmrs_warehouse;

SET @endDate = "2024-01-31";

call create_last_teen2_club_outcome_at_facility(@endDate);
SELECT 
    location, op.gender, COUNT(*) AS 'active in care below 10'
FROM
    last_teen_club_facility_outcome ltcfo
        LEFT JOIN
    omrs_patient op ON op.patient_id = ltcfo.pat
WHERE
    state IN ('Patient transfer in' , 'First time initiation')
        AND FLOOR((DATEDIFF(@endDate, op.birthdate) / 365.25)) < 10
GROUP BY location , op.gender;
```

# Microsoft SQL Teen Club Monthly 

## Active in care 

```sql
USE openmrs_reporting;

DECLARE @endDate DATE = '2024-12-31';

-- Run the stored procedure
EXEC dbo.create_last_teen2_club_outcome_at_facility @endDate = @endDate;

-- Query with age filter using FLOOR(DATEDIFF / 365.25)
SELECT 
    ltcfo.location,
    op.gender,
    COUNT(*) AS [active_in_care_10_19]
FROM
    ##last_teen_club_facility_outcome ltcfo
    LEFT JOIN omrs_patient op 
        ON op.patient_id = ltcfo.pat
WHERE
    ltcfo.state IN ('Patient transfer in', 'First time initiation')
    AND FLOOR(DATEDIFF(DAY, op.birthdate, @endDate) / 365.25) BETWEEN 10 AND 19
GROUP BY 
    ltcfo.location,
    op.gender
```

## Active above 19 male and female
```sql
USE openmrs_reporting;

DECLARE @endDate DATE = '2024-12-31';

-- Run the stored procedure
EXEC dbo.create_last_teen2_club_outcome_at_facility @endDate = @endDate;

-- Query results with age filter 20-22
SELECT 
    ltcfo.location,
    op.gender,
    COUNT(*) AS [active_in_care_20_22]
FROM
    ##last_teen_club_facility_outcome ltcfo
    LEFT JOIN omrs_patient op 
        ON op.patient_id = ltcfo.pat
WHERE
    ltcfo.state IN ('Patient transfer in', 'First time initiation')
    AND FLOOR(DATEDIFF(DAY, op.birthdate, @endDate) / 365.25) BETWEEN 20 AND 22
GROUP BY 
    ltcfo.location,
    op.gender;
```

## Active below 10 male and female
```sql
USE openmrs_reporting;

DECLARE @endDate DATE = '2024-12-31';

-- Run the stored procedure
EXEC dbo.create_last_teen2_club_outcome_at_facility @endDate = @endDate;

-- Query results for patients below 10 years
SELECT 
    ltcfo.location,
    op.gender,
    COUNT(*) AS [active_in_care_below_10]
FROM
    ##last_teen_club_facility_outcome ltcfo
    LEFT JOIN omrs_patient op 
        ON op.patient_id = ltcfo.pat
WHERE
    ltcfo.state IN ('Patient transfer in', 'First time initiation')
    AND FLOOR(DATEDIFF(DAY, op.birthdate, @endDate) / 365.25) < 10
GROUP BY 
    ltcfo.location,
    op.gender;
```










