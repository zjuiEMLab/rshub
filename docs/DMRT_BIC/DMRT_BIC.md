# DMRT_BIC Parameters

## Inputs
Array inputs can be comma-separated values (e.g., "20,20,20").

### Observation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive) or `sigma` (active). |  | scalar |
| `fGHz` | Default: `37`. Frequency; accepts arrays. | GHz | scalar/array |
| `angle` | Default: `40`. Incident angle; accepts arrays. | deg | scalar/array |

### Snow Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `depth` | Default: `[20, 20, 20]`. Snow layer thickness. | cm | layers |
| `rho` | Default: `[0.3, 0.3, 0.3]`. Snow density per layer. | g/cm^3 | layers |
| `kc` | Default: `[10000, 10000, 10000]`. Layered zeta parameter. |  | layers |
| `zp` | Default: `[1.2, 1.2, 1.2]`. Layered b parameter. |  | layers |
| `Tsnow` | Default: `[260, 260, 260]`. Snow layer temperature. | K | layers |

## Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `Tg` | Default: `270`. Ground temperature. | K | scalar |
| `mv` | Default: `0.15`. Soil moisture (volumetric). |  | scalar |
| `clayfrac` | Default: `0.3`. Soil clay fraction. |  | scalar |
| `surf_model_setting` | Default: `["QH", 0, 0]`. Surface model: `["QH", q, h]` (passive) or `["OH", rms_cm, cl_over_rms]` (active). |  | 3 |

## Outputs
| Variables/Parameters | Definition | Unit | Size | output_var |
| :------------------- | :---------------------------------------------------------- | :--- | :--------------------- | :--------- |
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
