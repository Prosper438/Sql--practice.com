# Hard Question section 1
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
SELECT patient_id,weight,height,
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
SELECT patients.patient_id,patients.first_name,patients.last_name,doctors.specialty
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
SELECT DISTINCT admissions.patient_id, CONCAT(admissions.patient_id,LEN(patients.last_name),year(patients.birth_date)) AS temp_password
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
SELECT  *,
CASE 
	WHEN patient_id % 2 = 0 then "Yes"
    WHEN patient_id % 2 = 1 THEN "NO"
END AS has_insurance
FROM admissions
   )

SELECT  has_insurance,
SUM(CASE
	WHEN has_insurance = "Yes" THEN 10
    ELSE 50
END ) AS cost_after_insurance
FROM GRADING
group by has_insurance
```
##### Question 6
Show the provinces that has more patients identified as 'M' than 'F'. Must only show full province_name.
``` sql
WITH gender_count as
 (
  select province_name, SUM( gender = "M") AS male_count, SUM(gender = "F") AS female_count
  from province_names
  JOIN patients
  USING (province_id)
  group by province_name
   )
SELECT province_name 
FROM gender_count
WHERE male_count > female_count
group by province_name
```
#####  Question 7
We are looking for a specific patient. Pull all columns for the patient who matches the following criteria:
- First_name contains an 'r' after the first two letters.
- Identifies their gender as 'F'
- Born in February, May, or December
- Their weight would be between 60kg and 80kg
- Their patient_id is an odd number
- They are from the city 'Kingston'.
``` sql
SELECT *
from patients
where (first_name LIKE "__r%" AND gender = "F" 
	AND MONTH(birth_date) IN (2,5,12) AND weight between 60 AND 80
	AND patient_id % 2 = 1 AND city = "Kingston")
```
##### Question 8
Show the percent of patients that have 'M' as their gender. Round the answer to the nearest hundreth number and in percent form.
``` sql
WITH male_percent as
  (
  SELECT ( select count(patient_id) FROM patients where gender = "M") as male_count,
  		 ( select COUNT(patient_id) from patients) AS total_count
    )
    SELECT CONCAT ( ROUND(male_count * 100.0/total_count,2), "%") AS male_percent
    From male_percent
```
##### Question 9
For each day display the total amount of admissions on that day. Display the amount changed from the previous date.
``` sql
select admission_date,COUNT(admission_date) AS no_of_admissions,
COUNT(admission_date) - LAG( COUNT(admission_date),1) OVER(order by admission_date) AS amount_change
FROM admissions
group by admission_date
```
##### Question 10
We need a breakdown for the total amount of admissions each doctor has started each year. Show the doctor_id, doctor_full_name, specialty, year, total_admissions for that year.
``` sql
   SELECT doctor_id,concat(first_name," ",last_name) AS full_name,specialty,
   YEAR(admission_date) AS selected_year,COUNT(YEAR(admission_date))
   FROM doctors
   LEFT JOIN admissions 
   ON admissions.attending_doctor_id = doctors.doctor_id
   group by selected_year,doctor_id
   order by 1
   
```
##### Question 11
Sort the province names in ascending order in such a way that the province 'Ontario' is always on top.
``` sql
WITH ordering as
(
SELECT province_name,
CASE 
    WHEN province_name = "Ontario" THEN 0
    ELSE 1
END AS LABEL
FROM province_names
)
select province_name
from ordering
ORDER BY LABEL,province_name
```
# Hard Questions Section 2
This section answers the question in the northwind database.
##### Question 1
Show the employee's first_name and last_name, a "num_orders" column with a count of the orders taken, and a column called "Shipped" that displays "On Time" if the order shipped_date is less or equal to the required_date, "Late" if the order shipped late, "Not Shipped" if shipped_date is null.
Order by employee last_name, then by first_name, and then descending by number of orders.
``` sql
select first_name,last_name,COUNT(order_id) as num_orders,
CASE
	WHEN shipped_date <= required_date THEN "On Time"
    WHEN shipped_date > required_date THEN "Late"
    ELSE "Not shipped"
END AS shipped
FROM employees
JOIN orders
USING(employee_id)
group by 1,2,4
order by last_name,first_name,num_orders desc
```
##### Question 2
Show how much money the company lost due to giving discounts each year, order the years from most recent to least recent. Round to 2 decimal places

``` sql
Select 
YEAR(o.order_date) AS 'order_year' , 
ROUND(SUM(p.unit_price * od.quantity * od.discount),2) AS 'discount_amount' 

from orders o 
JOIN order_details od ON o.order_id = od.order_id
JOIN products p ON od.product_id = p.product_id

group by YEAR(o.order_date)
order by order_year desc;
```
