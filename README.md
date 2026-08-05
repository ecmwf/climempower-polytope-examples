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

### Constraints endpoint

```
GET https://polytope-dss.ecmwf.int/api/v1/dataset/<dataset-id>/constraints/
```

Returns the official CDS constraint JSON for a dataset, listing all valid parameter combinations.

### Variable metadata endpoint

```
GET https://polytope-dss.ecmwf.int/api/v1/dataset/<dataset-id>/variable_metadata/
```

Returns metadata for all variables in a dataset, including:
- `type` — whether the variable is `"Reanalysis"`, `"Climate Projection"`, `"Forecast"`, etc.
- `description` — official CDS description of the variable
- `unit` — physical unit (where available)

This metadata is also injected into CovJSON responses under the `parameters` section.

### Units endpoint

```
GET https://polytope-dss.ecmwf.int/api/v1/dataset/<dataset-id>/units/
```

Returns a mapping of variable names to their units for the given dataset.

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
| **IPCC AR6 Atlas Projections** | `projections-climate-atlas` | ✓ | ✓ |

**Bounding box** — `"type": "boundingbox"`, coordinates are `[[lat_min, lon_min], [lat_max, lon_max]]`

**Polygon** — `"type": "polygon"`, coordinates are a closed ring of `[lat, lon]` pairs (first and last point must be identical)

---

## Default behaviour

### Dynamic defaults

When you omit a field, the API will automatically infer it from the constraints JSON based on the fields you *did* provide. If only one valid option exists for a missing field (given your other parameters), it is filled in automatically. If multiple options are possible, the API selects based on preferred values.

### Preferred defaults (RCP 8.5)

All climate-projection datasets default to **RCP 8.5** when the experiment field is omitted. This applies to: ECDE, heat-and-cold-spells, hydrology, agroclimatic, tourism, CORDEX, ECV-CMIP5, and projections-climate-atlas.

### EFAS Forecast defaults

- **Date**: defaults to 31 days ago (most recent reliably available forecast)
- **Lead time**: defaults to `["24", "48", "72", "120", "240"]` (5 forecast steps)
- Time labels are included in the CovJSON response derived from the forecast valid time

---

## Request fields per dataset

Fields without a default value are **mandatory**. Fields with a default value are optional — the default is shown in parentheses.

### `sis-heat-and-cold-spells`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["heat_wave_days"]` |
| `experiment` | dynamic | `["rcp8_5"]` |
| `definition` | dynamic | `"country_related"` |
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
| `experiment` | dynamic | `["rcp_8_5"]` |
| `gcm` | dynamic | `"mpi_esm_lr"` |
| `rcm` | dynamic | `"csc_remo2009"` |
| `hydrological_model` | dynamic | `["e_hypegrid"]` |
| `ensemble_member` | dynamic | `["r1i1p1"]` |
| `product_type` | no | `"climate_impact_indicators"` |
| `variable_type` | no | `"absolute_values"` |

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
| `temporal_aggregation` | no | `["monthly"]` (default) |
| `origin` | dynamic | `"projections"` |
| `gcm` | dynamic | `["ec_earth"]` |
| `rcm` | dynamic | `["rca4"]` |
| `experiment` | dynamic | `["rcp8_5"]` |
| `ensemble_member` | dynamic | `["r12i1p1"]` |
| `spatial_aggregation` | no | `"gridded"` |
| `version` | no | `"v2_0"` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `aridity_actual` | Monthly mean ratio of actual evapotranspiration to precipitation over 30 years |
| `consecutive_dry_days` | Longest period of consecutive days with daily precipitation below 1 mm |
| `cooling_degree_days` | Cumulative sum of daily degrees above 22°C |
| `daily_maximum_temperature` | Maximum value of daily maximum temperature of a period |
| `daily_minimum_temperature` | Minimum value of daily minimum temperature of a period |
| `days_with_high_fire_danger` | Days with Fire Weather Index > 30 (EFFIS classification) |
| `duration_of_meteorological_droughts` | Months with anomalously low precipitation (SPI-3 based) |
| `extreme_precipitation_total` | Total precipitation exceeding the 99th percentile of reference period |
| `extreme_sea_level` | Total water level for 100-year return period |
| `extreme_wind_speed_days` | Days with 10m wind speed above 98th percentile threshold |
| `fire_weather_index` | Canadian FWI — composite fire danger indicator |
| `flood_recurrence` | 50-year flood recurrence (ensemble mean) |
| `frequency_of_extreme_precipitation` | Days with precipitation above 95th percentile threshold |
| `frost_days` | Days with daily minimum temperature below 0°C |
| `growing_degree_days` | Cumulative sum of daily degrees above 5°C |
| `heating_degree_days` | Cumulative sum of daily degrees below 15.5°C |
| `heatwave_days` | Climatological hot days (≥3 consecutive days exceeding 99th percentile of daily max temp) |
| `high_utci_days` | Days with Universal Thermal Climate Index above 32°C |
| `hot_days` | Days with daily maximum temperature above 30°C |
| `magnitude_of_meteorological_droughts` | Cumulative severity of drought events (SPI-3 based) |
| `maximum_consecutive_five_day_precipitation` | Maximum 5-day precipitation total |
| `mean_river_discharge` | Mean annual daily river discharge over 30 years |
| `mean_soil_moisture` | Mean soil moisture in root zone as fraction of field capacity |
| `mean_temperature` | Air temperature at 2m above the surface |
| `mean_wind_speed` | Mean 10m wind speed |
| `relative_sea_level_rise` | Annual mean sea level relative to 1986-2005 |
| `snowfall_amount` | Cumulative snowfall during winter sports season (Nov-Apr) |
| `total_precipitation` | Accumulated liquid and frozen precipitation |
| `tropical_nights` | Days with minimum temperature above 20°C |
| `warmest_three_day_period` | Highest daily mean temperature averaged over 3-day window |

---

### `sis-agroclimatic-indicators`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["growing_season_length"]` |
| `period` | yes | `["201101_204012"]` |
| `temporal_aggregation` | yes | `"annual"` |
| `origin` | dynamic | `"noresm1_m_model"` |
| `experiment` | dynamic | `"rcp8_5"` |
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
| `experiment` | dynamic | `"rcp8_5"` |
| `product_type` | no | `"single_model"` |
| `gcm_model` | no | `["ec_earth"]` |
| `version` | no | `"v1_0"` |

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `daily_fire_weather_index` | Daily FWI values — higher values indicate more favorable wildfire conditions |
| `number_of_days_with_high_fire_danger` | Days per year with FWI > 30 (EFFIS classification) |
| `number_of_days_with_moderate_fire_danger` | Days per year with FWI > 15 (EFFIS classification) |
| `number_of_days_with_very_high_fire_danger` | Days per year with FWI > 45 (EFFIS classification) |
| `seasonal_fire_weather_index` | Mean FWI over European fire season (Jun–Sep) |

---

### `projections-cordex-domains-single-levels`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["mean_precipitation_flux"]` |
| `start_year` | yes | `["1951"]` |
| `end_year` | yes | `["1955"]` |
| `temporal_resolution` | yes | `"daily_mean"` |
| `experiment` | dynamic | `"rcp_8_5"` |
| `gcm_model` | dynamic | `"ichec_ec_earth"` |
| `rcm_model` | dynamic | `"knmi_racmo22e"` |
| `ensemble_member` | dynamic | `"r1i1p1"` |
| `domain` | no | `"europe"` |
| `horizontal_resolution` | no | `"0_11_degree_x_0_11_degree"` |
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
| `experiment` | dynamic | `"rcp_8_5"` |
| `model` | no | `"access1_0"` |

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

**Notes:**
- `"all"` is the only accepted variable value. It returns windstorm footprint data (maximum 10m wind gust per grid point during a 72-hour storm passage).
- The default date (1990-01-25) corresponds to Storm Daria. You can request any storm date available in the constraints — the dataset covers winter storms from 1979–2021.
- The data is on a high-resolution 1km grid (~0.017°). A 1°×1° bounding box returns ~3600 data points.

---

### `efas-forecast`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `["river_discharge_in_the_last_6_hours"]` |
| `model_levels` | no | `"surface_level"` |
| `year` | no | auto (31 days ago) |
| `month` | no | auto (31 days ago) |
| `day` | no | auto (31 days ago) |
| `time` | no | `["12:00"]` |
| `leadtime_hour` | no | `["24", "48", "72", "120", "240"]` |
| `data_format` | no | `"netcdf"` |
| `download_format` | no | `"zip"` |
| `product_type` | no | `["control_forecast"]` |
| `originating_centre` | no | `"ecmwf"` |
| `system_version` | no | `["operational"]` |

**Notes:**
- Date defaults to 31 days ago to ensure data availability.
- Lead time defaults to 5 forecast steps (24h, 48h, 72h, 120h, 240h). The CovJSON response includes time labels for each step.

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

---

### `projections-climate-atlas`

| Field | Mandatory | Example / default |
|-------|:---------:|-------------------|
| `variable` | yes | `"monthly_mean_of_daily_mean_temperature"` (scalar string) |
| `origin` | no | `"cordex"` (default) |
| `experiment` | no | `"rcp_8_5"` (default) |
| `domain` | no | `"europe"` (default) |
| `period` | no | `"2006-2100"` (default) |

**Notes:**
- All fields are **scalar strings** (not lists) — the CDS API requires this for this dataset.
- Default configuration uses CORDEX Europe downscaled projections under RCP 8.5 (2006–2100).
- Downloads are ~447 MB for CORDEX Europe; extraction takes 3–8 minutes total.
- Monthly variables return 1140 coverages (95 years × 12 months); annual variables return 95 coverages.
- The response includes ensemble mean across all available models (48 members for CORDEX).

**Available `variable` values:**

| Value | Description |
|-------|-------------|
| `monthly_mean_of_daily_mean_temperature` | Monthly mean of daily mean near-surface (2m) air temperature |
| `monthly_mean_of_daily_minimum_temperature` | Monthly mean of daily minimum near-surface air temperature |
| `monthly_mean_of_daily_maximum_temperature` | Monthly mean of daily maximum near-surface air temperature |
| `monthly_minimum_of_daily_minimum_temperature` | Lowest daily minimum temperature for each month |
| `monthly_maximum_of_daily_maximum_temperature` | Highest daily maximum temperature for each month |
| `monthly_count_of_days_with_maximum_temperature_above_35_c` | Days with daily max temperature above 35°C per month |
| `bias_adjusted_monthly_count_of_days_with_maximum_temperature_above_35_c` | Bias-adjusted count of days with max temp above 35°C |
| `monthly_count_of_days_with_maximum_temperature_above_40_c` | Days with daily max temperature above 40°C per month |
| `bias_adjusted_monthly_count_of_days_with_maximum_temperature_above_40_c` | Bias-adjusted count of days with max temp above 40°C |
| `monthly_count_of_frost_days` | Days with daily minimum temperature below 0°C |
| `annual_heating_degree_days` | Heating degree-days: cumulative degrees below heating threshold |
| `annual_cooling_degree_days` | Cooling degree-days: cumulative degrees above cooling threshold |
| `monthly_mean_of_daily_accumulated_precipitation` | Monthly mean of daily accumulated precipitation |
| `monthly_mean_of_daily_accumulated_snowfall_precipitation` | Monthly mean of daily accumulated snowfall |
| `monthly_maximum_of_1_day_accumulated_precipitation` | Maximum 1-day precipitation for each month (Rx1day) |
| `monthly_maximum_of_5_day_accumulated_precipitation` | Maximum 5-day precipitation for each month (Rx5day) |
| `annual_consecutive_dry_days` | Annual maximum consecutive dry days (CDD) |
| `standardized_precipitation_index_for_6_months_cumulation_period` | Standardized Precipitation Index over 6-month period (SPI-6) |
| `monthly_mean_of_daily_mean_wind_speed` | Monthly mean of daily mean 10m wind speed |
| `monthly_mean_of_sea_surface_temperature` | Monthly mean sea surface temperature (oceanic, CMIP5/CMIP6) |
| `monthly_mean_of_acidity_of_seawater` | Monthly mean seawater acidity (pH) |
| `monthly_mean_of_sea_ice_area_percentage` | Monthly mean sea-ice area percentage |

**Available `origin` values:** `cordex`, `cmip5`, `cmip6`

**Available `experiment` values:** `rcp_2_6`, `rcp_4_5`, `rcp_8_5`, `ssp1_2_6`, `ssp2_4_5`, `ssp3_7_0`, `ssp5_8_5` (SSP experiments only available with `cmip6` origin)

**Available `domain` values:** `europe`, `global` (CORDEX uses continental domains; CMIP5/CMIP6 use `global`)
