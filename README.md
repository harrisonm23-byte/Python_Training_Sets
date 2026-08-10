# Python_Training_Sets
Personally developed and created Python practice exercises covering functions and small data-oriented problems: data structures, classes, loops, indexing, etc.

## Example Exercise: City Abbreviations

One of my favorite practice exercises used a dictionary of East Coast and West Coast cities. After adding cities to dictionary keys "East Coast" and "West Coast", I created a function that updates a new dictionary key, cities["abbrev."], with an acronym or abbreviation for each city in cities, with formatting based on whether the city name contains a space. 

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
