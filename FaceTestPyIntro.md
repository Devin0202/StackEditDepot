# 人脸项目Python使用说明
## 版本维护
|Version|Date|Info|
|:--:|:--:|:--:|
|1.4|20190122|工具大更新|
## 简介
为了完善人脸项目的自动化测试架构，编写了一下三个Python脚本作为辅助工具，配合android端生成的测试log文件进行使用
 - AutoFrTest++
 - AutoFrTestParse
 - FrXlsxArrange
## 批量启动/处理功能(AutoFrTest)
考虑到全体测试集体积较大，不能一次性复制至眼镜端存储介质，需要借助移动硬盘或SD卡，分批进行传输+测试操作。目前方案是每 单个测试序列集 启动一次android端自动化测试
<font color="#ff0000">**特别提醒：**
请在使用该工具的系统(Windows/linux)下、对应的python版本(2/3)中找到subprocess.py这个文件，
加以修改
Please change the codes in subprocess.py
In "def run(...):"  
stdout, stderr = process.communicate()
-> stdout, stderr = process.communicate(timeout = timeout)</font>
### 参数配置
- cAdbDevices: Adb设备选择
- dataFolder: 测试集根目录
- objs: 人名分级文件夹
- cDataDst: 设备端Data目录
- cLogDst: 设备端Log目录
- cAppID & cAppObj: 待自启动android应用application ID
- cLocalResults: PC端Log下载目录
- folderDoneList: 已完成的文件夹(属于单个人名)
- recordLogFolder: 工具运行时日志存放位置
- isDos: Windows系统请置True，Linux为False
- isSDmode: 使用SD卡请置True，移动硬盘为False
### 内部参数
logWriter = EasyLog(recordLogFolder)
用于生成 工具运行时日志
## 中间数据解析功能(AutoFrTestParse)
将android端输出的Log信息语义化，创建含有丰富的中间数据信息xlsx，以供分析
### 参数配置
有两种配置方式
其一
``` shell
//命令行执行
python3 AutoFrTestParse.py -i ./facerecog-index.xlsx -d ./facerecog.log -o ./facerecog-interim-result.xlsx -l yawen/xingliujian/yukeke
//参数查询命令
python3 AutoFrTestParse.py -h
```
其二
``` python
//Hardcoding
//对AutoFrTestParse.py做如下修改
if False:
	xlsFileR, xlsFileW, srcRoot, checkList = parseOpt()
else:
    xlsFileR = "/home/devin/Desktop/G200/FrSet2018.xlsx"
    xlsFileW = "/home/devin/Desktop/G200/RoiChange/bigger4/InterimData.xlsx"
    srcRoot = "/home/devin/Desktop/G200/RoiChange/bigger4/"
    checkList = ["zhuyawen", "xinglj", "baoyuandong", "daiyi", "peiyi", "sunhaiyan"]
```
``` shell
//命令行执行
python3 AutoFrTestParse.py
```
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
## 差分比较解析功能(XlsxCompareDiff)
同一测试case，多次测试后，得到多个AutoFrTestParse结果，使用XlsxCompareDiff加以分析
### 参数配置
- xlsFile0R: 0号数据文件xlsx
- xlsFile1R: 1号数据文件xlsx
- xlsFileW: 差分表数据输出文件xlsx
- indexSheet: 中间数据表中的索引sheet
- hint: 需要分析的 测试集属性
### 内部参数
分别提供了比较工具compareTool和差分工具diffTool
``` python
def writeXlsx(iCompareCases, iA, iB, iWriteXlsx, iIndexSheet):
	...
    for x in iCompareCases:
        diffTool(fWws, fW0rs, fW1rs, x, compareEndRow)
        # compareTool(fWws, fW0rs, fW1rs, x, compareEndRow)
	...
```

*[单个测试序列集]: eg: 李明-1米-左-弱光-正面
*[已完成的文件夹]: 断点重续功能
*[标签文件xlsx]: eg: FrSet2018.xlsx
*[测试集属性]: eg: ["3m", "侧", "普"]
*[中间数据]: 由AutoFrTestParse.py生成的数据表

*[仿真测试]: 由于硬件限制，真实使用帧率低于测试集采集帧率，部分测试图会被略过
*[全集测试]: 测试集全集图片全部使用
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyNDcyNDcxNjNdfQ==
-->