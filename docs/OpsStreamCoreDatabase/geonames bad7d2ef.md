Created automatically when sqlver.spCreateGeonames is executed.

Calling sqlver.spCreateGeonames will create two tables in this schema (geonames.tblUS and geonames.tblUSPostal), and will download data from [geonames.org](https://geonames.org) and populate these tables.

These tables will then contain geolocation information and zip codes.

Whenever sqlver.spCreateGeonames  is executed, these tables will be dropped, recreated, and populated with freshly-downloaded data.