# maplibre-lab
Experiments in web mapping with MapLibre.  

Making MVT vector tiles of nightlights from osm points:  
```
#!/bin/bash
cd ~/maplibre-testing
rm -rf vector-tiles-nightlight

ogr2ogr -f MVT vector-tiles-nightlight PG:dbname=osm -sql "SELECT name, highway AS type, 1 + floor(random() * 10) AS radius, 1 + floor(random() * 9) AS color, (ST_DumpPoints(wkb_geometry)).geom::geometry(POINT,3857) wkb_geometry FROM toronto_lines WHERE highway IS NOT NULL UNION ALL SELECT name, 'amenity' AS type, 1 + floor(random() * 10) AS radius, 1 + floor(random() * 18) AS color, wkb_geometry FROM toronto_points WHERE other_tags LIKE '%amenity%'" -nlt LINESTRING -nln points -dsco MINZOOM=10 -dsco MAXZOOM=15 -dsco COMPRESS=NO -dsco SIMPLIFICATION=0.0 -dsco SIMPLIFICATION_MAX_ZOOM=0.0 -explodecollections

cp tiles-nightlight.json vector-tiles-nightlight
cp cors_server.py vector-tiles-nightlight
cd vector-tiles-nightlight
./cors_server.py
```
