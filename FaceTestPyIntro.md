# 人脸项目Python使用说明
## 版本维护
|Version|Date|Info|
|:--:|:--:|:--:|
|1.1|20181017|增加内部参数说明|
## 简介
为了完善人脸项目的自动化测试架构，编写了一下三个Python脚本作为辅助工具，配合android端生成的测试log文件进行使用
 - AutoFrTest
 - AutoFrTestParse
 - FrXlsxArrange
## 批量启动/处理功能(AutoFrTest)
考虑到全体测试集体积较大，不能一次性复制至设备存储介质，需要分批进行传输+测试操作。目前方案是每 单个测试序列集 启动一次android端自动化测试
### 参数配置
- dataFolder: 测试集根目录
- objs: 人名分级文件夹
- cDataDst: 设备端Data目录
- cLogDst: 设备端Log目录
- cAppID & cAppObj: 待自启动android应用application ID
- cLocalResults: PC端Log下载目录
- folderDoneList: 已完成的文件夹(属于单个人名)
## 中间数据解析功能(AutoFrTestParse)
将android端输出的Log信息语义化，创建含有丰富的中间数据信息xlsx，以供分析
### 参数配置
- xlsFileR: 人名-文件夹名-测试序列类别 标签文件xlsx
- xlsFileW: 中间数据输出文件xlsx
- srcRoot: android端输出的Log文件夹
- checkList: 人名分级文件夹
### 内部参数
此部分参数由研发人员调整
- futuresList: 并发用队列，设置为empty list
- executor: 并发量设置
## 总表解析功能(FrXlsxArrange)
由中间数据xlsx归纳更为简明的总结性xlsx，提供给非研发人员查询、分析
### 参数配置
- xlsFileR: 中间数据输出文件xlsx
- indexSheet: 中间数据表中的索引sheet
- hint: 需要分析的 测试集属性
- xlsFileW: 总表数据输出文件xlsx
### 内部参数
此部分参数随AutoFrTestParse改变，由研发人员调整
- needRowsInfo: 总表标签名
- needRows: 需提取 中间数据 对应的行号
- individuleStartRow: 中间数据 中个人数据起始行号

*[单个测试序列集]: eg: 李明-1米-左-弱光-正面
*[已完成的文件夹]: 断点重续功能
*[标签文件xlsx]: eg: FrSet2018.xlsx
*[测试集属性]: eg: ["3m", "侧", "普"]
*[中间数据]: 由AutoFrTestParse.py生成的数据表
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzU1MTY2MTYzXX0=
-->