---
title: Reading column density of dry air from MERRA-2
date: '2025-03-12'
summary: 读取MERRA-2数据，并计算干空气柱密度
---



[M2I3NVASM](https://disc.gsfc.nasa.gov/datasets/M2I3NVASM_5.12.4/summary) (or inst3_3d_asm_Nv) is an instantaneous 3-dimensional 3-hourly data collection in Modern-Era Retrospective analysis for Research and Applications version 2 (MERRA-2).

```

import xarray as xr
import numpy as np

# 读取 MERRA-2 数据文件
ds = xr.open_dataset("/home/wrfshared/WRF_Pacey/Data/MERRA2_400.inst3_3d_asm_Nv.20250101.nc4")  # 你的文件路径

# 读取变量
PS = ds["PS"].values  # Surface Pressure (Pa), shape: (time, lat, lon)
PL = ds["PL"].values  # Pressure Levels (Pa), shape: (time, level, lat, lon), PL 维度顺序从高空到地表
QV = ds["QV"].values  # Specific Humidity (kg/kg), shape: (time, level, lat, lon)


# 计算 ΔP（层间压力厚度）
delta_P = np.diff(PL, axis=1)  # Shape: (time, levels-1, lat, lon)

# 计算底层压力厚度（使用地表气压 PS）
delta_P_surface = PS - PL[:, -1, :, :]   # 计算地表层 ΔP

# 将 delta_P_surface 添加到 delta_P
delta_P = np.concatenate([delta_P, delta_P_surface[:, np.newaxis, :, :]], axis=1)  # 变为 (time, levels, lat, lon)

# 计算干空气柱密度 (kg/m²)
g = 9.81  # 重力加速度 (m/s²)
column_dry_air_mass = np.sum((delta_P / g) * (1 - QV), axis=1)  # sum over levels

# 计算干空气柱摩尔数 (mol/m²)
M_dry = 0.0289647  # kg/mol
column_dry_air_moles = column_dry_air_mass / M_dry

# 输出结果
print(f"Column Dry Air Mass (kg/m²): {column_dry_air_mass.mean()}") # 全球平均：9845 kg/m²
print(f"Column Dry Air Moles (mol/m²): {column_dry_air_moles.mean()}")  # 全球平均：339917 mol/m²

```


