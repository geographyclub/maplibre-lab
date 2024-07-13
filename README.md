# maplibre-lab
Experiments in web mapping with MapLibre.  

### How I make MVT vector tiles with BASH/GDAL

Nightlights:  
```
#!/bin/bash

cd ~/maplibre-testing
rm -rf vector-tiles-nightlight

ogr2ogr -f MVT vector-tiles-nightlight PG:dbname=osm -sql "SELECT name, highway AS type, 1 + floor(random() * 10) AS radius, 1 + floor(random() * 9) AS color, (ST_DumpPoints(wkb_geometry)).geom::geometry(POINT,3857) wkb_geometry FROM toronto_lines WHERE highway IS NOT NULL UNION ALL SELECT name, 'amenity' AS type, 1 + floor(random() * 10) AS radius, 1 + floor(random() * 18) AS color, wkb_geometry FROM toronto_points WHERE other_tags LIKE '%amenity%'" -nlt LINESTRING -nln points -dsco MINZOOM=10 -dsco MAXZOOM=15 -dsco COMPRESS=NO

cp tiles-nightlight.json vector-tiles-nightlight
cp cors_server.py vector-tiles-nightlight
cd vector-tiles-nightlight
./cors_server.py
```

Blockworld:  
```
#!/bin/bash

cd ~/maplibre-testing
rm -rf vector-tiles-blockworld

ogr2ogr -f MVT -s_srs 'epsg:4326' -t_srs 'epsg:3857' vector-tiles-blockworld ~/maps/srtm/topo15_432.gpkg -sql "SELECT fid, DN, geom FROM topo15_432" -nlt MULTIPOLYGON -nln blockworld -clipsrc -180 -85 180 85 -dsco MINZOOM=2 -dsco MAXZOOM=3 -dsco COMPRESS=NO

cp tiles-blockworld.json vector-tiles-blockworld
cp cors_server.py vector-tiles-blockworld
cd vector-tiles-blockworld
./cors_server.py
```
