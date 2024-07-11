# maplibre-lab
Experiments in web mapping with MapLibre.  

Making MVT vector tiles of OpenStreetMap highways:  
```
#!/bin/bash

cd ~/maplibre-testing
rm -rf vector-tiles*

ogr2ogr -f MVT vector-tiles-motorway PG:dbname=osm -sql "SELECT name, highway, ST_ChaikinSmoothing(wkb_geometry, 1) wkb_geometry FROM toronto_lines WHERE highway IS NOT NULL" -nlt LINESTRING -nln highway -dsco MINZOOM=10 -dsco MAXZOOM=15 -dsco COMPRESS=NO -dsco SIMPLIFICATION=0.0 -dsco SIMPLIFICATION_MAX_ZOOM=0.0 -explodecollections

cp tiles-osm.json vector-tiles/
cp cors_server.py vector-tiles/
cd ~/maplibre-testing/vector-tiles
./cors_server.py
```

Making MVT vector tiles of OpenStreetMap points:  
```
#!/bin/bash
cd ~/maplibre-testing
rm -rf vector-tiles*

ogr2ogr -f MVT vector-tiles PG:dbname=osm -sql "SELECT name, highway AS type, 1 + floor(random() * 10) AS radius, 1 + floor(random() * 9) AS color, (ST_DumpPoints(wkb_geometry)).geom::geometry(POINT,3857) wkb_geometry FROM toronto_lines WHERE highway IS NOT NULL UNION ALL SELECT name, 'amenity' AS type, 1 + floor(random() * 10) AS radius, 1 + floor(random() * 18) AS color, wkb_geometry FROM toronto_points WHERE other_tags LIKE '%amenity%'" -nlt LINESTRING -nln points -dsco MINZOOM=10 -dsco MAXZOOM=15 -dsco COMPRESS=NO -dsco SIMPLIFICATION=0.0 -dsco SIMPLIFICATION_MAX_ZOOM=0.0 -explodecollections

cp tiles.json vector-tiles/
cp cors_server.py vector-tiles/
cd vector-tiles
./cors_server.py
```
