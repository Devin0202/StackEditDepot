# 人脸项目android端自动化测试框架说明
## 版本维护
|Version|Date|Info|
|:--:|:--:|:--:|
|1.1|20190122|增加实时log功能相关内容|
## 简介
使用现有的camera1架构作为测试主体环境，设计的log机制服务于两种模式

TestCase mode:
: camera流替换为测试集图片序列

RealTime mode:
: camera流

在各个算法/功能模块的关键位置记录 log，并实现 log 文件化
## 测试主要代码(类)
 1. TestJob.java
 2. customXlog.java

## 代码部署主要流程
### 系统端
 1. 要求调整最大虚拟机堆空间至1024M
```
adb root
adb remount
adb pull /system/build.prop .

(更改build.prop条目：
dalvik.vm.heapgrowthlimit=1024m  
dalvik.vm.heapsize=1024m)

adb push build.prop /system
adb reboot
adb shell cat /system/build.prop
(确认修改是否成功)
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
下述代码中，涉及到"frameID: "字段，对应的logID，如果在不同线程记录，需要传递该值，切记！
另外，基于[dalvik.vm.heapsize]: [1024m]的情况，建议在/sdcard/TestData中存放的测试图片序列数量少于600张</font>
```
//初始化
private TestJob eg = new TestJob("/sdcard/TestData");
private int delay = 0;
private long appStart = System.currentTimeMillis();
private long appQuitStart;
private long appQuitEnd;
private String appQuitIndex = null;
private int frameCount = 0;
private boolean loadingPrint = true;

private boolean mEnableXlog = false;	//控制是否启用log
private boolean mEnableTest = false;	//控制log启用模式
private boolean mConsecutiveShow = false;
private String mSubFolder = "";			// For recording running-time logs

@Override
protected void onResume() {
	super.onResume();
	customXlog.instance = null;
	if (mEnableTest) {
		customXlog.getInstance("", "/sdcard/TestLog", mEnableXlog);
	}else{
		SimpleDateFormat sdf = new SimpleDateFormat("MMddHHmmss", Locale.US);
		mSubFolder = sdf.format(new Date());
		customXlog.getInstance("", "/sdcard/TestLog/", mSubFolder, mEnableXlog);
	}
	
	// Do else
}
```
```
//加载替换测试图像阶段
@Override  
public void onPreviewFrame(final byte[] img, final Camera camera) {
	if (!mHandlePreviewData) {
		updatePreviewBuf();
		return;
	}
	frameCount++;
	byte[] imgData = img;
	if(mEnableTest) {
		if (350 < delay) {
			eg.setLogID(frameCount, appStart);
			if (loadingPrint){
				customXlog.useInstance().cLog( "frameID: " + eg.logID + " status: loadingStart");
			}
			
			imgData = eg.getFrame();
			if (loadingPrint){
				customXlog.useInstance().cLog( "frameID: " + eg.logID + " file: " + eg.currentFile + " status: loadingEnd" + " fileSN: " + eg.lastIdx);
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
				if (650 < delay) {
					appQuitEnd = System.currentTimeMillis();
					customXlog.useInstance().cLog("frameID: " + eg.logID + " quitIndex: " + appQuitIndex + " quitCost: " + (appQuitEnd - appQuitStart) + " arrayRemain: " + mFaceRequest.getArraySize());
					try {
						Thread.sleep(3000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					finish();
				}else{
					;
				}
	            updatePreviewBuf();
	            return;
			}else{
	            ;
		    }
		}else{
			imgData = img;
			delay++;
			updatePreviewBuf();
			return;
		}
	}else{
		imgData = img;
		eg.logID = frameCount;
		String fabricatedFile = "/sdcard/TestData/" + mSubFolder + '/' + eg.logID + ".nv21";
		customXlog.useInstance().cLog( "frameID: " + eg.logID + " status: loadingStart");
		customXlog.useInstance().cLog( "frameID: " + eg.logID + " file: " + fabricatedFile + " status: loadingEnd" + " fileSN: " + eg.logID);
	}
	
	// Do else
}
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
//提取检测结果人脸位置信息
//c: 表示第几个人脸    roiFace:人脸位置    confidence:置信分数
customXlog.useInstance().cLog( "frameID: " + eg.logID  
  + " status: detectInfo " + c + " " + roiFace.left + " " + roiFace.top  
  + " " + roiFace.right + " " + roiFace.bottom + " " + faces[c].confidence);
```
```
//识别解析无效阶段，同时记录无效识别的人脸位置信息
//result: 无效的String返回值
customXlog.useInstance().cLog("frameID: " + faceReqinfo.mlogID + " status: recoBad " + result);
customXlog.useInstance().cLog( "frameID: " + faceReqinfo.mlogID  
  + " status: recgInvalidRoi " + faceReqinfo.mFaceIndex + " "  
  + faceReqinfo.mRoiFace.left + " " + faceReqinfo.mRoiFace.top + " "  
  + faceReqinfo.mRoiFace.right + " " + faceReqinfo.mRoiFace.bottom);
```
```
//识别解析成功阶段，同时记录有效识别的人脸位置信息
customXlog.useInstance().cLog("frameID: " + faceReqinfo.mlogID + " status: recoParse" + " ID: " + strId + " score: " + conf);
customXlog.useInstance().cLog( "frameID: " + faceReqinfo.mlogID  
  + " status: recgValidRoi " + faceReqinfo.mFaceIndex + " "  
  + faceReqinfo.mRoiFace.left + " " + faceReqinfo.mRoiFace.top + " "  
  + faceReqinfo.mRoiFace.right + " " + faceReqinfo.mRoiFace.bottom  
  + " ID: " + strId + " score: " + conf);
```
```
//识别阶段，同时记录待识别队列剩余个数
customXlog.useInstance().cLog("frameID: " + info.mlogID + " status: recoStart");
//Do recognization
//...
//Do recognization
customXlog.useInstance().cLog("frameID: " + info.mlogID + " status: recoEnd");
customXlog.useInstance().cLog("frameID: " + info.mlogID  
  + " CaseIndex: " + appQuitIndex  
  + " Running remains: " + getArraySize());
```
```
//请求识别队列溢出
synchronized (mObj) {
	boolean ret = mRequestQueue.offer(faceInfo);
	while (!ret) {
		customXlog.useInstance().cLog("frameID: " + logID + " status: ObjDiscardFromArray");
		mRequestQueue.remove();
		ret = mRequestQueue.offer(faceInfo);
	}
	mObj.notify();  
}
```
```
//onStop()阶段
@Override  
protected void onStop() {
	if (false == mEnableTest) {
		customXlog.useInstance().cLog("frameID: " + eg.logID + " quitIndex: " + mSubFolder + " quitCost: 0" + " arrayRemain: " + mFaceRequest.getArraySize());  
	}

	// Do else
}
```
```
//onDestroy()阶段
@Override  
protected void onDestroy() {
	//Do else
	if (mEnableTest) {
		android.os.Process.killProcess(android.os.Process.myPid());
	}
}
```
```
// Http 发送 记录网络耗时
public static String reqestFaceInfoByPathEx(StringBuffer infoS, int logId, String url, String id, String path, byte[] jpgData, int returnCount, int frameCount) throws Exception {  
	// Do else

	try{
		String tag = null;
		if (frameCount >= 0) {
			String curTime = Util.formatDate();
			tag = "uuid_" + mDeviceId + "_" + curTime + "_frameCount:" + frameCount;
		}

		if (tag != null) {
			res = sendFacePostWithTag(logId, url, map, fileMap, tag);
		}else{
			res = sendFacePost(url, map, fileMap);
		}

		if (null != res) {
			customXlog.useInstance().cLog("frameID: " + logId + " status: Http Response " + res.getStatus());
			infoS.append("Http Response " + res.getStatus());
			if (res.getStatus() == HttpURLConnection.HTTP_OK) {
				String result = new String(res.getContent(), "UTF-8");
				return result;
			}
		}
	}catch (Exception e) {
		infoS.append("Net connection error");
		customXlog.useInstance().cLog("frameID: " + logId + " status: Net connection error");
		customXlog.useInstance().cLog("frameID: " + logId + " status: Net connection error " + e.toString() + " " + e.getMessage() + " " + e.getClass());
		e.printStackTrace();
	}
	return null;  
}
```
```
// Http 接收 记录网络耗时
public static Response sendFacePostWithTag(int fLogId, String reqUrl, HashMap<String, String> map, HashMap<String, byte[]> fileMap, String tag) throws Exception {
	// Do else
	customXlog.useInstance().cLog("frameID: " + fLogId + " Http connect start");
	DataOutputStream obos = new DataOutputStream(conne.getOutputStream());
	customXlog.useInstance().cLog("frameID: " + fLogId + " Http connect end");
	customXlog.useInstance().cLog("frameID: " + fLogId + " requestID = " + tag);
	
	// Do else
	int code;
	InputStream inss = null;
	try {
		customXlog.useInstance().cLog("frameID: " + fLogId + " Http post start");
		code = conne.getResponseCode();
		customXlog.useInstance().cLog("frameID: " + fLogId + " Http getResponseCode end");
		if (code == 200) {
			inss = conne.getInputStream();
		}else{
			inss = conne.getErrorStream();
		}
		customXlog.useInstance().cLog("frameID: " + fLogId + " Http post end");
	}catch (SSLException var14) {
		var14.printStackTrace();
		return new Response();
	}
	String reqid = conne.getHeaderField("requestID");
	String timeConsume = conne.getHeaderField("time-consuming");
	String faceTimeConsume = conne.getHeaderField("face-time-consuming");
	String dataTimeConsume = conne.getHeaderField("data-time-consuming");
	customXlog.useInstance().cLog("frameID: " + fLogId + " requestID: " + tag + " timeConsume: " + timeConsume + " faceTimeConsume: " + faceTimeConsume + " dataTimeConsume: " + dataTimeConsume);
	
	//Do else
}
```
## 相关代码git
主干含测试代码:	git clone git@192.168.18.23:bingrui.fu/IntelligentRecog.git -b br_local_zhuce

Deprecated soon:
仿真测试:				git clone git@192.168.18.23:bingrui.fu/FacePlus.git -b masterDy
全集测试:				git clone git@192.168.18.23:bingrui.fu/FacePlus.git -b FullSetTest






*[log]: 时间戳及相关模块信息
*[仿真测试]: 由于硬件限制，真实使用帧率低于测试集采集帧率，部分测试图会被略过
*[全集测试]: 测试集全集图片全部使用
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQ3OTg3MDg1OV19
-->