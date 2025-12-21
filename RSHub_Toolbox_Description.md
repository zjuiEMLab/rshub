# RSHub Toolbox Description (Updated 12.21.2025)

This document explains how to use the RSHub toolbox to run models, including platform overview, run steps, main functions, parameters for different scenarios, and common error handling tips. Each model section describes its parameters and outputs.

## Getting Started

### Remote Sensing Hub (RSHub)

RSHub is a shared cloud computing platform for the remote sensing community to compute microwave scattering properties based on electromagnetic scattering mechanisms.

### Supporting Scenarios

- Bare soil (updating, temporal not working)
- Vegetation-covered soil
- Snow-covered soil

### Steps to Run Your Code

1. Register and obtain an access token at the [RSHub login page](https://rshub.zju.edu.cn/Login/#).
2. Navigate to your scenario and review the parameter lists.
3. Explore scenario demos for a quick start.

## rstool Toolbox

Three primary functions help you run, check, and download results.

### Install rstool Toolbox (Require python > 3.8)

```bash
pip install rshub
```

#### Function 1: Run a Model

```python
from rshub import submit_jobs

# data1 is a JSON-compatible dict defining parameter pairs
data1 = {
    "scenario_flag": scenario_flag,
    "output_var": output_var,
    "fGHz": fGHz,
    "algorithm": algorithm,
    "scatters": scatters1,
    "project_name": project_name,
    "task_name": task_name1,
    "token": token,
    "level_required": 1,
}
result = submit_jobs.run(data1)
```

**Common Parameters**

| Parameter      | Description                                                | Example/Default      | Required |
| :------------- | :--------------------------------------------------------- | :------------------- | :------- |
| project_name   | Name of your project                                       | "SMAPEX validation"  | Required |
| task_name      | Name of your task                                          | "uniform distribution" | Required |
| token          | Credential to run a job                                    | "skSLfdh3lds923"     | Required |
| scenario_flag  | Scenario category: 1 soil; 2 snow; 3 vegetation            | 3                    |          |
| output_var     | Main output: 1 backscatter; 2 brightness temperature       | 2                    |          |
| fGHz           | Frequency (GHz)                                            | "1.41"               |          |
| algorithm      | Soil: 1 VIE; Snow: 1 QMS, 2 BIC; Vegetation: 1 RT          | 1                    |          |
| scatters       | Model parameters                                           | See model section    |          |
| ...            | Other model parameters                                     | See model section    |          |
| level_required | Computing privilege (in development)                       | 1                    |          |

**Model Card (Supported Models and Flags)**

| Model Name                     | scenario_flag | algorithm   | output_var |
| :----------------------------- | :------------ | :---------- | :--------- |
| Vegetation Passive RT Model    | veg           | rt          | tb         |
| DMRT QMS Model (Active)        | snow          | rt          | sigma         |
| DMRT QMS Model (Passive)       | snow          | rt          | tb         |
| DMRT BIC Model (Active)        | snow          | rt          | sigma         |
| DMRT BIC Model (Passive)       | snow          | rt          | tb         |
| NMM3D VIE DDA Model (Active)   | soil          | full_wave   | sigma         |
| NMM3D VIE DDA Model (Passive)  | soil          | full_wave   | tb         |

#### Function 2: Check Job Status

```python
from rshub import submit_jobs
result = submit_jobs.check_completion(token, project_name, task_name)
print(result)
```

#### Function 3: Retrieve Error Messages from Failed Jobs

```python
from rshub import load_file
loader = load_file(
    token="YOUR_TOKEN",
    project_name="demo_project",
    task_name="snow_run_001",
    scenario_flag="snow",      # matches output_info.csv
    algorithm="qms",
    output_var="tb",
)
message = loader.load_error_message()
```

#### Function 4: Retrieve Results

```python
from rshub import load_file
loader = load_file(
    token="YOUR_TOKEN",
    project_name="demo_project",
    task_name="snow_run_001",
    scenario_flag="snow",      # matches output_info.csv
    algorithm="qms",
    output_var="tb",
    size_threshold_mb=50,      # switch to disk download above this size
    chunk_size=8192,           # streaming chunk size when downloading
    show_progress=True,        # show tqdm progress for large downloads
)
data_multi = loader.load_outputs()
# Using the vegetation model as an example:
TU_all = data_multi["TU_all"]       # Brightness temperature
theta_obs = data_multi["theta_obs"] # Incident angles
```

**Inputs of `load_file`**

| Input Name   | Description                                                       | Type        | Default | Required |
| :----------- | :---------------------------------------------------------------- | :---------- | :------ | :------- |
| token        | Personal token (register on RSHub to get one)                    | str         |         | Yes      |
| project_name | Name of your project                                              | str         |         | Yes      |
| task_name    | Name of your task                                                 | str         |         | Yes      |
| scenario_flag| soil; snow; veg                       | str         |        | Yes      |
| algorithm   | Algorithms to run your case, options depends on each senario (e.g., vegetation: rt)                                  | str         |        | Yes      |
| output_var   | sigma; tb                                  | str         |        | Yes      |
| size_threshold_mb      | switch to disk download above this size (MB)        | int   | 50      | No        |
| chunk_size      | streaming chunk size when downloading        | int   | 8192      |No          |
| show_progress      | show tqdm progress for large downloads        | logic   | Yes      |No          |

## Model Input Parameters

### Vegetation Scenario

Diagram: vegetation-covered soil with scatterers (type, diameter, length, beta/orientation, density, distribution) above a soil layer (moisture, permittivity, clay fraction, rms height, temperature).

#### VPRT Parameters

Example:

```python
{
  "fGHz": 1.41,
  "scatters": [
    [1, 0.37, 7.85, 0.15, 0, 0, 0, 8, 0.24],
    [1, 0.444, 0.555, 0.0112, 35, 90, 0, 8, 0.24]
  ],
  "sm": 0.1,
  "rmsh": 0.01,
  "corlength": 0.1,
  "clay": 0.19,
  "perm_soil_r": 0,
  "perm_soil_i": 0,
  "rough_type": 2,
  "veg_height": 8,
  "err": 0.1,
  "Tgnd": 300,
  "Tveg": 300,
  "Flag_coupling": 1,
  "Flag_forced_cal": 0,
}
```

| Parameter       | Description                                                         | Unit | Default                                                         | Tags                       |
| :-------------- | :------------------------------------------------------------------ | :--- | :-------------------------------------------------------------- | :------------------------- |
| fGHz            | Frequency                                                           | GHz  | 1.41                                                            | Observation settings       |
| err             | Convergence error                                                   | K    | 0.1                                                             | Model settings             |
| Flag_forced_cal | 1 force calculate; 0 skip if cached                                 |      | 0                                                               | Model settings             |
| Tveg            | Vegetation temperature                                              | K    | 300                                                             | Vegetation settings        |
| veg_height      | Height of vegetation layer                                          | m    | 8                                                               | Vegetation settings        |
| scatters        | Scatterer properties in order                                       |      | See example                                                     | Vegetation settings        |
| type            | 1 cylinder; 0 disc                                                  |      |                                                                  |                            |
| VM              | Volumetric moisture of scatterer                                   |      |                                                                  |                            |
| L               | Length of scatterer                                                 | m    |                                                                  |                            |
| D               | Diameter of scatterer                                               | m    |                                                                  |                            |
| betar           | Orientation range of scatterer                                      | deg  |                                                                  |                            |
| density         | Density of scatterers                                               | m^-2 |                                                                  |                            |
| distribution    | Vertical distribution range of scatterers                           | m    |                                                                  |                            |
| Flag_coupling   | 1 volume-surface coupling; 0 volume only                            |      | 1                                                               | Vegetation/Soil settings   |
| sm              | Soil moisture                                                       |      | 0.1                                                             | Soil settings              |
| perm_soil_r     | Real part of soil permittivity                                      |      | 0                                                               | Soil settings              |
| perm_soil_i     | Imaginary part of soil permittivity                                 |      | 0                                                               | Soil settings              |
| clay            | Clay ratio                                                          |      | 0.19                                                            | Soil settings              |
| rmsh            | RMS height                                                          | m    | 0.01                                                            | Soil settings              |
| Tgnd            | Ground temperature                                                  | K    | 300                                                             | Soil settings              |
| corlength       | Correlation length for selected rough type                          |      | 0.1                                                             | Soil settings              |
| rough_type      | Autocorrelation: 1 Gaussian; 2 Exponential                          |      | 2                                                               | Soil settings              |

### Snow Scenario

Diagram: snow-covered soil with multiple snow layers (depth, density, diameter, stickiness, snow temperature, zeta, b) above a soil layer (moisture, permittivity, clay fraction, rms height, temperature).

#### DMRT-BIC Parameters

Example:

```python
{
  "output_var": 2,
  "fGHz": [9.6, 13.4, 17.2],
  "angle": [30, 40, 50],
  "depth": [20, 20, 20],
  "rho": [0.3, 0.3, 0.3],
  "zp": [1.2, 1.2, 1.2],
  "kc": [10000, 10000, 10000],
  "Tsnow": [260, 260, 260],
  "Tg": 270,
  "epsr_ground_r": [],
  "epsr_ground_i": [],
  "mv": 0.15,
  "clayfrac": 0.3,
  "surf_model_setting": [3, 0.1, 7],
  "lut_flag": 1,
}
```

| Parameter          | Description                                                                 | Unit  | Default/Typical                | Tags                    | Parent              |
| :----------------- | :-------------------------------------------------------------------------- | :---- | :----------------------------- | :---------------------- | :------------------ |
| lut_flag           | 1 use LUT for phase matrix/ks/ke; 0 compute via NMM3D (slow)                |       | 1                              | Model settings          |                      |
| output_var         | 1 active; 2 passive                                                         |       | 2                              | Model settings          |                      
| fGHz               | Simulation frequency                                                        | GHz   | 37                             | Observation settings    |                      |
| angle              | Incidence/observation angles                                                | deg   | [30, 40, 50]                   | Observation settings    |                      |
| depth              | Layer thickness                                                             | cm    | [20, 20, 20]                   | Snow settings           |                      |
| rho                | Layer density                                                               | g/cm^3| [0.3, 0.3, 0.3]                | Snow settings           |                      |
| kc                 | Layer zeta (inverse grain size)                                             |       | [10000, 10000, 10000]          | Snow settings           |                      |
| zp                 | Layer b (size distribution control)                                         |       | [1.2, 1.2, 1.2]                | Snow settings           |                      |
| Tsnow              | Layer snow temperature                                                      | K     | [260, 260, 260]                | Snow settings           |                      |
| Tg                 | Ground temperature                                                          | K     | 270                            | Soil settings           |                      |
| epsr_ground_r      | Real part of soil permittivity                                              |       | []                             | Soil settings           |                      |
| epsr_ground_i      | Imaginary part of soil permittivity                                         |       | []                             | Soil settings           |                      |
| mv                 | Soil moisture                                                               |       | 0.15                           | Soil settings           |                      |
| clayfrac           | Clay content weight fraction                                                |       | 0.3                            | Soil settings           |                      |
| surf_model_setting | Surface model settings                                                      |       | [1, 0, 0]                      | Soil settings           |                      |
| Active model       | Physical models for surface backscatter: 1 OH; 2 SPM3D; 3 NMM3D LUT         |       |                                | Active (surf_model_setting) | |
| Active rms height  | Rough ground rms height (cm); rms=0 assumes flat bottom boundary            | cm    |                                | Active                  |                      |
| Active corr length | Correlation length / rms height                                             |       |                                | Active                  |                      |
| Passive model      | Physical model for passive: 1 Q/H                                          |       |                                | Passive (surf_model_setting) | |
| Passive roughness  | Roughness height factor (unitless)                                          |       |                                | Passive                 |                      |
| Passive pol mix    | Polarization mixing factor (unitless)                                       |       |                                | Passive                 |                      |

Notes for LUT ranges when `lut_flag = 1`:

- fv_a = 0.10:0.05:0.45
- zp_a = 0.6:0.2:1.6
- kc_a = 5000:2000:15000
- fGHz_a = 9.6, 13.3, 17.2, 18.7, 37

If values are not exact, the closest LUT entry is used. `lut_flag = 0` computes matrices numerically (slow).

#### DMRT-QMS Parameters

Example:

```python
{
  "output_var": 2,
  "fGHz": [9.6, 17.2],
  "angle": [40],
  "depth": [30, 20, 7, 18],
  "rho": [0.111, 0.224, 0.189, 0.216],
  "dia": [0.05, 0.1, 0.2, 0.3],
  "tau": [0.12, 0.15, 0.25, 0.35],
  "Tsnow": [260, 260, 260, 260],
  "Tg": 270,
  "epsr_ground_r": [3],
  "epsr_ground_i": [1],
  "mv": 0.15,
  "clayfrac": 0.3,
  "surf_model_setting": [2, 0.305, 4]
}
```

| Parameter          | Description                                                       | Unit  | Default        | Tags            | Parent                  |
| :----------------- | :---------------------------------------------------------------- | :---- | :------------- | :-------------- | :---------------------- |
| output_var         | 1 active; 2 passive                                               |       | 2              | Model settings  |                         |
| fGHz               | Simulation frequency                                              | GHz   | 37             | Observation     |                         |
| angle              | Incidence/observation angle                                      | deg   | 40             | Observation     |                         |
| depth              | Layer thickness                                                   | cm    | [30, 20, 7, 18]| Snow settings   |                         |
| rho                | Layer density                                                     | g/cm^3| [0.111, 0.224, 0.189, 0.216] | Snow settings |      |
| dia                | Layer grain size                                                  | cm    | [0.05, 0.1, 0.2, 0.3]         | Snow settings |      |
| tau                | Layer stickiness                                                  |       | [0.12, 0.15, 0.25, 0.35]      | Snow settings |      |
| Tsnow              | Layer snow temperature                                            | K     | [260, 260, 260, 260]          | Snow settings |      |
| Tg                 | Ground temperature                                                | K     | 270            | Soil settings   |                         |
| epsr_ground_r      | Real part of soil permittivity                                    |       | 3              | Soil settings   |                         |
| epsr_ground_i      | Imaginary part of soil permittivity                               |       | 1              | Soil settings   |                         |
| mv                 | Soil moisture                                                     |       | 0              | Soil settings   |                         |
| clayfrac           | Clay content weight fraction                                      |       | 0.3            | Soil settings   |                         |
| surf_model_setting | Surface model settings                                            |       | [1, 0, 1, 7]   |                 |                         |
| Active model       | Physical models for surface backscatter: 1 OH; 2 SPM3D; 3 NMM3D LUT |     |                | Active          | surf_model_setting      |
| Active rms height  | Rough ground rms height (cm); rms=0 assumes flat boundary         | cm    |                | Active          | surf_model_setting      |
| Active corr length | Correlation length / rms height                                   |       |                | Active          | surf_model_setting      |
| Passive model      | Physical model for passive: 1 Q/H                                 |       |                | Passive         | surf_model_setting      |
| Passive roughness  | Roughness height factor (unitless)                                |       |                | Passive         | surf_model_setting      |
| Passive pol mix    | Polarization mixing factor (unitless)                             |       |                | Passive         | surf_model_setting      |

### Soil Scenario

Diagram: bare soil volume (Lx, Lz) with properties (moisture, permittivity, rms height, temperature) and a DDA cube (Lx, Ly, Lz).

Example:

```python
{
  "fGHz": 1.26,
  "angle": 40,
  "output_var": 1,
  "h": 0.01,
  "cLx": 0.1,
  "cLy": 0.1,
  "Lx": 1.6,
  "Ly": 1.6,
  "Lz": 0.05,
  "xr": -0.9,
  "yr": -0.9,
  "zr": 0,
  "d": 0.01,
  "epsr_ice_re": 5.2,
  "epsr_ice_im": 0.46,
  "epsr_g_re": 5.2,
  "epsr_g_im": 0.46,
  "Ts": 300.75,
  "Tg": 295.15,
  "nr": 15,
  "ir_beg": 1,
  "ir_end": 15,
  "tol": 0.001,
  "rest": 10,
  "maxiter": 30000,
  "N": 10000,
  "seed": 100
}
```

| #  | Parameter   | Description                                    | Unit | Default  | Tags            |
| :- | :---------- | :--------------------------------------------- | :--- | :------- | :-------------- |
| 1  | output_var  | 1 active; 2 passive                            |      | 1        | Model settings  |
| 2  | fGHz        | Simulation frequency                           | GHz  | 1.26     | Observation     |
| 3  | angle       | Incidence/observation angle                    | deg  | 40       | Observation     |
| 4  | h           | RMS height of soil roughness                   | m    | 0.01     | Soil settings   |
| 5  | cLx         | Correlation length of roughness in x-axis      | m    | 0.1      | Soil settings   |
| 6  | cLy         | Correlation length of roughness in y-axis      | m    | 0.1      | Soil settings   |
| 7  | Lx          | Cut cube length in x-axis                      | m    | 1.6      | Soil settings   |
| 8  | Ly          | Cut cube length in y-axis                      | m    | 1.6      | Soil settings   |
| 9  | Lz          | Cut cube length in z-axis                      | m    | 0.05     | Soil settings   |
| 10 | xr          | Start edge in x-axis                           | m    | -0.9     | Soil settings   |
| 11 | yr          | Start edge in y-axis                           | m    | -0.9     | Soil settings   |
| 12 | zr          | Start edge in z-axis                           | m    | 0        | Soil settings   |
| 13 | d           | Unit length of a cube in a cut cube            | m    | 0.01     | Soil settings   |
| 14 | epsr_ice_re | Real part of top layer permittivity            |      | 5.2      | Soil settings   |
| 15 | epsr_ice_im | Imaginary part of top layer permittivity       |      | 0.46     | Soil settings   |
| 16 | epsr_g_re   | Real part of substrate permittivity            |      | 5.2      | Soil settings   |
| 17 | epsr_g_im   | Imaginary part of substrate permittivity       |      | 0.46     | Soil settings   |
| 18 | Ts          | Top layer temperature                          | K    | 300.75   | Soil settings   |
| 19 | Tg          | Substrate temperature                          | K    | 295.15   | Soil settings   |
| 20 | nr          | Monte Carlo realization count                  |      | 15       | Model settings  |
| 21 | ir_beg      | Realization start number                       |      | 1        | Model settings  |
| 22 | ir_end      | Realization end number                         |      | 15       | Model settings  |
| 23 | tol         | GMRES tolerance                                |      | 0.001    | Model settings  |
| 24 | rest        | GMRES restart number                           |      | 10       | Model settings  |
| 25 | maxiter     | GMRES maximum iterations in inner loop         |      | 30000    | Model settings  |
| 26 | seed        | Seed (not needed)                              |      | 100      | Model settings  |
| 27 | N           | Not used                                       |      | 10000    | Model settings  |

## Model Output Parameters

### Vegetation Scenario

```python
data = load_file(token, project_name, task_name1,scenario_flag=scenario_flag,algorithm=algorithm,output_var=output_var)
data_multi = data.load_outputs(fGHz=fGHz)
# Read variables into python
TU_all = data_multi['TU_all'] # Tbs
theta_obs = data_multi['theta_obs'] # theta
```

| Variable   | Definition                     | Unit  | Size                                   |
| :--------- | :----------------------------- | :---- | :------------------------------------- |
| TU_all     | Brightness temperature         | K     | polarizations x incident angles        |
| theta_obs  | Incident angles                | deg   | 1 x incident angles                    |

### Snow Scenario

```python
data = load_file(token, project_name, task_name,scenario_flag=scenario_flag,algorithm=algorithm,output_var=output_var1)
data_passive = data.load_outputs(fGHz=fGHz[0], inc_ang=inc_ang)
# Read variables into python

TB_v.append(data_passive['Tb_v0'][:,0]) # vertical Tbs
TB_h.append(data_passive['Tb_h0'][:,0]) # horizontal Tbs
```

```python
data = load_file(token, project_name, task_name,scenario_flag=scenario_flag,algorithm=algorithm,output_var=output_var)
data_active = data.load_outputs(fGHz=fGHz[0], inc_ang=inc_ang)
# Read variables into python

backscatter_vv.append(data_active['vvdb'][:,0]) # VV backscatters
backscatter_vh.append(data_active['vhdb'][:,0]) # VH backscatters
```

| Variable  | Definition                                                      | Unit | Size                   | output_var |
| :-------- | :-------------------------------------------------------------- | :--- | :--------------------- | :--------- |
| Tb_v0     | TB at vertical polarization at incident angle `inc_ang`         | K    | 1 x 1                  | 2 passive  |
| Tb_h0     | TB at horizontal polarization at incident angle `inc_ang`       | K    | 1 x 1                  | 2 passive  |
| vvdb      | Total backscatter VV                                            | dB   | 1 x 1                  | 1 active   |
| vhdb      | Total backscatter VH                                            | dB   | 1 x 1                  | 1 active   |
| hhdb      | Total backscatter HH                                            | dB   | 1 x 1                  | 1 active   |
| hvdb      | Total backscatter HV                                            | dB   | 1 x 1                  | 1 active   |
| albedo    | Scattering albedo of each layer                                 |      | 1 x layers             | 1 active, 2 passive |
| epsr_ground| Ground effective permittivity                                  |      | 1 x 1                  | 1 active, 2 passive |
| epsr_eff  | Snow effective permittivity of each layer                       |      | 9 x 1                  | 1 active   |
| deg0      | Sampling angles of TB in air                                    | deg  | angles x 1             | 2 passive  |
| TBv       | TB at vertical polarization at `deg0`                           | K    | deg0 x 1               | 2 passive  |
| TBh       | TB at horizontal polarization at `deg0`                         | K    | deg0 x 1               | 2 passive  |
| ot        | Optical thickness of each layer                                 | cm   | layers x 1             | 1 active, 2 passive |
| epsr_snow | Snow effective permittivity of each layer                       |      | layers x 1             | 2 passive  |
| vv_vol    | Volume backscatter VV (linear; 10log10 for dB)                  |      | 1 x 1                  | 1 active   |
| hv_vol    | Volume backscatter HV (linear; 10log10 for dB)                  |      | 1 x 1                  | 1 active   |
| vh_vol    | Volume backscatter VH (linear; 10log10 for dB)                  |      | 1 x 1                  | 1 active   |
| hh_vol    | Volume backscatter HH (linear; 10log10 for dB)                  |      | 1 x 1                  | 1 active   |
| vv_surf   | Surface backscatter VV (linear; 10log10 for dB)                 |      | 1 x 1                  | 1 active   |
| hv_surf   | Surface backscatter HV (linear; 10log10 for dB)                 |      | 1 x 1                  | 1 active   |
| vh_surf   | Surface backscatter VH (linear; 10log10 for dB)                 |      | 1 x 1                  | 1 active   |
| hh_surf   | Surface backscatter HH (linear; 10log10 for dB)                 |      | 1 x 1                  | 1 active   |

### Soil Scenario

```python
data = load_file(token, project_name, task_name,scenario_flag=scenario_flag,algorithm=algorithm,output_var=output_var)
data_active = data.load_outputs(fGHz=fGHz[0], inc_ang=inc_ang)
```

| Variable    | Definition                                               | Unit | Size | output_var |
| :---------- | :------------------------------------------------------- | :--- | :--- | :--------- |
| Tb          | Brightness temperature                                   | K    | polarizations x 1 | 2 passive |
| backscatter | Backscatter (VV, VH, HV, HH order)                       | dB   | 4 x 1 | 1 active   |
| thetas      | Azimuth angles                                           | deg  |      | 1 active   |
| phis        | Polar angles                                             | deg  |      | 1 active   |
| g_coh       | Coherent bistatic scattering coefficient                 | dB   |      | 1 active   |
| g_inc       | Incoherent bistatic scattering coefficient               | dB   |      | 1 active   |
| g_tot       | Total bistatic scattering coefficient                    | dB   |      | 1 active   |
