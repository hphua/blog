# APP应用的H5资源文件工程化

###应用场景
![](http://upload-images.jianshu.io/upload_images/1263508-e4e9311b23283d74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

APP工程中使用H5页面，这些H5资源将与APP安装包一起分发出去。APP应用开发与H5页面开发，通常是由两批人员实现。那么H5页面有必要存在于独立的工程中，而非与APP工程放一起。只需要在APP打包前，将H5工程输出资源，复制到APP工程中就可以。
H5工程是一VUE项目，编译生成文件存放在dist目录下。

![H5工程](http://upload-images.jianshu.io/upload_images/1263508-f588a57ccafdeeb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*目标：*
1、APP应用开发人员，不一定需要熟悉H5内容。
2、APP可直接使用H5工程输出的内容。开发时不需要同时打开H5工程；
3、H5工程输出内容，可复用。

###方法说明
*涉及工具*
WebStorm、npm、TeamCity、Maven

![](http://upload-images.jianshu.io/upload_images/1263508-00c98219fce49199.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*TeamCity构建步骤：*
![](http://upload-images.jianshu.io/upload_images/1263508-1d118395ce432bb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
1、ready准备工作
```
rd /s /q .\dist
rd /s /q .\target
dir
```
2、npn install
3、构建工程
`npm run build`
4、压缩vue工程输出资源
```
md .\target
cd .\dist
zip -r ..\target\examvue.zip *
```
5、将ZIP资源发布到maven中心库
```
mvn deploy:deploy-file -e -DgroupId=com.hb.generalqb -DartifactId=examvue -Dversion=0.0.1-SNAPSHOT -Dpackaging=zip -Dfile=.\target\examvue.zip -DrepositoryId=snapshots -Durl=http://192.168.1.116:8081/nexus/content/repositories/snapshots
```
注：版本号附加<font color=#ff000>SNAPSHOT</font>，这样版本可方便迭代。
上述为了支持mvn命令，需要对maven的setting.xml中添加如下内容：
![](http://upload-images.jianshu.io/upload_images/1263508-f42ad8e26ad4e784.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*APP工程*
通用mvn命令，获取vue的资源。示例：
```
mvn dependency:copy-dependencies -DoutputDirectory=.\\nexus\\  -DincludeScope=runtime 
```

下载ZIP到本地。

解压ZIP文件到APP资源存放目录下。
 ```
unzip -o ./nexus/examvue-0.0.1-SNAPSHOT.zip -d ./platforms/ios/HBApp/assets/
```


本文章适合部分团队。有不足的地方，欢迎点评。若有其它解决方案，请留言！


















