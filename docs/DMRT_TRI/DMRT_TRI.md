# DMRT_TRI Parameters

## Inputs
Array inputs can be comma-separated values (e.g., "20,20,20").

### Observation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive) or `sigma` (active). |  | scalar |
| `fGHz` | Default: `[13.4, 17.2, 37]`. Frequency; accepts arrays. | GHz | scalar/array |
| `angle` | Default: `40`. Incident angle; accepts arrays. | deg | scalar/array |

### Snow Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `depth` | Default: `[20, 20, 20]`. Snow layer thickness. | cm | layers |
| `rho` | Default: `[0.3, 0.3, 0.3]`. Snow density per layer. | g/cm^3 | layers |
| `kc` | Default: `[10000, 10000, 10000]`. Layered zeta parameter. |  | layers |
| `zp` | Default: `[1.2, 1.2, 1.2]`. Layered b parameter. |  | layers |
| `wet` | Default: `[0, 0, 0]`. Layered wetness (percent water content). | % | layers |
| `film` | Default: `[0, 0, 0]`. Layered film percentage of total water (0/50/100). | % | layers |
| `Tsnow` | Default: `[273, 273, 273]`. Snow layer temperature. | K | layers |

### Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `Tg` | Default: `270`. Ground temperature. | K | scalar |
| `mv` | Default: `0.4`. Soil moisture (volumetric). |  | scalar |
| `clayfrac` | Default: `0.3`. Soil clay fraction. |  | scalar |
| `surf_model_setting` | Default: `["QH", 0, 0]`. Surface model: `["QH", q, h]` (passive) or `["OH", rms_cm, cl_over_rms]` (active). |  | 3 |

## Outputs
| Variables/Parameters | Definition | Unit | Size | output_var |
| :------------------- | :-------------------------------------------------------------- | :--- | :--------------------- | :--------- |
| inc_ang      | incident angles                                    | deg  | input incident angles x 1             | 2 passive  |
| Tb_v0     | TB at vertical polarization at incident angle `inc_ang`         | K    | input incident angles x 1                  | 2 passive  |
| Tb_h0     | TB at horizontal polarization at incident angle `inc_ang`       | K    | input incident angles x 1                  | 2 passive  |
| deg0      | Sampling angles of TB in air (different to incident angles)                                   | deg  | angles x 1             | 2 passive  |
| TBv       | TB at vertical polarization at `deg0`                           | K    | deg0 x 1               | 2 passive  |
| TBh       | TB at horizontal polarization at `deg0`                         | K    | deg0 x 1               | 2 passive  |
| vvdb      | Total backscatter VV                                            | dB   | 1 x 1                  | 1 active   |
| vhdb      | Total backscatter VH                                            | dB   | 1 x 1                  | 1 active   |
| hhdb      | Total backscatter HH                                            | dB   | 1 x 1                  | 1 active   |
| hvdb      | Total backscatter HV                                            | dB   | 1 x 1                  | 1 active   |
