# Mapped pubs in the UK

Create a map of the UK based on certain fun statistics (currently works for pubs)

Taken from https://ramiro.org/notebook/mapping-pubs/

## Setting up the server

### You'll need to install:
- pip3 (brew install pip3)
- python 3.7
- pandas (pip3 install pandas)
- anaconda (https://docs.anaconda.com/anaconda/install/)
- matplotlib

Special notes on installing matplotlib

- Follow the guide here: https://matplotlib.org/basemap/users/installing.html
- It is possible that you won't get `mpl_toolkit.basecamp` even once you have followed the instructions in the guide above. If that happens, you should install basecamp directly from github: 

```
sudo pip install https://github.com/matplotlib/basemap/archive/master.zip
```

Copy and paste the code from the guide here into a python file: https://ramiro.org/notebook/mapping-pubs/, there are a couple of things to change:

In the line that you add the csv file name to, add `error_bad_lines=False`. This will skip places where there is missing data.
```
df = pd.read_csv('pubsuk.csv', error_bad_lines=False)
```

#### Remove some lines of code:

This doesn't work with the current api format.
```
df.dropna(axis=1, how='any', inplace=True)
```

These filter out pubs, but we have already done that in our API query.
```
df.features_properties_tags_amenity.value_counts().head()

df = df[df.features_properties_tags_amenity.str.match(r'\bpub\b')]
df.features_properties_tags_amenity.value_counts()
```

In this part, replace the `Longitude` and `Latitude` values with `features__longitude` and `features__latitude`. This is the column name that will be returned from the JSON to CSV parser later on.
```
locations = [loc for loc in zip(df.features__longitude.values, df.features__latitude.values) if within_bbox(bbox, loc)]
```

## Getting the data

The data comes from openstreetmap.org, which is a public maps API, it's very comprehensive. Visit https://www.openstreetmap.org/export#map=6/57.238/-1.395 to get a plot of anywhere in the world. You'll need to copy the coorindates of the location in the top left side of the screen.

In order to query large areas, you need to use the overpass api (which you can read about here https://wiki.openstreetmap.org/wiki/Overpass_API/Applications). For our use case you can use a handy node.js package: https://github.com/perliedman/query-overpass.

The main steps:
```
npm install -g query-overpass
```

Enter the coorindates you took from the openstreetmap.org site in the following order: S, W, N, E, so for example with UK cooridinates:
```
echo 'node(49.507,-11.804,61.275,2.256)[amenity=pub];out;' | query-overpass >> map.json
```

This will give you a huge JSON output from the API, writing `>> map.json` will store it in a file.

## Putting the data into the python script

With the current script it's not quite as simple, you'll have to map the data and replace the `geometry.coordinates` field with Longitude and Latitude in order to separate them into separate columns. I wrote a simple bit of code that will do that.

A note on the data structure:
```
{ 
  data: {
    "type": "Features",
    "features": [... your data]
  }
}
```

So to get the Longitude and Latitude from the opentstreet api data and then write it to a JSON file:
```
const result = data.features.map(graph => {
  return {
    ...graph,
    Longitude: graph.geometry.coordinates[0],
    Latitude: graph.geometry.coordinates[1]
  };
});

const toFile = {
  "type": "FeatureCollection",
  "features": result 
}

var fs = require('fs');

fs.writeFile('plot.json', JSON.stringify(toFile), function (err) {
  if (err) throw err;
});
```

Once you have your json data, it's time to head back to the internet, we'll need to convert the JSON data to CSV format. I used this tool: https://sqlify.io/convert/json/to/csv.

Download the CSV data and add it to your python script, and change the csv file name appropriately.

Feel free to annotate the file as you like; you might want to add your name to the bottom, or change the colours or font.

Finally, run the script!




