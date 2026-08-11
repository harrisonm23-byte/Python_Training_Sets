# Python_Training_Sets
Personally developed and created Python practice exercises covering functions and small data-oriented problems: data structures, classes, loops, indexing, etc.

## Intro / AI-Assistance Disclaimer
I developed these exercises as part of my Python practice, building them around datasets and rules that I ideated independently. Unlike in an exam setting, I used AI tools as a tutor and code reviewer. I would write the code myself, share my attempted solutions, and ask for feedback on errors, syntax, and alternative approaches. I then repeatedly re-wrote and re-ran the exercises in my terminal to reinforce the underlying concepts, build fluency, and develop procedural memory.

## _______________________________

## Exercise 1: City Abbreviations
    cities["abbrev"]

Based on a dictionary of East Coast and West Coast cities, I abbreviate or 'acronymize' the city names based on whether they contain one word or two. Start with a dictionary of cities across the West Coast and East coast, and create a function that updates a new key, cities["abbrev"], with  an acronym or abbreviation for each city based on whether the city name contains a space.

For those with a space, capitalize the first letter of each word and combine them to make an acronym. For one word city names, capitalize and return only the first three letters. 

The exercise practices nested iteration, dictionary access, conditionals, string indexing and slicing, `.upper()`, and `.append()`.

```python
cities = {
    "East Coast": ["New York", "Boston", "Washington DC"],
    "West Coast": ["Los Angeles", "San Francisco", "Palo Alto"],
    "abbrev": []
}

for coast in ["East Coast", "West Coast"]:
    for city in cities[coast]:
        if " " in city:
            cities["abbrev"].append(
                city[0].upper()
                + city[city.index(" ") + 1].upper()
            )
        else:
            cities["abbrev"].append(city[:3].upper())

print(cities["abbrev"])
```

Output:

```python
['NY', 'BOS', 'WD', 'LA', 'SF', 'PA']
```

For cities containing a space, the exercise constructs an abbreviation from the first letter of each word. For single word cities, it instead uses the first three letters and converts them to uppercase.

Updated cities dict:

```python
print(cities)

{'East Coast': ['New York', 'Boston', 'Washington DC'],
'West Coast': ['Los Angeles', 'San Francisco', 'Palo Alto'], 
'abbrev': ['NY', 'BOS', 'WD', 'LA', 'SF', 'PA']}
```

## __________________________________________

## Exercise 2: Sunrise and Sunset Calculator: 
    sun_times(day, anchors)

For a given a date, find the sunrise and sunset based on provided equinox and solstice sunrise/sunset timing ("anchor dates"). 

Goal: Write a a function that approximate sunrise/sunset times based on anchor data, and estimates the sunrise and sunset for any date in the calendar year.

The function approximates the equinoxes and solstice dates as March 21, June 21, September 21, and December 21, ignoring annual variations in seasonal timing. 

```python
from datetime import date

spring = (3, 21)
summer = (6, 21)
fall = (9, 21)
winter = (12, 21)

def hm(h, m=0):
    return h * 60 + m

def sun_times(day, anchors):
    
    """
    anchors format: {
    spring:  (sunrise_minutes, sunset_minutes),
    summer:  (sunrise_minutes, sunset_minutes),
    fall:  (sunrise_minutes, sunset_minutes),
    winter: (sunrise_minutes, sunset_minutes)
    }
    Times are minutes after midnight.
    Example: 
    6:30 AM: hm(6, 30) = 360, 6:15 PM: hm(18, 15) = 1095
    """
    
# Determine which pair of anchor dates surrounds the target date in order to obtain a date index.

    month = day.month
    
    d = day.day
    
    if (month, d) >= spring and (month, d) < summer:
        last_anchor = spring
        next_anchor = summer
        day_index = (day - date(day.year, *spring)).days

    elif (month, d) >= summer and (month, d) < fall:
        last_anchor = summer
        next_anchor = fall
        day_index = (day - date(day.year, *summer)).days

    elif (month, d) >= fall and (month, d) < winter:
        last_anchor = fall
        next_anchor = winter
        day_index = (day - date(day.year, *fall)).days

    elif (month, d) >= winter:
        last_anchor = winter
        next_anchor = spring
        day_index = (day - date(day.year, *winter)).days

    else:
        # Jan 1 through Mar 20
        last_anchor = winter
        next_anchor = spring
        day_index = (
            day - date(day.year - 1, *winter)
        ).days

    last_sunrise, last_sunset = anchors[last_anchor]
    next_sunrise, next_sunset = anchors[next_anchor]
```
Apply a linear smoothing factor of ~1/90 to the date index, and multiply it by the difference between next and last anchor sunrise/sunset.

```python    
    sunrise = (
        last_sunrise
        + day_index * (1 / 90)
        * (next_sunrise - last_sunrise)
    )

    sunset = (
        last_sunset
        + day_index * (1 / 90)
        * (next_sunset - last_sunset)
    )

    return sunrise, sunset

# alternatively, define span = (date(day.year, *next_anchor) - date(day.year, *last_anchor)).days

```

Example: 

```python
anchors = {
    spring:  (hm(6, 0), hm(18, 0)),   # 6:00 AM, 6:00 PM
    summer:  (hm(5, 0), hm(20, 30)),   # 5:00 AM, 8:30 PM
    fall:  (hm(6, 0), hm(18, 0)),   # 6:00 AM, 6:00 PM
    winter: (hm(7, 0), hm(16, 40)),    # 7:00 AM, 4:40 PM
}

sunrise, sunset = sun_times(
    date(2026, 5, 5),
    anchors
)

print(sunrise)
print(sunset)
```

## ____________________________________________________

## Exercise 3: Family Vacation

I frequently practiced Python during a visit to New York for a maternal family event. A common topic of family gatherings surrounds the boarding of everyone's pets. Some relatives accept pets and other don't. 

Accordingly, this exercise determines whose pets comes along or have to be boarded, depending on who is visiting who. This exercise tests Boolean logic, dictionaries, nested data structures, and loops.

Disclaimer: Admittedly, this exervise is largely fictitious – no one ever brings their cat. 

```python
people = {
    "Mom":     {"avoids": []},
    "Jay":     {"avoids": []},
    "Sue":     {"avoids": ["cat"]},
    "Steven":  {"avoids": []},
    "Kyle":    {"avoids": []},
    "Shannon": {"avoids": []},
    "Arnold":  {"avoids": ["dog"]},
}

households = {
    "Mom":          {"residents": ["Mom"],             "accepts_pets": True},
    "Jay_and_Sue":  {"residents": ["Jay", "Sue"],      "accepts_pets": True},
    "Steven":       {"residents": ["Steven"],          "accepts_pets": True},
    "Kyle_Shannon": {"residents": ["Kyle", "Shannon"], "accepts_pets": False},
    "Arnold":       {"residents": ["Arnold"],          "accepts_pets": True},
}

pets = {
    "Parker": {"kind": "dog", "owner": "Mom"},
    "Mouse":  {"kind": "cat", "owner": "Mom"},
    "Corgy":  {"kind": "dog", "owner": "Steven"},
    "Wilson": {"kind": "dog", "owner": "Jay"},
    "Collie": {"kind": "dog", "owner": "Shannon"},
}
```
Now define households, house rules, and boarding, and format the results: 

```python 
def household_of(person, households):
    for house, info in households.items():
        if person in info["residents"]:
            return house
    return None


def house_rules(house, households, people):
    avoided = []

    for resident in households[house]["residents"]:
        avoided.extend(people[resident]["avoids"])

    return avoided


def boarding(pets, visits, households, people):
    result = {}

    for pet, info in pets.items():
        owner = info["owner"]
        kind = info["kind"]

        if owner not in visits:
            result[pet] = ("Stays", f"since {owner} is not traveling")
            continue

        house = household_of(visits[owner], households)

        if kind in house_rules(house, households, people):
            result[pet] = (
                "Boarded",
                f"since {owner} is visiting {house}, who cannot have {kind}s")

        elif households[house]["accepts_pets"]:
            result[pet] = (
                "Comes",
                f"since {owner} is visiting {house}, who welcomes {kind}s")

        else:
            result[pet] = (
                "Boarded",
                f"since {owner} is visiting {house}, who does not take {kind}s")
                
    return result
```
Example: 
    Mom visiting Aunt Sue
    
```python
  visits = {"Mom": "Sue"}

for pet, (boards, why) in boarding(
    pets, visits, households, people
).items():
    print(pet, boards, why)
```
Result: 
```python
Parker Comes since Mom is visiting Jay_and_Sue, who welcomes dogs
Mouse Boarded since Mom is visiting Jay_and_Sue, who cannot have cats
Corgy Stays since Steven is not traveling
Wilson Stays since Jay is not traveling
Collie Stays since Shannon is not traveling
```

## Exercise 4: ID Replacement Generator

In this exercise, I practiced nested dictionaries, indexing, loops, string manipulation, arithmetic, and functions. 

This exercise creates a system for generating and tracking ID numbers. Each policyholder is represented by a numbered key in a dictionary containing the card holder's current ID, prior IDs, and the characters changed in the most recent generation.

Each ID contains 9 characters: 4 letters and 5 digits. When a new ID is generated, 2 characters are replaced according to a set of indexing and arithmetic rules. 

Rules:

    - Each ID must contain exactly 9 characters.
    - Each ID must contain exactly 4 letters and 5 digits.
    - Exactly 2 characters must be replaced per generation.
    - The characters replaced in the previous generation are preserved.
    - No previously used ID may be returned.

Replacement logic: 

1. If a number or letter was replaced in the previous ID generation, preserve that character.
    
2. For the remaining positions:
    
    To replace a _letter_: Add the _product_ of the ID’s numeric values to the target letter by mapping its alphabetical position (A-Z) to a corresponding numerical value between 1-26. 
    
    To replace a _number_: Sum the digits of the product of the ID's numeric values, and add this number to an existing replacement candidate. 
    
```python

"""Calculate the replacement values using the sum of the digits in the existing ID and the index of the character being replaced. Numeric results are mapped to either letters A–Z or digits 0–9 depending on the type of the existing character.
    
Characters changed during the immediately preceding generation are kept in the next generation, and the resulting ID is checked against all previous IDs before being assigned to the holder."""

def assign_new_id(members, holder):
    member = members[holder]
    current = member["current_id"]

    used = {
        id_
        for m in members.values()
        for id_ in m["id_history"]
    }

    available = [
        i for i in range(len(current))
        if i not in member["last_changed"]
    ]

    seed = 1
    for c in current:
        if c.isdigit():
            seed *= int(c) + 1

    while True:
        first = available[seed % len(available)]
        rest = [i for i in available if i != first]
        second = rest[seed % len(rest)]
        new = list(current)

        for i in (first, second):
            if current[i].isalpha():
                new[i] = chr(65 + (seed + i) % 26)
                if new[i] == current[i]:
                    new[i] = chr(65 + (seed + i + 1) % 26)
            else:
                new[i] = str((seed + i) % 10)
                if new[i] == current[i]:
                    new[i] = str((seed + i + 1) % 10)

        new = "".join(new)

        if new not in used:
            break

        seed += 1

    member["current_id"] = new
    member["id_history"].append(new)
    member["last_changed"] = [first, second]

    return new
```

Example: 


```python
