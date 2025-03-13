---
title: 自动下载并读取 GEOS-FP数据中的10米风速
date: '2025-03-12'
summary: Automatically downloads and reads 10-meter wind speeds from GEOS-FP data.
---

[GEOS-FP数据门户](https://portal.nccs.nasa.gov/datashare/gmao/geos-fp/das/)提供了包括`tavg1_2d_slv_Nx` 在内的多种产品。

## 命名规则
- **tavg1**：表示 **1 小时平均**（Time-averaged over 1 hour）。
- **2d**：表示 **二维变量**（2D variables），即地表或接近地表的大气变量。
- **slv**：表示 **地表变量**（Surface and near-surface variables），包括温度、风速、降水、湿度等。
- **Nx**：表示 **非插值网格（Non-interpolated grid）**，即数据在模型网格上直接输出，没有额外的插值处理。

## 主要变量
`tavg1_2d_slv_Nx` 产品包含多个气象变量，以下是一些常见变量：

| 变量名称 | 描述 |
|----------|------|
| `T2M`    | 2米气温（Surface Air Temperature, K） |
| `U10M`   | 10米东西向风速（10-meter U-component Wind, m/s） |
| `V10M`   | 10米南北向风速（10-meter V-component Wind, m/s） |
| `PS`     | 地表气压（Surface Pressure, Pa） |
| `RH2M`   | 2米相对湿度（Relative Humidity, %） |
| `PRECTOT` | 总降水率（Precipitation Rate, mm/s） |
| `QV2M`   | 2米比湿（Specific Humidity, kg/kg） |

## 代码

```
#!/usr/bin/env python
# coding: utf-8
# Date Created: 2024-11-20
# Author: Zhipeng Pei (zhipeng.pei@whu.edu.cn)
# Description:测试自动下载并读取 GEOS-FP数据中的10米风速,分辨率为 0.25 （纬度）×0.3125 （经度）

import os
import numpy as np
import xarray as xr
import requests
import matplotlib.pyplot as plt
from datetime import datetime, timedelta

def download_nc_file_with_progress(url, local_path):
    """
    下载文件并实时显示进度。
    如果文件已存在，则直接使用。

    :param url: 文件URL
    :param local_path: 本地保存路径
    """
    os.makedirs(os.path.dirname(local_path), exist_ok=True)

    if os.path.exists(local_path):
        print(f"文件已存在，直接使用: {local_path}")
        return

    response = requests.get(url, stream=True)
    if response.status_code != 200:
        raise ValueError(f"下载失败: {url}，状态码: {response.status_code}")

    total_size = int(response.headers.get('Content-Length', 0))
    print(f"正在下载文件: {url}")
    print(f"文件大小: {total_size / (1024 * 1024):.2f} MB\n")

    with open(local_path, "wb") as f:
        downloaded = 0
        for chunk in response.iter_content(chunk_size=1024 * 1024):  # 1MB per chunk
            if chunk:
                f.write(chunk)
                downloaded += len(chunk)
                percent = (downloaded / total_size) * 100
                print(f"\r下载进度: {percent:.2f}%", end='')

    print("\n下载完成。\n")

def get_nearest_wind_speed_tavg(lon, lat, year, month, day, hour, minute):
    """
    获取全球U10M、V10M风场，以及指定位置的最近10m平均风速（tavg1_2d_slv_Nx产品）

    :param lon: 经度 (float)
    :param lat: 纬度 (float)
    :param year: 年 (int)
    :param month: 月 (int)
    :param day: 日 (int)
    :param hour: 小时 (int)
    :param minute: 分钟 (int)
    :return:
        global_u10m: 全球U10M风速场 (2D array)
        global_v10m: 全球V10M风速场 (2D array)
        nearest_point_wind_speed: 指定点的10米平均风速 (float)
        nearest_time: 最近的时间点 (datetime)
        lons: 经度数组
        lats: 纬度数组
    """

    # 1. 计算最近的30分钟时刻
    target_time = datetime(year, month, day, hour, minute)

    if minute <= 30:
        nearest_time = target_time.replace(minute=30)
    else:
        nearest_time = target_time.replace(minute=30) + timedelta(hours=1)

    nearest_year = nearest_time.year
    nearest_month = nearest_time.month
    nearest_day = nearest_time.day
    nearest_hour = nearest_time.hour
    nearest_minute = nearest_time.minute

    # 2. 构建tavg1_2d_slv_Nx文件名和URL
    date_str = f"{nearest_year}{nearest_month:02d}{nearest_day:02d}"
    time_str = f"{nearest_hour:02d}{nearest_minute:02d}"
    product_str = "tavg1_2d_slv_Nx"

    file_name = f"GEOS.fp.asm.{product_str}.{date_str}_{time_str}.V01.nc4"
    file_url = (
        f"https://portal.nccs.nasa.gov/datashare/gmao/geos-fp/das/"
        f"Y{nearest_year}/M{nearest_month:02d}/D{nearest_day:02d}/{file_name}"
    )
    local_path = os.path.join("geos_fp_data", file_name)

    # 3. 下载文件（已有则跳过）
    download_nc_file_with_progress(file_url, local_path)

    # 4. 读取数据
    ds = xr.open_dataset(local_path)

    # 5. 读取全球U10M和V10M
    global_u10m = ds['U10M'].squeeze().values  # 自动去掉单一维度 (1, 721, 1152) -> (721, 1152)
    global_v10m = ds['V10M'].squeeze().values  # 自动去掉单一维度 (1, 721, 1152) -> (721, 1152)
    lons = ds['lon'].values
    lats = ds['lat'].values

    # 6. 找到最近的经纬度网格点
    lon_idx = np.abs(lons - lon).argmin()
    lat_idx = np.abs(lats - lat).argmin()

    # 7. 读取指定位置的风速，并转换为标量
    nearest_point_wind_speed = ds['U10M'].isel(lon=lon_idx, lat=lat_idx).squeeze().values

    ds.close()

    return global_u10m, global_v10m, lons, lats, nearest_point_wind_speed, nearest_time

# 示例调用
if __name__ == "__main__":
    lon = -179
    lat = 90
    year = 2025
    month = 3
    day = 1
    hour = 0
    minute = 56

    (global_u10m, global_v10m, lons, lats, nearest_point_wind_speed,
     nearest_time) = get_nearest_wind_speed_tavg(lon, lat, year, month, day, hour, minute)

    print(f"最近的30分钟时间点: {nearest_time}")
    print(f"最近的10米平均风速: {nearest_point_wind_speed:.2f} m/s")

    # 可视化U10M全球风场
    plt.figure(figsize=(12, 6))
    plt.pcolormesh(lons, lats, global_u10m, shading='auto', cmap='jet',vmin=-2, vmax=2)
    plt.colorbar(label='U10 (m/s)')
    plt.xlim(110, 114)
    plt.ylim(25, 30)
    plt.xlabel('Longitude')
    plt.ylabel('Latitude')
    plt.title(f'Global 10-meter eastward wind at {nearest_time}')
    plt.grid(True)
    plt.show()
```
