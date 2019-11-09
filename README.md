# mapped-uk

Create a map of the UK based on certain fun statistics

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
`sudo pip install https://github.com/matplotlib/basemap/archive/master.zip`

Copy and paste the code from the guide here: https://ramiro.org/notebook/mapping-pubs/, there's nothing to change.

## Getting the data

The data comes from openstreetmap.org, which is a public maps API, it's very comprehensive. Visit https://www.openstreetmap.org/export#map=6/57.238/-1.395 to get a plot of anywhere in the world. You'll need to copy the coorindates of the location in the top left side of the screen.

In order to query large areas, you need to use the overpass api (which you can read about here https://wiki.openstreetmap.org/wiki/Overpass_API/Applications). For our use case you can use a handy node.js package: https://github.com/perliedman/query-overpass.

The main steps:
`npm install -g query-overpass`

Enter the coorindates you took from the openstreetmap.org site in the following order: S, W, N E, so for example with UK cooridinates:
`echo 'node(57.7,11.9,57.8,12.0)[amenity=pub];out;' | query-overpass >> map.json`

This will give you a huge JSON output from the API, writing `>> map.json` will store it in a file.

