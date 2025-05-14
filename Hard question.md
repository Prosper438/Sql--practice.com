# Hard question section
This covers the hard questions from the hospital database.

#####  Question 1
Show all of the patients grouped into weight groups.Show the total amount of patients in each weight group.
Order the list by the weight group decending.
For example, if they weight 100 to 109 they are placed in the 100 weight group, 110-119 = 110 weight group, etc.
 ``` sql
 SELECT
  COUNT(*) AS patients_in_group,
  FLOOR(weight / 10) * 10 AS weight_group
FROM patients
GROUP BY weight_group
ORDER BY weight_group DESC;
-- Alternative answer
SELECT
  TRUNCATE(weight, -1) AS weight_group,
  count(*)
FROM patients
GROUP BY weight_group
ORDER BY weight_group DESC;
```
##### Question 2
Show patient_id, weight, height, isObese from the patients table.Display isObese as a boolean 0 or 1.
Obese is defined as weight(kg)/(height(m)2) >= 30.weight is in units kg.
height is in units cm.
``` sql
select patient_id,weight,height,
CASE
	WHEN weight/POWER(height/100.0,2) >= 30 THEN 1
 	ELSE 0
END as isObese
FROM patients
```
##### Question 3
Show patient_id, first_name, last_name, and attending doctor's specialty.
Show only the patients who has a diagnosis as 'Epilepsy' and the doctor's first name is 'Lisa'
``` sql
select patients.patient_id,patients.first_name,patients.last_name,doctors.specialty
FROM patients
LEFT JOIN admissions
USING(patient_id)
LEFT JOIN doctors
ON admissions.attending_doctor_id = doctors.doctor_id
where doctors.first_name = "Lisa" AND admissions.diagnosis ="Epilepsy"
```
#####  Question 4
All patients who have gone through admissions, can see their medical documents on our site. 
Those patients are given a temporary password after their first admission. Show the patient_id and temp_password.
The password must be the following, in order:
1. patient_id
2. the numerical length of patient's last_name
3. year of patient's birth_date
```sql
select DISTINCT admissions.patient_id, CONCAT(admissions.patient_id,LEN(patients.last_name),year(patients.birth_date)) AS temp_password
FROM admissions
LEFT JOIN patients
USING(patient_id)
```
#####  Question 5
Each admission costs $50 for patients without insurance, and $10 for patients with insurance. All patients with an even patient_id have insurance.
Give each patient a 'Yes' if they have insurance, and a 'No' if they don't have insurance. Add up the admission_total cost for each has_insurance group.
``` sql
WITH GRADING AS
 (
select  *,
CASE 
	WHEN patient_id % 2 = 0 then "Yes"
    WHEN patient_id % 2 = 1 THEN "NO"
END AS has_insurance
FROM admissions
   )

select  has_insurance,
SUM(CASE
	WHEN has_insurance = "Yes" THEN 10
    ELSE 50
END ) AS cost_after_insurance
FROM GRADING
group by has_insurance
```
