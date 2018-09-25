# 人脸项目Python使用说明
## 版本维护
|Version|Date|Info|
|:--:|:--:|:--:|
|1.0|20180925|内部初稿|
## 简介
为了完善人脸项目的自动化测试架构，编写了一下三个Python脚本作为辅助工具，配合android端生成的测试log文件进行使用
 - AutoFrTest
 - AutoFrTestParse
 - FrXlsxArrange
## 批量启动/处理功能(AutoFrTest)
考虑到全体测试集体积较大，不能一次性复制至设备存储介质，需要分批进行传输+测试操作。目前方案是每 单个测试序列集 启动一次android端自动化测试
### 参数配置
- 测试集根目录
- 人名分级文件夹
- 设备端Data目录
- 设备端Log目录
- 待自启动android应用application ID
- PC端Log下载目录
- 已完成的文件夹(属于单个人名)
## 中间数据解析功能(AutoFrTestParse)
将android端输出的Log信息语义化，创建含有丰富数据信息的xlsx以供分析
### 参数配置
- 人名-文件夹名-测试序列类别 标签文件xlsx
- 中间数据输出文件xlsx
- android端输出的Log文件夹
- 人名分级文件夹
## 总表解析功能(FrXlsxArrange)
由中间数据xlsx归纳更为简明的总结性xlsx，提供给非研发人员查询、分析
### 参数配置
- 中间数据输出文件xlsx
- 中间数据表中的索引sheet
- 需要分析的 序列属性
- 总表数据输出文件xlsx



*[单个测试序列集]: eg: 李明-1米-左-弱光-正面
*[已完成的文件夹]: 断点重续功能
*[标签文件xlsx]: eg: FrSet2018.xlsx
*[序列属性]: eg: ["3m", "侧", "普"]


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY2OTA2MzEzOF19
-->