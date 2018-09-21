# 人脸项目android端自动化测试框架说明
## 版本维护
|Version|Date|Info|
|:--:|:--:|:--:|
|1.0|20180921|内部初稿|
## 简介
使用现有的camera1架构作为测试主体环境，将camera流替换为测试集图片序列，在各个算法/功能模块的关键位置记录 log，并实现 log 文件化
## 测试代码(类)
 1. TestJob.java
 2. customXlog.java

## 代码部署主要流程
### 系统端

 1. 要求调整最大虚拟机堆空间至1024M
```
[dalvik.vm.heapsize]: [1024m]
```
 2. 新建文件夹：/sdcard/TestData、/sdcard/TestLog
### 软件端
 1. 应用开启largeHeap属性
```
<application
  android:largeHeap="true"
```
 2. 应用开启外盘读写权限
 3. 测试代码部署
<font color="#ff0000">**特别提醒：**
下述代码中，涉及到"frameID: "字段，对应的logID，如果在不同线程记录，需要传递该值，切记！</font>
```
//初始化
customXlog.getInstance("", "/sdcard/TestLog", true);
private TestJob eg = new TestJob("/sdcard/TestData");
private int delay = 0;  
private long appStart = System.currentTimeMillis();  
private long appQuitStart;  
private long appQuitEnd;  
private String appQuitIndex = null;
private int frameCount = 0;
```
```
//加载替换测试图像阶段
@Override
public void onPreviewFrame(final byte[] img, final Camera camera) {
	if(!mHandlePreviewData) {
		return;
	}

    frameCount++;
    byte[] imgData;
	if (15 < delay) {
	    eg.setLogID(frameCount, appStart);
	    if (loadingPrint){
            customXlog.useInstance().cLog( "frameID: " + eg.logID + " status: loadingStart");
        }
	    imgData = eg.getFrame();
        if (loadingPrint){
            customXlog.useInstance().cLog( "frameID: " + eg.logID + " file: " + eg.currentFile + " status: loadingEnd");
        }

        if (null != eg.currentFile) {
            appQuitStart = System.currentTimeMillis();
            appQuitIndex = eg.currentFile.split("/")[3];
        }else{
            loadingPrint = false;
        }

	    if (null == imgData) {
	        imgData = img;
	        delay++;
	        if (300 < delay) {
                appQuitEnd = System.currentTimeMillis();
                customXlog.useInstance().cLog("frameID: " + eg.logID
                        + " quitIndex: " + appQuitIndex
                        + " quitCost: " + (appQuitEnd - appQuitStart)
                        + " arrayRemain: " + mFaceRequest.getArraySize());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                android.os.Process.killProcess(android.os.Process.myPid());
			}else{
	            ;
            }
            return;
        }else{
            ;
        }
    }else{
        imgData = img;
	    delay++;
	    return;
    }
    //...
```
```
//检测阶段
customXlog.useInstance().cLog( "frameID: " + eg.logID + " status: detectStart");
//Do detection
//...
//Do detection
if (faces != null) {
    customXlog.useInstance().cLog( "frameID: " + eg.logID + " status: detectEnd" + " faceNum: " + faces.length);
}else{
    customXlog.useInstance().cLog( "frameID: " + eg.logID + " status: detectEnd" + " faceNum: 0");
}
```
```
//识别解析无效阶段
//result: 无效的String返回值
customXlog.useInstance().cLog("frameID: " + faceReqinfo.mlogID + " status: recoBad " + result);
```
```
//识别解析成功阶段
customXlog.useInstance().cLog("frameID: " + faceReqinfo.mlogID + " status: recoParse" + " ID: " + strId + " score: " + conf);
```
```
//识别阶段
customXlog.useInstance().cLog("frameID: " + info.mlogID + " status: recoStart");
//Do recognization
//...
//Do recognization
customXlog.useInstance().cLog("frameID: " + info.mlogID + " status: recoEnd");
```
```
//请求识别队列溢出
customXlog.useInstance().cLog("frameID: " + logID + " status: ObjDiscardFromArray");
```
## 相关代码git
git clone git@192.168.18.23:bingrui.fu/FacePlus.git -b masterDy







*[log]: 时间戳及相关模块信息
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MjMzMzY0NTRdfQ==
-->