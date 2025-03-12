---
title: How to overlay local raster file on a basemap using Python
date: '2024-10-13'
summary: 使用python在底图上叠加栅格
---


[Basemap API](https://matplotlib.org/basemap/stable/index.html)
```
# https://matplotlib.org/basemap/stable/index.html
import rioxarray as rxr
import os
import matplotlib.pyplot as plt
import numpy as np
from mpl_toolkits.basemap import Basemap

original_raster = rxr.open_rasterio('/Users/peisipand/Downloads/temperature/test.tif') # crs of this tif should be WGS 84
# original_raster.plot()
raster = original_raster[0]
test = raster.data
raster.data[raster.data == -9999] = np.nan
print(original_raster.rio.crs)
lon,lat = np.meshgrid(raster.x, raster.y)
# 定义宽度和高度的尺寸（以厘米为单位）
width_cm = 17
height_cm = 14
# 将厘米转换为英寸
width_inch = width_cm / 2.54
height_inch = height_cm / 2.54
# 创建一个指定尺寸的图形（以英寸为单位）
fig = plt.figure(figsize=(width_inch,height_inch))
# 更新全局字体大小为 8
plt.rcParams.update({'font.size': 8})
# Create the basemap
# width=12000000: 指地图的宽度为 12,000,000 米（即 12,000 公里）
# height=9000000: 指地图的高度为 9,000,000 米（即 9,000 公里）。
m = Basemap(width=12000000,height=9000000,projection='lcc',
            resolution='c',lat_1=45.,lat_2=55,lat_0=50,lon_0=-107.)
# Draw some elements
m.drawcoastlines()
m.drawcountries()
m.drawstates()
m.shadedrelief()
# Draw gridlines
parallels = range(-60, 81, 20)
m.drawparallels(parallels, labels=[True, False, False, False])
meridians = range(-180, 181, 20)
m.drawmeridians(meridians, labels=[False, False, False, True])
# # Plot the raster
x, y = m(*np.meshgrid(raster.x, raster.y))
cs = m.pcolormesh(x, y, raster.data, cmap='jet', alpha=0.8,vmin=0, vmax=30)
cbar = m.colorbar(cs,location='bottom',pad=0.5)
cbar.set_label('Temperature (C)')
plt.title('title')
plt.savefig('test.png',dpi=300)
plt.show()

```


