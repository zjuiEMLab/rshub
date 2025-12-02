# Welcome to RSHub!
Remote Sensing Hub (RSHub) is a shared cloud computing platform for the remote sensing community to compute microwave scattering properties based on microwave electromagnetic scattering mechanisms.

## Getting Started
RSHub utilizing **tokens** to run, query, and download results. Register your account today to get a token!!

### Steps to run your code
1. Go to [RShub wesite](https://rshub.zju.edu.cn/Login) to register your account and get an access token.
2. Navigate to your scenario and check parameter lists
3. Explore scenario demos for a quick start
      
#### Explore scenario demos
- [x] **[Vegetation demo](https://github.com/zjuiEMLab/rshub/blob/main/demo/Vegetation-demo.ipynb): Uniform vs. Layered Vegetation Brightness Temperature** 🌵🌲🌳🎍🎋🌾

    > This demo compares the brightness temperature from uniformly and nonuniformly distributed vegetation covered land surfaces at 1.41 GHz based on the radiative transfer theory.

- [x] **[Vegetation Validation](https://github.com/zjuiEMLab/rshub/blob/main/demo/Vegetation-Validation.ipynb): Validate passive VRT using SMAPVEX12 data** 🌵🌲🌳🎍🎋🌾

    > This demo validate the modeled brightness temperature at 1.41 from VPT using SMAPVEX12 cite F5 measurements .
- [x] **[Snow demo DMRT-QMS](https://github.com/zjuiEMLab/rshub/blob/main/demo/Snow-demo-DMRT-QMS.ipynb): Brightness temperature and backscatter of a three-layer snow scenario**

    > This demo estimates multilayer brightness temperature and/or backscatter of snow using DMRT-QMS model.

- [x] **[Snow demo DMRT-BIC](https://github.com/zjuiEMLab/rshub/blob/main/demo/Snow-demo-DMRT-BIC.ipynb): Brightness temperature and backscatter of a three-layer Snow scenario**

    > This demo estimates multilayer brightness temperature and/or backscatter of snow using DMRT-BIC model.

- [x] **[Soil Model](https://github.com/zjuiEMLab/rshub/blob/main/demo/Soil-demo-1.ipynb)**
    > This demo estimates brightness temperature and/or backscatter of soil using NMM3D-VIE-DDA model.


# Rshub toolbox description

*Yiwen | Jan 6th, 2024

This document describes how to use rstool box to run models. Detailed description of each model parameters are described in each model section.

## Getting Started

### Remote Sensing Hub (RSHub)

RSHub is a shared cloud computing platform for the remote sensing community to compute microwave scattering properties based on microwave electromagnetic scattering mechanisms.

### Supporting Scenarios

*   Bare soil
*   Vegetation-covered soil
*   Snow-covered soil

### Steps to Run Your Code

1.  Go to [RSHub website Registration Page](https://rshub.zju.edu.cn/Login/#) to register your account and get an access token.
2.  Navigate to your scenario and check parameter lists.
3.  Explore scenario demos for a quick start.

## rstool toolbox

There are three main functions to run, check, and download results

### Install rstool toolbox

```bash
!pip install rshub
```

#### Function 1: Run a model

```python
from rshub import submit_jobs

# data1 is a json file that defines pairs of parameters
data1 = {
    'scenario_flag': scenario_flag,
    'output_var': output_var,
    'fGHz': fGHz,
    'algorithm': algorithm,
    'scatters': scatters1,
    'project_name': project_name,
    'task_name': task_name1,
    'token': token,
    'level_required': 1
}
result = submit_jobs.run(data1)
```

**Common Parameters:**

| Parameters | Description | Example | Required |
| :--- | :--- | :--- | :--- |
| project_name | Name of your project | "SMAPEX validation" | Required |
| task_name | Name of your task | "uniform distribution" | Required |
| token | Your credential to run a job | "skSLfdh3lds923" | Required |
| scenario_flag | Scenario category: 1: Bare soil; 2: Snow; 3: Vegetation | 3 | |
| output_var | Main output variable: 1: Backscatter; 2: Brightness Temperature | 2 | |
| fGHz | Frequency (GHz) | "1.41" | |
| algorithm | Soil: 1.VIE; Snow: 1. QMS; 2.BIC; Vegetation: 1.RT | 1 | |
| scatters | Model parameters | See each model session | |
| ... | Other model parameters | See each model session | |
| level_required | Computing privilege (currently developing) | 1 | |

**Model card (Currently supported Models and their flags)**

| Model Name                        | scenario_flag | algorithm   | output_var |
| :---                              | :---         | :---        | :---       |
| Vegetation Passive RT Model        | 'veg'        | 'rt'        | 'tb'       |
| DMRT QMS Model (Active)           | 'snow'       | 'rt'        | 'bs'       |
| DMRT QMS Model (Passive)          | 'snow'       | 'rt'        | 'tb'       |
| DMRT BIC Model (Active)           | 'snow'       | 'rt'        | 'bs'       |
| DMRT BIC Model (Passive)          | 'snow'       | 'rt'        | 'tb'       |
| NMM3D VIE DDA Model (Active)      | 'soil'       | 'full_wave' | 'bs'       |
| NMM3D VIE DDA Model (Passive)     | 'soil'       | 'full_wave' | 'tb'       |

#### Function 2: Check Job Status

```python
from rshub import submit_jobs
result = submit_jobs.check_completion(token, project_name, task1_name)
print(result)
```

#### Function 3: Retrieve error messages from failed jobs

```python
from rshub.load_file import load_file
data = load_file(token, project_name, task_name1, fGHz[1])
message = data.load_error_message()
```

#### Function 4: Retrieve Results

```python
from rshub.load_file import load_file
data1 = load_file(token, project_name, task_name1, fGHz, scenario_flag, output_var)
data_multi = data1.load_outputs()
# Using vegetation model as an example,
# Brightness temperature and incident angles are stored in "data"
TU_all = data_multi['TU_all'] # Brightness temperature
theta_obs = data_multi['theta_obs'] # IncidentAngles
```

**Inputs of load_file**

| inputs name | description | type | default value | required |
| :--- | :--- | :--- | :--- | :--- |
| token | personal token (register on RSHub website to get a token) | str | | required |
| project_name | name of your project | str | | required |
| task_name | name of your task | str | | required |
| fGHz | frequency (only accept a single value; not in array) | float | | required |
| scenario_flag | scenario flags (1: soil; 2: snow; 3: vegetation) | int | 1 | required |
| output_var | output types (1: active; 2: passive) | int | 1 | required |
| inc_ang | incident angle (not needed in vegetation scenario, not in array) | int/float | 40 | |

*Details of outputs are shown in the "Model Output Parameters" session*

---

## Model Input Parameters

### Vegetation Scenario


[Image: Diagram of a vegetation-covered soil scenario showing a plant with scatterers (Type, D, L, β, orientation, density, distribution) above a soil layer (mv, ε, clay_frac, rmsh, T).]

#### VPRT
A full list of Model Parameters:

```python
{"fGHz": 1.41, "scatters": [[1, 0.37, 7.85, 0.15, 0, 0, 0, 8, 0.24], [1, 0.444, 0.555, 0.0112, 35, 90, 0, 8, 0.24]], "sm": 0.1, "rmsh": 0.01, "corlength": 0.1, "clay": 0.19, "perm_soil_r": 0, "perm_soil_i": 0, "rough_type": 2, "veg_height": 8, "err": 0.1, "Tgnd": 300, "Tveg": 300, "Flag_coupling": 1, "Flag_forced_cal": 0,"core_num": 10}
```

| Parameters      | Description                                                                 | unit     | Default                                                                                      | Tags            |
| :---            | :---                                                                       | :---     | :---                                                                                         | :---            |
| fGHz            | Frequency                                                                  | GHz      | 1.41                                                                                         | Observation Settings  |
| err             | Convergence error                                                          | K        | 0.1                                                                                          | Model settings |
| Flag_forced_cal | 1: force calculate; 0: do not calculate if results are already in the database |          | 0                                                                                            | Model settings |
| Tveg            | Vegetation temperature                                                     | K        | 300                                                                                          | Vegetation settings  |
| veg_height      | Height of the vegetation layer                                             | m        | 8                                                                                            | Vegetation settings  |
| scatters        | Scatter property MUST be in order                                          |          | `[[1, 0.37, 7.85, 0.15, 0, 0, 0, 8, 0.24], [1, 0.444, 0.555, 0.0112, 35, 90, 0, 8, 0.24]]` | Vegetation settings  |
| ├─ type         | 1 for cylinder, 0 for disc                                                 |          |                                                                                              |                 |
| ├─ VM           | Volumetric moisture of scatterer                                           |          |                                                                                              |                 |
| ├─ L            | Length of the scatterer                                                    | m        |                                                                                              |                 |
| ├─ D            | Diameter of the scatterer                                                  | m        |                                                                                              |                 |
| ├─ betar        | Orientation range of the scatterer                                         | degree   |                                                                                              |                 |
| ├─ density      | Density of the scatterer                                                   | m^-2     |                                                                                              |                 |
| └─ distribution | Vertical distribution range of the scatterers                              | m        |                                                                                              |                 |
| Flag_coupling   | 1: use volume-surface coupling; 0: volume scattering only                  |          | 1                                                                                            | Vegetation settings, Soil settings      |
| sm              | Soil moisture                                                              |          | 0.1                                                                                          | Soil Settings   |
| perm_soil_r     | Real part of the permittivity constant of the soil                         |          | 0                                                                                            | Soil Settings   |
| perm_soil_i     | Image part of soil permittivity                                            |          | 0                                                                                            | Soil Settings   |
| clay            | clay ratio                                                                 |          | 0.19                                                                                         | Soil Settings   |
| rmsh            | rms height                                                                 | m        | 0.01                                                                                         | Soil Settings   |
| Tgnd            | Ground temperature                                                         | K        | 300                                                                                          | Soil Settings   |
| corlength       | correlation length of rough surface for selected rough type                |          | 0.1                                                                                          | Soil Settings   |
| rough_type      | autocorrelation function to compute soil roughness 1: Gaussian correlation function; 2: Exponential correlation function |      | 2                                                                                            | Soil Settings   |

### Snow Scenario

[Image: Diagram of a snow-covered soil scenario. It shows multiple snow layers (1, 2, ... n) with parameters (sd, ρ, D, T, T_snow, ζ, b) on top of a soil layer (mv, ε, clay_frac, rmsh, T).]
**Legend:**
*   **sd**: snow depth
*   **ρ**: density
*   **D**: diameter
*   **τ**: stickiness
*   **T_snow**: snow temperature
*   **ζ**: inversely proportional to grainsize
*   **b**: clustering effect of particles

#### DMRT-BIC
A full list of model parameters:

```python
{"output_var":2,"fGHz": [9.6,13.4,17.2], "angle": [30,40,50], "depth": [20,20,20],"rho": [0.3,0.3,0.3],"zp": [1.2,1.2,1.2],"kc":[10000,10000,10000],"Tsnow": [260,260,260],"Tg": 270,"epsr_ground_r":[],"epsr_ground_i":[],"mv": 0.15,"clayfrac": 0.3, "surf_model_setting":[3,0.1,7],"lut_flag":1,"Nquad":64, "del":0.0005,"nd":64,"nrlz":50}
```

| Parameters | Description | unit | Default Values | Tags | Parent items |
| :--- | :--- | :--- | :--- | :--- | :--- |
| lut_flag | 1: use LUT to retrieve phase matrix, ks, ke 0: compute phase matrix using NMM3D (slow) | | 1 | Model settings | |
| output_var | 1: active; 2:passive | | 2 | Model settings | |
| Nquad | Number of quadrature angles; default to 32 if using lut; typical values: 64, 128 (for lower frequencies) | | 32 | Model settings | |
| nrlz | Number of realizations to generate phase matrix, ks, ke. The parameter is not in use if lut_flag = 1. | | 50 | Model settings | |
| fGHz | Simulation frequency | GHz | 37 | Observation settings | |
| angle | Incidence angle / observation angle | degree | [30, 40, 50] | Observation settings | |
| depth | Layer thickness | cm | [20, 20, 20] | Snow settings | |
| rho | Layer density | g/cm^3 | [0.3, 0.3, 0.3] | Snow settings | |
| kc | Layer <ζ>: inversely proportio | | [10000, 10000, 10000] | Snow settings | |
| zp | Layer b: control size distribution | | [1.2, 1.2, 1.2] | Snow settings | |
| Tsnow | Layer snow temperature | K | [260, 260, 260] | Snow settings | |
| Tg | Ground temperature | K | 270 | Soil settings | |
| epsr_ground_r | Real part of the soil permittivity | | [] | Soil settings | |
| epsr_ground_i | Imagery part of the soil permittivity | | [] | Soil settings | |
| mv | Soil moisture | | 0.15 | Soil settings | |
| clayfrac | Clay content weight fraction | | 0.3 | Soil settings | |
| surf_model_setting | | | [1,0,0] | Soil settings | |
| &nbsp;&nbsp;&nbsp;Active | | | | | surf_model_setting |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Physical models to calculate surface backscattering | 1. 'OH' model; 2.'SPM3D'; 3. ''NMM3D' look up table; | | | | Active |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;rough ground rms height | rough ground rms height, (cm) rms == 0 assumes flat bottom boundary | cm | | | Active |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;correlation length | correlation length / rms height | | | | Active |
| &nbsp;&nbsp;&nbsp;Passive | | | | | surf_model_setting |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Physical model | 1: Q/H model | | | | Passive |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;roughness height factor, unitless | | | | | Passive |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;polarization mixing factor, unitless | | | | | Passive |
| lut_flag | 1: use look up table (lut) for snow; 0: compute phase matrix/ks/ke directly from numerical wave approach (very slow) | | 1 | Snow settings &nbsp; Wave Approach | |
| Nquad | number of quadrature angles | | 64 | Snow settings &nbsp; Wave Approach | |
| del | unit length delta (m) | | 0.0005 | Snow settings &nbsp; Wave Approach | |
| nd | number of delta; length of cubic = nd*del | | 64 | Snow settings &nbsp; Wave Approach | |
| nrlz | number of realizations | | 50 | Snow settings &nbsp; Wave Approach | |

> **Notes:** Phase matrix, extinction parameter (ke), etc. can be retrieved from previous computed look up tables or computed directly from numerical wave approach using DDA. If using look up tables (**lut_flag=1**), the ranges of volumetric water content of snow (fv), <ζ> (kc), b parameter (zp), and frequency (fGHz) are in the following ranges:
> *   fv_a = 0.10:0.05:0.45;
> *   zp_a = 0.6:0.2:1.6;
> *   kc_a = [5000:2000:15000];
> *   fGHz_a = [9.6 13.3 17.2 18.7 37];
>
> If parameters are not at the exact number, it'll read lut that has the closest parameters.
> Set lut_flag=0 will compute phase matrix/ke/ks numerically. This process can be **SLOW!**

#### DMRT-QMS
A full list of model parameters:
```python
{"output_var":2,"fGHz": [9.6,17.2], "angle": [40,], "depth": [30,20,7,18],"rho": [0.111,0.224,0.189,0.216],"dia": [0.05,0.1,0.2,0.3],"tau":[0.12,0.15,0.25,0.35],"Tsnow": [260,260,260,260],"Tg": 270,"epsr_ground_r":[3],"epsr_ground_i":[1],"mv": 0.15,"clayfrac": 0.3, "surf_model_setting":[2,0.305,4]}
```

| Parameters | Description | Unit | Default values | Tags | Parent items |
| :--- | :--- | :--- | :--- | :--- | :--- |
| output_var | 1: active; or 2:passive | | 2 | Model settings | |
| fGHz | Simulation frequency | GHz | 37 | Observation settings | |
| angle | Incidence angle / observation angle | degree | 40 | Observation settings | |
| depth | Layer thickness | cm | [30,20,7,18] | Snow settings | |
| rho | Layer density | g/cm^3 | [0.111,0.224,0.189,0.216] | Snow settings | |
| dia | Layer grain size | cm | [0.05,0.1,0.2,0.3] | Snow settings | |
| tau | Layer stickiness | | [0.12,0.15,0.25,0.35] | Snow settings | |
| Tsnow | Layer snow temperature | K | [260,260,260,260] | Snow settings | |
| Tg | Ground temperature | K | 270 | Soil settings | |
| epsr_ground_r | Real part of the soil permittivity | | 3 | Soil settings | |
| epsr_ground_i | Imagery part of the soil permittivity | | 1 | Soil settings | |
| mv | Soil moisture | | 0 | Soil settings | |
| clayfrac | Clay content weight fraction | | 0.3 | Soil settings | |
| surf_model_setting | | | [1,0,1,7] | | |
| &nbsp;&nbsp;&nbsp;Active | | | | | surf_model_setting |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Physical models to calculate surface backscattering | 1. 'OH' model; 2.'SPM3D'; 3. 'NMM3D' look up table; | | | | Active |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;rough ground rms height | rough ground rms height, (cm) rms == 0 assumes flat bottom boundary | | | | Active |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;correlation length | correlation length / rms height | | | | Active |
| &nbsp;&nbsp;&nbsp;Passive | | | | | surf_model_setting |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Physical model | 1: Q/H model | | | | Passive |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;roughness height factor, unitless | | | | | Passive |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;polarization mixing factor, unitless | | | | | Passive |
### Soil Scenario

[Image: Diagram of a bare soil scenario. Left side shows a soil volume with dimensions Lx and Lz and properties (mv, ε, rmsh, T). Right side shows a DDA (Discrete Dipole Approximation) cube with dimensions Lx, Ly, Lz.]

```python
{"fGHz": 1.26, "angle": 40, "output_var": 1, "h": 0.01, "cLx": 0.1, "cLy": 0.1, "Lx": 1.6, "Ly": 1.6, "Lz": 0.05, "xr": -0.9, "yr": -0.9, "zr": 0, "d": 0.01, "epsr_ice_re": 5.2, "epsr_ice_im": 0.46, "epsr_g_re": 5.2, "epsr_g_im": 0.46, "Ts": 300.75, "Tg": 295.15, "nr": 15, "ir_beg": 1, "ir_end": 15, "tol": 0.001, "rest": 10, "maxiter": 30000, "N": 10000, "seed": 100}
```

| | Parameters | Description | unit | Default values | Tags |
| :-- | :--- | :--- | :--- | :--- | :--- |
| 1 | output_var | 1: active; 2: passive | | 1 | Model settings |
| 2 | fGHz | Simulation frequency | GHz | 1.26 | Observation settings |
| 3 | angle | Incidence angle / observation angle | degree | 40 | Observation settings |
| 4 | h | RMS height of the soil roughness | m | 0.01 | Soil settings |
| 5 | cLx | Correlation length of the roughness in x-axis | m | 0.1 | Soil settings |
| 6 | cLy | Correlation length of the roughness in y-axis | m | 0.1 | Soil settings |
| 7 | Lx | Cut cube length in x-axis | m | 1.6 | Soil settings |
| 8 | Ly | Cut cube length in y-axis | m | 1.6 | Soil settings |
| 9 | Lz | Cut cube length in z-axis | m | 0.05 | Soil settings |
| 10 | xr | start edge in x-axis | m | -0.9 | Soil settings |
| 11 | yr | start edge in y-axis | m | -0.9 | Soil settings |
| 12 | zr | start edge in z-axis | m | 0 | Soil settings |
| 13 | d | unit length of a cube in a cut cube | m | 0.01 | Soil settings |
| 14 | epsr_ice_re | Real part of top layer medium permittivity | | 5.2 | Soil settings |
| 15 | epsr_ice_im | Imagine part of top layer medium permittivity | | 0.46 | Soil settings |
| 16 | epsr_g_re | Real part of Substrate layer medium permittivity | | 5.2 | Soil settings |
| 17 | epsr_g_im | Imagine part of Substrate layer medium permittivity | | 0.46 | Soil settings |
| 18 | Ts | Top layer medium temperature | K | 300.75 | Soil settings |
| 19 | Tg | Substrate layer temperature | K | 295.15 | Soil settings |
| 20 | nr | Monte Carlo realization times | | 15 | Model settings |
| 21 | ir_beg | Realization start number | | 1 | Model settings |
| 22 | ir_end | Realization end number | | 15 | Model settings |
| 23 | tol | GMRES tolerance | | 0.001 | Model settings |
| 24 | rest | GMRES restart number | | 10 | Model settings |
| 25 | maxiter | GMRES maximum iteration in inner loop | | 30000 | Model settings |
| 26 | seed | seed; No need | | 100 | Model settings |
| 27 | N | No need | | 10000 | Model settings |
---

## Model Output Parameters

### Vegetation Scenario

**Code Example:**
```python
data = load_file(token, project_name, task_name, fGHz, scenario_flag, output_var)
data = data.load_outputs()
TU_all = data['TU_all'] # Brightness temperature
theta_obs = data['theta_obs'] # Incident angles
```
**Output lists:**
| Variable | Definition | unit | Size |
| :--- | :--- | :--- | :--- |
| TU_all | Brightness temperature | K | # of polarization(V,H) x # of incident angles |
| theta_obs | Incident angles | degree | 1 x # of incident angles |

### Snow Scenario

**Code Example:**
```python
data = load_file(token, project_name, task_name1, fGHz[1], scenario_flag, output_var1, inc_ang)
data_active = data.load_outputs()
TB_v.append(data_active['Tb_v0'][:,0]) # vertical Tbs
TB_h.append(data_active['Tb_h0'][:,0]) # horizontal Tbs
```
**Output lists:**
| Variable | Definition | unit | Size | output_var |
| :--- | :--- | :--- | :--- | :--- |
| Tb_v0 | TB at vertical polarization at input incident angle inc_ang | K | 1 x 1 | 2:passive |
| Tb_h0 | TB at horizontal polarization at input incident angle inc_ang | K | 1 x 1 | 2:passive |
| vvdb | total backscatter in vv polarization | db | 1 x 1 | 1:active |
| vhdb | total backscatter in vh polarization | db | 1 x 1 | 1:active |
| hhdb | total backscatter in hh polarization | db | 1 x 1 | 1:active |
| hvdb | total backscatter in hv polarization | db | 1 x 1 | 1:active |
| albedo | scattering albedo of each layer | | 1 x # layers | 1:active, 2:passive |
| epsr_ground | ground effective permittivity | | 1 x 1 | 1:active, 2:passive |
| epsr_eff | snow effective permittivity of each layer | | 9 x 1 | 1:active |
| deg0 | sampling angles of TB in air, in degree | degree | # x 1 | 2:passive |
| TBv | TB at vertical polarization at deg0 | K | # deg0 x 1 | 2:passive |
| TBh | TB at horizontal polarization at deg0 | K | # deg0 x 1 | 2:passive |
| ot | optical thickness of each layer | cm | # layers x 1 | 1:active, 2:passive |
| epsr_snow | snow effective permittivity of each layer | | # layers x 1 | 2:passive |
| vv_vol   | volume backscatter in vv polarization in linear scale; 10log10 to get it in dB scale || 1 x 1 | 1:active |
| hv_vol   | volume backscatter in hv polarization in linear scale || 1 x 1 | 1:active |
| vh_vol   | volume backscatter in vh polarization in linear scale || 1 x 1 | 1:active |
| hh_vol   | volume backscatter in hh polarization in linear scale || 1 x 1 | 1:active |
| vv_surf  | surface backscatter in vv polarization in linear scale || 1 x 1 | 1:active |
| hv_surf  | surface backscatter in hv polarization in linear scale || 1 x 1 | 1:active |
| vh_surf  | surface backscatter in vh polarization in linear scale || 1 x 1 | 1:active |
| hh_surf  | surface backscatter in hh polarization in linear scale || 1 x 1 | 1:active |

### Soil Scenario

**Code Example:**
```python
data = load_file(token, project_name, task_name1, fGHz[1], scenario_flag, output_var1, inc_ang)
data_active = data1.load_outputs()
```
**Output lists:**
| Variable | Definition | unit | Size | output_var |
| :--- | :--- | :--- | :--- | :--- |
| Tb | Brightness temperature | K | # of polarization x 1 | 2: passive |
| backscatter | Backscatter in order of VV, VH, HV, HH | dB | 4 x 1 | 1: active |
| thetas | Azimuth angles | degree | | 1: active |
| phis | Polar angles | degree | | 1: active |
| g_coh | Coherent bistatic scattering coefficient in dB | dB | | 1: active |
| g_inc | Incoherent bistatic scattering coefficient in dB | dB | | 1: active |
| g_tot | Total bistatic scattering coefficient in dB | dB | | 1: active |
