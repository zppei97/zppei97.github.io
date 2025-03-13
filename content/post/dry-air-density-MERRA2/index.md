---
title: Reading column density of dry air from MERRA-2
date: '2025-03-12'
summary: 读取MERRA-2数据，并计算干空气柱密度
---

MERRA-2（Modern-Era Retrospective Analysis for Research and Applications, Version 2）提供了多个时间分辨率的数据产品，其中 `tavg3_3d_asm_Nv` 和 `inst3_3d_asm_Nv` 主要区别在于 **时间平均方式**。

## 命名规则
- **tavg3**：表示 **3 小时平均**（Time-averaged over 3 hours）。
- **inst3**：表示 **3 小时的瞬时值**（Instantaneous 3-hourly）。
- **3d**：表示 **三维变量**（3D variables），即整个大气柱的数据。
- **asm**：表示 **分析系统**（Analysis System），指代 MERRA-2 的大气分析数据。
- **Nv**：表示 **模型层数据**（Model vertical levels），数据以模式层级（sigma 级）提供，而非固定气压层。

## 主要区别
| 数据产品 | 含义 | 主要特点 |
|----------|------|---------|
| `tavg3_3d_asm_Nv` | 3 小时平均值 | 表示过去 3 小时的平均状态，适用于分析趋势和气候统计。 |
| `inst3_3d_asm_Nv` | 3 小时瞬时值 | 表示数据记录时刻的真实状态，适用于研究短时间尺度的变化。 |

## 主要变量
这两个产品都包含多个 **三维大气变量**，以下是一些常见变量：

| 变量名称 | 描述 |
|----------|------|
| `T`      | 气温（Temperature, K） |
| `U`      | 东西向风速（Eastward Wind, m/s） |
| `V`      | 南北向风速（Northward Wind, m/s） |
| `OMEGA`  | 垂直速度（Vertical Velocity, Pa/s） |
| `QV`     | 比湿（Specific Humidity, kg/kg） |
| `RH`     | 相对湿度（Relative Humidity, %） |
| `H`      | 位势高度（Geopotential Height, m） |

## 代码
这里以[inst3_3d_asm_Nv](https://disc.gsfc.nasa.gov/datasets/M2I3NVASM_5.12.4/summary)为例，给出了下载数据及计算干空气柱质量的代码

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


