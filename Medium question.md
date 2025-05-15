## MEDIUM QUESTION SECTION 1
This sections gives answers to the question in the hospital database.
##### Question 1
Show unique birth years from patients and order them by ascending.
``` sql
SELECT distinct YEAR(birth_date)AS birth_year
FROM patients
order by 1 ASC
```

##### Question 2
Show unique first names from the patients table which only occurs once in the list.
For example, if two or more people are named 'John' in the first_name column then don't include their name in the output list. If only 1 person is named 'Leo' then include them in the output.
 ``` sql
SELECT first_name
FROM patients
group by first_name
having COUNT(first_name) = 1
```
##### Question 3
Show patient_id and first_name from patients where their first_name start and ends with 's' and is at least 6 characters long.
``` sql
SELECT
  patient_id,
  first_name
FROM patients
WHERE first_name LIKE 's____%s';
```
##### Question 4
Show patient_id, first_name, last_name from patients whos diagnosis is 'Dementia'.
Primary diagnosis is stored in the admissions table.
``` sql
select patients.patient_id,first_name,last_name
FROM patients
JOIN admissions
USING(patient_id)
where diagnosis = "Dementia"
```
##### Question 5
Display every patient's first_name.
Order the list by the length of each name and then by alphabetically.
```sql
select first_name
FROM patients
order by LEN(first_name) , first_name
```
##### Question 6
Show first and last name, allergies from patients which have allergies to either 'Penicillin' or 'Morphine'. Show results ordered ascending by allergies then by first_name then by last_name.
``` sql
SELECT first_name,last_name,allergies
from patients
where allergies in ("Penicillin","Morphine")
order by allergies,first_name,last_name

```
##### Question 7
Show patient_id, diagnosis from admissions. Find patients admitted multiple times for the same diagnosis.
``` sql
SELECT patient_id,diagnosis
FROM admissions
group by diagnosis,patient_id
having COUNT(diagnosis) > 1
```
##### Question 8
Show the total amount of male patients and the total amount of female patients in the patients table.
Display the two results in the same row.
``` sql
SELECT (SELECT COUNT(*) from patients where gender = "M") AS male_count,
  		 (SELECT COUNT(*) FROM patients where gender = "F") AS female_count
```
##### Question 9
Show the city and the total number of patients in the city.
Order from most to least patients and then by city name ascending.
``` sql
select city,count(patient_id)
from patients
group by city
order by 2 desc,	city
```
##### Question 10
Show first name, last name and role of every person that is either patient or doctor.
The roles are either "Patient" or "Doctor".
``` sql
SELECT first_name,last_name,"patient"
from patients
UNION ALL
select first_name,last_name,"Doctor"
FROM doctors
```
##### Question 11
Show all allergies ordered by popularity. Remove NULL values from query.
``` sql
SELECT allergies,COUNT(allergies) AS allergies_count
FROM patients
where allergies IS NOT NULL
group by allergies
order by allergies_count DESC
```
##### Question 12
Show all patient's first_name, last_name, and birth_date who were born in the 1970s decade. Sort the list starting from the earliest birth_date.
``` sql
SELECT first_name,last_name,birth_date
FROM patients
WHERE birth_date between "1970-01-01" AND "1979-12-31"
order by birth_date
```
##### Question 13
We want to display each patient's full name in a single column. Their last_name in all upper letters must appear first, then first_name in all lower case letters. Separate the last_name and first_name with a comma. Order the list by the first_name in decending order
EX: SMITH,jane.
``` sql
SELECT CONCAT(upper(last_name),",",lower(first_name)) AS altered_name
FROM patients
order by first_name DESC
```
##### Question 14
Show the province_id(s), sum of height; where the total sum of its patient's height is greater than or equal to 7,000.
``` sql
SELECT province_id,SUM(height) AS sum_height
from patients
group by province_id
having SUM(height) >= 7000
```
##### Question 15
Show the difference between the largest weight and smallest weight for patients with the last name 'Maroni'.
``` sql
SELECT max(weight) - MIN(weight) AS weight_range
FROM patients
where last_name = "Maroni"
```
##### Question 16
