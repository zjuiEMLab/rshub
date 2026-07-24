# DDA_TRI Parameters

## Inputs

### `parameters.json`

Only parameters that actually participate in the current calculation are listed below.

| Parameter | Definition | Unit | Size |
| --- | --- | --- | --- |
| `fGHz` | Microwave frequency. | GHz | scalar |
| `angle` | Incidence angle. | deg | scalar |
| `Lx` | Simulation-domain length in the x direction. | m | scalar |
| `Ly` | Simulation-domain length in the y direction. | m | scalar |
| `Lz` | Simulation-domain depth in the z direction. | m | scalar |
| `xr` | Position of the left x boundary. | m | scalar |
| `yr` | Position of the back y boundary. | m | scalar |
| `zr` | Position of the bottom z boundary. | m | scalar |
| `d` | Spatial discretization resolution. | m | scalar |
| `epsr_g_re` | Real part of the ground relative permittivity. |  | scalar |
| `epsr_g_im` | Imaginary part of the ground relative permittivity. |  | scalar |
| `Tg` | Ground temperature used in the transmitted brightness-temperature contribution. | K | scalar |
| `Ts` | Top layer medium temperature (dummy variable, may not be used). | K | scalar |
| `nr` | Number of medium realizations generated and combined in post-processing. |  | scalar/integer |
| `tol` | GMRES convergence tolerance. |  | scalar |
| `rest` | GMRES restart parameter. |  | scalar/integer |
| `maxiter` | Maximum GMRES iteration count. |  | scalar/integer |

### Soil profile file (from bottom to top)

| Parameter | Definition | Unit |
| --- | --- | --- |
| soilType | Roughness correlation type: `1` for Gaussian and `2` for exponential. |  |
| RMSHeight | RMS roughness height. | m |
| CLx | Correlation length in the x direction. | m |
| CLy | Correlation length in the y direction. | m |
| layerZaxis | layer (top) elevation parameter (from bottom to top). | m |
| epsr_re | Real part of layer relative permittivity. |  |
| epsr_im | Imaginary part of layer relative permittivity. |  |
| Tb | Layer physical temperature used in layer-emission calculations. | K |

## Outputs

### Active output

Output file: `Active_fGHz{fGHz}_ob_angle{angle}.h5`

The polarization order is **VV, HV, VH, HH**.

| Variable | Definition | Unit | Size |
| --- | --- | --- | --- |
| `backscatter` | Total bistatic scattering coefficients in the backscattering direction, `gamma_back` (linear scale), in VV, HV, VH, HH order. |  | `4 x 1` |
| `backscatter_angle` | Backscattering angles and angle errors. | deg | scalar struct |
| `thetas` | Scattering polar angles. | deg | `nTheta x 1` |
| `phis` | Scattering azimuth angles. | deg | `nPhi x 1` |
| `g_tot` | Total bistatic scattering coefficients. | dB | `nTheta x nPhi x 4` |
| `g_coh` | Coherent bistatic scattering coefficients. | dB | `nTheta x nPhi x 4` |
| `g_inc` | Incoherent bistatic scattering coefficients. | dB | `nTheta x nPhi x 4` |
| `theta_xoz` | Scattering angles in the incidence plane. | deg | `2*nTheta x 1` |
| `g_tot_inxozVV` | Total VV bistatic coefficients in the incidence plane. |  | `2*nTheta x 1` |
| `g_tot_inxozHV` | Total HV bistatic coefficients in the incidence plane. |  | `2*nTheta x 1` |
| `g_tot_inxozVH` | Total VH bistatic coefficients in the incidence plane. |  | `2*nTheta x 1` |
| `g_tot_inxozHH` | Total HH bistatic coefficients in the incidence plane. |  | `2*nTheta x 1` |
| `info_json` | JSON metadata for the outputs. |  | scalar string |

### Passive output


Output file: `Passive_fGHz{fGHz}_ob_angle{angle}.h5`

| Variable | Definition | Unit | Size |
| --- | --- | --- | --- |
| `Tb` | Mean V- and H-polarized brightness temperatures. | K | `2 x 1` |
| `info_json` | JSON metadata for the outputs. |  | scalar string |
