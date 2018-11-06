# 人脸ROI区域可视化工具
## 版本维护
|Version|Date|Info|
|:--:|:--:|:--:|
|1.1|20181106|纠正错误&添加ROI框着色、后缀信息
## 简介
配合自动化测试框架结果使用，将人脸检测结果与请求人脸识别的输入情况进行可视化，先将测试集原始图像(.nv21)转换为jpg，并在此基础上绘制人脸ROI区域以供分析
### 参数配置
- 自动化测试结果目录(logFolder)
- 测试集根目录(dataFolder)
- 测试图像集三级子目录(level3Folder)
- PC端ROI图存储子目录(rectsFolder)
- PC端带ROI区域原图存储子目录(fullImgFolder)
- 人名配对字典(nameMap)
- 人名分级文件夹(objs)
- 测试集图片尺寸(cols, rows)
- PC端存图根目录(dstImgRoot)
- 测试集图片后缀(suffix)
- 测试图片数据量验证(fileBytes)
- PC端opencv存图设置(cvImgSaver)
### 自动化测试结果目录描述
```
.
├──TestResults
	└── guangming
	 	├── 09-27-18-39.txt
	 	├── 09-27-18-40.txt
	 	├── 09-27-18-46.txt
```
### 测试图片集目录描述
```
.
├── guangming
    └── cameraData
        ├── 0906164442
            ├── 16-44-42-739.nv21
            ├── 16-44-42-755.nv21
            ├── 16-44-42-784.nv21
```
### PC端存图目录描述
从下述的目录描述可知，在单一个体(人名)的情况下，最终会产生3个文件夹

cameraData
: 带ROI区域的全图 与 ROI子图 共同存放

FullImage
: 仅存放 带ROI区域的全图

Rects
: 仅存放ROI子图
```
.
├── guangming
│   ├── cameraData
│   │   ├── 0906164442
│   │   │   ├── XX-XX-XX-XX0.jpg
│   │   │   ├── XX-XX-XX-XX0_D.jpg
│   │   │   ├── XX-XX-XX-XX0_I.jpg
│   │   │   ├── XX-XX-XX-XX1.jpg
│   │   │   ├── XX-XX-XX-XX1_D.jpg
│   │   │   ├── XX-XX-XX-XX1_E.jpg
│   │   │   ├── XX-XX-XX-XX2.jpg
│   │   │   ├── XX-XX-XX-XX2_D.jpg
│   │   │   ├── XX-XX-XX-XX2_R.jpg
        ...
│   │   ├── 0906164555
│   │   │   ├── XX-XX-XX-XX0.jpg
│   │   │   ├── XX-XX-XX-XX0_D.jpg
│   │   │   ├── XX-XX-XX-XX0_I.jpg
│   │   │   ├── XX-XX-XX-XX1.jpg
│   │   │   ├── XX-XX-XX-XX1_D.jpg
│   │   │   ├── XX-XX-XX-XX1_E.jpg
│   │   │   ├── XX-XX-XX-XX2.jpg
│   │   │   ├── XX-XX-XX-XX2_D.jpg
│   │   │   ├── XX-XX-XX-XX2_R.jpg
        ...
...
│   ├── FullImage
│   │   ├── 0906164442
│   │   │   ├── XX-XX-XX-XX0.jpg
│   │   │   ├── XX-XX-XX-XX1.jpg
        ...
│   │   ├── 0906164555
│   │   │   ├── XX-XX-XX-XX0.jpg
│   │   │   ├── XX-XX-XX-XX1.jpg
        ...
...
│   └── Rects
│   │   ├── 0906164442
│   │   │   ├── XX-XX-XX-XX0_D.jpg
│   │   │   ├── XX-XX-XX-XX0_I.jpg
│   │   │   ├── XX-XX-XX-XX1_D.jpg
│   │   │   ├── XX-XX-XX-XX1_E.jpg
│   │   │   ├── XX-XX-XX-XX2_D.jpg
│   │   │   ├── XX-XX-XX-XX2_R.jpg
        ...
│       ├── 0906164555
│   │   │   ├── XX-XX-XX-XX0_D.jpg
│   │   │   ├── XX-XX-XX-XX0_I.jpg
│   │   │   ├── XX-XX-XX-XX1_D.jpg
│   │   │   ├── XX-XX-XX-XX1_E.jpg
│   │   │   ├── XX-XX-XX-XX2_D.jpg
│   │   │   ├── XX-XX-XX-XX2_R.jpg
        ...
...
```
### PC端存图-ROI着色及子图后缀描述

白框( _D后缀 )
:  此ROI框体由人脸检测结果生成

黄框( _I后缀 )
:  此ROI框体由 无效的人脸识别结果 生成

绿框( _R后缀 )
:  此ROI框体由正确的人脸识别结果生成

红框( _E后缀 )
:  此ROI框体由错误的人脸识别结果生成


*[无效的人脸识别结果]: 识别算法认为当前的输入图像并不包含人脸
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc2MTQwNzI0MV19
-->