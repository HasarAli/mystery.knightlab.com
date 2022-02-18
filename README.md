# mystery.knightlab.com
4 Queries to solve the mystery murder


## Get the crime scene report

```SQL
SELECT *
FROM   crime_scene_report
WHERE  date IS 20180115
AND    type IS "murder"
AND    city IS "SQL City"

## Get the interviews from the suspects
SELECT NAME,
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

## Get the killer according to the interviews

```SQL
select member.id, p.name, transcript from person as p 
join get_fit_now_member as member on member.person_id = p.id
join get_fit_now_check_in as checkin on checkin.membership_id = member.id
join drivers_license as dl on dl.id = p.license_id
join interview on interview.person_id = p.id

where member.id like "48Z%"
and member.membership_status  is "gold"
and checkin.check_in_date is 20180109
and dl.plate_number like "%H42W%"
```

## Get the conspirator

```SQL
SELECT p.name, annual_income as income, height, hair_color, 
car_make || " " ||car_model as car, event_name, COUNT(name) as attended, 
min(fb.date) as "min date", max(fb.date) as "max date"
FROM facebook_event_checkin as fb
JOIN person as p ON fb.person_id = p.id
JOIN income ON income.ssn = p.ssn
join drivers_license as dl on dl.id = p.license_id
GROUP BY name
Having COUNT(name) = 3
AND fb.date > 20171130
AND fb.date < 20180101
AND event_name like "%sql%"
AND height BETWEEN 65 AND 67
AND hair_color is "red"
AND car_make is "Tesla"
AND car_model is "Model S"
```

