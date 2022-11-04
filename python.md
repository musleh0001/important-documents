<div align="center">
    <h1>Python</h1>
</div>

#### Time module

```python
import datetime

# date
tday = datetime.date.today()
tdelta = datetime.timedelta(days=7)
print(tday + tdelta)

# Note: timedelta helps to calculate date and time
# date = date + timedelta [return date]
# timedelta = date1 + date2 [return timedelta]

bday = datetime.date(2023, 3, 10)
till_bday = bday - tday
print(till_bday) # return timedelta

# time
t = datetime.time(10, 56, 47) # hour, minute, second
print(t)

# datetime
dt = datetime.datetime(2022, 20, 23, 10, 35, 65)
print(dt)

dt_today = datetime.datetime.today() # can't use timezone
dt_now = datetime.datetime.now() # can use timezone
dt_utcnow = datetime.datetime.utcnow() # can use timezone

import pytz

dt_now = datetime.datetime.now(tz=pytz.UTC)
dt_bdt = dt_now.astimezone(pytz.timezone('Asia/Dhaka'))
print(dt_bdt)
print(dt_bdt.strftime("%B %d, %Y"))

# convert datetime string to datetime
dt_str = "July 26, 2022"
dt = datetime.datetime.strptime(dt_str, "%B %d, %Y")
print(dt)

# Note: 
# strftime - Datetime to String
# strptime - String to Datetime

# list of all timezone
for tz in pytz.all_timezones:
    print(tz)
```

#### CSV file manipulation

```python
import csv

with open("names.csv", "r") as csv_file:
    csv_reader = csv.reader(csv_file)

    # skip 1st line
    next(csv_reader)

    for line in csv_reader:
        print(line)

# write
with open("names.csv", "r") as csv_file:
    csv_reader = csv.reader(csv_file, delimiter=",")

    with open("new_names.csv", "w") as new_file:
        csv_writer = csv.writer(new_file, delimiter="-")

        for line in csv_reader:
            csv_writer.writerow(line)

# dict
with open("names.csv", "r") as csv_file:
    csv_reader = csv.DictReader(csv_file, delimiter=",")

    for line in csv_reader:
        print(line)
```

#### Regular Expression

```python
import re

pattern = re.compile(r'abc')
matches = pattern.finditer(str)
for match in matches:
    print(match)
```
