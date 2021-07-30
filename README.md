## 需求文档
1、交互方法，与H5的交互方法都需要加上。

* [Kotlin版（这里可以点击）](https://github.com/zhuyitian/RequirementsDocument/blob/main/%E9%9C%80%E8%A6%81%E5%8A%A0%E5%85%A5%E7%9A%84%E6%96%B9%E6%B3%95.md)  
* [Java版（这里可以点击）](https://github.com/zhuyitian/RequirementsDocument/blob/main/%E9%9C%80%E8%A6%81%E5%8A%A0%E5%85%A5%E7%9A%84%E6%96%B9%E6%B3%95_java.md)  

2、接口[Api（这里可以点击）](https://github.com/zhuyitian/RequirementsDocument/blob/main/api%E6%96%87%E6%A1%A3.md)

## 启动流程

![](https://github.com/zhuyitian/RequirementsDocument/blob/main/src/open.png?raw=true)



0、显示H5
有广告链接时先显示广告页（倒计时5S可以跳过）同一时间加载web页面
1、显示马甲




![](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/main.png)  

## 需要找对接人获取的信息

 1、Facebook参数  
 2、Branch参数  
 3、Firebase账号 

## 给对接人提供的信息文件
1、使用keytool获取签名文件MD5 SHA1 SHA256 

```
keytool -list -v -keystore xxx.jks
```

2、Branch schema  

![](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/branch_scheme_info.png)  

3、Application id  

![](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/application_id.png)


## 测试链接说明  

![](https://gitee.com/google_project_team/googlevestrequire/raw/master/src/test_flow.jpeg)  

# RequirementsDocument
