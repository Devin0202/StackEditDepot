# 测试集说明
## 版本维护
|Version|Date|UpdateInfo|
|:--:|:--:|:--:|
|1.0|20190304|初稿|
## 简介

可用于人脸检测与识别的双人测试图像序列

序列具有时序性，但不保证双人脸同时出现

## 基本描述
|主体对象|数目|分辨率|格式|标签文件|
|:--:|:--:|:--:|:--:|:--:|
|人体|2|1280 * 720|Jpg|有

## 文件(夹)组织
```
.
sunhaiyan-X-daiyi
└── cameraData
    ├── 0222102558
    │   ├── 10-25-58-000.jpg
    │   ├── 10-25-58-030.jpg
    │   ├── 10-25-58-060.jpg
    │   ├── 10-25-58-090.jpg
    │   ├── 10-25-58-120.jpg
    │   ├── ...
```
一级目录："sunhaiyan-X-daiyi"

二级目录："cameraData"

三级目录："0222102558"

Eg: 10-25-58-000.jpg 为图片文件

## 标签文件说明
<font color="#ff00ff">**DoubleSet1m.txt
DoubleSet2m.txt
DoubleSet3m.txt**</font>
每个txt对应不同距离，非精确
txt中有3个属性
 - Case: 文件夹名称，Eg: 0222102558
 - Left: 左侧人物
 - Right: 右侧人物


*[sunhaiyan-X-daiyi]: 命名规则为两人姓名

*[0222102558]: 命名规则为 月日时分秒

*[10-25-58-000.jpg]: 命名规则为 时分秒毫秒

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcyNTQ2MDc1N119
-->
