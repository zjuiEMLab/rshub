# VPRT Parameters

## Inputs
### Observation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `output_var` | Default: `tb`. Output type: `tb` (passive). Active mode not exposed in the UI. |  | scalar |
| `fGHz` | Default: `1.41`. Frequency. | GHz | scalar |

### Vegetation Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `scatters` | Scatter populations; each entry is a 12-element array. |  | list |
| `veg_height` | Default: `8`. Vegetation height. | m | scalar |
| `Tveg` | Default: `300`. Vegetation temperature. | K | scalar |

### Soil Parameters

| Variables/Parameters | Definition | Unit | Size |
| --- | --- | --- | --- |
| `sm` | Default: `0.1`. Soil moisture (volumetric). |  | scalar |
| `rmsh` | Default: `0.01`. Surface rms height. | m | scalar |
| `corlength` | Default: `0.1`. Surface correlation length. |  | scalar |
| `clay` | Default: `0.19`. Soil clay fraction. |  | scalar |
| `rough_type` | Default: `2`. Roughness model: `1` Gaussian, `2` Exponential. |  | scalar |
| `Tgnd` | Default: `300`. Ground temperature. | K | scalar |

Each `scatters` entry is ordered as:

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

## Outputs
| Variables/Parameters | Definition | Unit | Size |
| :------------------- | :----------------------------- | :---- | :------------------------------------- |
| TU_all     | Brightness temperature         | K     | polarizations x incident angles        |
| theta_obs  | Incident angles                | deg   | 1 x incident angles    
