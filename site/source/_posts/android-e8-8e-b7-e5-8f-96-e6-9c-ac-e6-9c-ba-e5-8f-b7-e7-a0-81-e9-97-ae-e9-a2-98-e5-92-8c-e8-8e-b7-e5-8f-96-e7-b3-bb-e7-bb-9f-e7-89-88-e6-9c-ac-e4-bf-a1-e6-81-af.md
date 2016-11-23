---
title: Android获取本机号码问题和获取系统版本信息
tags:
  - TelephonyManager
id: 208
categories:
  - Android
date: 2012-05-09 02:30:13
---

今天要获取用户反馈意见，我默认将用户的手机号码上传了，结果发现并不是所有的手机能拿到本机号码。找了下发现原是用户的SIM卡没有写入本机号码导致的。

Android获取本机号码：

<span style="color: #ff0000;">TelephonyManager phoneMgr = (TelephonyManager)this.getSystemService(Context.TELEPHONY_SERVICE);</span>

<span style="color: #ff0000;">String phoneNum = phoneMgr.getLine1Number(); </span>

AndroidManifest.xml中添加权限:

<span style="color: #ff0000;">&lt;uses-permission android:name="android.permission.READ_PHONE_STATE" /&gt;</span>

刚才也说了，手机号码不是所有的都能获取。只是有一部分可以拿到。这个是由于移动运营商没有把手机号码的数据写入到sim卡中。

手机型号 Build.MODEL

<span style="color: #ff0000;">String MODEL The end-user-visible name for the end product.</span>

sdk版本 Build.VERSION.SDK

<span style="color: #ff0000;">String SDK This constant is deprecated. Use SDK_INT to easily get this as an integer.</span>

frimware版本号(系统版本号) Build.VERSION.RELEASE

<span style="color: #ff0000;">String RELEASE The user-visible version string.</span>

获取手机的其他信息：

<span style="color: #ff0000;">private void getPhoneStatus(){</span>
<div>
<div><span style="color: #ff0000;">TelephonyManager phoneMgr=(TelephonyManager)this.getSystemService(Context.TELEPHONY_SERVICE);      </span></div>
<div><span style="color: #ff0000;">String model = Build.MODEL; //手机型号 </span></div>
<div><span style="color: #ff0000;">String phoneNum = phoneMgr.getLine1Number();//本机电话号码 </span></div>
<div><span style="color: #ff0000;">String sdkVersion = Build.VERSION.SDK;//SDK版本号 </span></div>
<div><span style="color: #ff0000;">String osVersion = Build.VERSION.RELEASE;//Firmware/OS 版本号</span></div>
<div><span style="color: #ff0000;">}</span></div>
</div>
Build中包括 硬件厂商，硬件编号，序列号等很多信息。调用方法也很简单，和上面类似的。

下面是Google提供的参考表：
<table id="constants" border="0">
<tbody>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[BOARD](http://developer.android.com/reference/android/os/Build.html#BOARD)</td>
<td>The name of the underlying board, like "goldfish".</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[BOOTLOADER](http://developer.android.com/reference/android/os/Build.html#BOOTLOADER)</td>
<td>The system bootloader version number.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[BRAND](http://developer.android.com/reference/android/os/Build.html#BRAND)</td>
<td>The brand (e.g., carrier) the software is customized for, if any.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[CPU_ABI](http://developer.android.com/reference/android/os/Build.html#CPU_ABI)</td>
<td>The name of the instruction set (CPU type + ABI convention) of native code.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[CPU_ABI2](http://developer.android.com/reference/android/os/Build.html#CPU_ABI2)</td>
<td>The name of the second instruction set (CPU type + ABI convention) of native code.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[DEVICE](http://developer.android.com/reference/android/os/Build.html#DEVICE)</td>
<td>The name of the industrial design.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[DISPLAY](http://developer.android.com/reference/android/os/Build.html#DISPLAY)</td>
<td>A build ID string meant for displaying to the user</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[FINGERPRINT](http://developer.android.com/reference/android/os/Build.html#FINGERPRINT)</td>
<td>A string that uniquely identifies this build.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[HARDWARE](http://developer.android.com/reference/android/os/Build.html#HARDWARE)</td>
<td>The name of the hardware (from the kernel command line or /proc).</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[HOST](http://developer.android.com/reference/android/os/Build.html#HOST)</td>
<td></td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[ID](http://developer.android.com/reference/android/os/Build.html#ID)</td>
<td>Either a changelist number, or a label like "M4-rc20".</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[MANUFACTURER](http://developer.android.com/reference/android/os/Build.html#MANUFACTURER)</td>
<td>The manufacturer of the product/hardware.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[MODEL](http://developer.android.com/reference/android/os/Build.html#MODEL)</td>
<td>The end-user-visible name for the end product.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[PRODUCT](http://developer.android.com/reference/android/os/Build.html#PRODUCT)</td>
<td>The name of the overall product.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[RADIO](http://developer.android.com/reference/android/os/Build.html#RADIO)</td>
<td>The radio firmware version number.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[SERIAL](http://developer.android.com/reference/android/os/Build.html#SERIAL)</td>
<td>A hardware serial number, if available.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[TAGS](http://developer.android.com/reference/android/os/Build.html#TAGS)</td>
<td>Comma-separated tags describing the build, like "unsigned,debug".</td>
</tr>
<tr>
<td>long</td>
<td>[TIME](http://developer.android.com/reference/android/os/Build.html#TIME)</td>
<td></td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[TYPE](http://developer.android.com/reference/android/os/Build.html#TYPE)</td>
<td>The type of build, like "user" or "eng".</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[UNKNOWN](http://developer.android.com/reference/android/os/Build.html#UNKNOWN)</td>
<td>Value used for when a build property is unknown.</td>
</tr>
<tr>
<td>[String](http://developer.android.com/reference/java/lang/String.html)</td>
<td>[USER](http://developer.android.com/reference/android/os/Build.html#USER)</td>
</tr>
</tbody>
</table>
=================================================