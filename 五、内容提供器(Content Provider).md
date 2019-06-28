---
title: 五、内容提供器
---
# 五、内容提供器  
内容提供器(Content Provider)主要用于在不同的应用程序之间实现数据的共享功能，它提供了一套完整的机制，允许一个程序访问另外一个程序中的数据，同时还能有效保证被访问数据的安全性。是Android实现跨程序共享数据的标准方式。    

## 1、运行时权限
### 1.1Android权限机制详解  
访问系统的网络状态以及监听开机广播，在AndroidManifest.xml中添加权限声明：  
  
	<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    	package="com.example.xxxxx.broadcasttest">
	
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />  //访问网络状态权限声明
   	<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />  //监听开机广播权限声明
	</manifest>
访问系统的网络状态和监听开机广播涉及到用户设备的安全性，因此必须在AndroidManifest.xml中加入权限声明，否则程序会崩溃。  
常用软件普遍存在滥用权限的情况，不管到底用不用得到先把权限申请了再说。所有Android开发团队从6.0系统加入了运行时权限功能。  
Android现在的所有权限分为两类，普通权限和危险权限。普通权限是安心不会直接威胁到用户的安全和隐私的权限，系统会帮我们进行授权，不需要用户操作。危险权限则表示哪些可能会触及用户隐私或对社保安全性造成影响的权限，如获取设备联系人、定位设备的地理位置等，这些需要用户手动点击授权，否则程序无法使用相应的功能。  
  
| 权限组名   |  权限名 |  
| :-------| :-------:|  
|CALENDAR  | 	READ_CALENDAR |    
|CALENDAR |  WRITE\_CALENDAR |      
|---------------------------|-------------------------------------|
|CAMERA| CAMERA|  
|---------------------------|-------------------------------------|
|CONTACTS | READ_CONTACTS|
|CONTACTS | WRITE_CONTACTS|
|CONTACTS | GET_CONTACTS|
|---------------------------|-------------------------------------|
|LOCATION |  ACCESS\_FINE_LOCATION|
|LOCATION|  ACCESS\_COARSE_LOCATION|
|---------------------------|-------------------------------------|
|MICROPHONE|RECORD_AUDIO|
|---------------------------|-------------------------------------|
|PHONE|READ\_PHONE_STATE|
|PHONE|CALL_PHONE|
|PHONE|READ\_CALL_LOG|
|PHONE|WRITE\_CALL_STATE|
|PHONE|ADD_VOICEMAIL|
|PHONE|USE_SIP|
|PHONE|PROCESS_OUTGOING_CALLS|
|---------------------------|-------------------------------------|
|SENSORS|BODY_SENSORS|
|---------------------------|-------------------------------------|
|SMS|SEND_SMS|
|SMS|RECEIVE_SMS|
|SMS|READ_SMS|
|SMS|RECEIVE_WAP_SMS|
|SMS|RECEIVE_MMS|
|---------------------------|-------------------------------------|
|STORAGE|READ_EXTERNAL_STORAGE|
|STORAGE|WRITE_EXTERNAL_STORAGE|
|---------------------------|-------------------------------------|
使用权限时，如果属于这张表的权限，就需要进行运行时权限处理，如果不在这样表中那么只需要在AndroidManifest.xml文件中添加一下权限声明就可以。  
权限处理时处理的是权限名，但是用户授权后对应的权限组中其他的权限也会同时被授权。  
### 1.2运行时权限申请（262页）  


  
