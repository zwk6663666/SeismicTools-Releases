# Seismic Tools

面向结构抗震研究的桌面工具（PySide6 + Matplotlib），涵盖动力分析与拟静力试验数据处理。

![Seismic Tools 主界面](docs/images/03_wave_spectrum.png)

## 功能

| 分组 | 功能 |
| --- | --- |
| 动力 | **Response Spectrum**：按 GB 50111-2006、JTG/T 2231-01-2020、GB 50011-2010（2016 年版）生成设计加速度反应谱 |
| | **Wave Response Spectrum**：地震波 SA、SV、SD、PSA、PSV 反应谱与三联谱，支持多阻尼比 |
| | **Amplitude Seismic Wave**：地震波调幅至目标峰值加速度，可缩放时间轴 |
| | **Ground Motion Params**：35 项地震动参数（峰值、强度、持时、谱强度、频率参数） |
| 拟静力 | **Hysteresis Analysis**：滞回曲线分圈、割线刚度、残余位移、耗能、等效粘滞阻尼比、滞回环拆解 |
| | **Skeleton Curve**：骨架曲线提取，五种方法计算等效屈服点、峰值点、极限点与延性系数 |
| | **Hysteresis Correction**（试验功能）：按三种竖向加载装置修正 P-Δ 效应对滞回曲线的影响，对比修正前后的特征值 |
| | **Test Curve Processing**（试验功能）：检测并剔除位移时程曲线中的等待段（水平段），对比处理前后的滞回曲线与时程曲线 |
| 桩基础 | **Pile Data**：桩截面、土层、桩位、承台与荷载输入，立面图与平面图，JSON 工程文件，算例 |
| | **Bearing Capacity**：单桩轴向容许承载力（八种方法）、桩身材料验算、群桩效率与实体基础验算 |
| | **Pile Forces**：m 法计算承台位移、桩顶内力以及桩身弯矩、剪力、位移与土抗力分布，影响函数可查表或直接计算 |
| | **Spring Stiffness**：群桩与单桩的六自由度刚度矩阵，桩身、桩侧、桩端分布弹簧刚度，用于有限元桩基模拟 |
| | 桩基页面可导出 PDF 计算书：含封面、目录、计算过程（公式由 LaTeX 排版）与计算图示，跟随界面语言 |

所有图形可右键导出为图片（PNG/TIFF/SVG/PDF 等，可设置尺寸、分辨率与样式），所有表格可右键导出数据，多曲线页面支持一键批量导出。

## 文档与界面语言

程序内置用户手册，支持中英文界面切换。

## 其他版本

C++ 版本（Qt 6）：[SeismicToolsCpp-Releases](https://github.com/zwk6663666/SeismicToolsCpp-Releases/releases)

## 作者

Weike Zhang（zwk6663666@gmail.com）
