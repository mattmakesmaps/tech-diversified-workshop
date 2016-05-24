# Carto DB Notes

- **Username:** td-data-workshop
- **Password:** td-data-workshop

## Adding Datasets

**Dataset with Separate Lat/Lon Columns**

CartoDB can consume links to `data.seattle.gov` **CSV** exports and **ZIP'd SHPs** directly.
For example, for the [My Neigbhorhood Map](https://data.seattle.gov/Community/My-Neighborhood-Map/82su-5fxf)
dataset, clicking on the `Export` button will open up a drop-down of available formats. The `CSV` link:
 (https://data.seattle.gov/api/views/82su-5fxf/rows.csv?accessType=DOWNLOAD) can be directly pasted into
CartoDB. CartoDB will pickup the separate Lat/Lon columns and automatically georeference.

**Dataset with Only a Shape or Location Column**

For datasets stored with a "location" column on `data.seattle.gov` but no separate `lat` `lon` columns,
You can use this workflow to import the dataset into CartoDB, but will need to manually create split `latitude` and `longitude`
columns. An example of this type of dataset is the [Landmarks](https://data.seattle.gov/Community/Landmarks/7nqc-eijt)
dataset. See the [CartoDB FAQ](https://docs.carto.pageospatial.com/faqs.html) For SQL snippets on creating split columns.

1. Add two new string type columns, `latitude` and `longitude`
2. UPDATE these columns with values from the `data.seattle.gov` `shape` or `location` column.

```
-- MK NOTE: `-?\d+[\.]?\d+` == "An optional '-' symbol, followed by 
-- one or more digits, followed by an optional '.' symbol
-- followed by one more more digits.
UPDATE landmarks
SET latitude = (regexp_matches(shape, '(-?\d+[\.]?\d+)?,\s(-?\d+[\.]?\d+)'))[1], longitude = (regexp_matches(shape, '(-?\d+[\.]?\d+)?,\s(-?\d+[\.]?\d+)'))[2]
```

3. Clicking on the `GEO` button next to the `the_geom` column allows
you to assign lat/lon columns to be used to construct the geometry. Select
out the columns you just populated.

## Symbolizing Data

- Use the wizard to create a categorical style map.
- One example of this is to symbolize the `seattle_cultural_space_inventory` against the `dominant_discipline` field.

## Spatial Analysis Example

An example creating a map using the `neighborhoods` and `seattle_cultural_inventory_dataset`.

Create two fields: A raw count of feature intersecting a neighborhood, and a density calculation.

Flipping between the `raw count` map and the `density` depicts the data in different ways. Compare small neighborhoods to large neighborhoods for example.

```
-- Update field `cultural_inventory_cnt` with
-- number of `seattle_cultural_space_inventory` features
-- Insersecting It.
UPDATE neighborhoods
SET cultural_inventory_cnt = (
  SELECT COUNT(seattle_cultural_space_inventory.cartodb_id)
  FROM seattle_cultural_space_inventory
  WHERE ST_Intersects(
    neighborhoods.the_geom,
    seattle_cultural_space_inventory.the_geom
  )
)

-- Landmark Density = Count of Landmarks / Area
UPDATE neighborhoods
SET cultural_inventory_density = (cultural_inventory_cnt/area)*1000
```

Another example showing new construction permits by neighborhood.

- Create a new map selecting the `neighborhoods` and `building_permits_current` layers.
- From the data view filter the `building_permits_current` layer to constrain:
  - `action_type` == `new`
  - `category` attribute to include only `SINGLE FAMILY/DUPLEX` and `MULTIFAMILY`
- Note how the `sql` pane of the `building_permit_current` table now has custom sql matching our filters

```sql
SELECT * FROM building_permits_current
WHERE action_type IN ('NEW') 
  AND action_type IS NOT NULL
AND category IN ('SINGLE FAMILY / DUPLEX','MULTIFAMILY') 
  AND category IS NOT NULL
```

- Add two new columns to `neighborhoods` dataset `bld_permit_cnt` and `bld_permit_density`.

Calculate
```sql
UPDATE neighborhoods
SET bld_permit_cnt = (
  SELECT COUNT(building_permits_current.cartodb_id)
  FROM building_permits_current
  WHERE ST_Intersects(
    neighborhoods.the_geom,
    building_permits_current.the_geom
  ) 
  -- APPLY Building Permit Filters
  AND action_type IN ('NEW') AND action_type IS NOT NULL
  AND category IN ('SINGLE FAMILY / DUPLEX','MULTIFAMILY') AND category IS NOT NULL
)

-- Density = Count / Area
UPDATE neighborhoods
SET bld_permit_density = (bld_permit_cnt/area)*1000
```

## Bus Stops Related to POIs

**Question:** What bus stops have the largest number of "places of interest"
  (ammenities such as shops, resturants, schools, places of worship etc).
  within a mile?

### Sourcing Data

- `data.seattle.gov` actually doesn't contain datasets for either places
  of interest or bus stops/bus routes.
- Point of Interest and Bus Stop data available from [Mapzen
  Metro Extracts](https://mapzen.com/data/metro-extracts/)
- `OSM2PGSQL SHP` format contains more easy to access points
  of interest data (businesses, schools, places of worship, etc).
- `IMPOSM SHP` has easier to access bus stop information.

- The bounding box (extent) for the Seattle area is really large, so
  I've subset the data here: (https://github.com/mattmakesmaps/tech-diversified-workshop)
- Import `imposm_transportation_stuff.zip`, `osm_points.zip`, `osm_lines.zip`, and `osm_polys.zip` by copying and pasting the following links into
 - https://github.com/mattmakesmaps/tech-diversified-workshop/raw/master/imposm_transportation_stuff.zip
 - https://github.com/mattmakesmaps/tech-diversified-workshop/raw/master/osm_points.zip
 - https://github.com/mattmakesmaps/tech-diversified-workshop/raw/master/osm_lines.zip
 - https://github.com/mattmakesmaps/tech-diversified-workshop/raw/master/osm_polys.zip

### Filtering Data

Looking at the `seattle_imposm_transport_points` dataset, we can see
that it contains many different types of features in the `type`
attribute. Since our question only revolves around bus stops, let's
filter this dataset to include only bus stops.

- In the data view for thsi dataset, click on the `type` attribute and
  select `filter by this column`.
- Select `clear selection` to null out all distinct type values.
- select `bus_stop` to filter this dataset to bus stops exclusively.

The `osm_points` dataset contains too many rows for cartodb to provide
a filter list, so we needto use SQL to filter the data.

To subset the `osm_points` dataset to any features that have a populated
`shop` or `amenity` column, use the following SQL

```sql
SELECT * FROM osm_points
WHERE shop IS NOT NULL
OR amenity IS NOT NULL
```

### Perform Analysis

Create a new map in CartoDB, and select the `seattle_imposm_transport_points`
and `osm_points` datasets to be included.

Our analysis will revolve around getting a count of ammentiy/shop points
in the `osm_points` dataset that are within a 1-mile radius of a bus stop.
In order to store the results of our analysis we'll need to add a new
column to `seattle_imposm_trasnport_points` called `poi_cnt`.

- Select the `seattle_imposm_transport_points` dataset and click the
`add column` button. Name the column `poi_cnt` and give it a data type
of number.

--TODO:
--find out what unit of measure the buffer is operating on.
--"create new datasets" to filter out osm points to just amenity and shop values.
--

SELECT id, COUNT(*)
FROM seattle_imposm_transport_points AS stops
INNER JOIN osm_pints AS pois
ON ST_Intersects(pois.the_geom, ST_Buffer(stops.the_geom, 0.0005)) = TRUE
WHERE stops.type = 'bus_stop'
GROUP BY stops.id

-- SLOW
SELECT id, COUNT(*)
FROM seattle_imposm_transport_points_bus_stops AS stops
INNER JOIN osm_points_amenities_shops AS pois
ON ST_Intersects(
  pois.the_geom::geography,
  ST_Buffer(stops.the_geom::geography, 100)) = TRUE
GROUP BY stops.id

-- FAST
SELECT stops.id, COUNT(*)
FROM osm_points_amenities_shops AS pois
INNER JOIN seattle_imposm_transport_points_bus_stops AS stops
ON ST_DWITHIN(
  pois.the_geom::geography,
  stops.the_geom::geography,
  500)
GROUP BY stops.id

-- UPDATE
UPDATE seattle_imposm_transport_points_bus_stops
SET poi_count = poi_count.count
FROM (
  SELECT stops.id, COUNT(*)
  FROM osm_points_amenities_shops AS pois
  INNER JOIN seattle_imposm_transport_points_bus_stops AS stops
  ON ST_DWITHIN(
    pois.the_geom::geography,
    stops.the_geom::geography,
    500)
  GROUP BY stops.id
) AS poi_count
WHERE seattle_imposm_transport_points_bus_stops.id = poi_count.id

Things to do after updating the data:
- Add infowindows
- COnvert POIs to cluster or heatmap.
- Add classified ramp to stops dataset based on POI count.