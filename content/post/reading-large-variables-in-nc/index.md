---
title: 读取nc文件中的变量时内存不足
date: '2024-09-10'
summary: Reading large variables from nc file in chunks in Matlab
tags:
  - Matlab
toc: false
---

## 一、遇到的问题
当我读取这个变量时，Matlab 显示有关内存不足的错误。

    Error using matlab.internal.imagesci.netcdflib
    Requested 360x360x159x121 (9.3GB) array exceeds maximum array size preference (8.0GB). 
    This might cause MATLAB to become unresponsive.

当我们说一个变量是`single`类型时，这意味着该变量在内存中占用 4 个字节的空间。 

    1KB = 1024 byte
    1MB = 1024 KB
    1GB = 1024 MB
    1TB = 1024 GB

因此，维度为 360x360x159x121 的`single`类型变量意味着其大小为：

    360*360*159*121*4 / (1024*1024*1024) = 9.3 GB

而我的笔记本电脑的内存只有8GB。

## 二、解决方案——分块读取

使用 `ncread` 的 `start` 和 `count` 参数分块读取数据，这样可以避免一次性将整个变量加载到内存中。`start` 指定读取的起始位置，`count` 指定读取的大小。例如：

```matlab
filename = 'uvtr_hour1-002.nc'; 
varname = 'tr17_1';  % 维度为360*360*159*121，我们需要计算得到一个 360*360*121的变量，即在第三维度进行积分。

% 获取变量的信息
info = ncinfo(filename, varname);

% 定义分块读取的大小
% 维度为360*360*159*30、类型为single的变量大小为 2.3 GB，可以接受
blockSize = [360, 360, 159, 30];  % 根据自己电脑的实际情况设置每次读取的大小

% 初始化空数组以存储结果
data_single = single(zeros(info.Size));

% 读取的开始位置
start = [1, 1, 1, 1];

% 循环读取整个数据集
for i = 1:blockSize(3):info.Size(3)
    % 读取数据块
    tempData = ncread(filename, varname, start, count);
end

```

