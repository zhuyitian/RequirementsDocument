对接文档
==================================================

需要接入的三方  
------------------------------------------------------------
#### 1、[Google登录](https://developers.google.com/identity/sign-in/android/start)  

* 触发的开关交互方法`openGoogle`  

* 触发Google登录之后的[后续流程详见第七点](#login "三方登陆流程") 
####
2、Facebook SDK接入官方文档[点此跳转](https://developers.facebook.com/docs/app-ads/sdk-setup?locale=zh_CN)
#### 3、[Branch](https://help.branch.io/developers-hub/docs/android-basic-integration#section-configure-app)   

* 配置在``` <category android:name="android.intent.category.LAUNCHER" />```的Activity里面(manifest和Activity， 即启动的Activity)

#### 4、[FCM](https://firebase.google.com/docs/cloud-messaging/android/client?authuser=0)  

* [推送消息处理](#fcm "推送消息处理")详见`第八点`

#### 5、[paytm](https://gitee.com/google_project_team/googlevestrequire/blob/master/paytm%E6%8E%A5%E5%85%A5/paytm%20invoke.md)  

* 触发的开关交互方法`openPayTm` 

## 一、启动流程

>打开	---->	请求接口 ----->  
>#### 0、显示H5  
>##### 加载web页面
>#### 1、显示原生页面

![](https://github.com/zhuyitian/RequirementsDocument/blob/main/src/open.png?raw=true)  
![](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/main.png)  

> titlebar的文案从webview中获取 `实现WebClient` 在onPageFinished方法中 webView.getTitle()  

## 二、SDK版本等要求
* Google Play上架要求最低版本为21  目标30
* 必须支持64位  
* 应用支持http协议 cleartextTrafficPermitted="true"
* webview可以选取文件`WebChromeClient onShowFileChooser()`
* 一般情况下，webview中系统回回退键，webview可以后退的时候就后退，无法后退了在finish()
* 键盘输入适配(遮挡输入框的问题)

## 三、需要给对接人提供的信息文件
1、使用keytool获取签名文件MD5 SHA1 SHA256 

```
keytool -list -v -keystore xxx.jks
```

2、Branch schema  

![](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/branch_scheme_info.png)  

3、Application id  

![](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/application_id.png)

## 四、需要找对接人获取的信息
1、host(开关域名)  标识code   
3、Branch参数  
4、Firebase账号 

## 五、WebView设置
``` xml
<string name="android_web_agent">ANDROID_AGENT_NATIVE/2.0&#8194;%1$s</string>
```


``` java 
String userAgentString = webSettings.getUserAgentString();
userAgentString = "ANDROID_AGENT_NATIVE/2.0" + " " + userAgentString;
webSettings.setUserAgentString(userAgentString);
webView.addJavascriptInterface(new AppJs(this), "AppJs");
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
	webView.setLayerType(View.LAYER_TYPE_HARDWARE, null);
} else {
	webView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
}

webSettings.setJavaScriptEnabled(true);
webSettings.setJavaScriptCanOpenWindowsAutomatically(true);
webSettings.setAllowFileAccess(true);
webSettings.setRenderPriority(WebSettings.RenderPriority.NORMAL);
webSettings.setAppCacheEnabled(true);
webSettings.setAppCachePath(getExternalCacheDir().getPath());
webSettings.setDatabaseEnabled(true);
webSettings.setCacheMode(WebSettings.LOAD_DEFAULT);
webSettings.setEnableSmoothTransition(true);
webSettings.setDomStorageEnabled(true);
webSettings.setUseWideViewPort(true);
webSettings.setLoadWithOverviewMode(true);
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    webSettings.mixedContentMode = WebSettings.MIXED_CONTENT_ALWAYS_ALLOW
}
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
	mWebView.setWebContentsDebuggingEnabled(false);
}
//TODO 需要实现WebViewClient和WebChromeClient
// onShowFileChooser() 选择文件(图片)
webView.setWebChromeClient();
// onPageFinished在里面可以获取webView.getTitle()给titlebar设置标题
webView.setWebViewClient();
        
webView.clearHistory()
webView.setDrawingCacheEnabled(true);
webView.setScrollBarStyle(View.SCROLLBARS_INSIDE_OVERLAY);
webView.setOnLongClickListener(new View.OnLongClickListener() {
    @Override
    public boolean onLongClick(View v) {
        WebView.HitTestResult result = mWebView.getHitTestResult();
        if (result != null) {
            int type = result.getType();
            if (type == WebView.HitTestResult.IMAGE_TYPE) {
           		//TODO实现长按保存图片
                showSaveImageDialog(result);
            }
        }
        return false;
    }
});
webView.setDownloadListener(new DownloadListener() {
    @Override
    public void onDownloadStart(String url, String userAgent, String contentDisposition,
                                String mimeType, long contentLength) {
        Uri uri = Uri.parse(url);
        Intent intent = new Intent(Intent.ACTION_VIEW, uri);
        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        }
    }
});
```


## 六、AppJs给H5提供的交互方法   
> 类名可以随意，只要注册的interfaceName是`AppJs`即可  

```
public static final String FORBID_BACK_FOR_JS = "forbidBackForJS";
public static final String GET_DEVICE_ID = "getDeviceId";
public static final String GET_GA_ID = "getGaId";
public static final String GET_GOOGLE_ID = "getGoogleId";
public static final String IS_CONTAINS_NAME = "isContainsName";
public static final String OPEN_BROWSER = "openBrowser";
public static final String OPEN_GOOGLE = "openGoogle";
public static final String OPEN_PAY_TM = "openPayTm";
public static final String OPEN_PURE_BROWSER = "openPureBrowser";
public static final String SHOULD_FORBID_SYS_BACK_PRESS = "shouldForbidSysBackPress";
public static final String SHOW_TITLE_BAR = "showTitleBar";
public static final String TAKE_CHANNEL = "takeChannel";
public static final String TAKE_FCM_PUSH_ID = "takeFCMPushId";
public static final String TAKE_PORTRAIT_PICTURE = "takePortraitPicture";
public static final String TAKE_PUSH_ID = "takePushId";

// 注册到web的方法
// addJavascriptInterface(xxx(), "AppJs")

class xxx {
    @JavascriptInterface
    public String isNewEdition() {
        return "true";
    }

    @JavascriptInterface
    @Nullable
    public String callMethod(String data) {
        try {
            JSONObject dataObj = new JSONObject(data);
            String methodName = dataObj.optString("name");
            JSONObject paraObj = dataObj.optJSONObject("parameter");
            String paraStr = dataObj.optString("parameter");
            switch (methodName) {
                case GET_DEVICE_ID:
                    return getDeviceId();
                case OPEN_PAY_TM:
                    openPayTm(paraObj.toString());
                case SHOW_TITLE_BAR:
                    showTitleBar(Boolean.valueOf(paraStr));
                ···
                default:
                    return null;
            }
        } catch (JSONException e) {
            return null;
        }
    }
}
```

### 以下为需要实现的方法(最好单独写在其他类)

``` java  
/**
 * 获取设备id
 * 必须保证有值
 * 获取不到的时候生成一个UUID
 */
@NonNull
public String getDeviceId() {
    //TODO
}

/**
 * 获取个推设备id
 * 传空串就行
 */
public String takePushId() {
    return "";
}

/**
 * 获取fcm 令牌
 * 看FCM推送的文档，有监听和获取令牌的方法
 * 详情见第八点
 */
public String takeFCMPushId() {
    //fcm生成的注册令牌
    //TODO
}

/**
 * 获取渠道
 */
public String takeChannel() {
    return "google";
}

/**
 * 获取ANDROID_ID
 * public static final String ANDROID_ID
 */
public String getGoogleId() {
    //TODO
}

/**
 * 集成branch包的时候已经带有Google Play Service核心jar包
 * 获取gpsadid 谷歌广告id
 * AdvertisingIdClient.getAdvertisingIdInfo() 异步方法
 */
public String getGaId() {
    //TODO
}

/**
 * H5调用原生谷歌登录  
 * 后续流程看第七点
 *
 * @param data {"sign":"","host":"https://bb.skr.today"}
 */
public void openGoogle(String data) {
    //TODO
}

/**
 * 打开paytm
 * 本地有paytm打开应用/没有打开web版 paytm支付(web版)需要新开一个页面
 * @param data  {"textToken":"","orderId":"","mid":"","amount":0.0}
 */
public void openPayTm(String data) {
    //TODO
}

/**
 * 头像获取
 *  流程:H5调用方法 - 自己打开图片选择器 - 回调返回H5
 *  base64使用格式：Base64.NO_WRAP
 * 
 * @param callbackMethod 回传图片时调用H5的方法名
 */
public void takePortraitPicture(String callbackMethod) {
    // TODO
    // 参考实现：成员变量记录下js方法名，图片转成base64字符串后调用该js方法传递给H5
    // 下面一段代码仅供参考，能实现功能即可
    if (!TextUtils.isEmpty(callbackMethod)) {
        StringBuilder builder = StringBuilder(callbackMethod).append("(");
        builder.append("'").append("data:image/png;base64,").append(str).append("'");
        builder.append(")");
        String method = builder.toString();
        String javaScript = "javascript:" + method;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            webView.evaluateJavascript(javaScript, null);
        } else {
            webView.loadUrl(javaScript);
        }
    }
}

/**
 * 控制webview是否显示 TitleBar
 * （点击返回键webview 后退）
 *
 * @param visible
 */
public void showTitleBar(boolean visible) {
    //TODO
}

/**
 *  AppJs是否存在交互方法 告诉H5是否存在传入的对应方法
 *
 * @param name 方法名
 */
fun isContainsName(callbackMethod: String, name: String): String {
   Log.v(TAG, "isContainsName:${callbackMethod};${name}")
   val has: Boolean = when (name) {
        FORBID_BACK_FOR_JS,GET_DEVICE_ID,GET_GA_ID,... -> {
            true
        }
        else -> {
            false
        }
    }
    if (context is WebActivity) {
        context.runOnUiThread {
            val webView = context.getWebView()
            val javaScript = "javascript:$callbackMethod('${has.toString()}')"
            webView.evaluateJavascript(javaScript, null)
        }
    }
  return has.toString()
}

/**
 * 由h5控制是否禁用系统返回键
 * @param forbid     是否禁止返回键 1:禁止
 */
public void shouldForbidSysBackPress(int forbid) {
    //TODO 以下仅供参考
    //WebActivity成员变量记录下是否禁止
    mContext.setShouldForbidBackPress(forbid);
    //WebActivity 重写onBackPressed方法 变量为1时禁止返回操作
}

/**
 * 由h5控制返回键功能
 *
 * @param forbid     是否禁止返回键 1:禁止
 * @param methodName 反回时调用的h5方法 例如:detailBack() webview需要执行javascrept:detailBack()
 */
public void forbidBackForJS(int forbid, String methodName) {
    //TODO 以下仅供参考
    mContext.setShouldForbidBackPress(forbid);
    //同上
    mContext.setBackPressJSMethod(methodName);
    //WebActivity成员变量记录下js方法名 在禁止返回时调用js方法
}

/**
 * 使用手机里面的浏览器打开 url
 *
 * @param url 打开 url
 */
public void openBrowser(String url) {
    //TODO 以下仅供参考
    Uri uri = Uri.parse(url);
    Intent intent = new Intent();
    intent.setAction(Intent.ACTION_VIEW);
    intent.setData(uri);
    if (intent.resolveActivity(mContext.getPackageManager()) != null) {
        mContext.startActivity(intent);
    }
}

/**
 * 打开一个基本配置的webview （不修改UA、可以缓存）
 * 打开新页面 
 * 加载webview的情况分类(判断依据：url、postData、html)
 *    |-------1、只有url：webView.loadUrl()
 *    |-------2、有url和postData：webView.postUrl()
 *    |-------3、有html webView.loadDataWithBaseURL()
 *
 * @param json 打开web传参 选填
 * {"title":"", 打开时显示的标题
 *  "url":"", 加载的地址
 *  "hasTitleBar":false, 是否显示标题栏 //注意：后台可能会返回null，需做好空值判断，默认设定false
 *  "rewriteTitle":true, 是否通过加载的Web重写标题 //注意：后台可能会返回null，需做好空值判断，默认设定false
 *  "stateBarTextColor":"black", 状态栏字体颜色 black|white
 *  "titleTextColor":"#FFFFFF", 标题字体颜色
 *  "titleColor":"#FFFFFF", 标题背景色
 *  "postData":"", webView post方法时需要传参
 *  "html":"", 加载htmlCode,
 *  "webBack":true, true:web回退(点击返回键webview可以回退就回退，无法回退的时候关闭该页面)|false(点击返回键关闭该页面) 直接关闭页面
 * }
 */
public void openPureBrowser(String json) {
    //TODO
}

/**
 * 使用手机里面的浏览器打开 url
 * 注：如果是WhatsApp对话跳转，先判断本地是否安装了WhatsApp，
       如果已经安装直接跳转，没有安装再打开浏览器
 *
 * @param url 打开 url
 */
public void openBrowser(String url) {
    if (mContext instanceof WbMainActivity) {
        Uri uri = Uri.parse(url);
        Intent intent = new Intent().setAction(Intent.ACTION_VIEW).setData(uri);
       //判断是否为WhatsApp跳转 且本地已安装
        if (url.contains("https://api.whatsapp.com/") && isAPPInstalled(mContext, "com.whatsapp")) {
            intent.setPackage("com.whatsapp");
        }
        if (intent.resolveActivity(mContext.getPackageManager()) != null) {
            mContext.startActivity(intent);
        }
    }
}

public Boolean isAPPInstalled(Context context, String packageName) {
    PackageManager pm = context.getPackageManager();
    List<PackageInfo> pinfo = pm.getInstalledPackages(0);
    for (int i = 0; i < pinfo.size(); i++) {
        if (pinfo.get(i).packageName.equals(packageName)) {
            return true;
        }
    }
    return false;
}

```


#### 交互中出错常见问题  
* 拼接上传base64字符串时，注意最后的`')`和base64之间不要留空和空行  
![Base64拼接问题](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/base64_error.jpg)   

## <span id="login">七、三方登陆流程</span>  
![打开Firebase登录授权](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/google_login_config.png)  
1、H5调用Js方法请求进行三方登陆
> 传给客户端一个JsonString如下格式：  
> ```{"sign":"781h18fn1u34n","host":"https://bb.skr.today"}```  

2、三方登陆获取他们授权提供的提供的 id、name、email  

* 发起Google 登录

3、请求服务器接口  
>进行GET请求  
>host:H5传过来的值  
>path如下：
>
>```
>/user/google/doLogin2.do
>
>```
>请求参数
>
>```
>id:  
>name:  
>email:  
>type: 0 : Facebook | 1 : Google    
>sign: H5传过来的值
>```

示例接口请求结果如下

``` json
{
	"code": "200",
	"data": {
		"token1": "anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==",
		"token2": "NTkxMjRkM2ZiYmFhMmMwNjYyZjg0MDI0NjNjYzMyMWU=",
		"url": "https://bb.skr.today/zh_cn/?sign=11"
	}
}
```


4、储存Token  
> token1、token2需要存到CookieManager中储存格式如下:  
> 
>  * `分成两次存储`  
>  * 没有token时不用存储，只需要加载url  
> 
> ``` java  
> "token1="anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==";expires=1; path=/" 
>  
> "token2="anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==";expires=1; path=/"  
> //例如：
> CookieManager.getInstance().setCookie(host, "token1="anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==";expires=1; path=/")
> CookieManager.getInstance().setCookie(host, "token2="anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==";expires=1; path=/")
> ```
>    
> 
> host:H5传过来的值

```
CookieManager.getInstance().setCookie(host, cookie)
```


5、跳转的接口中给定的url  

```webView.loadUrl()```  


> 以下代码仅供参考  


``` kotlin

fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?): Boolean {
    ...
    ...
    if (requestCode == RC_SIGN_IN) {
       Task<GoogleSignInAccount> task = GoogleSignIn.getSignedIn}ccountFromIntent(data);
       GoogleSignInAccount account;
       try {
           account = task.getResult(ApiException.class);
           String id = account.getId();
           String name = signInAccount.getDisplayName();
           //TODO接口请求获取服务器返回的token保存到cookie中然后打开指定的url
           /**
            *@param type Facebook:0 | google:1
            *@param host 调用登录时H5传过来的值
            *@param sign 调用登录时H5传过来的值
            */
            "${host}/user/google/doLogin2.do?id=${id}&name=${name}&email=${email}&sign=${sign}&type={type}"
       } catch (ApiException e) {
       }
    }
}
    
请求返回值:{"code":"200","data":{"token1":"anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==","token2":"NTkxMjRkM2ZiYmFhMmMwNjYyZjg0MDI0NjNjYzMyMWU=","url":"https://bb.skr.today/zh_cn/?sign=11"}}
储存格式: 
	   "token1="anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==";expires=1; path=/"  
	   "token2="anN4aG9leHpheWlndWJuY3RocG9udHFoa2tjMg==";expires=1; path=/"  
	    CookieManager.getInstance().setCookie(host, cookie)
	    public abstract void setCookie(String url, String value);
```

## <span id="fcm">八、FCM推送</span>  
> 要求：

> * 透传消息需要在通知栏创建Notification
> * 点击通知效果  
> url有值：webview打开该url；url为空：打开app

透传消息数据格式:

```remoteMessage.getData().get("data")```

``` json
{
	"createTime": "",long 推送的时间
	"pushTopic": "",string 推送的标题
	"pushContent": "",string 展示推送的内容文本
	"url": ""string 点击推送需要跳转的Url
}
```


> 以下代码仅供参考  

```
@Override
public onMessageReceived(RemoteMessage remoteMessage) {
    super.onMessageReceived(remoteMessage);
    Map<String, String> data = remoteMessage.getData();
    if (data.isEmpty()) {
        PushMessage pushMessage = Gson().fromJson(remoteMessage.getData(), PushMessage.class);
        createNotification(getBaseContext(), pushMessage);
    }

    // Check if message contains a notification payload.
    RemoteMessage.Notification notification = remoteMessage.getNotification();
    if (notification != null) {
        showNotification(getBaseContext(), notification);
    }
}
```


```
private void createNotification(Context context, PushMessage pushMessageModel) {
    String channelId = context.getString(R.string.app_name);
    String notificationTitle = pushMessageModel.pushTopic;
    if (TextUtils.isEmpty(notificationTitle)) {
        notificationTitle = channelId;
    }
    NotificationCompat.Builder builder = new NotificationCompat.Builder(context, channelId);
    builder.setContentTitle(notificationTitle);
    builder.setContentText(pushMessageModel.pushContent);
    builder.setAutoCancel(true);
    int createTime = pushMessageModel.createTime;
    builder.setWhen(createTime);
    String brand = Build.BRAND;
    PendingIntent intent = setPendingIntent(context, pushMessageModel);
    builder.setSmallIcon(R.mipmap.push);
    if (!TextUtils.isEmpty(brand) && brand.equalsIgnoreCase("samsung")) {
        Bitmap bitmap = BitmapFactory.decodeResource(context.getResources(), R.mipmap.push);
        builder.setLargeIcon(bitmap);
    }
    builder.setContentIntent(intent);
    builder.setDefaults(NotificationCompat.DEFAULT_ALL);
    NotificationManager notificationManager = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
    if (notificationManager != null) {
        int notificationId = (int) System.currentTimeMillis();
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            createNotificationChannel(channelId, notificationManager);
        }
        notificationManager.notify(notificationId, builder.build());
    }

}

@RequiresApi(Build.VERSION_CODES.O)
private NotificationChannel createNotificationChannel(
        String channelId,
        NotificationManager notificationManager
) {
    NotificationChannel notificationChannel =
            new NotificationChannel(
                    channelId,
                    channelId,
                    NotificationManager.IMPORTANCE_DEFAULT
            );
    notificationChannel.enableLights(true); //开启指示灯，如果设备有的话。
    notificationChannel.enableVibration(true); //开启震动
    notificationChannel.setLightColor(Color.RED); // 设置指示灯颜色
    notificationChannel.setLockscreenVisibility(Notification.VISIBILITY_PRIVATE);//设置是否应在锁定屏幕上显示此频道的通知
    notificationChannel.setShowBadge(true); //设置是否显示角标
    notificationChannel.setBypassDnd(true); // 设置绕过免打扰模式
    notificationChannel.setVibrationPattern(new long[]{100, 200, 300, 400}); //设置震动频率
    notificationChannel.setDescription(channelId);
    notificationManager.createNotificationChannel(notificationChannel);
    return notificationChannel;
}
```


```
private PendingIntent setPendingIntent(Context context, PushMessage data) {
    Intent intent;
    String url = data.url;
    if (TextUtils.isEmpty(url)) {
        PackageManager packageManager = context.getPackageManager();
        intent = packageManager.getLaunchIntentForPackage(context.getPackageName());
    } else {
       //TODO WebView打开这个Url
        intent =
    }
    return PendingIntent.getActivity(
            context,
            System.currentTimeMillis(),
            intent,
            PendingIntent.FLAG_CANCEL_CURRENT
    );
}
```


## FCM推送自测方式(需科学上网)
要发送消息，应用服务器需发出 POST 请求。  

```
https://fcm.googleapis.com/fcm/send
```

HTTP 标头必须包含以下标头：

* Authorization: key=YOUR_SERVER_KEY
* Content-Type: application/json（JSON 格式）；application/x-www-form-urlencoded;charset=UTF-8（纯文本格式）。 如果省略 Content-Type，即视为纯文本格式。

例如：

```
Content-Type:application/json
Authorization:key=AIzaSyZ-1u...0GBYzPu7Udno5aA

{
	"to": "bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",//推送设备的令牌
	"data": {
		"data": {
			"createTime": 1588821695000,
			"pushTopic": "topic",
			"pushContent": "推送测试内用1",
			"url": "https://www.baidu.com"
		}
	}
}
```


![Service Key](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/fcm_key.png)  
