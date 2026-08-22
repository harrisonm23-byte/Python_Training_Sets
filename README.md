# Python_Training_Sets
Personally developed Python practice exercises.

## Intro / Disclaimer
I developed these exercises as part of my Python practice process. I designed these exercises independently. I used AI as a tutors and code reviewers. I'd write the code myself, share my attempted solutions, and receive feedback on errors, syntax, and alternatives. I then practiced the exercises in my terminal in order to build procedural memory and develop fluency.

## _______________________________

## Exercise 1: City Abbreviations
    cities["abbrev"]

Based on a dictionary of East Coast and West Coast cities, I abbreviate or 'acronymize' the cities' names based on whether they contain one word or two words. I start with a dictionary of cities across the west coast and east coast, and then create a function that updates a new key, cities["abbrev"], with an acronym or abbreviation for each city.

For cities containing a space, the function returns an acronym using the first letter of each word. For single-word cities, it instead uses the first three letters and converts them to uppercase. 

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
Updated cities dict:

```python
print(cities)

{'East Coast': ['New York', 'Boston', 'Washington DC'],
'West Coast': ['Los Angeles', 'San Francisco', 'Palo Alto'], 
'abbrev': ['NY', 'BOS', 'WD', 'LA', 'SF', 'PA']}
```
Note: This version only uses the first two words of a city name with multiple words. For example, New York City becomes --> 'NY', not 'NYC'. For that, we would use .split().
## __________________________________________

## Exercise 2: Sunrise and Sunset Calculator: 
    sun_times(day, anchors)

The following function estimates sunrise and sunset timing for any date in the calendar year. For a given date, it approximates the time of sunrise and sunset by calculating the date's relative position between two of four anchor points, which individualy represent an equinox or solstice (i.e., "anchor dates"). 

The function further approximates the equinoxes and solstices as March 21, June 21, September 21, and December 21, ignoring annual variations in seasonal timing. 

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
Apply a linear factor of ~1/90 to the date index, and multiply it by the difference between the next and last anchor date sunrise/sunset.

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

# alternatively, we can also define span = (date(day.year, *next_anchor) - date(day.year, *last_anchor)).days

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
    anchors)

print(sunrise)
print(sunset)
```

## ____________________________________________________

## Exercise 3: ID Code Generator

This exercise creates a system for generating  ID numbers, each containing 4 letters and 5 digits. 

Each policyholder is represented by a numbered key in a dictionary containing the card holder's current ID, prior IDs, and characters changed in the most recent generation.

When a new ID is generated, 2 characters are replaced according to a set of indexing and arithmetic rules. 

Rules:

    - Each ID must contain exactly 9 characters.
    - Each ID must contain exactly 4 letters and 5 digits.
    - Exactly 2 characters must be replaced per generation.
    - The characters replaced in the previous generation are preserved.
    - No previously used ID may be returned.

Replacement logic: 

1. If a number or letter was replaced in the previous ID generation, preserve that character.
    
2. For the remaining positions:

   - If a number: Sum the digits of the product of the ID's numeric values, and add this number to the target digit.
   - If a letter: Map the letter's position (A-Z) to a corresponding numerical value between 1-26.
       - Add the product of the ID’s numeric values to the target letter, and re-assign to a new letter. If 
    
```python

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
# Sample members dataset
members = {
    101: {
        "current_id": 'ABCD12345',
        "id_history": ['ABGH12345', 'ABCD12345',]
        "last_changed": [2, 3]  
    },
}

# Run function for member 101
holder = 101
new_id = assign_new_id(members, holder)

print(f"Generated New ID: {new_id}")
print("Updated Member Data:")
print(members[holder])

# Output

Generated New ID: STCD12345
Updated Member Data:
{'current_id': 'STCD12345',
 'id_history': ['ABGH12345', 'ABCD12345', 'STCD12345'],
 'last_changed': [0, 1]}

# The function changes ABCD12345 to STCD12345, replacing A with S and B with T.
```
## ____________________________________________________

## Exercise 4: Pet Coordinator

I often practiced Python during a visit to New York for a maternal family event. A common topic at such gatherings concerns the boarding of everyone's pets, since some relatives accept pets and other don't. 

This exercise determines whose pet can tag along, and whose have to be boarded. The exercise practices Boolean logic, dictionaries, nested data structures, and loops.

```python
people = {
    "Mom":     {"rules": []},
    "Jay":     {"rules": []},
    "Sue":     {"rules": ["cat"]},
    "Steven":  {"rules": []},
    "Kyle":    {"rules": []},
    "Shannon": {"rules": []},
    "Arnold":  {"rules": ["dog"]},
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
    rule_against = []

    for resident in households[house]["residents"]:
        rule_against.extend(people[resident]["rules"])

    return rule_against


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

# Result: 
Parker Comes since Mom is visiting Jay_and_Sue, who welcomes dogs
Mouse Boarded since Mom is visiting Jay_and_Sue, who cannot have cats
Corgy Stays since Steven is not traveling
Wilson Stays since Jay is not traveling
Collie Stays since Shannon is not traveling
```


---

Briefly, the Ratio Method. 

def ratio_method(incident_count, population, total_damages, gdp_per_capita,
                 sv1, sv2, th, lc1, lc2):



## Thanks for reading!
