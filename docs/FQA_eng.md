# RSHub Model Use & DMRT/VPRT FAQ

## RSHub Quick Start
- Q: How do I get a token and set up the environment?  
  A: Register and log in at https://rshub.zju.edu.cn/Login/# to obtain your personal token; install Python > 3.8 locally and run `pip install rshub`.
- Q: How do I submit a job?  
  A: Use `submit_jobs.run`. Key fields: `scenario_flag` (soil/snow/veg), `algorithm` (e.g., veg: rt; snow: qms/bic), `output_var` (tb or sigma), `fGHz`, `scatters` (model parameters), `project_name`, `task_name`, `token`. Example:  
  ```python
  from rshub import submit_jobs
  data = {
      "scenario_flag": "snow",
      "algorithm": "bic",
      "output_var": "tb",
      "fGHz": [9.6, 13.4, 17.2],
      "scatters": {},  # fill with parameters below
      "project_name": "demo_proj",
      "task_name": "bic_test",
      "token": "YOUR_TOKEN",
      "level_required": 1,
  }
  result = submit_jobs.run(data)
  ```
- Q: How do I check job status and errors?  
  A: Use `submit_jobs.check_completion(token, project_name, task_name)` for status; on failure call `load_file(...).load_error_message()` for logs.
- Q: How do I download and view results?  
  A: Call `load_file(token, project_name, task_name, scenario_flag, algorithm, output_var, size_threshold_mb=50).load_outputs()`. Large files switch to disk download automatically; returns a dict (e.g., `TU_all`, `theta_obs`), access by keys for plotting/post-processing.

## DMRT_BIC / DMRT_QMS (Soil / Snow / Observation Parameters)
- Q: How to choose output type?  
  A: `output_var`: `tb` for brightness temperature, `sigma` for backscatter.
- Q: How to set observation parameters?  
  A: `fGHz` frequency list (common `[9.6, 13.4, 17.2, 18.7, 37]`; LUT matches nearest); `angle`/`deg0inc` incidence angle list (e.g., `[30, 40, 50]`).
- Q: How to organize snow layer parameters?  
  A: Provide equal-length arrays per layer: `depth` (cm) / `rho` (g/cm^3) / `Tsnow` (K). BIC uses `zp` (b) and `kc` (zeta) for grain distribution; QMS uses `dia` (grain size, cm) and `tau` (stickiness/optical depth). Layer counts must match.
- Q: What soil/surface parameters are available?  
  A: `Tg` ground temperature; `mv` soil moisture; `clayfrac` clay fraction; `epsr_ground_r/i` custom dielectric; `surf_model_setting` controls surface model and roughness (active: 1 OH / 2 SPM3D / 3 NMM3D LUT + rms height + correlation length ratio; passive: 1 Q/H + roughness factor + polarization mixing factor).
- Q: When to use LUT vs high-accuracy computation?  
  A: For BIC, `lut_flag=1` uses LUT (fv, kc, zp, fGHz within supported ranges, nearest match); `lut_flag=0` calls NMM3D explicit computation (slower, more precise). For finer grids, tune integration/discretization params (e.g., `Nquad`).
- Q: Differences between BIC and QMS?  
  A: Outputs are consistent; microstructure differs: BIC uses `zp`/`kc`, QMS uses `dia`/`tau`. Choose based on available grain descriptors.

## VPRT Model (Soil / Vegetation / Observation Parameters)
- Q: How to set observation and model controls?  
  A: `fGHz` frequency (default 1.41 GHz); `Flag_coupling`=1 enables volume–surface coupling; `Flag_forced_cal`=1 forces recalculation; `err` is the convergence tolerance.
- Q: How to input vegetation parameters?  
  A: `scatters` is a list of scatterers: `[type (1 cylinder / 0 disc), VM, L, D, betar, density, orientation?, distribution, ...]` filled in the given order; `veg_height` vegetation height; `Tveg` vegetation temperature. Keep order/dimensions consistent with the example.
- Q: How to set soil parameters?  
  A: `sm` soil moisture; `clay` clay fraction; `perm_soil_r/i` for direct dielectric assignment; `rmsh` RMS height; `corlength` correlation length; `rough_type` correlation function (1 Gaussian / 2 Exponential); `Tgnd` ground temperature.
- Q: How to read results?  
  A: `load_outputs` returns `TU_all` (brightness temperature matrix), `theta_obs` (incident angles), etc. Confirm coupling switch and scatterer settings match your scene before inversion or comparison.

## Input Checks & Common Pitfalls
- Q: What if layer counts or array lengths differ?  
  A: Arrays like `depth`/`rho`/`Tsnow` must be the same length; otherwise jobs fail or results are unreliable.
- Q: What if default roughness/model is unsuitable?  
  A: DMRT series: adjust `surf_model_setting`; VPRT: tune `rough_type`, `rmsh`, `corlength`, and enable `Flag_coupling` if needed.
- Q: How to do parameter sensitivity?  
  A: Use defaults as baseline, vary one parameter at a time (e.g., `fGHz`, `rho`, `mv`, `rmsh`), observe outputs, then adjust combinations.
