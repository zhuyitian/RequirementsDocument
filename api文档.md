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
```

请求例如：

> ```
> https://d2d0drb98uxrz0.cloudfront.net/admin/client/vestSign.do?vestCode=CUHX4A73&version=1.0&channelCode=google&deviceId=ebe2f38b83fcd5d1&timestamp=1584324183767
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
			"gtId": "11",
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