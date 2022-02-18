# mystery.knightlab.com
## 4 Queries To Solve The Murder Mystery

### Get the crime scene report

```SQL
SELECT description
FROM   crime_scene_report
WHERE  date IS 20180115
AND    type IS "murder"
AND    city IS "SQL City"
```

### Get the interviews from the witnesses

```SQL
SELECT name,
       transcript
FROM   person    AS p
JOIN   interview AS i
ON     p.id = i.person_id
WHERE  (
              NAME LIKE "%Annabel%"
       AND    address_street_name IS "Franklin Ave")
OR     (
              address_street_name IS "Northwestern Dr"
       AND    address_number =
              (
                     SELECT max(address_number)
                     FROM   person
                     WHERE  address_street_name IS "Northwestern Dr" ) )
```

### Get the killer according to the interviews

```SQL
SELECT member.id,
       p.name,
       transcript
FROM   person             AS p
JOIN   get_fit_now_member AS member
ON     member.person_id = p.id
JOIN   get_fit_now_check_in AS check_in
ON     check_in.membership_id = member.id
JOIN   drivers_license AS dl
ON     dl.id = p.license_id
JOIN   interview
ON     interview.person_id = p.id
WHERE  member.id LIKE "48Z%"
AND    membership_status IS "gold"
AND    check_in_date IS 20180109
AND    plate_number LIKE "%H42W%"
```

### Get the conspirator

```SQL
SELECT   name,
         annual_income AS income,
         height,
         hair_color,
         car_make || " " || car_model AS car,
         event_name,
         Count(name)            AS attended,
         Min(fb.date)           AS "min date",
         Max(fb.date)           AS "max date"
FROM     facebook_event_checkin AS fb
JOIN     person                 AS p
ON       fb.person_id = p.id
JOIN     income
ON       income.ssn = p.ssn
JOIN     drivers_license AS dl
ON       dl.id = p.license_id
GROUP BY name
HAVING   Count(name) = 3
AND      date > 20171130
AND      date < 20180101
AND      event_name LIKE "%sql%"
AND      height BETWEEN 65 AND 67
AND      hair_color IS "red"
AND      car_make IS "Tesla"
AND      car_model IS "Model S"
```
