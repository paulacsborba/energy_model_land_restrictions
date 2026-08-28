# Land restrictions and Brazil’s net-zero energy transition

This repository contains the energy-system model, geospatial analysis scripts, scenario definitions, and supporting data developed to investigate how land-use restrictions and land-governance criteria affect Brazil’s transition towards a net-zero energy system.

The study combines a spatially explicit assessment of land availability for sugarcane and soybean production with a sector-coupled energy-system model implemented in Calliope. The model represents interactions between biomass production, biofuels, electricity, hydrogen, synthetic fuels, transport, and industrial energy use.

1. The scenarios assess how conservation priorities, land-tenure conditions, and the preferential use of degraded pastures change:

the amount and spatial distribution of land available for bioenergy;
the allocation of sugarcane and soybean production;
the choice of fuels and conversion technologies;
the decarbonization of transport and industry;
system costs and infrastructure requirements;
land-use conflicts, water-security risks, labour risks, and direct land-use-change emissions.
Repository contents

2. The repository is organised around the following files and compressed directories:

energy_model_land_restrictions/
├── model.yaml
├── model_spores.yaml
├── scenarios.yaml
├── overrides3.yaml
├── data (2).zip
├── techs.zip
├── locations.zip
├── geospatial_code.zip
├── LICENSE
└── README.md
model.yaml: main Calliope model configuration for the least-cost simulations.
model_spores.yaml: model configuration for generating near-optimal alternatives using SPORES.
scenarios.yaml: names and combinations of model overrides used in each scenario.
overrides3.yaml: scenario-specific land availability, costs, demand assumptions, technology restrictions, and SPORES settings.
data (2).zip: energy-service demand and biomass-resource time series used by the model.
techs.zip: definitions of production, conversion, transport, storage, and demand technologies.
locations.zip: spatial nodes, land pools, infrastructure locations, and links between model regions.
geospatial_code.zip: QGIS/Python scripts used to construct land-availability scenarios, calculate zonal areas, downscale crop allocation, and estimate sustainability indicators.

# Model scenarios

Five principal land-use scenarios are evaluated. All scenarios use 2050 technology costs, net-zero energy-service demands, and restrictions on fossil-fuel use. They differ in the land considered available for expanding sugarcane and soybean production.

1. Baseline
scenario_baseline

The Baseline scenario represents the technical land potential after applying the common legal and physical land exclusions adopted in the study. It provides the reference against which the additional land-policy scenarios are compared.

Calliope overrides:

scenario_baseline:
  - costs_2050
  - baseline
  - fossil_banned
  - demand_nz
2. Conservation
scenario_conservation

The Conservation scenario excludes priority areas for biodiversity conservation in addition to the restrictions applied in the Baseline scenario. It tests the consequences of strengthening ecological safeguards for bioenergy expansion.

Calliope overrides:

scenario_conservation:
  - costs_2050
  - conservation
  - fossil_banned
  - demand_nz
3. Governance
scenario_governance

The Governance scenario restricts land availability according to land-tenure and registration conditions. It prioritises areas with stronger evidence of formalised land governance and avoids areas presenting greater tenure uncertainty.

Calliope overrides:

scenario_governance:
  - costs_2050
  - governance
  - fossil_banned
  - demand_nz
4. Degraded pastures
scenario_degraded_pastures

The Degraded Pastures scenario directs new sugarcane and soybean production towards degraded pasture areas. It represents a land-sparing strategy intended to reduce pressure on natural vegetation and productive agricultural land.

Calliope overrides:

scenario_degraded_pastures:
  - costs_2050
  - degraded_pastures
  - fossil_banned
  - demand_nz
5. Integrated safeguards
scenario_integrated

The Integrated scenario applies conservation, land-governance, and degraded-pasture criteria simultaneously. It is the most restrictive scenario and represents a combined environmental and governance strategy for bioenergy expansion.

Calliope overrides:

scenario_integrated:
  - costs_2050
  - integrated
  - fossil_banned
  - demand_nz
EPE comparison scenario
scenario_epe

An additional scenario is included for comparison with the 2050 transport-energy assumptions from the Brazilian Energy Research Office (EPE). Unlike the five main net-zero land scenarios, this configuration applies the EPE light-vehicle ethanol demand assumption.

scenario_epe:
  - costs_2050
  - baseline
  - force_ethanol_light_vehicles
  - demand_nz
Geospatial analysis

The geospatial workflow is provided in geospatial_code.zip. The scripts were developed for execution through the QGIS Python environment and use QGIS Processing, PyQGIS, GDAL, and raster-calculation functions.

After extraction, the scripts are organised into three groups:

geospatial_code/
├── available_lands/
├── indicators/
└── emissions/
Available-land analysis

The scripts in available_lands/ generate scenario-specific land-availability rasters for sugarcane and soybean.

They perform the following operations:

reclassify crop suitability or production-cost rasters;
combine crop suitability with the common land-availability mask;
apply degraded-pasture, land-governance, or conservation restrictions;
construct the Integrated scenario by applying the three safeguards together;
calculate available area by model zone;
identify land shared by sugarcane and soybean;
separate shared, sugarcane-only, and soybean-only land pools to avoid double counting.

The main scripts are:

00_raster_soybean_reclassification.py: reclassifies the soybean production-cost raster into suitability or cost classes.
01_raster_sugarcane_baseline.py and 01_raster_soybean_baseline.py: construct the Baseline crop-availability rasters.
02_raster_sugarcane_degraded_pastures.py and 02_raster_soybean_degraded_pastures.py: restrict crop expansion to degraded pastures.
02_raster_sugarcane_governance.py and 03_raster_soybean_governance.py: apply land-tenure criteria.
04_raster_sugarcane_conservation.py and 04_raster_soybean_conservation.py: remove priority areas for conservation.
05_raster_sugarcane_integrated.py and 05_raster_soybean_integrated.py: combine conservation, governance, and degraded-pasture restrictions.
06_raster_area.py and 06_raster_area_soybean.py: calculate available crop area by model zone.
07_intersection_soy_cane.py: identifies intersections between sugarcane and soybean suitability and constructs mutually consistent land pools.

The resulting zonal areas are transferred to the Calliope model through the land-pool definitions in locations/ and the scenario-specific values in overrides3.yaml.

# Sustainability indicators

The scripts in indicators/ intersect the final spatial allocation of sugarcane and soybean with sustainability-risk datasets.

01_slavery_likely_score_version_3.py: estimates exposure to recorded labour-rights and slavery-related risk indicators.
02_water_security_index_v3.py: calculates water-security risk for the allocated crop areas.
03_soybean_governance_indicator.py: evaluates the land-tenure conditions of the allocated soybean area.
04_landuse_indicator_v2.py: estimates potential land-use conflict by combining the downscaled crop allocation with land-cover information.

These scripts produce raster outputs and zonal CSV summaries. Where applicable, indicators are expressed using a three-level scale:

1 = low risk
2 = medium risk
3 = high risk

The indicators identify potential exposure or areas requiring additional assessment. They should not be interpreted as proof that a specific project causes a social or environmental impact.

# Direct land-use-change emissions

The emissions/260630_emissions_v0.py script estimates direct land-use-change emissions associated with the final downscaled sugarcane and soybean allocations.

The calculation combines the allocated crop areas with biome and original land-cover carbon-stock information. It exports scenario-level and spatially disaggregated results, together with diagnostic summaries used to verify the weighted carbon-stock calculations.

# Geospatial software requirements

The geospatial scripts require:

QGIS with its Python environment;
PyQGIS;
QGIS Processing;
GDAL;
the input raster and vector datasets described in the associated paper.

Before running the scripts, update the input and output paths defined at the beginning of each file. The archived scripts preserve the directory structure used during the analysis and therefore contain absolute paths from the original computational environment.

The scripts should generally be executed in numerical order because later stages use outputs generated by earlier scripts.

A simplified workflow is:

Input spatial datasets
        ↓
Common land exclusions
        ↓
Crop suitability classification
        ↓
Scenario-specific land restrictions
        ↓
Available area by model zone
        ↓
Sugarcane–soybean intersection
        ↓
Calliope land-pool constraints
        ↓
Energy-system optimisation
        ↓
Spatial downscaling of model results
        ↓
Risk indicators and land-use-change emissions
Preparing the Calliope model

The model was developed using:

Calliope 0.6.10

A compatible Python environment and an optimisation solver are required. The provided configurations use Gurobi.

Install Calliope in a dedicated environment:

conda create -n land-restrictions python=3.10
conda activate land-restrictions
pip install calliope==0.6.10

A valid Gurobi installation and licence must also be available.

Clone the repository:

git clone https://github.com/paulacsborba/energy_model_land_restrictions.git
cd energy_model_land_restrictions

Extract the input directories:

unzip "data (2).zip"
unzip techs.zip
unzip locations.zip
unzip geospatial_code.zip

After extraction, the repository should contain the following directories:

data/
techs/
locations/
geospatial_code/
Running the least-cost scenarios

Create a directory for the results:

mkdir results

Run an individual scenario with:

calliope run model.yaml \
  --scenario scenario_baseline \
  --save_netcdf results/scenario_baseline.nc

For example, the Integrated scenario can be run with:

calliope run model.yaml \
  --scenario scenario_integrated \
  --save_netcdf results/scenario_integrated.nc

To run all five principal scenarios:

for scenario in \
  scenario_baseline \
  scenario_conservation \
  scenario_governance \
  scenario_degraded_pastures \
  scenario_integrated
do
  calliope run model.yaml \
    --scenario "$scenario" \
    --save_netcdf "results/${scenario}.nc"
done

The simulations are computationally demanding. Execution on a high-performance computing system with sufficient memory and wall time is recommended.

# Near-optimal solutions

The model_spores.yaml configuration generates alternative system designs using the SPORES functionality in Calliope.

The current configuration generates five alternatives within 10% of the least-cost system cost:

spores_number: 5
slack: 0.10

Run a near-optimal scenario with:

calliope run model_spores.yaml \
  --scenario scenario_baseline \
  --save_netcdf results/scenario_baseline_spores.nc

The SPORES results are used to assess whether the main conclusions depend on a single least-cost solution or remain consistent across alternative system configurations with similar total costs.

Model outputs

Calliope stores each model result as a NetCDF file. Results can be reopened with Calliope:

import calliope

model = calliope.read_netcdf("results/scenario_baseline.nc")

They can also be inspected with xarray:

import xarray as xr

results = xr.open_dataset("results/scenario_baseline.nc")
print(results)

The Zenodo deposit associated with this repository contains the model outputs used to produce the results reported in the paper
(10.5281/zenodo.22147960).

# Reproducibility notes

The geospatial input datasets are not all redistributed because some are large or subject to access and licensing conditions. Users must obtain the original spatial datasets from their respective providers and update the paths in the geospatial scripts.

The archived code documents the analytical workflow used in the study. Exact reproduction requires:

the same versions of the input spatial datasets;
the original coordinate reference systems and raster resolutions;
Calliope 0.6.10;
compatible versions of QGIS, PyQGIS, and GDAL;
the model input data included in or linked to the repository;
a compatible optimisation solver.
