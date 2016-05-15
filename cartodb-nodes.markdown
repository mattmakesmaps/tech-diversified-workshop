# Carto DB Notes

- **Username:** td-data-workshop
- **Password:** td-data-workshop

## Adding Datasets

CartoDB can consume links to `data.seattle.gov` **CSV** exports and **ZIP'd SHPs** directly.
For example, for the [My Neigbhorhood Map](https://data.seattle.gov/Community/My-Neighborhood-Map/82su-5fxf)
dataset, clicking on the `Export` button will open up a drop-down of available formats. The `CSV` link:
 (https://data.seattle.gov/api/views/82su-5fxf/rows.csv?accessType=DOWNLOAD) can be directly pasted into
CartoDB. CartoDB will pickup the separate Lat/Lon columns and automatically georeference.

For datasets stored with a "location" column on `data.seattle.gov` but no separate `lat` `lon` columns,
You can use this workflow to import the rows into CartoDB, but will need to manually create split `lat` and `lon`
columns. An example of this type of dataset is the [Landmarks](https://data.seattle.gov/Community/Landmarks/7nqc-eijt)
dataset. See the [CartoDB FAQ](https://docs.carto.pageospatial.com/faqs.html) For SQL snippets on creating split columns.

Untested regex for coordinate split

`/-?\d+[\.]?\d+/g`
Try using the `regex_matches` function in See: http://www.postgresql.org/docs/9.1/static/functions-string.html
