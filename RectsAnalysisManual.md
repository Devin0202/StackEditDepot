# 人脸ROI区域可视化工具
## 版本维护
|Version|Date|Info|
|:--:|:--:|:--:|
|1.0|20180929|内部初稿|
## 简介
配合自动化测试框架结果使用，将人脸检测结果与请求人脸识别的输入情况进行可视化，先将测试集原始图像(.nv21)转换为jpg，并在此基础上绘制人脸ROI区域以供分析
### 参数配置
- 测试集根目录(dataFolder)
- 人名分级文件夹(objs)
- 自动化测试结果目录(logFolder)
- 测试图像集三级子目录(level3Folder)
- PC端存图根目录(dstImgRoot)
- PC端ROI图存储子目录(rectsFolder)
- PC端带ROI区域原图存储子目录(fullImgFolder)
- 人名配对字典(nameMap)
- 测试集图片尺寸(cols, rows)
- 测试集图片后缀(suffix)
- PC端opencv存图设置(cvImgSaver)
### 自动化测试结果目录描述
```
.
├──TestResults
│	└── guangming
│   │	├── 09-27-18-39.txt
│   │	├── 09-27-18-40.txt
│   │	├── 09-27-18-46.txt
```
### 测试图片集目录描述
```
.
├── guangming
│   └── cameraData
│       ├── 0906164442
│       │   ├── 16-44-42-739.nv21
│       │   ├── 16-44-42-755.nv21
│       │   ├── 16-44-42-784.nv21
```
### PC端存图目录描述
```
.
├── guangming
│   ├── cameraData
│   │   ├── 0906164442
        │   ├── XX-XX-XX-XXX_D.jpg
        │   ├── XX-XX-XX-XXX_I.jpg
        ...
│   │   ├── 0906164555
        │   ├── XX-XX-XX-XXX_D.jpg
        │   ├── XX-XX-XX-XXX_I.jpg
        ...
...
│   ├── FullImage
│   │   ├── 0906164442
        │   ├── XX-XX-XX-XXX_D.jpg
        │   ├── XX-XX-XX-XXX_I.jpg
        ...
│   │   ├── 0906164555
        │   ├── XX-XX-XX-XXX_D.jpg
        │   ├── XX-XX-XX-XXX_I.jpg
        ...
...
│   └── Rects
│       ├── 0906164442
        │   ├── XX-XX-XX-XXX_D.jpg
        │   ├── XX-XX-XX-XXX_I.jpg
        ...
│       ├── 0906164555
        │   ├── XX-XX-XX-XXX_D.jpg
        │   ├── XX-XX-XX-XXX_I.jpg
        ...
...
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcwMzQyNTA1OV19
-->