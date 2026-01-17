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
| scenario_flag  | Scenario category: soil, snow, veg                         | "veg"                |          |
| output_var     | Main output: sigma (backscatter) or tb (brightness temp)   | "tb"                 |          |
| fGHz           | Frequency (GHz)                                            | "1.41"               |          |
| algorithm      | Soil: vie; Snow: qms, bic, tri; Vegetation: rt             | "rt"                 |          |
| scatters       | Model parameters                                           | See model section    |          |
| ...            | Other model parameters                                     | See model section    |          |
| level_required | Computing privilege (in development)                       | 1                    |          |

**Model Card (Supported Models and Flags)**

| Model Name                     | scenario_flag | algorithm   | output_var |
| :----------------------------- | :------------ | :---------- | :--------- |
| Vegetation Passive RT Model    | veg           | rt          | tb         |
| DMRT QMS Model (Active)        | snow          | qms         | sigma      |
| DMRT QMS Model (Passive)       | snow          | qms         | tb         |
| DMRT BIC Model (Active)        | snow          | bic         | sigma      |
| DMRT BIC Model (Passive)       | snow          | bic         | tb         |
| DMRT TRI Model (Active)        | snow          | tri         | sigma      |
| DMRT TRI Model (Passive)       | snow          | tri         | tb         |
| NMM3D VIE DDA Model (Active)   | soil          | vie         | sigma      |
| NMM3D VIE DDA Model (Passive)  | soil          | vie         | tb         |

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
  "output_var": "tb",
  "fGHz": 1.41,
  "scatters": [
    [1, 0.37, 7.85, 0.15, 0, 10, 0, 8, 0.24, 0, 0, 1]
  ],
  "sm": 0.1,
  "rmsh": 0.01,
  "corlength": 0.1,
  "clay": 0.19,
  "rough_type": 2,
  "veg_height": 8,
  "Tgnd": 300,
  "Tveg": 300,
}
```

##### Inputs

###### Observation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive). Active mode not exposed in the UI. |  | scalar |
| `fGHz` | Default: `1.41`. Frequency. | GHz | scalar |

###### Vegetation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `scatters` | Scatter populations; each entry is a 12-element array. |  | list |
| `veg_height` | Default: `8`. Vegetation height. | m | scalar |
| `Tveg` | Default: `300`. Vegetation temperature. | K | scalar |

###### Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `sm` | Default: `0.1`. Soil moisture (volumetric). |  | scalar |
| `rmsh` | Default: `0.01`. Surface rms height. | m | scalar |
| `corlength` | Default: `0.1`. Surface correlation length. |  | scalar |
| `clay` | Default: `0.19`. Soil clay fraction. |  | scalar |
| `rough_type` | Default: `2`. Roughness model: `1` Gaussian, `2` Exponential. |  | scalar |
| `Tgnd` | Default: `300`. Ground temperature. | K | scalar |

Scatterer fields (12 values per entry):

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `scatters[1] type` | Index 1. 1: cylinder; 0: disc. |  | scalar |
| `scatters[2] VM` | Index 2. Volumetric moisture. |  | scalar |
| `scatters[3] L` | Index 3. Length. | m | scalar |
| `scatters[4] D` | Index 4. Diameter. | m | scalar |
| `scatters[5] beta1` | Index 5. Orientation min. | deg | scalar |
| `scatters[6] beta2` | Index 6. Orientation max. | deg | scalar |
| `scatters[7] disbot` | Index 7. Vertical start. | m | scalar |
| `scatters[8] distop` | Index 8. Vertical end. | m | scalar |
| `scatters[9] NA` | Index 9. Number density. |  | scalar |
| `scatters[10] profile_a` | Index 10. Profile parameter a. |  | scalar |
| `scatters[11] profile_b` | Index 11. Profile parameter b. |  | scalar |
| `scatters[12] profile_c` | Index 12. Profile parameter c. |  | scalar |

### Snow Scenario

Diagram: snow-covered soil with multiple snow layers (depth, density, diameter, stickiness, snow temperature, zeta, b) above a soil layer (moisture, permittivity, clay fraction, rms height, temperature).

#### DMRT-BIC Parameters

Example:

```python
{
  "output_var": "tb",
  "fGHz": [37],
  "angle": [40],
  "depth": [20, 20, 20],
  "rho": [0.3, 0.3, 0.3],
  "kc": [10000, 10000, 10000],
  "zp": [1.2, 1.2, 1.2],
  "Tsnow": [260, 260, 260],
  "Tg": 270,
  "mv": 0.15,
  "clayfrac": 0.3,
  "surf_model_setting": ["QH", 0, 0]
}
```

##### Inputs

###### Observation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive) or `sigma` (active). |  | scalar |
| `fGHz` | Default: `37`. Frequency; accepts arrays. | GHz | scalar/array |
| `angle` | Default: `40`. Incident angle; accepts arrays. | deg | scalar/array |

###### Snow Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `depth` | Default: `[20, 20, 20]`. Snow layer thickness. | cm | layers |
| `rho` | Default: `[0.3, 0.3, 0.3]`. Snow density per layer. | g/cm^3 | layers |
| `kc` | Default: `[10000, 10000, 10000]`. Layered zeta parameter. |  | layers |
| `zp` | Default: `[1.2, 1.2, 1.2]`. Layered b parameter. |  | layers |
| `Tsnow` | Default: `[260, 260, 260]`. Snow layer temperature. | K | layers |

###### Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `Tg` | Default: `270`. Ground temperature. | K | scalar |
| `mv` | Default: `0.15`. Soil moisture (volumetric). |  | scalar |
| `clayfrac` | Default: `0.3`. Soil clay fraction. |  | scalar |
| `surf_model_setting` | Default: `["QH", 0, 0]`. Surface model: `["QH", q, h]` (passive) or `["OH", rms_cm, cl_over_rms]` (active). |  | 3 |

#### DMRT-TRI Parameters

Example:

```python
{
  "output_var": "tb",
  "fGHz": [13.4, 17.2, 37],
  "angle": [40],
  "depth": [20, 20, 20],
  "rho": [0.3, 0.3, 0.3],
  "kc": [10000, 10000, 10000],
  "zp": [1.2, 1.2, 1.2],
  "wet": [0, 0, 0],
  "film": [0, 0, 0],
  "Tsnow": [273, 273, 273],
  "Tg": 270,
  "mv": 0.4,
  "clayfrac": 0.3,
  "surf_model_setting": ["QH", 0, 0]
}
```

##### Inputs

###### Observation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive) or `sigma` (active). |  | scalar |
| `fGHz` | Default: `[13.4, 17.2, 37]`. Frequency; accepts arrays. | GHz | scalar/array |
| `angle` | Default: `40`. Incident angle; accepts arrays. | deg | scalar/array |

###### Snow Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `depth` | Default: `[20, 20, 20]`. Snow layer thickness. | cm | layers |
| `rho` | Default: `[0.3, 0.3, 0.3]`. Snow density per layer. | g/cm^3 | layers |
| `kc` | Default: `[10000, 10000, 10000]`. Layered zeta parameter. |  | layers |
| `zp` | Default: `[1.2, 1.2, 1.2]`. Layered b parameter. |  | layers |
| `wet` | Default: `[0, 0, 0]`. Layered wetness (percent water content). | % | layers |
| `film` | Default: `[0, 0, 0]`. Layered film percentage of total water (0/50/100). | % | layers |
| `Tsnow` | Default: `[273, 273, 273]`. Snow layer temperature. | K | layers |

###### Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `Tg` | Default: `270`. Ground temperature. | K | scalar |
| `mv` | Default: `0.4`. Soil moisture (volumetric). |  | scalar |
| `clayfrac` | Default: `0.3`. Soil clay fraction. |  | scalar |
| `surf_model_setting` | Default: `["QH", 0, 0]`. Surface model: `["QH", q, h]` (passive) or `["OH", rms_cm, cl_over_rms]` (active). |  | 3 |

#### DMRT-QMS Parameters

Example:

```python
{
  "output_var": "tb",
  "fGHz": [37],
  "angle": [40],
  "depth": [20, 20, 20],
  "rho": [0.3, 0.3, 0.3],
  "dia": [0.15, 0.15, 0.15],
  "tau": [0.1, 0.1, 0.1],
  "Tsnow": [260, 260, 260],
  "Tg": 270,
  "mv": 0.15,
  "clayfrac": 0.3,
  "surf_model_setting": ["QH", 0, 0]
}
```

##### Inputs

###### Observation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive) or `sigma` (active). |  | scalar |
| `fGHz` | Default: `37`. Frequency; accepts arrays. | GHz | scalar/array |
| `angle` | Default: `40`. Incident angle; accepts arrays. | deg | scalar/array |

###### Snow Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `depth` | Default: `[20, 20, 20]`. Snow layer thickness. | cm | layers |
| `rho` | Default: `[0.3, 0.3, 0.3]`. Snow density per layer. | g/cm^3 | layers |
| `dia` | Default: `[0.15, 0.15, 0.15]`. Grain size per layer. | cm | layers |
| `tau` | Default: `[0.1, 0.1, 0.1]`. Stickiness per layer. |  | layers |
| `Tsnow` | Default: `[260, 260, 260]`. Snow layer temperature. | K | layers |

###### Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `Tg` | Default: `270`. Ground temperature. | K | scalar |
| `mv` | Default: `0.15`. Soil moisture (volumetric). |  | scalar |
| `clayfrac` | Default: `0.3`. Soil clay fraction. |  | scalar |
| `surf_model_setting` | Default: `["QH", 0, 0]`. Surface model: `["QH", q, h]` (passive) or `["OH", rms_cm, cl_over_rms]` (active). |  | 3 |

### Soil Scenario

Diagram: bare soil volume (Lx, Lz) with properties (moisture, permittivity, rms height, temperature) and a DDA cube (Lx, Ly, Lz).

Example:

```python
{
  "output_var": "tb",
  "fGHz": 1.26,
  "angle": 40,
  "soilType": 2,
  "rmsh": 0.01,
  "cLx": 0.1,
  "cLy": 0.1,
  "layerZaxis": 0.1,
  "epsr_re": 5.2,
  "epsr_im": 0.46,
  "Tg": 273.15,
  "Lx": 1.6,
  "Ly": 1.6,
  "Lz": 0.05,
  "xr": -0.9,
  "yr": -0.9,
  "zr": 0,
  "delta_d": 0.01,
  "epsr_sub_re": 5.2,
  "epsr_sub_im": 0.46,
  "Tsub": 295.15,
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

##### Inputs

###### Layered Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive) or `sigma` (active). |  | scalar |
| `fGHz` | Default: `1.26`. Frequency. | GHz | scalar |
| `angle` | Default: `40`. Incident angle. | deg | scalar |
| `soilType` | Default: `2`. Roughness type: `1` Gaussian, `2` Exponential. |  | scalar |
| `rmsh` | Default: `0.01`. RMS height. | m | scalar |
| `cLx` | Default: `0.1`. Correlation length X. | m | scalar |
| `cLy` | Default: `0.1`. Correlation length Y. | m | scalar |
| `layerZaxis` | Default: `0.1`. Total soil layer height from bottom. | m | scalar |
| `epsr_re` | Default: `5.2`. Top layer permittivity (real). |  | scalar |
| `epsr_im` | Default: `0.46`. Top layer permittivity (imag). |  | scalar |
| `Tg` | Default: `273.15`. Soil temperature. | K | scalar |

###### Substrate Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `epsr_sub_re` | Default: `5.2`. Substrate permittivity (real). |  | scalar |
| `epsr_sub_im` | Default: `0.46`. Substrate permittivity (imag). |  | scalar |
| `Tsub` | Default: `295.15`. Substrate temperature. | K | scalar |

###### Simulation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `Lx` | Default: `1.6`. Soil cube length X. | m | scalar |
| `Ly` | Default: `1.6`. Soil cube length Y. | m | scalar |
| `Lz` | Default: `0.05`. Soil cube length Z. | m | scalar |
| `xr` | Default: `-0.9`. Receiver start X. | m | scalar |
| `yr` | Default: `-0.9`. Receiver start Y. | m | scalar |
| `zr` | Default: `0`. Receiver start Z. | m | scalar |
| `delta_d` | Default: `0.01`. Discretization length. | m | scalar |
| `nr` | Default: `15`. Number of realizations. |  | scalar |
| `ir_beg` | Default: `1`. Realization start index. |  | scalar |
| `ir_end` | Default: `15`. Realization end index. |  | scalar |
| `tol` | Default: `0.001`. Solver tolerance. |  | scalar |
| `rest` | Default: `10`. Solver restart interval. |  | scalar |
| `maxiter` | Default: `30000`. Solver max iterations. |  | scalar |
| `N` | Default: `10000`. Sample count. |  | scalar |
| `seed` | Default: `100`. Random seed. |  | scalar |

## Model Output Parameters

### Vegetation Scenario

```python
data = load_file(token, project_name, task_name1,scenario_flag=scenario_flag,algorithm=algorithm,output_var=output_var)
data_multi = data.load_outputs(fGHz=fGHz)
# Read variables into python
TU_all = data_multi['TU_all'] # Tbs
theta_obs = data_multi['theta_obs'] # theta
```

| Variables/Parameters | Definition                     | Unit  | Size                                   |
| :------------------- | :----------------------------- | :---- | :------------------------------------- |
| TU_all               | Brightness temperature         | K     | polarizations x incident angles        |
| theta_obs            | Incident angles                | deg   | 1 x incident angles                    |

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

| Variables/Parameters | Definition                                                      | Unit | Size                   | output_var |
| :------------------- | :-------------------------------------------------------------- | :--- | :--------------------- | :--------- |
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

| Variables/Parameters | Definition                                               | Unit | Size | output_var |
| :------------------- | :------------------------------------------------------- | :--- | :--- | :--------- |
| Tb          | Brightness temperature                                   | K    | polarizations x 1 | 2 passive |
| backscatter | Backscatter (VV, VH, HV, HH order)                       | dB   | 4 x 1 | 1 active   |
| thetas      | Azimuth angles                                           | deg  |      | 1 active   |
| phis        | Polar angles                                             | deg  |      | 1 active   |
| g_coh       | Coherent bistatic scattering coefficient                 | dB   |      | 1 active   |
| g_inc       | Incoherent bistatic scattering coefficient               | dB   |      | 1 active   |
| g_tot       | Total bistatic scattering coefficient                    | dB   |      | 1 active   |
