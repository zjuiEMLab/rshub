# RSHub 模型调用与 DMRT/VPRT FQA

## RSHub 模型调用速览
- Q: 如何获取 token、准备运行环境？  
  A: 在 https://rshub.zju.edu.cn/Login/# 注册登录后获取个人 token；本地安装 Python>3.8，执行 `pip install rshub`。
- Q: 如何提交任务？  
  A: 用 `submit_jobs.run`，核心字段：`scenario_flag`（soil/snow/veg）、`algorithm`（如 veg:rt，snow:qms/bic）、`output_var`（tb 或 sigma）、`fGHz`、`scatters`（模型参数）、`project_name`、`task_name`、`token`。示例：  
  ```python
  from rshub import submit_jobs
  data = {
      "scenario_flag": "snow",
      "algorithm": "bic",
      "output_var": "tb",
      "fGHz": [9.6, 13.4, 17.2],
      "scatters": {},  # 按下文参数填充
      "project_name": "demo_proj",
      "task_name": "bic_test",
      "token": "YOUR_TOKEN",
      "level_required": 1,
  }
  result = submit_jobs.run(data)
  ```
- Q: 如何查看任务状态与错误？  
  A: `submit_jobs.check_completion(token, project_name, task_name)` 查看状态；失败时用 `load_file(...).load_error_message()` 获取日志。
- Q: 如何下载与查看结果？  
  A: 用 `load_file(token, project_name, task_name, scenario_flag, algorithm, output_var, size_threshold_mb=50).load_outputs()`，大文件自动切换磁盘下载；返回 dict（如 `TU_all`、`theta_obs` 等），按键访问并绘图/后处理。

## DMRT_BIC / DMRT_QMS（土壤/积雪/观测参数）
- Q: 输出类型怎么选？  
  A: `output_var`：`tb` 为亮温，`sigma` 为后向散射系数。
- Q: 观测参数如何设置？  
  A: `fGHz` 频率列表（常用 `[9.6, 13.4, 17.2, 18.7, 37]`，LUT 自动匹配最近值）；`angle`（QMS/BIC）/`deg0inc`(TRI) 为入射角列表（如 `[30, 40, 50]`）。
- Q: 积雪层参数怎么组织？  
  A: 按层提供等长数组：`depth`(cm) / `rho`(g/cm^3) / `Tsnow`(K)。BIC 使用 `zp`(b) 与 `kc`(zeta) 描述粒径分布；QMS 使用 `dia`(粒径, cm) 与 `tau`(stickiness/光学厚度)。层数必须一致。
- Q: 土壤/地表参数有哪些？  
  A: `Tg` 地温；`mv` 土壤含水；`clayfrac` 粘土分数；`epsr_ground_r/i` 自定义介电常数；`surf_model_setting` 控制地表模型与粗糙度（主动: 1 OH + rms 高度 + 相关长度比；被动: 1 Q/H + 粗糙度因子 + 极化混合因子）。
- Q: BIC 和 QMS 有何差异？  
  A: 输出接口一致，但微结构参数不同：BIC 用 `zp`/`kc`，QMS 用 `dia`/`tau`。按可获取的粒径表征选择模型。

## VPRT 模型（土壤/植被/观测参数）
- Q: 观测与模型控制如何设？  
  A: `fGHz` 频率（默认 1.41 GHz）；
- Q: 植被参数如何输入？  
  A: `scatters` 为散射体列表：`[type(1柱/0盘), VM, L, D, betar, density, orientation, distribution, ...]` 按示例顺序填写；`veg_height` 植被层高；`Tveg` 植被温度。散射体顺序与维度需与示例一致。
- Q: 土壤参数怎么设？  
  A: `sm` 土壤含水；`clay` 粘土分数；`perm_soil_r/i` 直接给介电常数时可填；`rmsh` 坡面 RMS 高度；`corlength` 相关长度；`rough_type` 相关函数（1 高斯 / 2 指数）；`Tgnd` 地表温度。
- Q: 结果怎么看？  
  A: `load_outputs` 返回 `TU_all` 亮温矩阵、`theta_obs` 入射角等。确认耦合开关与散射体参数符合场景后再进行反演或对比分析。

## 输入检查与常见陷阱
- Q: 层数或数组长度不一致会怎样？  
  A: `depth`/`rho`/`Tsnow` 等必须等长，否则任务失败或结果不可信。
- Q: 默认粗糙度/模型不合适怎么办？  
  A: DMRT 系列通过 `surf_model_setting` 调整；VPRT 通过 `rough_type`、`rmsh`、`corlength` 微调。
- Q: 如何做参数敏感性？  
  A: 以默认值为基线，逐个修改单一参数（如 `fGHz`、`rho`、`mv`、`rmsh`），观察输出变化，再组合调参。
