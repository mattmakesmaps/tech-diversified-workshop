# tech-diversified-workshop

data.seattle.gov analytics page: https://data.seattle.gov/analytics?start=t-12m&end=-1m

## Datasets of Interest

All datasets freely available from (data.seattle.gov)

- [Real Time 911](https://data.seattle.gov/Public-Safety/Seattle-Real-Time-Fire-911-Calls/kzjm-xkqj)
- [Police Deparment Police Report Incident](https://data.seattle.gov/Public-Safety/Seattle-Police-Department-Police-Report-Incident/7ais-f98f)
- [Building Permits](https://data.seattle.gov/Permitting/Building-Permits-Current/mags-97de)
- [Public Outreach and Engagement Events](https://data.seattle.gov/Community/Public-Outreach-and-Engagement-Events/8pec-7ugc)
- [Building Code Violations](https://data.seattle.gov/Community/Code-Violation-Cases/dk8m-pdjf)
- [Neighborhoods](https://data.seattle.gov/dataset/Neighborhoods/2mbt-aqqx)
- [Seattle Communities Online Inventory](https://data.seattle.gov/Community/Seattle-Communities-Online-Inventory/5ytf-wban)
- [Seattle Cultural Space Inventory](https://data.seattle.gov/Community/Seattle-Cultural-Space-Inventory/vsxr-aydq)
- [My Neighborhood Map (POIS)](https://data.seattle.gov/Community/My-Neighborhood-Map/82su-5fxf)
- [Landmarks Dataset](https://data.seattle.gov/Community/Landmarks/7nqc-eijt)
- [coffee part1](https://data.seattle.gov/Finance/coffee-part-1/5zuz-gefe) <-- File under `weird`
- [Viewpoints](https://data.seattle.gov/Community/Viewpoints/5bsb-yqra)
- [Heritage Trees](https://data.seattle.gov/Community/Heritage-Trees/5979-eagq)

## Ideas

### Intersting Things About data.seattle.gov

- Show how you can make a map.
- Show how data are malformed (e.g. Landmarks dataset has a location column containing degree symbol -- messes with geocoding).
- Show how you can get metadata using `About This Dataset` feature, (e.g. metadata for Landmarks dataset shows a link to nifty
  Dept. of Neighborhoods web map).

### Neighborhood Resources

- Would be cool to create a CartoDB visualization of "Neighborhood Resources".
- The idea would be to tie everything into the `Neighborhoods` spatial dataset referenced above.
- Some datasets, such as building permits, already have spatial locations.
- Others, such as `Seattle Communities Online Inventory` have a text column `Neighborhood` that could be used to
  perform a tabular join.
