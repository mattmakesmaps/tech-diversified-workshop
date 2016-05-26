# tech-diversified-workshop

data.seattle.gov analytics page: https://data.seattle.gov/analytics?start=t-12m&end=-1m

## Datasets of Interest

All datasets freely available from (data.seattle.gov)
Not all datasets will be used, but these are cool.

- [Real Time 911](https://data.seattle.gov/Public-Safety/Seattle-Real-Time-Fire-911-Calls/kzjm-xkqj) Loaded
- [Police Deparment Police Report Incident](https://data.seattle.gov/Public-Safety/Seattle-Police-Department-Police-Report-Incident/7ais-f98f) Loaded
- [Building Permits](https://data.seattle.gov/Permitting/Building-Permits-Current/mags-97de) Loaded
- [Public Outreach and Engagement Events](https://data.seattle.gov/Community/Public-Outreach-and-Engagement-Events/8pec-7ugc) Loaded
- [Building Code Violations](https://data.seattle.gov/Community/Code-Violation-Cases/dk8m-pdjf) Loaded
- [Neighborhoods](https://data.seattle.gov/dataset/Neighborhoods/2mbt-aqqx) Loaded
- [Seattle Communities Online Inventory](https://data.seattle.gov/Community/Seattle-Communities-Online-Inventory/5ytf-wban)
- [Seattle Cultural Space Inventory](https://data.seattle.gov/Community/Seattle-Cultural-Space-Inventory/vsxr-aydq) Loaded
- [My Neighborhood Map (POIS)](https://data.seattle.gov/Community/My-Neighborhood-Map/82su-5fxf) Loaded
- [Landmarks Dataset](https://data.seattle.gov/Community/Landmarks/7nqc-eijt) Loaded
- [coffee part1](https://data.seattle.gov/Finance/coffee-part-1/5zuz-gefe) <-- File under `weird`
- [Viewpoints](https://data.seattle.gov/Community/Viewpoints/5bsb-yqra) Loaded
- [Heritage Trees](https://data.seattle.gov/Community/Heritage-Trees/5979-eagq) Loaded

## Workshop Flow

### Introduction of `data.seattle.gov`

- Show metrics page to display number of views/datasets, etc.
- Show examples of data in tabular format from various categories.
- Show examples of data in map format from various categories.
- Explain how the data need to have a "location" column in order to be mapped (not just address or lat+lon).
- Show how datasets can be malformed (e.g "Viewpoints" dataset has lat, lon and address columns, but no "location" column == can't be mapped).
- Show how you can get metadata using `About This Dataset` feature, that may help when the data are malformed.
  - e.g. metadata for Landmarks dataset shows a link to nifty Dept. of Neighborhoods web map.

### Introduction of CartoDB

- Overview of what it is (webmap + repository for data).
- Show examples of maps (not from Seattle Data Gov)
- Show examples of SQL Queries + Maps to create information from data (not from Seattle Data Gov)
- Highlight the fact that asking "Place Based Questions" can be thought of as "GIS".

### Let's Make a `Neighborhood Resources` Map

- The idea would be to tie supporting datasets in CartoDB into the `Neighborhoods` spatial dataset referenced above.

#### Load the data

- Select a handful of the above datasets for use in the `Neighborhood Resources` map.
- These data will be in either SHP format (`Neighborhood Boundaries`) or CSV (all other datasets).
- Pre-load all but one in CSV format.
- Demonstrate loading of dataset into CartoDB.

#### Demonstrate SELECTS

- From a single dataset, show an example of a `SELECT`.
  - Perhaps using Code Violations dataset, show usage of `LIMIT` 10, `DISTINCT` case type, `WHERE` case date > Jan 01, 2016.

#### Perform Joins

- Using the neighborhood dataset, perform a spatial join on another dataset that already has a geometry column (e.g. Landmarks)
  To get counts of Landmarks per neighborhood.
- Use a tabular join on `neigbhorhood` field of `Seattle Communities Online Inventory` dataset to show popup for each neigbhorhood geom
  displaying the list of online resources contained within that tabular dataset.

#### Spatial Operations

- Show examples of a few basic spatial operations:
  - Features from dataset `A` within a buffer `n` from features in dataset `B`.

## Resources for participants in Tech Diversified Repo

- Links to `data.seattle.gov` datasets of interest, grouped by category.
- Annotated SQL statements used in CartoDB workshop.
