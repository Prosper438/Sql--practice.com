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
