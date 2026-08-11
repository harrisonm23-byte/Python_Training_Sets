# Python_Training_Sets
Personally developed and created Python practice exercises covering functions and small data-oriented problems: data structures, classes, loops, indexing, etc.

## ____________________________________________________

## Exercise 1: City Abbreviations

Based on a dictionary of East Coast and West Coast cities, I abbreviate or acronymize the city names based on whether they contain one word or two. 

I start by adding a list of cities to dictionary keys "East Coast" and "West Coast". I then create a function that updates a new dictionary key, cities["abbrev."], with either an acronym or abbreviation for each city in the cities dictionary, with formatting based on whether the city name contains a space.

For those with a space, capitalize and combine the first letter of each word. For those cities with a one word name, capitalize only the first three letters, and abbreviate the name using just this string. 

The exercise practices nested iteration, dictionary access, conditionals, string indexing and slicing, `.upper()`, and `.append()`.

```python
cities = {
    "East Coast": ["New York", "Boston", "Washington DC"],
    "West Coast": ["Los Angeles", "San Francisco", "Palo Alto"],
    "abbrev.": []
}

for coast in ["East Coast", "West Coast"]:
    for city in cities[coast]:
        if " " in city:
            cities["abbrev."].append(
                city[0].upper()
                + city[city.index(" ") + 1].upper()
            )
        else:
            cities["abbrev."].append(city[:3].upper())

print(cities["abbrev."])
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
'abbrev.': ['NY', 'BOS', 'WD', 'LA', 'SF', 'PA']}
```

## ____________________________________________________

## Exercise 2: Sunrise and Sunset Calculator

For a given a location, find the sunrise and sunset around the equinox and solstice dates ("anchor dates"). Write a function that smooths out the sunrise/sunset timing based on the anchor dates data, and estimates the sunrise and sunset for any date in the calendar year.

The function approximates the equinoxes and solstice dates as March 21, June 21, September 21, and December 21, ignoring annual variations in seasonal timing. 

```python
from datetime import date

def sun_times(day, anchors):
    """
    Estimate sunrise and sunset using simple linear interpolation.

    anchors format:
    {
        (3, 21):  (sunrise_minutes, sunset_minutes),
        (6, 21):  (sunrise_minutes, sunset_minutes),
        (9, 21):  (sunrise_minutes, sunset_minutes),
        (12, 21): (sunrise_minutes, sunset_minutes),
    }

    Times are minutes after midnight.
    Example: 6:30 AM = 390, 6:15 PM = 1095
    """

    month = day.month
    d = day.day
```
Next, determine which pair of anchor dates surrounds the target date in order to obtain a date index.

```python
    if (month, d) >= (3, 21) and (month, d) < (6, 21):
        last_anchor = (3, 21)
        next_anchor = (6, 21)
        day_index = (day - date(day.year, 3, 21)).days

    elif (month, d) >= (6, 21) and (month, d) < (9, 21):
        last_anchor = (6, 21)
        next_anchor = (9, 21)
        day_index = (day - date(day.year, 6, 21)).days

    elif (month, d) >= (9, 21) and (month, d) < (12, 21):
        last_anchor = (9, 21)
        next_anchor = (12, 21)
        day_index = (day - date(day.year, 9, 21)).days

    elif (month, d) >= (12, 21):
        last_anchor = (12, 21)
        next_anchor = (3, 21)
        day_index = (day - date(day.year, 12, 21)).days

    else:
        # Jan 1 through Mar 20
        last_anchor = (12, 21)
        next_anchor = (3, 21)
        day_index = (
            day - date(day.year - 1, 12, 21)
        ).days

    last_sunrise, last_sunset = anchors[last_anchor]
    next_sunrise, next_sunset = anchors[next_anchor]
```
Last, apply a linear 1/90 smoothing factor to the date index, multiplied by the difference between next and last anchor sunrise & sunset.

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
```

Example: 

```python
anchors = {
    (3, 21):  (360, 1080),   # 6:00 AM, 6:00 PM
    (6, 21):  (300, 1200),   # 5:00 AM, 8:00 PM
    (9, 21):  (360, 1080),   # 6:00 AM, 6:00 PM
    (12, 21): (420, 960),    # 7:00 AM, 4:00 PM
}

sunrise, sunset = sun_times(
    date(2026, 5, 5),
    anchors
)

print(sunrise)
print(sunset)
```

## ____________________________________________________
