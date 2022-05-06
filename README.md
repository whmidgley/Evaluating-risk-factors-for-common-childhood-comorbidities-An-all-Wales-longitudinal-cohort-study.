# Evaluating risk factors for common childhood comorbidities An all-Wales longitudinal cohort study.
## SQL

--weight-- 

-- This finds all patients with BMI recording between the ages 16 and 2 inclusive

-- below selects those underweight (although I did not use these weight classifications in the end)

CREATE TABLE SAIL1415V.WM_WEIGHT AS (
SELECT perc.ALF_PE, perc.GNDR_CD, perc.EVENT_VAL, perc.EVENT_CD, perc.EVENT_YR, perc.BMI_RANK, perc.EVENT_VAL AS BMI,
YEAR(perc.EVENT_DT - perc.WOB) AS BMI_AGE, perc.EVENT_DT AS BMI_DT, 'Underweight' AS Weight FROM 
(SELECT *, PERCENT_RANK() OVER(PARTITION BY bmi.GNDR_CD, (bmi.EVENT_YR -YEAR(bmi.WOB)) ORDER BY bmi.EVENT_VAL) AS BMI_RANK FROM 
(SELECT DISTINCT * FROM SAIL1415V.WLGP_GP_EVENT_CLEANSED_20220301 wgeac
WHERE wgeac.EVENT_CD = '22K..'
AND YEAR(wgeac.WOB) > 1998
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) <= 16
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) >= 2
AND wgeac.EVENT_VAL > 0
AND wgeac.EVENT_VAL < 100) bmi
) perc
WHERE perc.BMI_RANK < 0.02
AND perc.ALF_PE IS NOT NULL
)
WITH DATA;

--below selects those healthy weight

INSERT INTO SAILW1415V.WM_WEIGHT
SELECT perc.ALF_PE, perc.GNDR_CD, perc.EVENT_VAL, perc.EVENT_CD, perc.EVENT_YR, perc.BMI_RANK, perc.EVENT_VAL AS BMI,
YEAR(perc.EVENT_DT - perc.WOB) AS BMI_AGE, perc.EVENT_DT AS BMI_DT, 'Healthy' AS Weight FROM 
(SELECT *, PERCENT_RANK() OVER(PARTITION BY bmi.GNDR_CD, (bmi.EVENT_YR -YEAR(bmi.WOB)) ORDER BY bmi.EVENT_VAL) AS BMI_RANK FROM 
(SELECT DISTINCT * FROM SAIL1415V.WLGP_GP_EVENT_CLEANSED_20220301 wgeac
WHERE wgeac.EVENT_CD = '22K..'
AND YEAR(wgeac.WOB) > 1998
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) <= 16
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) >= 2
AND wgeac.EVENT_VAL > 0
AND wgeac.EVENT_VAL < 100) bmi
) perc
WHERE perc.BMI_RANK >= 0.02
AND perc.BMI_RANK < 0.91
AND perc.ALF_PE IS NOT NULL;

--below selects those overweight

INSERT INTO SAILW1415V.WM_WEIGHT
SELECT perc.ALF_PE, perc.GNDR_CD, perc.EVENT_VAL, perc.EVENT_CD, perc.EVENT_YR, perc.BMI_RANK, perc.EVENT_VAL AS BMI,
YEAR(perc.EVENT_DT - perc.WOB) AS BMI_AGE, perc.EVENT_DT AS BMI_DT, 'Overweight' AS Weight FROM 
(SELECT *, PERCENT_RANK() OVER(PARTITION BY bmi.GNDR_CD, (bmi.EVENT_YR -YEAR(bmi.WOB)) ORDER BY bmi.EVENT_VAL) AS BMI_RANK FROM 
(SELECT DISTINCT * FROM SAIL1415V.WLGP_GP_EVENT_CLEANSED_20220301 wgeac
WHERE wgeac.EVENT_CD = '22K..'
AND YEAR(wgeac.WOB) > 1998
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) <= 16
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) >= 2
AND wgeac.EVENT_VAL > 0
AND wgeac.EVENT_VAL < 100) bmi
) perc
WHERE perc.BMI_RANK >= 0.91
AND perc.BMI_RANK < 0.98
AND perc.ALF_PE IS NOT NULL;

--below selects those very overweight

INSERT INTO SAILW1415V.WM_WEIGHT
SELECT perc.ALF_PE, perc.GNDR_CD, perc.EVENT_VAL, perc.EVENT_CD, perc.EVENT_YR, perc.BMI_RANK, perc.EVENT_VAL AS BMI,
YEAR(perc.EVENT_DT - perc.WOB) AS BMI_AGE, perc.EVENT_DT AS BMI_DT, 'VeryOver' AS Weight FROM 
(SELECT *, PERCENT_RANK() OVER(PARTITION BY bmi.GNDR_CD, (bmi.EVENT_YR -YEAR(bmi.WOB)) ORDER BY bmi.EVENT_VAL) AS BMI_RANK FROM 
(SELECT DISTINCT * FROM SAIL1415V.WLGP_GP_EVENT_CLEANSED_20220301 wgeac
WHERE wgeac.EVENT_CD = '22K..'
AND YEAR(wgeac.WOB) > 1998
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) <= 16
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) >= 2
AND wgeac.EVENT_VAL > 0
AND wgeac.EVENT_VAL < 100) bmi
) perc
WHERE perc.BMI_RANK >= 0.98
AND perc.BMI_RANK < 0.995
AND perc.ALF_PE IS NOT NULL;

--below selects those obese

INSERT INTO SAILW1415V.WM_WEIGHT
SELECT perc.ALF_PE, perc.GNDR_CD, perc.EVENT_VAL, perc.EVENT_CD, perc.EVENT_YR, perc.BMI_RANK, perc.EVENT_VAL AS BMI,
YEAR(perc.EVENT_DT - perc.WOB) AS BMI_AGE, perc.EVENT_DT AS BMI_DT, 'SevereObese' AS Weight FROM 
(SELECT *, PERCENT_RANK() OVER(PARTITION BY bmi.GNDR_CD, (bmi.EVENT_YR -YEAR(bmi.WOB)) ORDER BY bmi.EVENT_VAL) AS BMI_RANK FROM 
(SELECT DISTINCT * FROM SAIL1415V.WLGP_GP_EVENT_CLEANSED_20220301 wgeac
WHERE wgeac.EVENT_CD = '22K..'
AND YEAR(wgeac.WOB) > 1998
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) <= 16
AND YEAR(wgeac.EVENT_DT - wgeac.WOB) >= 2
AND wgeac.EVENT_VAL > 0
AND wgeac.EVENT_VAL < 100) bmi
) perc
WHERE perc.BMI_RANK >= 0.995
AND perc.ALF_PE IS NOT NULL;

-- (bmi RANK IS based off sail DATA hence might be biased) -- N.B. I did not use these ranks in the final project

--comorbidities used

CREATE TABLE SAILW1415V.WM_COMORBID_CD AS (
SELECT *, 'Type 2 Diabetes' AS COMORBIDITY FROM SAILREFRV.READ_CD rc
WHERE rc.READ_CD like 'C10F%'
)
WITH DATA;

INSERT INTO SAILW1415V.WM_COMORBID_CD 
SELECT *, 'BMI' AS COMORBIDITY FROM SAILREFRV.READ_CD rc
WHERE rc.READ_CD like '22K%';

INSERT INTO SAILW1415V.WM_COMORBID_CD 
SELECT *, 'Constipation' AS COMORBIDITY FROM SAILREFRV.READ_CD rc
WHERE rc.READ_CD like '19CZ%'
OR rc.READ_CD like '19C.%'
OR rc.READ_CD like '19C2%'
OR rc.READ_CD like 'J520%';

INSERT INTO SAILW1415V.WM_COMORBID_CD 
SELECT *, 'Obesity' AS COMORBIDITY FROM SAILREFRV.READ_CD rc
WHERE rc.READ_CD like 'C38%';

INSERT INTO SAILW1415V.WM_COMORBID_CD 
SELECT *, 'Depression' AS COMORBIDITY FROM SAILREFRV.READ_CD rc
WHERE rc.READ_CD like 'E112%'
OR rc.READ_CD like 'E113%'
OR rc.READ_CD like 'E2B%'
OR rc.READ_CD like 'Eu32%'
OR rc.READ_CD like 'Eu33%';

INSERT INTO SAILW1415V.WM_COMORBID_CD 
SELECT *, 'Fatty Liver' AS COMORBIDITY FROM SAILREFRV.READ_CD rc
WHERE rc.READ_CD like 'J61y1';

--child comorbidities--
--Below adds a label on all events with relevant comorbidities
CREATE TABLE SAILW1415V.WM_CHILD_COMORBIDITY AS (
SELECT ch.*, cc.COMORBIDITY FROM
(SELECT wgeac.ALF_PE, wgeac.GNDR_CD, w.BMI_AGE, w.BMI_DT, wgeac.WOB, wgeac.EVENT_DT,
YEAR(wgeac.EVENT_DT - wgeac.WOB) AS EVENT_AGE,
YEAR(wgeac.WOB) AS YOB, wgeac.EVENT_YR,
wgeac.EVENT_CD, wgeac.EVENT_VAL, w.BMI_RANK, w.BMI, w.WEIGHT
FROM SAIL1415V.WLGP_GP_EVENT_CLEANSED_20220301 wgeac
INNER JOIN SAILW1415V.WM_WEIGHT w
ON wgeac.ALF_PE = w.ALF_PE
WHERE YEAR(wgeac.EVENT_DT - wgeac.WOB) >= 0) ch
LEFT JOIN SAILW1415V.WM_COMORBID_CD cc
ON ch.EVENT_CD = cc.READ_CD
WHERE EVENT_DT IS NOT NULL)
WITH DATA;

--creating binary columns to prepare for summing comorbidities--

CREATE TABLE SAILW1415V.WM_LOGREG AS (
SELECT chc.*,
CASE WHEN chc.COMORBIDITY = 'Type 2 Diabetes' 
		THEN 1 ELSE 0
END AS DIAB,
CASE WHEN chc.COMORBIDITY = 'Constipation' 
		THEN 1 ELSE 0
END AS CONST,
CASE WHEN chc.COMORBIDITY = 'Fatty Liver' 
		THEN 1 ELSE 0
END AS FL,
CASE WHEN chc.COMORBIDITY = 'Depression' 
		THEN 1 ELSE 0
END AS DEP,
CASE WHEN chc.COMORBIDITY = 'Obesity' 
		THEN 1 ELSE 0
END AS OBESE
FROM SAILW1415V.WM_CHILD_COMORBIDITY chc
)
WITH DATA;

-- Adds WIMD to those 11-16

--PLOT# tables were used for creating graphs using all events, since creating graphs is much easier using one column rather than 4 binary columns

CREATE TABLE SAILW1415V.WM_PLOT15 AS (
SELECT * FROM 
(SELECT chc.*, wcagcl.WIMD_2014_DECILE FROM SAILW1415V.WM_CHILD_COMORBIDITY chc
INNER JOIN SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl
ON chc.ALF_PE = wcagcl.ALF_PE
WHERE chc.COMORBIDITY IS NULL
AND chc.BMI_DT < (chc.WOB + 16 YEARS)
AND chc.BMI_DT >= (chc.WOB + 11 YEARS)
AND chc.EVENT_DT < (chc.WOB + 16 YEARS)
AND chc.EVENT_DT >= (chc.WOB + 11 YEARS)
ORDER BY RAND() 
LIMIT 500) UNION 
(SELECT chc.*, wcagcl.WIMD_2014_DECILE FROM SAILW1415V.WM_CHILD_COMORBIDITY chc
INNER JOIN SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl
ON chc.ALF_PE = wcagcl.ALF_PE
WHERE chc.COMORBIDITY IS NOT NULL
AND chc.BMI_DT < (chc.WOB + 16 YEARS)
AND chc.BMI_DT >= (chc.WOB + 11 YEARS)
AND chc.EVENT_DT < (chc.WOB + 16 YEARS)
AND chc.EVENT_DT >= (chc.WOB + 11 YEARS))
)
WITH DATA;

-- Adds WIMD to those 6-11

CREATE TABLE SAILW1415V.WM_PLOT10 AS (
SELECT * FROM 
(SELECT chc.*, wcagcl.WIMD_2014_DECILE FROM SAILW1415V.WM_CHILD_COMORBIDITY chc
INNER JOIN SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl
ON chc.ALF_PE = wcagcl.ALF_PE
WHERE chc.COMORBIDITY IS NOT NULL
AND chc.BMI_DT < (chc.WOB + 11 YEARS)
AND chc.BMI_DT >= (chc.WOB + 6 YEARS)
AND chc.EVENT_DT < (chc.WOB + 11 YEARS)
AND chc.EVENT_DT >= (chc.WOB + 6 YEARS))
)
WITH DATA;

-- Adds WIMD to those 2-6

CREATE TABLE SAILW1415V.WM_PLOT5 AS (
SELECT * FROM 
(SELECT chc.*, wcagcl.WIMD_2014_DECILE FROM SAILW1415V.WM_CHILD_COMORBIDITY chc
INNER JOIN SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl
ON chc.ALF_PE = wcagcl.ALF_PE
WHERE chc.COMORBIDITY IS NOT NULL
AND chc.BMI_DT < (chc.WOB + 6 YEARS)
AND chc.BMI_DT >= (chc.WOB + 2 YEARS)
AND chc.EVENT_DT < (chc.WOB + 6 YEARS)
AND chc.EVENT_DT >= (chc.WOB + 2 YEARS))
)
WITH DATA;

-- sums all per age per person, per date BMI was taken -- this table was not used

CREATE TABLE SAILW1415V.WM_SUM_ALLAGE AS (
SELECT lr.GNDR_CD,
lr.YOB,
lr.BMI_DT,
lr.EVENT_AGE,
AVG(BMI_RANK) AS AVG_BMI,
AVG(BMI) AS AVG_BMI_RAW,
CASE WHEN AVG(BMI_RANK) < 0.02 THEN 'Underweight'
WHEN AVG(BMI_RANK) >=0.02 AND AVG(BMI_RANK) <0.91 THEN 'Healthy'
WHEN AVG(BMI_RANK) >=0.91 AND  AVG(BMI_RANK) <0.98 THEN 'Overweight'
WHEN AVG(BMI_RANK) >=0.98 AND  AVG(BMI_RANK) <0.995 THEN 'Very Over'
WHEN AVG(BMI_RANK) >=0.995 THEN 'SevereObese'
END AS AVG_WEIGHT,
SUM(DIAB) AS DIAB, SUM(CONST) AS CONST, SUM(FL) AS FL, SUM(DEP) AS DEP, SUM(OBESE) AS OBESE
FROM SAILW1415V.WM_LOGREG lr
WHERE lr.YOB >= 1998
GROUP BY lr.ALF_PE, lr.GNDR_CD, lr.YOB, lr.BMI_DT, EVENT_AGE 
)
WITH DATA;

--all events of patients 2-5 yrs

CREATE TABLE SAILW1415V.WM_AGE5 AS (
SELECT lr.*,
wcagcl.WIMD_2014_DECILE
FROM SAILW1415V.WM_LOGREG lr
INNER JOIN SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl
ON lr.ALF_PE = wcagcl.ALF_PE
WHERE lr.BMI_DT < (lr.WOB + 6 YEARS)
AND lr.BMI_DT >= (lr.WOB + 2 YEARS)
AND lr.EVENT_DT < (lr.WOB + 6 YEARS)
AND lr.EVENT_DT >= (lr.WOB + 2 YEARS)
)
WITH DATA;


--all patients 2-6 yrs (although multiple WIMD recordings may result in multiple rows per person)

CREATE TABLE SAILW1415V.WM_SUM_AGE5 AS (
SELECT a5.ALF_PE,
a5.GNDR_CD,
a5.YOB,
AVG(BMI_RANK) AS AVG_BMI,
AVG(BMI) AS AVG_BMI_RAW,
CASE WHEN AVG(BMI_RANK) < 0.02 THEN 'Underweight'
WHEN AVG(BMI_RANK) >=0.02 AND AVG(BMI_RANK) <0.91 THEN 'Healthy'
WHEN AVG(BMI_RANK) >=0.91 AND  AVG(BMI_RANK) <0.98 THEN 'Overweight'
WHEN AVG(BMI_RANK) >=0.98 AND  AVG(BMI_RANK) <0.995 THEN 'Very Over'
WHEN AVG(BMI_RANK) >=0.995 THEN 'SevereObese'
END AS AVG_WEIGHT,
a5.WIMD_2014_DECILE,
SUM(DIAB) AS DIAB, SUM(CONST) AS CONST, SUM(FL) AS FL, SUM(DEP) AS DEP, SUM(OBESE) AS OBESE
FROM SAILW1415V.WM_AGE5 a5
GROUP BY a5.ALF_PE, a5.GNDR_CD, a5.YOB, a5.WIMD_2014_DECILE
)
WITH DATA;

--all events of patients 6-11 yrs

CREATE TABLE SAILW1415V.WM_AGE10 AS (
SELECT lr.*,
wcagcl.WIMD_2014_DECILE
FROM SAILW1415V.WM_LOGREG lr
INNER JOIN SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl
ON lr.ALF_PE = wcagcl.ALF_PE
WHERE lr.BMI_DT < (lr.WOB + 11 YEARS)
AND lr.BMI_DT >= (lr.WOB + 6 YEARS)
AND lr.EVENT_DT < (lr.WOB + 11 YEARS)
AND lr.EVENT_DT >= (lr.WOB + 6 YEARS)
)
WITH DATA;

--all patients 6-11 yrs

CREATE TABLE SAILW1415V.WM_SUM_AGE10 AS (
SELECT a10.ALF_PE,
a10.GNDR_CD,
a10.YOB,
AVG(BMI_RANK) AS AVG_BMI,
AVG(BMI) AS AVG_BMI_RAW,
CASE WHEN AVG(BMI_RANK) < 0.02 THEN 'Underweight'
WHEN AVG(BMI_RANK) >=0.02 AND AVG(BMI_RANK) <0.91 THEN 'Healthy'
WHEN AVG(BMI_RANK) >=0.91 AND  AVG(BMI_RANK) <0.98 THEN 'Overweight'
WHEN AVG(BMI_RANK) >=0.98 AND  AVG(BMI_RANK) <0.995 THEN 'Very Over'
WHEN AVG(BMI_RANK) >=0.995 THEN 'SevereObese'
END AS AVG_WEIGHT,
a10.WIMD_2014_DECILE,
SUM(DIAB) AS DIAB, SUM(CONST) AS CONST, SUM(FL) AS FL, SUM(DEP) AS DEP, SUM(OBESE) AS OBESE
FROM SAILW1415V.WM_AGE10 a10
GROUP BY a10.ALF_PE, a10.GNDR_CD, a10.YOB, a10.WIMD_2014_DECILE
)
WITH DATA;

--all events of patients 11-16 yrs

CREATE TABLE SAILW1415V.WM_AGE15 AS (
SELECT lr.ALF_PE, lr.BMI_DT, lr.BMI_RANK, lr.CONST, lr.DEP, lr.DIAB, lr.FL, lr.OBESE , lr.GNDR_CD, lr.YOB, lr.BMI, lr.EVENT_DT,
wcagcl.WIMD_2014_DECILE
FROM SAILW1415V.WM_LOGREG lr
INNER JOIN SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl
ON lr.ALF_PE = wcagcl.ALF_PE
WHERE lr.BMI_DT < (lr.WOB + 16 YEARS)
AND lr.BMI_DT >= (lr.WOB + 11 YEARS)
AND lr.EVENT_DT < (lr.WOB + 16 YEARS)
AND lr.EVENT_DT >= (lr.WOB + 11 YEARS)
)
WITH DATA;

--all patients 11-16 yrs

CREATE TABLE SAILW1415V.WM_SUM_AGE15 AS (
SELECT a15.ALF_PE,
a15.GNDR_CD,
a15.YOB,
AVG(BMI_RANK) AS AVG_BMI,
AVG(BMI) AS AVG_BMI_RAW,
CASE WHEN AVG(BMI_RANK) < 0.02 THEN 'Underweight'
WHEN AVG(BMI_RANK) >=0.02 AND AVG(BMI_RANK) <0.91 THEN 'Healthy'
WHEN AVG(BMI_RANK) >=0.91 AND  AVG(BMI_RANK) <0.98 THEN 'Overweight'
WHEN AVG(BMI_RANK) >=0.98 AND  AVG(BMI_RANK) <0.995 THEN 'Very Over'
WHEN AVG(BMI_RANK) >=0.995 THEN 'SevereObese'
END AS AVG_WEIGHT,
a15.WIMD_2014_DECILE,
SUM(DIAB) AS DIAB, SUM(CONST) AS CONST, SUM(FL) AS FL, SUM(DEP) AS DEP, SUM(OBESE) AS OBESE
FROM SAILW1415V.WM_AGE15 a15
GROUP BY a15.ALF_PE, a15.GNDR_CD, a15.YOB, a15.WIMD_2014_DECILE
)
WITH DATA;

--can I predict comorbities in later life from younger BMI records?

--all BMI recordings from 2-6yr olds but comorbidities from 6-11 yr olds linked by ALF_PE (i.e. patient ID)

CREATE TABLE SAILW1415V.WM_BMI_5_TO_10 AS (
SELECT sa5.ALF_PE, sa5.AVG_BMI AS AVG_BMI5, sa5.AVG_BMI_RAW AS AVG_BMI_RAW5, sa5.GNDR_CD, sa5.YOB, sa5.WIMD_2014_DECILE,
sa10.DIAB, sa10.CONST, sa10.FL, sa10.DEP
FROM SAILW1415V.WM_SUM_AGE5 sa5
INNER JOIN SAILW1415V.WM_SUM_AGE10 sa10
ON sa5.ALF_PE = sa10.ALF_PE
)
WITH DATA;

--all BMI recordings from 2-6yr olds but comorbidities from 11-16 yr olds

CREATE TABLE SAILW1415V.WM_BMI_5_TO_15 AS (
SELECT sa5.ALF_PE, sa5.AVG_BMI, sa5.AVG_BMI_RAW, sa5.GNDR_CD, sa5.YOB, sa5.WIMD_2014_DECILE,
sa15.DIAB, sa15.CONST, sa15.FL, sa15.DEP
FROM SAILW1415V.WM_SUM_AGE5 sa5
INNER JOIN SAILW1415V.WM_SUM_AGE15 sa15
ON sa5.ALF_PE = sa15.ALF_PE
)
WITH DATA;

--all BMI recordings from 6-11yr olds but comorbidities from 11-16 yr olds

CREATE TABLE SAILW1415V.WM_BMI_10_TO_15 AS (
SELECT sa10.ALF_PE, sa10.AVG_BMI, sa10.AVG_BMI_RAW, sa10.GNDR_CD, sa10.YOB, sa10.WIMD_2014_DECILE,
sa15.DIAB, sa15.CONST, sa15.FL, sa15.DEP
FROM SAILW1415V.WM_SUM_AGE10 sa10
INNER JOIN SAILW1415V.WM_SUM_AGE15 sa15
ON sa10.ALF_PE = sa15.ALF_PE
)
WITH DATA;

SELECT DISTINCT sa15.ALF_PE FROM SAILW1415V.WM_SUM_AGE15 sa15
INNER JOIN
(SELECT sa5.ALF_PE FROM SAILW1415V.WM_SUM_AGE5 sa5
INNER JOIN  SAILW1415V.WM_SUM_AGE10 sa10
ON sa5.ALF_PE = sa10.ALF_PE) sa510
ON sa15.ALF_PE = sa510.ALF_PE;

-- Following line used to count the number of patints in a certain group. e.g. below counts number of patients ages 2-6.

--N.B. this includes those with GNDR_CD 9, therefore it will be slightly larger than stated.

--The number quoted in the dissertation was calculated using R

SELECT count(*) FROM SAILW1415V.WM_SUM_AGE10;

## R
### Setup

Where I have written \*\*\*, this means I have removed text for security sake


#Install packages if necessary
#install.packages("SAILDBUtils")
#install.packages("tidyverse")
#install.packages("rstatix")
#install.packages("ggpubr")
#install.packages("ROCR")
#install.packages("pryr")
#install.packages("caret")
#install.packages("ggsignif")

#Load libraries
library(tidyverse)
library(SAILDBUtils)
library(rstatix)
library(ggpubr)
library(ROCR)
library(pryr)
library(caret)
library(ggsignif)

#set working directory
setwd("P:/\*\*\*/workspace/1415/Scripts/R Scripts")

#Connect to SAIL
a <- SAILDBUtils::SAILConnect("\*\*\*", "\*\*\*")

#SQL global table names
w <- paste0("SAILW1415V.WM_WEIGHT w")
wcagcl <- paste0("SAIL1415V.WDSD_CLEAN_ADD_GEOG_CHAR_LSOA2011_20220328 wcagcl")
cc <- paste0("SAILW1415V.WM_COMORBID_CD cc")
chc <- paste0("SAILW1415V.WM_CHILD_COMORBIDITY chc")
lr <- paste0("SAILW1415V.WM_LOGREG lr")
a5 <- paste0("SAILW1415V.WM_AGE5 a5")
sa5 <- paste0("SAILW1415V.WM_SUM_AGE5 sa5")
a10 <- paste0("SAILW1415V.WM_AGE10 a10")
sa10 <- paste0("SAILW1415V.WM_SUM_AGE10 sa10")
a15 <- paste0("SAILW1415V.WM_AGE15 a15")
sa15 <- paste0("SAILW1415V.WM_SUM_AGE15 sa15")
saa <- paste0("SAILW1415V.WM_SUM_ALLAGE saa")
b5t10 <- paste0("SAILW1415V.WM_BMI_5_TO_10 b5t10")
b5t15 <- paste0("SAILW1415V.WM_BMI_5_TO_15 b5t15")
b10t15 <- paste0("SAILW1415V.WM_BMI_10_TO_15 b10t15")
p15 <- paste0("SAILW1415V.WM_PLOT15")
p10 <- paste0("SAILW1415V.WM_PLOT10")
p5 <- paste0("SAILW1415V.WM_PLOT5")

#Age stratification
##Each of the following does the same for each age group. It replaces the summed number of events regarding each comorbidity
##with a 1 if they've had at least 1 event regarding the comorbidity within the time frame and a 0 if they have not had any.
##I also left another column for depression and constipation summed so I could perform linear regression.
##It is at this point that I removed GNDR_CD 9 and all children born after the year 1999.
##5 Year olds
FYOs_qry <- paste0("SELECT sa5.GNDR_CD, sa5.YOB, sa5.AVG_BMI, sa5.AVG_BMI_RAW, sa5.AVG_WEIGHT, sa5.WIMD_2014_DECILE,
                   CASE WHEN sa5.DIAB >= 1 THEN 1 ELSE 0 END AS DIAB_YN,
                   sa5.CONST AS CONST_FREQ,
                   CASE WHEN sa5.CONST >= 1 THEN 1 ELSE 0 END AS CONST_YN,
                   CASE WHEN sa5.FL >= 1 THEN 1 ELSE 0 END AS FL_YN,
                   sa5.DEP AS DEP_FREQ,
                   CASE WHEN sa5.DEP >= 1 THEN 1 ELSE 0 END AS DEP_YN,
                   CASE WHEN sa5.DIAB >=1
                    OR sa5.CONST >=1
                    OR sa5.FL >=1
                    OR sa5.DEP >=1 THEN 1 ELSE 0 END AS ADVERSE_OUTCOME,
                  CASE WHEN sa5.DIAB >=1
                    OR sa5.FL >=1 THEN 1 ELSE 0 END AS DFL,
                  CASE WHEN sa5.OBESE >= 1 THEN 1 ELSE 0 END AS OBESE_YN
                   FROM ", sa5,
                   " WHERE sa5.YOB >= 1999
                   AND sa5.GNDR_CD != 9")
FYOs <- SAILDBUtils::runSQL(a, FYOs_qry)
FYOs$GNDR_CD <- as.factor(FYOs$GNDR_CD)
FYOs$AVG_WEIGHT <- factor(FYOs$AVG_WEIGHT, levels = c("Underweight","Healthy", "Overweight", "Very Over", "SevereObese"))

#15 Year olds

FtYOs_qry <- paste0("SELECT sa15.GNDR_CD, sa15.YOB, sa15.AVG_BMI, sa15.AVG_BMI_RAW, sa15.AVG_WEIGHT, sa15.WIMD_2014_DECILE,
                   CASE WHEN sa15.DIAB >= 1 THEN 1 ELSE 0 END AS DIAB_YN,
                   sa15.CONST AS CONST_FREQ,
                   CASE WHEN sa15.CONST >= 1 THEN 1 ELSE 0 END AS CONST_YN,
                   CASE WHEN sa15.FL >= 1 THEN 1 ELSE 0 END AS FL_YN,
                   sa15.DEP AS DEP_FREQ,
                   CASE WHEN sa15.DEP >= 1 THEN 1 ELSE 0 END AS DEP_YN, 
                   CASE WHEN sa15.DIAB >=1
                    OR sa15.CONST >=1
                    OR sa15.FL >=1
                    OR sa15.DEP >=1 THEN 1 ELSE 0 END AS ADVERSE_OUTCOME,
                   CASE WHEN sa15.DIAB >=1
                    OR sa15.FL >=1 THEN 1 ELSE 0 END AS DFL,
                  CASE WHEN sa15.OBESE >= 1 THEN 1 ELSE 0 END AS OBESE_YN 
                    FROM ", sa15,
                    " WHERE sa15.YOB >= 1999 
                   AND sa15.GNDR_CD != 9")
FtYOs <- SAILDBUtils::runSQL(a, FtYOs_qry)
FtYOs$AVG_WEIGHT <- factor(FtYOs$AVG_WEIGHT, levels = c("Underweight","Healthy", "Overweight", "Very Over", "SevereObese"))
FtYOs$GNDR_CD <- as.factor(FtYOs$GNDR_CD)
#Five year old effect on later life
##Effect on 10 yr olds
FtT_qry <- paste0("SELECT b5t10.GNDR_CD, b5t10.YOB, b5t10.AVG_BMI5 AS AVG_BMI, b5t10.AVG_BMI_RAW5 AS AVG_BMI_RAW, b5t10.WIMD_2014_DECILE,
                   CASE WHEN b5t10.DIAB >= 1 THEN 1 ELSE 0 END AS DIAB_YN,
                   b5t10.CONST AS CONST_FREQ,
                   CASE WHEN b5t10.CONST >= 1 THEN 1 ELSE 0 END AS CONST_YN,
                   CASE WHEN b5t10.FL >= 1 THEN 1 ELSE 0 END AS FL_YN,
                   b5t10.DEP AS DEP_FREQ,
                   CASE WHEN b5t10.DEP >= 1 THEN 1 ELSE 0 END AS DEP_YN, 
                   CASE WHEN b5t10.DIAB >=1
                    OR b5t10.CONST >=1
                    OR b5t10.FL >=1
                    OR b5t10.DEP >=1 THEN 1 ELSE 0 END AS ADVERSE_OUTCOME,
                  CASE WHEN b5t10.DIAB >=1
                    OR b5t10.FL >=1 THEN 1 ELSE 0 END AS DFL
                  FROM ", b5t10,
                  " WHERE b5t10.YOB >= 1999 
                   AND b5t10.GNDR_CD != 9")
FtT <- SAILDBUtils::runSQL(a, FtT_qry)
FtT$GNDR_CD <- as.factor(FtT$GNDR_CD)

##Effect on 15 yr olds
FtFt_qry <- paste0("SELECT b5t15.GNDR_CD, b5t15.YOB, b5t15.AVG_BMI, b5t15.AVG_BMI_RAW, b5t15.WIMD_2014_DECILE,
                   CASE WHEN b5t15.DIAB >= 1 THEN 1 ELSE 0 END AS DIAB_YN,
                   b5t15.CONST AS CONST_FREQ,
                   CASE WHEN b5t15.CONST >= 1 THEN 1 ELSE 0 END AS CONST_YN,
                   CASE WHEN b5t15.FL >= 1 THEN 1 ELSE 0 END AS FL_YN,
                   b5t15.DEP AS DEP_FREQ,
                   CASE WHEN b5t15.DEP >= 1 THEN 1 ELSE 0 END AS DEP_YN, 
                   CASE WHEN b5t15.DIAB >=1
                    OR b5t15.CONST >=1
                    OR b5t15.FL >=1
                    OR b5t15.DEP >=1 THEN 1 ELSE 0 END AS ADVERSE_OUTCOME,
                  CASE WHEN b5t15.DIAB >=1
                    OR b5t15.FL >=1 THEN 1 ELSE 0 END AS DFL FROM ", b5t15,
                   " WHERE b5t15.YOB >= 1999 
                   AND b5t15.GNDR_CD != 9")
FtFt <- SAILDBUtils::runSQL(a, FtFt_qry)
FtFt$GNDR_CD <- as.factor(FtFt$GNDR_CD)


##Effect of 10 yr old BMI on 15 yr olds
TtFt_qry <- paste0("SELECT b10t15.GNDR_CD, b10t15.YOB, b10t15.AVG_BMI, b10t15.AVG_BMI_RAW, b10t15.WIMD_2014_DECILE,
                   CASE WHEN b10t15.DIAB >= 1 THEN 1 ELSE 0 END AS DIAB_YN,
                   b10t15.CONST AS CONST_FREQ,
                   CASE WHEN b10t15.CONST >= 1 THEN 1 ELSE 0 END AS CONST_YN,
                   CASE WHEN b10t15.FL >= 1 THEN 1 ELSE 0 END AS FL_YN,
                   b10t15.DEP AS DEP_FREQ,
                   CASE WHEN b10t15.DEP >= 1 THEN 1 ELSE 0 END AS DEP_YN, 
                   CASE WHEN b10t15.DIAB >=1
                    OR b10t15.CONST >=1
                    OR b10t15.FL >=1
                    OR b10t15.DEP >=1 THEN 1 ELSE 0 END AS ADVERSE_OUTCOME,
                   CASE WHEN b10t15.DIAB >=1
                    OR b10t15.FL >=1 THEN 1 ELSE 0 END AS DFL FROM ", b10t15,
                   " WHERE b10t15.YOB >= 1999 
                   AND b10t15.GNDR_CD != 9")
TtFt <- SAILDBUtils::runSQL(a, TtFt_qry)
TtFt$GNDR_CD <- as.factor(TtFt$GNDR_CD)

#In order to find the number of patients in each group for these last 6 groups, I used the nrow() function for each
#to account for YOB>1999 and GNDR_CD != 9 rather than using "COUNT" in SQL.

#Following code makes functions to make the process of writing and testing models easier

#Train test split
splitData <- function(someData, trainPercent) {
  
  bound <- floor(nrow(someData)*trainPercent)
  someData <- someData[sample(nrow(someData)),]
  someData.train <- someData[1:bound,]
  someData.test <- someData[(bound+1):nrow(someData),]
  
  obj <- list(
    "train" = someData.train,
    "test" = someData.test)
  return(obj)
}

#Down-sampling, Take only a sample of negative cases to give an equally weighted sample
weight <- function(someData, comorbid) {
  
  filter(someData, comorbid == 1) %>%
    rbind(., filter(someData, comorbid == 0)[sample(nrow(.)),]) %>%
    .[sample(nrow(.)),]
}

#Test model, N.B. I changed the title name for the ROC curve each time I ran it so I could give each ROC curve a suitable
#title without having to write code to do that, or writing the whole thing out each time
testModel <- function(model, testData, observed) {
  
  model.pd <- predict(model, testData, type = "response")
  
  model.pd2 <- prediction(model.pd, observed)
  model.ROC.perf <- performance(model.pd2, "tpr", "fpr")
  
  model.AUC.perf <- performance(model.pd2, "auc")
  model.AUC <- model.AUC.perf@y.values[[1]]
  model.spec.perf <- performance(model.pd2, "spec", x.measure = "cutoff")
  model.sens.perf <- performance(model.pd2, "sens", x.measure = "cutoff")
  
  best.sum <- which.max(abs(model.sens.perf@y.values[[1]]+model.spec.perf@y.values[[1]]))
  cutoff <- model.sens.perf@x.values[[1]][best.sum]
  model.conf <- table(predicted = as.numeric(model.pd> cutoff), observed = observed)
  
  model.sen <- (model.conf[2,2])/(model.conf[1,2]+model.conf[2,2])
  model.spec <- (model.conf[1,1])/(model.conf[1,1]+model.conf[2,1])
  
  model.ppv <- (model.conf[2,2]/
                  (model.conf[2,1]+model.conf[2,2]))
  model.npv <- (model.conf[1,1]/
                  (model.conf[1,2]+model.conf[1,1]))
  
  model.acc <- (model.conf[2,2]+model.conf[1,1])/
    (model.conf[1,1]+model.conf[1,2]+model.conf[2,1]+model.conf[2,2])
  
  obj <- list(
    "AUC" = model.AUC,
    "conf" = model.conf,
    "sen" = model.sen,
    "spec" = model.spec,
    "ppv" = model.ppv,
    "npv" = model.npv,
    "acc" = model.acc)
  return(obj)
}
modelROC <- function(model, testData, observed) {
  
  model.pd <- predict(model, testData, type = "response")
  
  model.pd2 <- prediction(model.pd, observed)
  model.ROC.perf <- performance(model.pd2, "tpr", "fpr")
  
  plot(model.ROC.perf, main = "Depression in group 3", colorize = TRUE)
  abline(a = 0, b = 1)
}


#Crossfold Validation
#Raw BMI, some use Year of birth
AAcv <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$ADVERSE_OUTCOME)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$ADVERSE_OUTCOME)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$ADVERSE_OUTCOME)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$ADVERSE_OUTCOME)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$ADVERSE_OUTCOME)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$ADVERSE_OUTCOME)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$ADVERSE_OUTCOME)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$ADVERSE_OUTCOME)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$ADVERSE_OUTCOME)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$ADVERSE_OUTCOME)
  
  lr1 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$ADVERSE_OUTCOME)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$ADVERSE_OUTCOME)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$ADVERSE_OUTCOME)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$ADVERSE_OUTCOME)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$ADVERSE_OUTCOME)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$ADVERSE_OUTCOME)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$ADVERSE_OUTCOME)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$ADVERSE_OUTCOME)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$ADVERSE_OUTCOME)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$ADVERSE_OUTCOME)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
DFLcv.YOB <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DFL)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DFL)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DFL)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DFL)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DFL)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DFL)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DFL)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DFL)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DFL)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DFL)
  
  lr1 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DFL)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DFL)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DFL)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DFL)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DFL)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DFL)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DFL)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DFL)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DFL)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DFL)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
DFLcv <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DFL)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DFL)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DFL)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DFL)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DFL)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DFL)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DFL)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DFL)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DFL)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DFL)
  
  lr1 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DFL ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DFL)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DFL)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DFL)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DFL)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DFL)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DFL)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DFL)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DFL)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DFL)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DFL)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
diabcv <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DIAB_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DIAB_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DIAB_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DIAB_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DIAB_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DIAB_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DIAB_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DIAB_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DIAB_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DIAB_YN)
  
  lr1 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DIAB_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DIAB_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DIAB_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DIAB_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DIAB_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DIAB_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DIAB_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DIAB_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DIAB_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DIAB_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
FLcv <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$FL_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$FL_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$FL_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$FL_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$FL_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$FL_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$FL_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$FL_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$FL_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$FL_YN)
  
  lr1 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(FL_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$FL_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$FL_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$FL_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$FL_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$FL_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$FL_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$FL_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$FL_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$FL_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$FL_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
FLcv.YOB <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$FL_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$FL_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$FL_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$FL_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$FL_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$FL_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$FL_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$FL_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$FL_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$FL_YN)
  
  lr1 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(FL_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$FL_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$FL_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$FL_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$FL_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$FL_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$FL_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$FL_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$FL_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$FL_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$FL_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
Depcv <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DEP_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DEP_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DEP_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DEP_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DEP_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DEP_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DEP_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DEP_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DEP_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DEP_YN)
  
  lr1 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DEP_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DEP_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DEP_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DEP_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DEP_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DEP_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DEP_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DEP_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DEP_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DEP_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
Depcv.YOB <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DEP_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DEP_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DEP_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DEP_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DEP_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DEP_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DEP_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DEP_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DEP_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DEP_YN)
  
  lr1 <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DEP_YN ~ GNDR_CD + YOB + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DEP_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DEP_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DEP_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DEP_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DEP_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DEP_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DEP_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DEP_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DEP_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DEP_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
constcv <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$CONST_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$CONST_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$CONST_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$CONST_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$CONST_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$CONST_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$CONST_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$CONST_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$CONST_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$CONST_YN)
  
  lr1 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$CONST_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$CONST_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$CONST_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$CONST_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$CONST_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$CONST_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$CONST_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$CONST_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$CONST_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$CONST_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}

#Ranked BMI
DFLcv.rank <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DFL)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DFL)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DFL)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DFL)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DFL)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DFL)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DFL)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DFL)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DFL)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DFL)
  
  lr1 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DFL ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DFL)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DFL)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DFL)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DFL)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DFL)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DFL)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DFL)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DFL)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DFL)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DFL)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
diabcv.rank <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DIAB_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DIAB_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DIAB_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DIAB_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DIAB_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DIAB_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DIAB_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DIAB_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DIAB_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DIAB_YN)
  
  lr1 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DIAB_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DIAB_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DIAB_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DIAB_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DIAB_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DIAB_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DIAB_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DIAB_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DIAB_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DIAB_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DIAB_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
FLcv.rank <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$FL_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$FL_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$FL_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$FL_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$FL_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$FL_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$FL_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$FL_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$FL_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$FL_YN)
  
  lr1 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(FL_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$FL_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$FL_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$FL_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$FL_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$FL_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$FL_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$FL_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$FL_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$FL_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$FL_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
Depcv.rank <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$DEP_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$DEP_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$DEP_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$DEP_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$DEP_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$DEP_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$DEP_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$DEP_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$DEP_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$DEP_YN)
  
  lr1 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(DEP_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$DEP_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$DEP_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$DEP_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$DEP_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$DEP_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$DEP_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$DEP_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$DEP_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$DEP_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$DEP_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}
constcv.rank <- function(someData, trainPercent, predictors){
  
  someData.split1 <-  splitData(someData, trainPercent)
  someData.split2 <-  splitData(someData, trainPercent)
  someData.split3 <-  splitData(someData, trainPercent)
  someData.split4 <-  splitData(someData, trainPercent)
  someData.split5 <-  splitData(someData, trainPercent)
  someData.split6 <-  splitData(someData, trainPercent)
  someData.split7 <-  splitData(someData, trainPercent)
  someData.split8 <-  splitData(someData, trainPercent)
  someData.split9 <-  splitData(someData, trainPercent)
  someData.split10 <-  splitData(someData, trainPercent)
  
  someData.weight1 <-  weight(someData.split1$train, someData.split1$train$CONST_YN)
  someData.weight2 <-  weight(someData.split2$train, someData.split2$train$CONST_YN)
  someData.weight3 <-  weight(someData.split3$train, someData.split3$train$CONST_YN)
  someData.weight4 <-  weight(someData.split4$train, someData.split4$train$CONST_YN)
  someData.weight5 <-  weight(someData.split5$train, someData.split5$train$CONST_YN)
  someData.weight6 <-  weight(someData.split6$train, someData.split6$train$CONST_YN)
  someData.weight7 <-  weight(someData.split7$train, someData.split7$train$CONST_YN)
  someData.weight8 <-  weight(someData.split8$train, someData.split8$train$CONST_YN)
  someData.weight9 <-  weight(someData.split9$train, someData.split9$train$CONST_YN)
  someData.weight10 <-  weight(someData.split10$train, someData.split10$train$CONST_YN)
  
  lr1 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI + WIMD_2014_DECILE,
             data = someData.weight1,
             family = "binomial")
  lr2  <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight2,
              family = "binomial")
  lr3 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight3,
             family = "binomial")
  lr4 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight4,
             family = "binomial")
  lr5 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight5,
             family = "binomial")
  lr6 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight6,
             family = "binomial")
  lr7 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight7,
             family = "binomial")
  lr8 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight8,
             family = "binomial")
  lr9 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
             data = someData.weight9,
             family = "binomial")
  lr10 <- glm(CONST_YN ~ GNDR_CD + AVG_BMI  + WIMD_2014_DECILE,
              data = someData.weight10,
              family = "binomial")
  
  test1 <- testModel(lr1, someData.split1$test, someData.split1$test$CONST_YN)
  test2 <- testModel(lr2, someData.split2$test, someData.split2$test$CONST_YN)
  test3 <- testModel(lr3, someData.split3$test, someData.split3$test$CONST_YN)
  test4 <- testModel(lr4, someData.split4$test, someData.split4$test$CONST_YN)
  test5 <- testModel(lr5, someData.split5$test, someData.split5$test$CONST_YN)
  test6 <- testModel(lr6, someData.split6$test, someData.split6$test$CONST_YN)
  test7 <- testModel(lr7, someData.split7$test, someData.split7$test$CONST_YN)
  test8 <- testModel(lr8, someData.split8$test, someData.split8$test$CONST_YN)
  test9 <- testModel(lr9, someData.split9$test, someData.split9$test$CONST_YN)
  test10 <- testModel(lr10, someData.split10$test, someData.split10$test$CONST_YN)
  
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  auc.mean <- (test1$AUC + test2$AUC + test3$AUC + test4$AUC + test5$AUC + test6$AUC + test7$AUC + test8$AUC + test9$AUC + test10$AUC)/10
  acc.mean <- (test1$acc + test2$acc + test3$acc + test4$acc + test5$acc + test6$acc + test7$acc + test8$acc + test9$acc + test10$acc)/10
  sen.mean <- (test1$sen + test2$sen + test3$sen + test4$sen + test5$sen + test6$sen + test7$sen + test8$sen + test9$sen + test10$sen)/10
  spec.mean <- (test1$spec + test2$spec + test3$spec + test4$spec + test5$spec + test6$spec + test7$spec + test8$spec + test9$spec + test10$spec)/10
  ppv.mean <- (test1$ppv + test2$ppv + test3$ppv + test4$ppv + test5$ppv + test6$ppv + test7$ppv + test8$ppv + test9$ppv + test10$ppv)/10
  npv.mean <- (test1$npv + test2$npv + test3$npv + test4$npv + test5$npv + test6$npv + test7$npv + test8$npv + test9$npv + test10$npv)/10
  
  means <- list(
    "AUC" = auc.mean,
    "acc" = acc.mean,
    "sen" = sen.mean,
    "spec" = spec.mean,
    "ppv" = ppv.mean,
    "npv" = npv.mean)
  
  return(means)
}

### Plots

Where I have written \*\*\*, this means I have removed text for security sake


source("P:/\*\*\*/workspace/1415/Scripts/R Scripts/Setup for export.R")
plot15 <- paste0("SELECT * FROM ", p15) %>% SAILDBUtils::runSQL(a, .)
plot15 <- filter(plot15, COMORBIDITY == "Constipation" | COMORBIDITY == "BMI" | COMORBIDITY == "Depression" | COMORBIDITY == "Fatty Liver" | COMORBIDITY == "Type 2 Diabetes") 
plot15$COMORBIDITY <- droplevels(plot15$COMORBIDITY)
levels(plot15$COMORBIDITY) <- c("Overall", "Constipation", "Depression", "MAFLD", "T2DM")
plot15$COMORBIDITY <- factor(plot15$COMORBIDITY, levels = c("Overall" ,"T2DM", "MAFLD", "Constipation", "Depression"))
plot15 <- filter(plot15, BMI < 50) # using a cutoff of 50 for the graphs to make the graphs easier to read. This only 
#cuts off a small number of extreme values

plot5 <- paste0("SELECT * FROM ", p5) %>% SAILDBUtils::runSQL(a, .)
plot5 <- filter(plot5, COMORBIDITY == "Constipation" | COMORBIDITY == "BMI") 
plot5$COMORBIDITY <- droplevels(plot5$COMORBIDITY)
levels(plot5$COMORBIDITY) <- c("Overall", "Constipation")
plot5$COMORBIDITY <- factor(plot5$COMORBIDITY, levels = c("Overall", "Constipation"))
plot5 <- filter(plot5, BMI < 50) 


plot10 <- paste0("SELECT * FROM ", p10) %>% SAILDBUtils::runSQL(a, .)
plot10 <- plot10 %>% filter(COMORBIDITY == "Constipation" | COMORBIDITY == "BMI" | COMORBIDITY == "Depression" | COMORBIDITY == "Fatty Liver" | COMORBIDITY == "Type 2 Diabetes") 
plot10$COMORBIDITY <- droplevels(plot10$COMORBIDITY)
levels(plot10$COMORBIDITY) <- c("Overall", "Constipation", "Depression", "MAFLD", "T2DM")
plot10$COMORBIDITY <- factor(plot10$COMORBIDITY, levels = c("Overall" ,"T2DM", "MAFLD", "Constipation", "Depression"))
plot10 <- filter(plot10, BMI < 50) 

#Crosstabs
comorbid.xtabFt <- xtabs(~ DIAB_YN + CONST_YN + FL_YN + DEP_YN, FtYOs)
summary(comorbid.xtab)

#significant (I went through all combinations of 2 conditions to find the signifiacnce of all)
xtabs(~ DEP_YN + CONST_YN, TYOs) %>% summary()
xtabs(~ CONST_YN + FL_YN, TYOs) %>% summary()
xtabs(~ DIAB_YN + DEP_YN, FtYOs) %>% summary()




#% with a comorbidity
(((filter(FYOs, ADVERSE_OUTCOME ==1) %>% nrow()) + (filter(TYOs, ADVERSE_OUTCOME ==1) %>% nrow()) + (filter(FtYOs, ADVERSE_OUTCOME ==1) %>% nrow()))
  /(nrow(FYOs)+nrow(TYOs)+nrow(FtYOs)))*100
#\# with a comorbidity
(filter(FYOs, ADVERSE_OUTCOME ==1) %>% nrow()) + (filter(TYOs, ADVERSE_OUTCOME ==1) %>% nrow()) + (filter(FtYOs, ADVERSE_OUTCOME ==1) %>% nrow())



colnames(WIMDmeanTab) <- c("2-6yrs", "7-11yrs", "12-16yrs")
rownames(WIMDmeanTab) <- c("Overall", "T2DM", "MAFLD", "Constipation", "Depression")

WIMDmeanTab<- as.table(WIMDmeanTab)

numMat<- matrix(c(nrow(filter(plot5, COMORBIDITY == "Overall")), nrow(filter(plot10, COMORBIDITY == "Overall")), nrow(filter(plot15, COMORBIDITY == "Overall")),
                  nrow(filter(plot5, COMORBIDITY == "T2DM")), nrow(filter(plot10, COMORBIDITY == "T2DM")), nrow(filter(plot15, COMORBIDITY == "T2DM")),
                  nrow(filter(plot5, COMORBIDITY == "MAFLD")), nrow(filter(plot10, COMORBIDITY == "MAFLD")), nrow(filter(plot15, COMORBIDITY == "MAFLD")),
                  nrow(filter(plot5, COMORBIDITY == "Constipation") ), nrow(filter(plot10, COMORBIDITY == "Constipation") ), nrow(filter(plot15, COMORBIDITY == "Constipation") ),
                  nrow(filter(plot5, COMORBIDITY == "Depression") ), nrow(filter(plot10, COMORBIDITY == "Depression") ), nrow(filter(plot15, COMORBIDITY == "Depression"))), ncol=3, byrow=TRUE)

colnames(numMat) <- c("2-6yrs", "7-11yrs", "12-16yrs")
rownames(numMat) <- c("Overall", "T2DM", "MAFLD", "Constipation", "Depression")

numTab <- as.table(numMat)

numMatdist <- matrix(c(nrow(FYOs), nrow(TYOs), nrow(FtYOs), nrow(FtT), nrow(FtFt), nrow(TtFt), 
                       nrow(filter(FYOs, DIAB_YN == 0 & FL_YN == 0 & CONST_YN == 0 & DEP_YN == 0)), nrow(filter(TYOs, DIAB_YN == 0 & FL_YN == 0 & CONST_YN == 0 & DEP_YN == 0)), nrow(filter(FtYOs, DIAB_YN == 0 & FL_YN == 0 & CONST_YN == 0 & DEP_YN == 0)), nrow(filter(FtT, DIAB_YN == 0 & FL_YN == 0 & CONST_YN == 0 & DEP_YN == 0)), nrow(filter(FtFt, DIAB_YN == 0 & FL_YN == 0 & CONST_YN == 0 & DEP_YN == 0)), nrow(filter(TtFt, DIAB_YN == 0 & FL_YN == 0 & CONST_YN == 0 & DEP_YN == 0)),
                       nrow(filter(FYOs, DIAB_YN == 1)), nrow(filter(TYOs, DIAB_YN == 1)), nrow(filter(FtYOs, DIAB_YN == 1)), nrow(filter(FtT, DIAB_YN == 1)), nrow(filter(FtFt, DIAB_YN == 1)), nrow(filter(TtFt, DIAB_YN == 1)),
                       nrow(filter(FYOs, FL_YN == 1)), nrow(filter(TYOs, FL_YN == 1)), nrow(filter(FtYOs, FL_YN == 1)), nrow(filter(FtT, FL_YN == 1)), nrow(filter(FtFt, FL_YN == 1)), nrow(filter(TtFt, FL_YN == 1)),
                       nrow(filter(FYOs, CONST_YN == 1)), nrow(filter(TYOs, CONST_YN == 1)), nrow(filter(FtYOs, CONST_YN == 1)), nrow(filter(FtT, CONST_YN == 1)), nrow(filter(FtFt, CONST_YN == 1)), nrow(filter(TtFt, CONST_YN == 1)), 
                       nrow(filter(FYOs, DEP_YN == 1)), nrow(filter(TYOs, DEP_YN == 1)), nrow(filter(FtYOs, DEP_YN == 1)), nrow(filter(FtT, DEP_YN == 1)), nrow(filter(FtFt, DEP_YN == 1)), nrow(filter(TtFt, DEP_YN == 1))),
                     ncol=6, byrow=TRUE)

colnames(numMatdist) <- c("2-6yrs", "7-11yrs", "12-16yrs", "BMI:2-6yrs Comorbidity:7-11yrs", "BMI:2-6yrs Comorbidity:12-16yrs", "BMI:7-11yrs Comorbidity:12-16yrs")
rownames(numMatdist) <- c("Overall", "None", "T2DM", "MAFLD", "Constipation", "Depression")

numTabdist <- as.table(numMatdist)


#significance of BMI difference between comorbidities

p15com <- plot15 %>%
  rstatix::wilcox_test(BMI ~ COMORBIDITY) %>%
  filter(p.adj<0.05) %>%
  rstatix::add_significance("p.adj") %>%
  rstatix::add_y_position() %>%
  mutate(y.position = c(49,53,57,61,49,65,69,45,49,41))

p10com <- plot10 %>%
  rstatix::wilcox_test(BMI ~ COMORBIDITY) %>%
  filter(p.adj<0.05) %>%
  rstatix::add_significance("p.adj") %>%
  rstatix::add_y_position() %>%
  mutate(y.position = c(34,38, 42, 46))

p5com <- plot5 %>%
  rstatix::wilcox_test(BMI ~ COMORBIDITY) %>%
  filter(p.adj<0.05) %>%
  rstatix::add_significance("p.adj") %>%
  rstatix::add_y_position() %>%
  mutate(y.position = c(0))

#plotting boxplots with significance n.b. boxplots plot median whereas p values show difference in mean
#therefore boxplots that look the same may be significantly different because the median is not always the same as the mean
ggplot(plot15) + 
  aes(x=COMORBIDITY) +
  aes(y=BMI) + 
  geom_boxplot(outlier.shape = NA) +
  ggtitle("Boxplot of BMI stratified by comorbidity in 11-16 yr olds") +
  ggpubr::stat_pvalue_manual(p15com, label = "p.adj.signif") +
  xlab("Comorbidity") +
  ylab("BMI") +
  ylim(10,70)

ggplot(plot10) + 
  aes(x=COMORBIDITY) +
  aes(y=BMI) + 
  geom_boxplot(outlier.shape = NA) +
  ggtitle("Boxplot of BMI stratified by comorbidity in 6-11 yr olds") +
  ggpubr::stat_pvalue_manual(p10com, label = "p.adj.signif") +
  xlab("Comorbidity") +
  ylab("BMI") +
  ylim(10,47)

ggplot(plot5) + 
  aes(x=COMORBIDITY) +
  aes(y=BMI) + 
  geom_boxplot(outlier.shape = NA) +
  ggtitle("Boxplot of BMI stratified by comorbidity in 2-6 yr olds") +
  #ggpubr::stat_pvalue_manual(p5com, label = "p.adj") +
  xlab("Comorbidity") +
  ylab("BMI") +
  ylim(10,23)

#WIMD

p15comWIMD <- plot15 %>%
  rstatix::wilcox_test(WIMD_2014_DECILE ~ COMORBIDITY) %>%
  filter(p.adj<0.05) %>%
  rstatix::add_significance("p.adj") %>%
  rstatix::add_y_position() %>%
  mutate(y.position = c(12,14,16,12,18,20,12))

p10comWIMD <- plot10 %>%
  rstatix::wilcox_test(WIMD_2014_DECILE ~ COMORBIDITY) %>%
  filter(p.adj<0.05) %>%
  rstatix::add_significance("p.adj") %>%
  rstatix::add_y_position() %>%
  mutate(y.position = c(11))

p5comWIMD <- plot5 %>%
  rstatix::wilcox_test(WIMD_2014_DECILE ~ COMORBIDITY) %>%
  filter(p<0.05) %>%
  rstatix::add_significance("p") %>%
  rstatix::add_y_position() %>%
  mutate(y.position = c(11))

ggplot(plot15) + 
  aes(x=COMORBIDITY) +
  aes(y=WIMD_2014_DECILE) + 
  geom_boxplot(outlier.shape = NA) +
  ggtitle("Boxplot of deprivation\nstratified by comorbidity in 11-16 yr olds") +
  ggpubr::stat_pvalue_manual(p15comWIMD, label = "p.adj.signif") +
  xlab("Comorbidity") +
  ylab("Deprivation (1-10, 1 = most deprived)") +
  ylim(0,21)

ggplot(plot10) + 
  aes(x=COMORBIDITY) +
  aes(y=WIMD_2014_DECILE) + 
  geom_boxplot(outlier.shape = NA) +
  ggtitle("Boxplot of deprivation\nstratified by comorbidity in 6-11 yr olds") +
  ggpubr::stat_pvalue_manual(p10comWIMD, label = "p.adj.signif") +
  xlab("Comorbidity") +
  ylab("Deprivation (1-10, 1 = most deprived)") +
  ylim(0,12)
mean(filter(plot10, COMORBIDITY == "Overall")$WIMD_2014_DECILE)
mean(filter(plot10, COMORBIDITY == "Constipation")$WIMD_2014_DECILE)


ggplot(plot5) + 
  aes(x=COMORBIDITY) +
  aes(y=WIMD_2014_DECILE) + 
  geom_boxplot(outlier.shape = NA) +
  ggtitle("Boxplot of deprivation\nstratified by comorbidity in 2-6 yr olds") +
  ggpubr::stat_pvalue_manual(p5comWIMD, label = "p.signif") +
  xlab("Comorbidity") +
  ylab("Deprivation (1-10, 1 = most deprived)") +
  ylim(0,12)

ggplot(plot15) + 
  aes(x=WIMD_2014_DECILE) +
  aes(y=BMI) + 
  geom_point(outlier.shape = NA) +
  ggtitle("Distribution of deprivation stratified by comorbidity in 12-16 yr olds") +
  #ggpubr::stat_pvalue_manual(p15comWIMD, label = "p.adj") +
  geom_smooth(method = "lm")
xlab("Comorbidity") +
  ylab("Deprivation (1-10, 1 = most deprived)") #+
#ylim(0,21)

#BMI histograms 

hist(plot15$BMI, main = "Histogram showing BMI of 11-16yr olds' events", xlab = "BMI", ylab = "No. events")
legend(x = "topright",
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2) 
mp15 <- mean(plot15$BMI)
mdp15 <- median(plot15$BMI)
abline(v = mp15, col = "blue", lwd = 2, lty = 2)
abline(v = mdp15, col = "red", lwd = 2, lty = 1)

hist(plot10$BMI, main = "Histogram showing BMI of 6-11yr olds' events", xlab = "BMI", ylab = "No. events")
legend(x = "topright",
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2) 
mp10 <- mean(plot10$BMI)
mdp10 <- median(plot10$BMI)
abline(v = mp10, col = "blue", lwd = 2, lty = 2)
abline(v = mdp10, col = "red", lwd = 2, lty = 1)

hist(plot5$BMI, main = "Histogram showing BMI of 2-6yr olds' events", xlab = "BMI", ylab = "No. events")
legend(x = "topright",
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2) 
mp5 <- mean(plot5$BMI)
mdp5 <- median(plot5$BMI)
abline(v = mp5, col = "blue", lwd = 2, lty = 2)
abline(v = mdp5, col = "red", lwd = 2, lty = 1)

hist(FtYOs$AVG_BMI_RAW, main = "Histogram showing BMI of 11-16yr olds", xlab = "BMI", xlim = c(0,50))
legend(x = "topright",
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2) 
mFtYOs <- mean(FtYOs$AVG_BMI_RAW)
mdFtYOs <- median(FtYOs$AVG_BMI_RAW)
abline(v = mFtYOs, col = "blue", lwd = 2, lty = 2)
abline(v = mdFtYOs, col = "red", lwd = 2, lty = 1)

hist(TYOs$AVG_BMI_RAW, main = "Histogram showing BMI of 6-11yr olds", xlab = "BMI", xlim = c(0,50))
legend(x = "topright",
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2) 
mTYOs <- mean(TYOs$AVG_BMI_RAW)
mdTYOs <- median(TYOs$AVG_BMI_RAW)
abline(v = mTYOs, col = "blue", lwd = 2, lty = 2)
abline(v = mdTYOs, col = "red", lwd = 2, lty = 1)

hist(FYOs$AVG_BMI_RAW,
     main = "Histogram showing BMI of 2-6yr olds",
     xlab = "BMI",
     xlim = c(0,50))
legend(x = "topright",
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2) 
mFYOs <- mean(FYOs$AVG_BMI_RAW)
mdFYOs <- median(FYOs$AVG_BMI_RAW)
abline(v = mFYOs, col = "blue", lwd = 2, lty = 2)
abline(v = mdFYOs, col = "red", lwd = 2, lty = 1)


#WIMD

hist(plot15$WIMD_2014_DECILE,
     main = "Histogram showing WIMD of 11-16yr olds' events",
     xlab = "WIMD",
     ylab = "No. events", ylim = c(0,40000))
legend(x = "topright",  inset = c(0,-0.11),
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2,
       bty = "n") 
wmp15 <- mean(plot15$WIMD_2014_DECILE)
wmdp15 <- median(plot15$WIMD_2014_DECILE)
abline(v = wmp15, col = "blue", lwd = 2, lty = 2)
abline(v = wmdp15, col = "red", lwd = 2, lty = 1)

hist(plot10$WIMD_2014_DECILE,
     main = "Histogram showing WIMD of 6-11yr olds' events",
     xlab = "WIMD",
     ylab = "No. events", ylim = c(0,35000))
legend(x = "topright",  inset = c(0,-0.11),
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2,
       bty = "n") 
wmp10 <- mean(plot10$WIMD_2014_DECILE)
wmdp10 <- median(plot10$WIMD_2014_DECILE)
abline(v = wmp10, col = "blue", lwd = 2, lty = 2)
abline(v = wmdp10, col = "red", lwd = 2, lty = 1)

hist(plot5$WIMD_2014_DECILE,
     main = "Histogram showing WIMD of 2-6yr olds' events",
     xlab = "WIMD",
     ylab = "No. events", ylim = c(0,25000))
legend(x = "topright",  inset = c(0,-0.11),
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2,
       bty = "n") 
wmp5 <- mean(plot5$WIMD_2014_DECILE)
wmdp5 <- median(plot5$WIMD_2014_DECILE)
abline(v = wmp5, col = "blue", lwd = 2, lty = 2)
abline(v = wmdp5, col = "red", lwd = 2, lty = 1)



hist(FtYOs$WIMD_2014_DECILE,
     main = "Histogram showing WIMD of 11-16yr olds",
     xlab = "WIMD",
     ylab = "No. children",
     ylim = c(0, 9000))
legend(x = "topright",  inset = c(0,-0.11),
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2,
       bty = "n") 
wmFtYOs <- mean(FtYOs$WIMD_2014_DECILE)
wmdFtYOs <- median(FtYOs$WIMD_2014_DECILE)
abline(v = wmFtYOs, col = "blue", lwd = 2, lty = 2)
abline(v = wmdFtYOs, col = "red", lwd = 2, lty = 1)

hist(TYOs$WIMD_2014_DECILE,
     main = "Histogram showing WIMD of 6-11yr olds",
     xlab = "WIMD", 
     ylab = "No. children", ylim = c(0,6000))
legend(x = "topright", inset = c(0,-0.11),
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2,
       bty = "n") 
wmTYOs <- mean(TYOs$WIMD_2014_DECILE)
wmdTYOs <- median(TYOs$WIMD_2014_DECILE)
abline(v = wmTYOs, col = "blue", lwd = 2, lty = 2)
abline(v = wmdTYOs, col = "red", lwd = 2, lty = 1)

hist(FYOs$WIMD_2014_DECILE,
     main = "Histogram showing WIMD of 2-6yr olds",
     xlab = "WIMD",
     ylab = "No. children", ylim = c(0,4000))
legend(x = "topright", inset = c(0,-0.1),
       legend = c("median", "mean"),
       lty = c(1,2),
       col = c("red", "blue"),
       lwd = 2,
       bty = "n") 
wmFYOs <- mean(FYOs$WIMD_2014_DECILE)
wmdFYOs <- median(FYOs$WIMD_2014_DECILE)
abline(v = wmFYOs, col = "blue", lwd = 2, lty = 2)
abline(v = wmdFYOs, col = "red", lwd = 2, lty = 1)

p15WIMD <- 
  rbind(
    data.frame(WIMD = plot15$WIMD_2014_DECILE, label = "EVENT"),
    data.frame(WIMD = FtYOs$WIMD_2014_DECILE, label = "PATIENT")
  )  %>% 
  rstatix::wilcox_test(WIMD ~ label)


#significance between event means and patient means
p15BMI <- 
  rbind(
    data.frame(BMI = plot15$BMI, label = "EVENT"),
    data.frame(BMI = FtYOs$AVG_BMI_RAW, label = "PATIENT")
  )  %>% 
  rstatix::wilcox_test(BMI ~ label)

p10WIMD <- 
  rbind(
    data.frame(WIMD = plot10$WIMD_2014_DECILE, label = "EVENT"),
    data.frame(WIMD = TYOs$WIMD_2014_DECILE, label = "PATIENT")
  )  %>% 
  rstatix::wilcox_test(WIMD ~ label)

p10BMI <- 
  rbind(
    data.frame(BMI = plot10$BMI, label = "EVENT"),
    data.frame(BMI = TYOs$AVG_BMI_RAW, label = "PATIENT")
  )  %>% 
  rstatix::wilcox_test(BMI ~ label)

p5WIMD <- 
  rbind(
    data.frame(WIMD = plot5$WIMD_2014_DECILE, label = "EVENT"),
    data.frame(WIMD = FYOs$WIMD_2014_DECILE, label = "PATIENT")
  )  %>% 
  rstatix::wilcox_test(WIMD ~ label)

p5BMI <- 
  rbind(
    data.frame(BMI = plot5$BMI, label = "EVENT"),
    data.frame(BMI = FYOs$AVG_BMI_RAW, label = "PATIENT")
  )  %>% 
  rstatix::wilcox_test(BMI ~ label)
  
  ### Regression
  
  Where I have written \*\*\*, this means I have removed text for security sake

source("P:/\*\*\*/workspace/1415/Scripts/R Scripts/Setup for export.R")
#N.B. These are only used for ROC curves and confusion matrices which I can't use CV for.
#Therefore I changed some parameters each time
#(e.g. adding or removing year of birth as a predictor or swapping BMI raw with normalised BMI)

#Predictors:
#Sex, Average BMI, WIMD Decile
#5 year olds
FYOs.75 <- splitData(FYOs, 0.75)

#Adverse outcome
FYOs.75trainCDA <- weight(FYOs.75$train, FYOs.75$train$ADVERSE_OUTCOME)


FALR.75 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI + WIMD_2014_DECILE,
               data = FYOs.75trainCDA,
               family = "binomial")

FALR.75.test <- testModel(FALR.75, FYOs.75$test, FYOs.75$test$ADVERSE_OUTCOME)
modelROC(FALR.75, FYOs.75$test, FYOs.75$test$ADVERSE_OUTCOME)
#N.B. this is a bad ROC curve because it incorporates constipation which isn't linked with the predictors


#Constipation
FYOs.75trainCDC <- weight(FYOs.75$train, FYOs.75$train$CONST_YN)


FCLR.75 <- glm(CONST_YN ~ GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
               data = FYOs.75trainCDC,
               family = "binomial")

FCLR.75.test <- testModel(FCLR.75, FYOs.75$test, FYOs.75$test$CONST_YN)
modelROC(FCLR.75, FYOs.75$test, FYOs.75$test$CONST_YN)
{
  FCLnR.75 <- lm(CONST_FREQ ~ GNDR_CD + YOB + AVG_BMI + WIMD_2014_DECILE,
                 data = FYOs.75$train)
  
  FCLnR.75.pd <- predict(FCLnR.75, FYOs.75$test, type = "response")
  
  plot(x=FYOs.75$test$CONST_FREQ, y=FCLnR.75.pd)
  
  summary(FCLnR.75)
}

#10 Year olds
TYOs.75 <- splitData(TYOs, 0.75)
#Adverse outcomes

TYOs.75trainCDA <- weight(TYOs.75$train, TYOs.75$train$ADVERSE_OUTCOME)


TALR.75 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI +  WIMD_2014_DECILE,
               data = TYOs.75trainCDA,
               family = "binomial")

TALR.75.test <- testModel(TALR.75, TYOs.75$test, TYOs.75$test$ADVERSE_OUTCOME)
modelROC(TALR.75, TYOs.75$test, TYOs.75$test$ADVERSE_OUTCOME)

#Constipation

TYOs.75trainCDC <- weight(TYOs.75$train, TYOs.75$train$CONST_YN)


TCLR.75 <- glm(CONST_YN ~ GNDR_CD +YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
               data = TYOs.75trainCDC,
               family = "binomial")

TCLR.75.test <- testModel(TCLR.75, TYOs.75$test, TYOs.75$test$CONST_YN)
modelROC(TCLR.75, TYOs.75$test, TYOs.75$test$CONST_YN)

#15 Year olds
FtYOs.75 <- splitData(FtYOs, 0.75)
#Adverse outcomes

FtYOs.75trainCDA <- weight(FtYOs.75$train, FtYOs.75$train$ADVERSE_OUTCOME)


FtALR.75 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                data = FtYOs.75trainCDA,
                family = "binomial")

FtALR.75.test <- testModel(FtALR.75, FtYOs.75$test, FtYOs.75$test$ADVERSE_OUTCOME)
modelROC(FtALR.75, FtYOs.75$test, FtYOs.75$test$ADVERSE_OUTCOME)

#Diabetes and Fatty liver
FtYOs.75trainCDDFL <- weight(FtYOs.75$train, FtYOs.75$train$DFL)


FtDFLLR.75 <- glm(DFL ~ GNDR_CD + AVG_BMI +  WIMD_2014_DECILE,
                  data = FtYOs.75trainCDDFL,
                  family = "binomial")

FtDFLLR.75.test <- testModel(FtDFLLR.75, FtYOs.75$test, FtYOs.75$test$DFL)
modelROC(FtDFLLR.75, FtYOs.75$test, FtYOs.75$test$DFL)


#Diabetes
FtYOs.75trainCDDi <- weight(FtYOs.75$train, FtYOs.75$train$DIAB_YN)


FtDiLR.75 <- glm(DIAB_YN ~ GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                 data = FtYOs.75trainCDDi,
                 family = "binomial")

FtDiLR.75.test <- testModel(FtDiLR.75, FtYOs.75$test, FtYOs.75$test$DIAB_YN)
modelROC(FtDiLR.75, FtYOs.75$test, FtYOs.75$test$DIAB_YN)

FtDiLR.75$coefficients
#Constipation
FtYOs.75trainCDC <- weight(FtYOs.75$train, FtYOs.75$train$CONST_YN)


FtCLR.75 <- glm(CONST_YN ~ GNDR_CD  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                data = FtYOs.75trainCDC,
                family = "binomial")

FtCLR.75.test <- testModel(FtCLR.75, FtYOs.75$test, FtYOs.75$test$CONST_YN)
modelROC(FtCLR.75, FtYOs.75$test, FtYOs.75$test$CONST_YN)

FtCLnR.75 <- lm(CONST_FREQ ~ GNDR_CD  + YOB + AVG_BMI + WIMD_2014_DECILE,
                data = FtYOs.75$train)

FtCLnR.75.pd <- predict(FtCLnR.75, FtYOs.75$test, type = "response")

plot(x=FtYOs.75$test$CONST_FREQ, y=FtCLnR.75.pd)

summary(FtCLnR.75)

#Fatty Liver
FtYOs.75trainCDFL <- weight(FtYOs.75$train, FtYOs.75$train$FL_YN)


FtFLLR.75 <- glm(FL_YN ~ GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                 data = FtYOs.75trainCDFL,
                 family = "binomial")

FtFLLR.75.test <- testModel(FtFLLR.75, FtYOs.75$test, FtYOs.75$test$FL_YN)
modelROC(FtFLLR.75, FtYOs.75$test, FtYOs.75$test$FL_YN)

#Depression

FtYOs.75trainCDDe <- weight(FtYOs.75$train, FtYOs.75$train$DEP_YN)


FtDeLR.75 <- glm(DEP_YN ~ GNDR_CD +YOB  + AVG_BMI_RAW +  WIMD_2014_DECILE,
                 data = FtYOs.75trainCDDe,
                 family = "binomial")

FtDeLR.75.test <- testModel(FtDeLR.75, FtYOs.75$test, FtYOs.75$test$DEP_YN)
modelROC(FtDeLR.75, FtYOs.75$test, FtYOs.75$test$DEP_YN)


FtDeLnR.75 <- lm(DEP_FREQ ~ GNDR_CD + YOB + AVG_BMI + WIMD_2014_DECILE,
                 data = FtYOs.75$train)

FtDeLnR.75.pd <- predict(FtDeLnR.75, FtYOs.75$test, type = "response")

plot(x=FtYOs.75$test$DEP_FREQ, y=FtDeLR.75.pd)



#Predictors:
#Average BMI raw


#5 year olds

FALR.75raw <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI_RAW + WIMD_2014_DECILE,
                  data = FYOs.75trainCDA,
                  family = "binomial")

FALR.75.testraw <- testModel(FALR.75raw, FYOs.75$test, FYOs.75$test$ADVERSE_OUTCOME)
modelROC(FALR.75raw, FYOs.75$test, FYOs.75$test$ADVERSE_OUTCOME)
#N.B. this is a bad ROC curve because it incorporates constipation which isn't linked with the predictors

#Diabetes
FDiLR.75raw <- glm(DIAB_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = FYOs.75trainCDDi,
                   family = "binomial")

FDiLR.75.testraw <- testModel(FDiLR.75raw, FYOs.75$test, FYOs.75$test$DIAB_YN)
modelROC(FDiLR.75raw, FYOs.75$test, FYOs.75$test$DIAB_YN)

#Constipation
FCLR.75raw <- glm(CONST_YN ~ GNDR_CD + YOB +AVG_BMI_RAW+  WIMD_2014_DECILE,
                  data = FYOs.75trainCDC,
                  family = "binomial")

FCLR.75.testraw <- testModel(FCLR.75raw, FYOs.75$test, FYOs.75$test$CONST_YN)
modelROC(FCLR.75raw, FYOs.75$test, FYOs.75$test$CONST_YN)
#Fatty Liver
FFLLR.75raw <- glm(FL_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = FYOs.75trainCDFL,
                   family = "binomial")

FFLLR.75.testraw <- testModel(FFLLR.75raw, FYOs.75$test, FYOs.75$test$FL_YN)
modelROC(FFLLR.75raw, FYOs.75$test, FYOs.75$test$FL_YN)

#Depression

FDeLR.75raw <- glm(DEP_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = FYOs.75trainCDDe,
                   family = "binomial")

FDeLR.75.testraw <- testModel(FDeLR.75raw, FYOs.75$test, FYOs.75$test$DEP_YN)
modelROC(FDeLR.75raw, FYOs.75$test, FYOs.75$test$DEP_YN)

#10 Year olds

#Adverse outcomes
TALR.75raw <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                  data = TYOs.75trainCDA,
                  family = "binomial")

TALR.75.testraw <- testModel(TALR.75raw, TYOs.75$test, TYOs.75$test$ADVERSE_OUTCOME)
modelROC(TALR.75raw, TYOs.75$test, TYOs.75$test$ADVERSE_OUTCOME)


#Diabetes
TDiLR.75raw <- glm(DIAB_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = TYOs.75trainCDDi,
                   family = "binomial")

TDiLR.75.testraw <- testModel(TDiLR.75raw, TYOs.75$test, TYOs.75$test$DIAB_YN)
modelROC(TDiLR.75raw, TYOs.75$test, TYOs.75$test$DIAB_YN)
#Constipation

TCLR.75raw <- glm(CONST_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                  data = TYOs.75trainCDC,
                  family = "binomial")

TCLR.75.testraw <- testModel(TCLR.75raw, TYOs.75$test, TYOs.75$test$CONST_YN)
modelROC(TCLR.75raw, TYOs.75$test, TYOs.75$test$CONST_YN)

#Fatty Liver
TFLLR.75raw <- glm(FL_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = TYOs.75trainCDFL,
                   family = "binomial")

TFLLR.75.testraw <- testModel(TFLLR.75raw, TYOs.75$test, TYOs.75$test$FL_YN)
modelROC(TFLLR.75raw, TYOs.75$test, TYOs.75$test$FL_YN)

#Depression
TDeLR.75raw <- glm(DEP_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = TYOs.75trainCDDe,
                   family = "binomial")

TDeLR.75.testraw <- testModel(TDeLR.75raw, TYOs.75$test, TYOs.75$test$DEP_YN)
modelROC(TDeLR.75raw, TYOs.75$test, TYOs.75$test$DEP_YN)


#15 Year olds

#Adverse outcomes

FtALR.75raw <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = FtYOs.75trainCDA,
                   family = "binomial")

FtALR.75.testraw <- testModel(FtALR.75raw, FtYOs.75$test, FtYOs.75$test$ADVERSE_OUTCOME)
modelROC(FtALR.75raw, FtYOs.75$test, FtYOs.75$test$ADVERSE_OUTCOME)


#Diabetes
FtDiLR.75raw <- glm(DIAB_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                    data = FtYOs.75trainCDDi,
                    family = "binomial")

FtDiLR.75.testraw <- testModel(FtDiLR.75raw, FtYOs.75$test, FtYOs.75$test$DIAB_YN)
modelROC(FtDiLR.75raw, FtYOs.75$test, FtYOs.75$test$DIAB_YN)


#Constipation
FtCLR.75raw <- glm(CONST_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = FtYOs.75trainCDC,
                   family = "binomial")

FtCLR.75.testraw <- testModel(FtCLR.75raw, FtYOs.75$test, FtYOs.75$test$CONST_YN)
modelROC(FtCLR.75raw, FtYOs.75$test, FtYOs.75$test$CONST_YN)


#Fatty Liver
FtFLLR.75raw <- glm(FL_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                    data = FtYOs.75trainCDFL,
                    family = "binomial")

FtFLLR.75.testraw <- testModel(FtFLLR.75raw, FtYOs.75$test, FtYOs.75$test$FL_YN)
modelROC(FtFLLR.75raw, FtYOs.75$test, FtYOs.75$test$FL_YN)

#Depression

FtDeLR.75raw <- glm(DEP_YN ~ GNDR_CD + YOB +AVG_BMI_RAW +  WIMD_2014_DECILE,
                    data = FtYOs.75trainCDDe,
                    family = "binomial")

FtDeLR.75.testraw <- testModel(FtDeLR.75raw, FtYOs.75$test, FtYOs.75$test$DEP_YN)
modelROC(FtDeLR.75raw, FtYOs.75$test, FtYOs.75$test$DEP_YN)


#5 year old BMI on 10 Yr olds
FtT.75 <- splitData(FtT, 0.75)

#Adverse outcome
FtT.75trainCDA <- weight(FtT.75$train, FtT.75$train$ADVERSE_OUTCOME)


FtTALR.75 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI + WIMD_2014_DECILE,
                 data = FtT.75trainCDA,
                 family = "binomial")
#N.B. Not enough people

FtTALR.75.test <- testModel(FtTALR.75, FtT.75$test, FtT.75$test$ADVERSE_OUTCOME)
modelROC(FtTALR.75, FtT.75$test, FtT.75$test$ADVERSE_OUTCOME)

#Diabetes or Fatty Liver

FtT.75trainCDDFL <- weight(FtT.75$train, FtT.75$train$DFL)


FtTDFLLR.75 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI +  WIMD_2014_DECILE,
                   data = FtT.75trainCDDFL,
                   family = "binomial")

FtTFLLR.75.test <- testModel(FtTDFLLR.75, FtT.75$test, FtT.75$test$DFL)
modelROC(FtTDFLLR.75, FtT.75$test, FtT.75$test$DFL)

#Diabetes
FtT.75trainCDDi <- weight(FtT.75$train, FtT.75$train$DIAB_YN)


FtTDiLR.75 <- glm(DIAB_YN ~  GNDR_CD + YOB +AVG_BMI +  WIMD_2014_DECILE,
                  data = FtT.75trainCDDi,
                  family = "binomial")

FtTDiLR.75.test <- testModel(FtTDiLR.75, FtT.75$test, FtT.75$test$DIAB_YN)
modelROC(FtTDiLR.75, FtT.75$test, FtT.75$test$DIAB_YN)

#Constipation
FtT.75trainCDC <- weight(FtT.75$train, FtT.75$train$CONST_YN)


FtTCLR.75 <- glm(CONST_YN ~ GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                 data = FtT.75trainCDC,
                 family = "binomial")

FtTCLR.75.test <- testModel(FtTCLR.75, FtT.75$test, FtT.75$test$CONST_YN)
modelROC(FtTCLR.75, FtT.75$test, FtT.75$test$CONST_YN)

FtTCLnR.75 <- lm(CONST_FREQ ~ GNDR_CD + YOB + AVG_BMI + WIMD_2014_DECILE,
                 data = FtT.75$train)

FtTCLnR.75.pd <- predict(FtTCLnR.75, FtT.75$test, type = "response")

plot(x=FtT.75$test$CONST_FREQ, y=FtTCLnR.75.pd)

summary(FtTCLnR.75)

#Fatty Liver
FtT.75trainCDFL <- weight(FtT.75$train, FtT.75$train$FL_YN)


FtTFLLR.75 <- glm(FL_YN ~ GNDR_CD + YOB +AVG_BMI +  WIMD_2014_DECILE,
                  data = FtT.75trainCDFL,
                  family = "binomial")

FtTFLLR.75.test <- testModel(FtTFLLR.75, FtT.75$test, FtT.75$test$FL_YN)
modelROC(FtTFLLR.75, FtT.75$test, FtT.75$test$FL_YN)

#Depression

FtT.75trainCDDe <- weight(FtT.75$train, FtT.75$train$DEP_YN)


FtTDeLR.75 <- glm(DEP_YN ~ GNDR_CD + YOB +AVG_BMI +  WIMD_2014_DECILE,
                  data = FtT.75trainCDDe,
                  family = "binomial")

FtTDeLR.75.test <- testModel(FtTDeLR.75, FtT.75$test, FtT.75$test$DEP_YN)
modelROC(FtTDeLR.75, FtT.75$test, FtT.75$test$DEP_YN)


FtTDeLnR.75 <- lm(DEP_FREQ ~ GNDR_CD + YOB + AVG_BMI + WIMD_2014_DECILE,
                  data = FtT.75$train)

FtTDeLnR.75.pd <- predict(FtTDeLnR.75, FtT.75$test, type = "response")

plot(x=FtT.75$test$DEP_FREQ, y=FtTDeLnR.75.pd)


#5 year old BMI on 15 Yr olds
FtFt.75 <- splitData(FtFt, 0.75)

#Adverse outcome
FtFt.75trainCDA <- weight(FtFt.75$train, FtFt.75$train$ADVERSE_OUTCOME)


FtFtALR.75 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI + WIMD_2014_DECILE,
                  data = FtFt.75trainCDA,
                  family = "binomial")

FtFtALR.75.test <- testModel(FtFtALR.75, FtFt.75$test, FtFt.75$test$ADVERSE_OUTCOME)
modelROC(FtFtALR.75, FtFt.75$test, FtFt.75$test$ADVERSE_OUTCOME)

#Diabetes or Fatty Liver

FtFt.75trainCDDFL <- weight(FtFt.75$train, FtFt.75$train$DFL)


FtFtDFLLR.75 <- glm(DFL ~ GNDR_CD + YOB + AVG_BMI +  WIMD_2014_DECILE,
                    data = FtFt.75trainCDDFL,
                    family = "binomial")

FtFtFLLR.75.test <- testModel(FtFtDFLLR.75, FtFt.75$test, FtFt.75$test$DFL)
modelROC(FtFtDFLLR.75, FtFt.75$test, FtFt.75$test$DFL)

#Diabetes
FtFt.75trainCDDi <- weight(FtFt.75$train, FtFt.75$train$DIAB_YN)


FtFtDiLR.75 <- glm(DIAB_YN ~ GNDR_CD + YOB +AVG_BMI +  WIMD_2014_DECILE,
                   data = FtFt.75trainCDDi,
                   family = "binomial")


FtFtDiLR.75.test <- testModel(FtFtDiLR.75, FtFt.75$test, FtFt.75$test$DIAB_YN)
modelROC(FtFtDiLR.75, FtFt.75$test, FtFt.75$test$DIAB_YN)

#Constipation
FtFt.75trainCDC <- weight(FtFt.75$train, FtFt.75$train$CONST_YN)


FtFtCLR.75 <- glm(CONST_YN ~  GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                  data = FtFt.75trainCDC,
                  family = "binomial")


FtFtCLR.75.test <- testModel(FtFtCLR.75, FtFt.75$test, FtFt.75$test$CONST_YN)
modelROC(FtFtCLR.75, FtFt.75$test, FtFt.75$test$CONST_YN)

FtFtCLnR.75 <- lm(CONST_FREQ ~ GNDR_CD + YOB + AVG_BMI + WIMD_2014_DECILE,
                  data = FtFt.75$train)

FtFtCLnR.75.pd <- predict(FtFtCLnR.75, FtFt.75$test, type = "response")

plot(x=FtFt.75$test$CONST_FREQ, y=FtFtCLnR.75.pd)

summary(FtFtCLnR.75)

#Fatty Liver
FtFt.75trainCDFL <- weight(FtFt.75$train, FtFt.75$train$FL_YN)


FtFtFLLR.75 <- glm(FL_YN ~ GNDR_CD + YOB +AVG_BMI +  WIMD_2014_DECILE,
                   data = FtFt.75trainCDFL,
                   family = "binomial")

FtFtFLLR.75.test <- testModel(FtFtFLLR.75, FtFt.75$test, FtFt.75$test$FL_YN)
modelROC(FtFtFLLR.75, FtFt.75$test, FtFt.75$test$FL_YN)

#Depression

FtFt.75trainCDDe <- weight(FtFt.75$train, FtFt.75$train$DEP_YN)


FtFtDeLR.75 <- glm(DEP_YN ~ GNDR_CD  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = FtFt.75trainCDDe,
                   family = "binomial")

FtFtDeLR.75.test <- testModel(FtFtDeLR.75, FtFt.75$test, FtFt.75$test$DEP_YN)
modelROC(FtFtDeLR.75, FtFt.75$test, FtFt.75$test$DEP_YN)


FtFtDeLnR.75 <- lm(DEP_FREQ ~ GNDR_CD + YOB + AVG_BMI + WIMD_2014_DECILE,
                   data = FtFt.75trainCDe)

FtFtDeLnR.75.pd <- predict(FtFtDeLnR.75, FtFt.75$test, type = "response")

plot(x=FtFt.75$test$DEP_FREQ, y=FtFtDeLnR.75.pd)

#10 year old BMI on 15 Yr olds
TtFt.75 <- splitData(TtFt, 0.75)

#Adverse outcome
TtFt.75trainCDA <- weight(TtFt.75$train, TtFt.75$train$ADVERSE_OUTCOME)


TtFtALR.75 <- glm(ADVERSE_OUTCOME ~ GNDR_CD + YOB +AVG_BMI + WIMD_2014_DECILE,
                  data = TtFt.75trainCDA,
                  family = "binomial")

TtFtALR.75.test <- testModel(TtFtALR.75, TtFt.75$test, TtFt.75$test$ADVERSE_OUTCOME)
modelROC(TtFtALR.75, TtFt.75$test, TtFt.75$test$ADVERSE_OUTCOME)

#Diabetes or Fatty Liver

TtFt.75trainCDDFL <- weight(TtFt.75$train, TtFt.75$train$DFL)


TtFtDFLLR.75 <- glm(DFL ~ GNDR_CD +YOB  + AVG_BMI_RAW +  WIMD_2014_DECILE,
                    data = TtFt.75trainCDDFL,
                    family = "binomial")

TtFtFLLR.75.test <- testModel(TtFtDFLLR.75, TtFt.75$test, TtFt.75$test$DFL)
modelROC(TtFtDFLLR.75, TtFt.75$test, TtFt.75$test$DFL)


#Diabetes
TtFt.75trainCDDi <- weight(TtFt.75$train, TtFt.75$train$DIAB_YN)


TtFtDiLR.75 <- glm(DIAB_YN ~ GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = TtFt.75trainCDDi,
                   family = "binomial")


TtFtDiLR.75.test <- testModel(TtFtDiLR.75, TtFt.75$test, TtFt.75$test$DIAB_YN)
modelROC(TtFtDiLR.75, TtFt.75$test, TtFt.75$test$DIAB_YN)

#Constipation
TtFt.75trainCDC <- weight(TtFt.75$train, TtFt.75$train$CONST_YN)


TtFtCLR.75 <- glm(CONST_YN ~  GNDR_CD +YOB  + AVG_BMI_RAW +  WIMD_2014_DECILE,
                  data = TtFt.75trainCDC,
                  family = "binomial")


TtFtCLR.75.test <- testModel(TtFtCLR.75, TtFt.75$test, TtFt.75$test$CONST_YN)
modelROC(TtFtCLR.75, TtFt.75$test, TtFt.75$test$CONST_YN)

TtFtCLnR.75 <- lm(CONST_FREQ ~ GNDR_CD + YOB + AVG_BMI + WIMD_2014_DECILE,
                  data = TtFt.75$train)

TtFtCLnR.75.pd <- predict(TtFtCLnR.75, TtFt.75$test, type = "response")

plot(x=TtFt.75$test$CONST_FREQ, y=TtFtCLnR.75.pd)

summary(TtFtCLnR.75)

#Fatty Liver
TtFt.75trainCDFL <- weight(TtFt.75$train, TtFt.75$train$FL_YN)


TtFtFLLR.75 <- glm(FL_YN ~ GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = TtFt.75trainCDFL,
                   family = "binomial")

TtFtFLLR.75.test <- testModel(TtFtFLLR.75, TtFt.75$test, TtFt.75$test$FL_YN)
modelROC(TtFtFLLR.75, TtFt.75$test, TtFt.75$test$FL_YN)

#Depression

TtFt.75trainCDDe <- weight(TtFt.75$train, TtFt.75$train$DEP_YN)


TtFtDeLR.75 <- glm(DEP_YN ~ GNDR_CD +YOB  +AVG_BMI_RAW +  WIMD_2014_DECILE,
                   data = TtFt.75trainCDDe,
                   family = "binomial")

TtFtDeLR.75.test <- testModel(TtFtDeLR.75, TtFt.75$test, TtFt.75$test$DEP_YN)
modelROC(TtFtDeLR.75, TtFt.75$test, TtFt.75$test$DEP_YN)


TtFtDeLnR.75 <- lm(DEP_FREQ ~ GNDR_CD + AVG_BMI_RAW + WIMD_2014_DECILE,
                   data = TtFt.75trainCDDe)

TtFtDeLnR.75.pd <- predict(TtFtDeLnR.75, TtFt.75$test, type = "response")

plot(x=TtFt.75$test$DEP_FREQ, y=TtFtDeLnR.75.pd,
     xlab = "Predicted frequency", ylab = "Observed frequency",
     main = ("Scattergraph of predicted depression events\nagainst observed events in Group 3"))
abline(a=0, b=1)
legend(x = "topright",
       legend = expression("R"^2*"=0.11"),
       bty = "n") 


### Comparing models

Where I have written \*\*\*, this means I have removed text for security sake

#Source the setup code
source("P:/\*\*\*/workspace/1415/Scripts/R Scripts/Setup for export.R")

#comparing predictors of diabetes in 15yr olds with and without WIMD
DFLcv0.75 <- DFL.cv(FtYOs, 0.75)
DFLcv0.8 <- DFLcv(FtYOs, 0.8)
DFLcv0.9 <- DFLcv(FtYOs, 0.9)

depcv0.75 <- Depcv(FtYOs, 0.75)
depcv0.8 <- Depcv(FtYOs, 0.8)
depcv0.9 <- Depcv(FtYOs, 0.9)

DFLcv0.75TtFt <- DFLcv(TtFt, 0.75)
DFLcv0.8TtFt <- DFLcv(TtFt, 0.8)
DFLcv0.9TtFt <- DFLcv(TtFt, 0.9)

constcv0.75 <- constcv(FtYOs, 0.75)
constcv0.8 <- constcv(FtYOs, 0.8)
constcv0.9 <- constcv(FtYOs, 0.9)

Fconstcv0.75 <- constcv(FYOs, 0.75)
Fconstcv0.8 <- constcv(FYOs, 0.8)
Fconstcv0.9 <- constcv(FYOs, 0.9)

#Rank

DFLcv0.75.rank <- DFLcv.rank(FtYOs, 0.75)
DFLcv0.8.rank <- DFLcv.rank(FtYOs, 0.8)
DFLcv0.9.rank <- DFLcv.rank(FtYOs, 0.9)

depcv0.75.rank <- Depcv.rank(FtYOs, 0.75)
depcv0.8.rank <- Depcv.rank(FtYOs, 0.8)
depcv0.9.rank <- Depcv.rank(FtYOs, 0.9)

DFLcv0.75FtFt.rank <- DFLcv.rank(FtT, 0.75)
DFLcv0.8FtFt.rank <- DFLcv.rank(FtFt, 0.8)
DFLcv0.9FtFt.rank <- DFLcv.rank(FtFt, 0.9)

DFLcv0.75TtFt.rank <- DFLcv.rank(TtFt, 0.75)
DFLcv0.8TtFt.rank <- DFLcv.rank(TtFt, 0.8)
DFLcv0.9TtFt.rank <- DFLcv.rank(TtFt, 0.9)

constcv0.75.rank <- constcv.rank(FtYOs, 0.75)
constcv0.8.rank <- constcv.rank(FtYOs, 0.8)
constcv0.9.rank <- constcv.rank(FtYOs, 0.9)

Fconstcv0.75.rank <- constcv.rank(FYOs, 0.75)
Fconstcv0.8.rank <- constcv.rank(FYOs, 0.8)
Fconstcv0.9.rank <- constcv.rank(FYOs, 0.9)

TDFLcv0.75.rank <- DFLcv.rank(TYOs, 0.75)
TDFLcv0.8.rank <- DFLcv.rank(TYOs, 0.8)
TDFLcv0.9.rank <- DFLcv.rank(TYOs, 0.9)

#Results used:
#5
FC <- constcv(FYOs, 0.75)
#10
TC <- constcv(TYOs, 0.75)
#15
FtDFL <- DFLcv(FtYOs, 0.75)
FtDi <- diabcv(FtYOs, 0.75)
FtFL <- FLcv(FtYOs, 0.75)
FtC <- constcv(FtYOs, 0.75)
FtDe <- Depcv(FtYOs, 0.75)

#G1
FtTC <- constcv(FtT, 0.75)
#G2
FtFtC <- constcv(FtFt, 0.75)
FtFtDe <- Depcv(FtFt, 0.75)
Depcv(TYOs, 0.75)
#G3
TtFtDFL <- DFLcv(TtFt, 0.75)
TtFtDi <- diabcv(TtFt, 0.75)
TtFtFL <- FLcv(TtFt, 0.75)
TtFtC <- constcv(TtFt, 0.75)
TtFtDe <- Depcv(TtFt, 0.75)

AAcv(FtYOs, 0.75)
DFLcv(FtYOs, 0.9)
Depcv.rank(FtYOs, 0.75)
FLcv.YOB(TtFt, 0.75)
