---
title: android WebView的使用方法
tags:
  - WebView
id: 163
categories:
  - Android
date: 2012-05-02 11:58:05
---

今天微博登录时，授权要在webView中完成，特写了个登录。

打开网页同时显示一个ProgressDialog,网页载入完毕则隐藏，点击页面上链接时再次显示ProgressDialog,载入完隐藏，可用返回键返回上一页。
XML布局：

[xml]
&lt;pre&gt;&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;AbsoluteLayout android:orientation=&quot;vertical&quot; android:id=&quot;@+id/tab1&quot; android:layout_width=&quot;fill_parent&quot; android:layout_height=&quot;fill_parent&quot;
  xmlns:android=&quot;http://schemas.android.com/apk/res/android&quot;&gt;
    &lt;WebView android:id=&quot;@+id/webView&quot;
    	android:layout_width=&quot;fill_parent&quot;
    	android:layout_height=&quot;fill_parent&quot;
    	android:layout_x=&quot;0.0dip&quot;
    	android:layout_y=&quot;0.0dip&quot;
    	android:layout_weight=&quot;1.0&quot; /&gt;
&lt;/AbsoluteLayout&gt;&lt;/pre&gt;
[/xml]

实现代码：

[java]

public class main extends Activity {

/** Called when the activity is first created. */

WebView webView;

ProgressDialog pd;

Handler handler;

@Override

public void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.main);

handler=new Handler(){

public void handleMessage(Message msg)

{//定义一个Handler，用于处理下载线程与UI间通讯

if (!Thread.currentThread().isInterrupted())

{

switch (msg.what)

{

case 0:

pd.show();//显示进度对话框

break;

case 1:

pd.hide();//隐藏进度对话框，不可使用dismiss()、cancel(),否则再次调用show()时，显示的对话框小圆圈不会动。

break;

}

}

super.handleMessage(msg);

}

};

init();

loadurl(webView,&quot;http://www.cocos2dev.com&quot;);

}

&amp;nbsp;

public void init(){//初始化

webView=(WebView)findViewById(R.id.webView);

webView.getSettings().setJavaScriptEnabled(true);//可用JS

webView.setScrollBarStyle(0);//滚动条风格，为0就是不给滚动条留空间，滚动条覆盖在网页上

webView.setWebViewClient(new WebViewClient(){

public boolean shouldOverrideUrlLoading(final WebView view, final String url) {

loadurl(view,url);//载入网页

return true;

}//重写点击动作,用webview载入

&amp;nbsp;

});

webView.setWebChromeClient(new WebChromeClient(){

public void onProgressChanged(WebView view,int progress){//载入进度改变而触发

if(progress==100){

handler.sendEmptyMessage(1);//如果全部载入,隐藏进度对话框

}

super.onProgressChanged(view, progress);

}

});

&amp;nbsp;

pd=new ProgressDialog(main.this);

pd.setProgressStyle(ProgressDialog.STYLE_SPINNER);

pd.setMessage(&quot;数据载入中，请稍候！&quot;);

}

&amp;nbsp;

public boolean onKeyDown(int keyCode, KeyEvent event) {//捕捉返回键

if ((keyCode == KeyEvent.KEYCODE_BACK) &amp;&amp; webView.canGoBack()) {

webView.goBack();

return true;

}else if(keyCode == KeyEvent.KEYCODE_BACK){

ConfirmExit();//按了返回键，但已经不能返回，则执行退出确认

return true;

}

return super.onKeyDown(keyCode, event);

}

&amp;nbsp;

public void ConfirmExit(){//退出确认

AlertDialog.Builder ad=new AlertDialog.Builder(main.this);

ad.setTitle(&quot;退出&quot;);

ad.setMessage(&quot;是否退出软件?&quot;);

ad.setPositiveButton(&quot;是&quot;, new DialogInterface.OnClickListener() {//退出按钮

@Override

public void onClick(DialogInterface dialog, int i) {

// TODO Auto-generated method stub

main.this.finish();//关闭activity

&amp;nbsp;

}

});

ad.setNegativeButton(&quot;否&quot;,new DialogInterface.OnClickListener() {

@Override

public void onClick(DialogInterface dialog, int i) {

//不退出不用执行任何操作

}

});

ad.show();//显示对话框

}

&amp;nbsp;

public void loadurl(final WebView view,final String url){

new Thread(){

public void run(){

handler.sendEmptyMessage(0);

view.loadUrl(url);//载入网页

}

}.start();

}

}

[/java]