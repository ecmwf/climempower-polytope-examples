# climempower-polytope-examples

This repo contains example notebooks for retrieving data from the polytope-DSS service for the ClimEmpower project.

## Datasets

All requests are sent to:

```
POST https://polytope-dss.ecmwf.int/api/v1/dataset/<dataset-id>/extract/
```

The request body always has two top-level keys:

- `request` — dataset-specific fields (variable, period, experiment, etc.)
- `feature` — the spatial shape to extract

### Supported shapes

| Dataset | Dataset ID | Bounding box | Polygon |
|---------|------------|:---:|:---:|
| Heat and Cold Spells | `sis-heat-and-cold-spells` | ✓ | ✓ |
| Hydrology Variables | `sis-hydrology-variables-derived-projections` | ✓ | ✓ |
| ECDE Climate Indicators | `sis-ecde-climate-indicators` | ✓ | ✓ |
| Agroclimatic Indicators | `sis-agroclimatic-indicators` | ✓ | ✓ |
| Tourism and Fire Danger | `sis-tourism-fire-danger-indicators` | ✓ | ✓ |
| CORDEX Domains | `projections-cordex-domains-single-levels` | ✓ | ✓ |
| ECV CMIP5 Bias-corrected | `sis-ecv-cmip5-bias-corrected` | ✓ | ✓ |
| European Wind Storm Indicators | `sis-european-wind-storm-indicators` | ✓ | ✓ |
| EFAS Forecast | `efas-forecast` | ✓ | ✓ |
| EFAS Seasonal | `efas-seasonal` | ✓ | ✓ |

**Bounding box** — `"type": "boundingbox"`, coordinates are `[[lat_min, lon_min], [lat_max, lon_max]]`

**Polygon** — `"type": "polygon"`, coordinates are a closed ring of `[lat, lon]` pairs (first and last point must be identical)

---

## Request fields per dataset

Fields without a default value are **mandatory**. Fields with a default value are optional — the default is shown in parentheses.

### `sis-heat-and-cold-spells`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["heat_wave_days"]` |
| `definition` | no | `"country_related"` |
| `experiment` | no | `["rcp8_5"]` |
| `ensemble_statistic` | no | `["ensemble_members_average"]` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `cold_spell_days` | Number of cold days in a year using specific definitions |
| `heat_wave_days` | Number of hot days in a year using specific definitions |

---

### `sis-hydrology-variables-derived-projections`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["river_discharge"]` |
| `period` | yes | `["2041_2070"]` |
| `time_aggregation` | yes | `"annual_mean"` |
| `product_type` | no | `"climate_impact_indicators"` |
| `variable_type` | no | `"absolute_values"` |
| `experiment` | no | `["historical"]` |
| `hydrological_model` | no | `["e_hypegrid"]` |
| `rcm` | no | `"csc_remo2009"` |
| `gcm` | no | `"mpi_esm_lr"` |
| `ensemble_member` | no | `["r1i1p1"]` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `aridity_actual` | Monthly mean ratio of actual evapotranspiration to precipitation |
| `aridity_potential` | Monthly mean ratio of potential evapotranspiration to precipitation |
| `flood_recurrence_2_years_return_period` | Return value of annual maximum river discharge at 2-year return period |
| `flood_recurrence_5_years_return_period` | Return value of annual maximum river discharge at 5-year return period |
| `flood_recurrence_10_years_return_period` | Return value of annual maximum river discharge at 10-year return period |
| `flood_recurrence_50_years_return_period` | Return value of annual maximum river discharge at 50-year return period |
| `maximum_river_discharge` | Mean annual daily maximum discharge over 30 years |
| `mean_runoff` | Monthly or annual mean values of daily runoff over 30 years |
| `mean_soil_moisture` | Monthly or annual mean soil moisture as fraction of field capacity |
| `minimum_river_discharge` | Mean annual daily minimum discharge over 30 years |
| `river_discharge` | Volume rate of water flow (ECV at daily resolution; CII as mean over 30 years) |
| `total_nitrogen_concentration_in_catchments` | |
| `total_nitrogen_concentration_in_local_streams` | |
| `total_nitrogen_load_in_catchments` | |
| `total_phosphorus_concentration_in_catchments` | |
| `total_phosphorus_concentration_in_local_streams` | |
| `total_phosphorus_load_in_catchments` | |
| `water_temperature_in_catchments` | |
| `water_temperature_in_local_streams` | |
| `wetness_actual` | Monthly mean precipitation minus actual evapotranspiration |
| `wetness_potential` | Monthly mean precipitation minus potential evapotranspiration |

---

### `sis-ecde-climate-indicators`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["total_precipitation"]` |
| `temporal_aggregation` | yes | `["yearly"]` |
| `origin` | no | `"projections"` |
| `gcm` | no | `["ec_earth"]` |
| `rcm` | no | `["racmo22e"]` |
| `experiment` | no | `["rcp4_5"]` |
| `ensemble_member` | no | `["r1i1p1"]` |
| `spatial_aggregation` | no | `"gridded"` |

**Available `variable` values:**

| Value |
|-------|
| `aridity_actual` |
| `consecutive_dry_days` |
| `cooling_degree_days` |
| `daily_maximum_temperature` |
| `daily_minimum_temperature` |
| `days_with_high_fire_danger` |
| `duration_of_meteorological_droughts` |
| `extreme_precipitation_total` |
| `extreme_sea_level` |
| `extreme_wind_speed_days` |
| `fire_weather_index` |
| `flood_recurrence` |
| `frequency_of_extreme_precipitation` |
| `frost_days` |
| `growing_degree_days` |
| `heating_degree_days` |
| `heatwave_days` |
| `high_utci_days` |
| `hot_days` |
| `magnitude_of_meteorological_droughts` |
| `maximum_consecutive_five_day_precipitation` |
| `mean_river_discharge` |
| `mean_soil_moisture` |
| `mean_temperature` |
| `mean_wind_speed` |
| `relative_sea_level_rise` |
| `snowfall_amount` |
| `total_precipitation` |
| `tropical_nights` |
| `warmest_three_day_period` |

---

### `sis-agroclimatic-indicators`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["growing_season_length"]` |
| `period` | yes | `["201101_204012"]` |
| `temporal_aggregation` | yes | `"annual"` |
| `origin` | no | `"noresm1_m_model"` |
| `experiment` | no | `"rcp4_5"` |
| `version` | no | `["1_0"]` |

**Available `variable` values:**

| Value |
|-------|
| `biologically_effective_degree_days` |
| `cold_spell_duration_index` |
| `frost_days` |
| `growing_season_length` |
| `heavy_precipitation_days` |
| `ice_days` |
| `maximum_number_of_consecutive_dry_days` |
| `maximum_number_of_consecutive_frost_days` |
| `maximum_number_of_consecutive_summer_days` |
| `maximum_number_of_consecutive_wet_days` |
| `maximum_of_daily_maximum_temperature` |
| `maximum_of_daily_minimum_temperature` |
| `mean_of_daily_maximum_temperature` |
| `mean_of_daily_mean_temperature` |
| `mean_of_daily_minimum_temperature` |
| `mean_of_diurnal_temperature_range` |
| `minimum_of_daily_maximum_temperature` |
| `minimum_of_daily_minimum_temperature` |
| `precipitation_sum` |
| `simple_daily_intensity_index` |
| `summer_days` |
| `tropical_nights` |
| `very_heavy_precipitation_days` |
| `warm_and_wet_days` |
| `warm_spell_duration_index` |
| `wet_days` |

---

### `sis-tourism-fire-danger-indicators`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["seasonal_fire_weather_index"]` |
| `period` | yes | `["2021_2025"]` |
| `time_aggregation` | yes | `"seasonal_indicators"` |
| `product_type` | no | `"single_model"` |
| `gcm_model` | no | `["ec_earth"]` |
| `experiment` | no | `"historical"` |
| `version` | no | `"v1_0"` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `daily_fire_weather_index` | Daily FWI values |
| `number_of_days_with_high_fire_danger` | Days per year with FWI > 30 |
| `number_of_days_with_moderate_fire_danger` | Days per year with FWI > 15 |
| `number_of_days_with_very_high_fire_danger` | Days per year with FWI > 45 |
| `seasonal_fire_weather_index` | Mean FWI over European fire season (Jun–Sep) |

---

### `projections-cordex-domains-single-levels`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["mean_precipitation_flux"]` |
| `start_year` | yes | `["1951"]` |
| `end_year` | yes | `["1955"]` |
| `temporal_resolution` | yes | `"daily_mean"` |
| `domain` | no | `"europe"` |
| `experiment` | no | `"historical"` |
| `horizontal_resolution` | no | `"0_11_degree_x_0_11_degree"` |
| `gcm_model` | no | `"ichec_ec_earth"` |
| `rcm_model` | no | `"knmi_racmo22e"` |
| `ensemble_member` | no | `"r1i1p1"` |
| `format` | no | `"zip"` |

**Available `variable` values:**

| Value |
|-------|
| `10m_wind_speed` |
| `10m_u_component_of_the_wind` |
| `10m_v_component_of_the_wind` |
| `200hpa_temperature` |
| `200hpa_u_component_of_the_wind` |
| `200hpa_v_component_of_the_wind` |
| `2m_air_temperature` |
| `2m_relative_humidity` |
| `2m_surface_specific_humidity` |
| `500hpa_geopotential_height` |
| `850hpa_u_component_of_the_wind` |
| `850hpa_v_component_of_the_wind` |
| `evaporation` |
| `land_area_fraction` |
| `maximum_2m_temperature_in_the_last_24_hours` |
| `mean_precipitation_flux` |
| `mean_sea_level_pressure` |
| `minimum_2m_temperature_in_the_last_24_hours` |
| `orography` |
| `surface_pressure` |
| `surface_solar_radiation_downwards` |
| `surface_thermal_radiation_downward` |
| `surface_upwelling_shortwave_radiation` |
| `total_cloud_cover` |
| `total_run_off_flux` |

---

### `sis-ecv-cmip5-bias-corrected`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["mean_2m_temperature"]` |
| `period` | yes | `["20060101_20301231"]` |
| `model` | no | `"access1_0"` |
| `experiment` | no | `"rcp_8_5"` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `maximum_2m_temperature` | Daily maximum air temperature at 2m |
| `mean_2m_temperature` | Daily mean air temperature at 2m |
| `minimum_2m_temperature` | Daily minimum air temperature at 2m |
| `precipitation_flux` | Daily precipitation flux |

---

### `sis-european-wind-storm-indicators`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `"all"` (plain string, not a list) |
| `product` | no | `["windstorm_footprints"]` |
| `year` | no | `["1990"]` |
| `month` | no | `["01"]` |
| `day` | no | `["25"]` |
| `spatial_aggregation` | no | `["other"]` |

**Available `variable` values:**

`"all"` is the only accepted value. It returns windstorm footprint data (maximum 10m wind gust per grid point during a 72-hour storm passage). Individual variables are not selectable separately.

---

### `efas-forecast`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["river_discharge_in_the_last_6_hours"]` |
| `model_levels` | no | `"surface_level"` |
| `year` | no | `["2020"]` |
| `month` | no | `["10"]` |
| `day` | no | `["14"]` |
| `time` | no | `["12:00"]` |
| `leadtime_hour` | no | `["0"]` |
| `data_format` | no | `"netcdf"` |
| `download_format` | no | `"zip"` |
| `product_type` | no | `["control_forecast"]` |
| `originating_centre` | no | `"ecmwf"` |
| `system_version` | no | `["operational"]` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `river_discharge_in_the_last_6_hours` | 6-hourly average river discharge |
| `river_discharge_in_the_last_24_hours` | 24-hourly average river discharge |
| `runoff_water_equivalent` | Surface and subsurface runoff over 6 hours |
| `snow_depth_water_equivalent` | Instantaneous snow water equivalent |
| `soil_wetness_index` | Root zone soil wetness index (0–1) |
| `volumetric_soil_moisture` | Soil moisture in m³/m³ |
| `elevation_v2_0` | Static elevation grid (model version 2.0) |
| `elevation_v3_0` | Static elevation grid (model version 3.0) |
| `elevation_v3_5` | Static elevation grid (model version 3.5) |
| `elevation_v4_0` | Static elevation grid (model version 4.0) |
| `elevation_v5_0` | Static elevation grid (model version 5.0) |
| `field_capacity_v2_0` | Soil field capacity (model version 2.0) |
| `field_capacity_v3_0` | Soil field capacity (model version 3.0) |
| `field_capacity_v3_5` | Soil field capacity (model version 3.5) |
| `field_capacity_v4_0` | Soil field capacity (model version 4.0) |
| `field_capacity_v5_0` | Soil field capacity (model version 5.0) |
| `soil_depth_v2_0` | Soil depth (model version 2.0) |
| `soil_depth_v3_0` | Soil depth (model version 3.0) |
| `soil_depth_v3_5` | Soil depth (model version 3.5) |
| `soil_depth_v4_0` | Soil depth (model version 4.0) |
| `soil_depth_v5_0` | Soil depth (model version 5.0) |
| `upstream_area_v2_0` | Upstream catchment area (model version 2.0) |
| `upstream_area_v3_0` | Upstream catchment area (model version 3.0) |
| `upstream_area_v3_5` | Upstream catchment area (model version 3.5) |
| `upstream_area_v4_0` | Upstream catchment area (model version 4.0) |
| `upstream_area_v5_0` | Upstream catchment area (model version 5.0) |
| `wilting_point_v2_0` | Soil wilting point (model version 2.0) |
| `wilting_point_v3_0` | Soil wilting point (model version 3.0) |
| `wilting_point_v3_5` | Soil wilting point (model version 3.5) |
| `wilting_point_v4_0` | Soil wilting point (model version 4.0) |
| `wilting_point_v5_0` | Soil wilting point (model version 5.0) |

---

### `efas-seasonal`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["river_discharge_in_the_last_24_hours"]` |
| `model_levels` | no | `"surface_level"` |
| `year` | no | `["2020"]` |
| `month` | no | `["11"]` |
| `leadtime_hour` | no | `["24"]` |
| `data_format` | no | `"netcdf"` |
| `download_format` | no | `"zip"` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `river_discharge_in_the_last_24_hours` | 24-hourly average river discharge |
| `runoff_water_equivalent` | Surface and subsurface runoff over 24 hours |
| `snow_depth_water_equivalent` | Instantaneous snow water equivalent |
| `soil_wetness_index` | Root zone soil wetness index (0–1) |
| `volumetric_soil_moisture` | Soil moisture in m³/m³ |
| `elevation_v2_0` | Static elevation grid (model version 2.0) |
| `elevation_v3_0` | Static elevation grid (model version 3.0) |
| `elevation_v3_5` | Static elevation grid (model version 3.5) |
| `elevation_v4_0` | Static elevation grid (model version 4.0) |
| `elevation_v5_0` | Static elevation grid (model version 5.0) |
| `field_capacity_v2_0` | Soil field capacity (model version 2.0) |
| `field_capacity_v3_0` | Soil field capacity (model version 3.0) |
| `field_capacity_v3_5` | Soil field capacity (model version 3.5) |
| `field_capacity_v4_0` | Soil field capacity (model version 4.0) |
| `field_capacity_v5_0` | Soil field capacity (model version 5.0) |
| `soil_depth_v2_0` | Soil depth (model version 2.0) |
| `soil_depth_v3_0` | Soil depth (model version 3.0) |
| `soil_depth_v3_5` | Soil depth (model version 3.5) |
| `soil_depth_v4_0` | Soil depth (model version 4.0) |
| `soil_depth_v5_0` | Soil depth (model version 5.0) |
| `upstream_area_v2_0` | Upstream catchment area (model version 2.0) |
| `upstream_area_v3_0` | Upstream catchment area (model version 3.0) |
| `upstream_area_v3_5` | Upstream catchment area (model version 3.5) |
| `upstream_area_v4_0` | Upstream catchment area (model version 4.0) |
| `upstream_area_v5_0` | Upstream catchment area (model version 5.0) |
| `wilting_point_v2_0` | Soil wilting point (model version 2.0) |
| `wilting_point_v3_0` | Soil wilting point (model version 3.0) |
| `wilting_point_v3_5` | Soil wilting point (model version 3.5) |
| `wilting_point_v4_0` | Soil wilting point (model version 4.0) |
| `wilting_point_v5_0` | Soil wilting point (model version 5.0) |