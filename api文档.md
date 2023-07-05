
## 

### GET请求

### Host

> host(https)和code需要找对接人要(在提供的物料里面)

### Path

```
/admin/client/vestSign.do
```

### 请求参数(query)

```
vestCode: 标识代码
channelCode: 渠道代码(谷歌市场现在可以写死google)
version: App当前版本名(例如1.0.0)  
deviceId: 设备ID(设备唯一id)
timestamp: 时间戳(系统时间)
countryCode: 具体获取方法，参考下方 CountryCodeUtils.java
```

请求例如：

> ```
> https://d2d0drb98uxrz0.cloudfront.net/admin/client/vestSign.do?vestCode=CUHX4A73&version=1.0&channelCode=google&deviceId=ebe2f38b83fcd5d1&timestamp=1584324183767&countryCode=US
> ```

### 返回数据样本

```
{
	"code": 200,
	"data": {
			"adjustToken": "11",
			"umKey": "11",
			"fieldCol": "black", //H5的状态栏和titlebar的字体颜色 fieldCol  white(白) ,black(黑)
			"gtSecert": "11",
			"screenDirect": 0,
			"umkeyIOS": "11",
			"version": "1.1,1.2",
			"h5Url": "https://app.zzwpx.com/", //H5业务链接地址
			"gtMaster": "11",
			"gtKey": "11",
			"backgroundCol": "#FFFFFF", //H5的状态栏和titlebar的背景色
			"advOn": 0, //1显示广告
			"advImg": "https://app.zzwpx.com/", //广告图片地址
			"advUrl": "https://app.zzwpx.com/", //广告业务链接地址
			"gtId": "11", //现在改为返回facebook id，如果不为空，需要手动进行设置facebook id，具体见下方‘手动设置facebook Id’
			"um": "11",
			"anUmengKey": "11", //安卓友盟key
			"channelName": "google",
			"screenOn": 1,
			"vestCode": "MMVUT5DX",
			"vestName": "KomTure",
			"channelCode": "google",
			"status": 0 //0显示H5地址
	},
	"msg": "成功"
}
```

### 手动设置Facebook Id
如果接口返回的`gtId`不为空，就需要调用`FacebookSdk.setApplicationId()`接口来进行手动设置Facebook Id。

例如：
接口返回"gtId":"1212121212"，此时，就需要调用`FacebookSdk.setApplicationId("1212121212")`方法来手动设置Facebook Id。



### 获取countryCode方法
```java
public class CountryCodeUtils {
    private static final String TAG = "CountryCodeUtils";

    private static volatile CountryCodeUtils instance;

    public static CountryCodeUtils getInstance() {
        if (instance == null) {
            synchronized (CountryCodeUtils.class) {
                if (instance == null) {
                    instance = new CountryCodeUtils();
                }
            }
        }
        return instance;
    }


    public String getDeviceCountryCode(Context context) {
        String countryCode;

        TelephonyManager tm = (TelephonyManager) context.getSystemService(Context.TELEPHONY_SERVICE);
        if (tm != null) {
            countryCode = tm.getSimCountryIso();
            if (countryCode != null && countryCode.length() == 2) {
                return countryCode.toUpperCase(Locale.ENGLISH);
            }

            if (tm.getPhoneType() == TelephonyManager.PHONE_TYPE_CDMA) {
                countryCode = getCDMACountryIso();
            } else {
                countryCode = tm.getNetworkCountryIso();
            }

            if (countryCode != null && countryCode.length() == 2) {
                return countryCode.toUpperCase(Locale.ENGLISH);
            }
        }

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            countryCode = context.getResources().getConfiguration().getLocales().get(0).getCountry();
        } else {
            countryCode = context.getResources().getConfiguration().locale.getCountry();
        }

        if (countryCode != null && countryCode.length() == 2) {
            return countryCode.toUpperCase(Locale.ENGLISH);
        }

        return "US";
    }

    @SuppressLint("PrivateApi")
    private String getCDMACountryIso() {
        try {
            Class<?> systemProperties = Class.forName("android.os.SystemProperties");
            Method get = systemProperties.getMethod("get", String.class);
    
            String homeOperator = ((String) get.invoke(systemProperties,
                    "ro.cdma.home.operator.numeric"));

            int mcc = Integer.parseInt(homeOperator.substring(0, 3));
            
            switch (mcc) {
                case 404:
                case 405:
                case 406:
                    return "IN";
                case 510:
                    return "ID";
                case 515:
                    return "PH";
                case 724:
                    return "BR";
                default:
                    return "US";
            }
        } catch (Exception ignored) {
            Log.e(TAG, "getCDMACountryIso: error = " + ignored.getMessage());
        }

        return "US";
    }

}

```




