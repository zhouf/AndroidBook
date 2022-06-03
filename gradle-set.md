# 提升AndroidStudio中的Gradle编译速度

> Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化构建开源工具。它使用一种基于Groovy的特定领域语言(DSL)来声明项目设置

## 项目中的gradle设置

在AndroidStudio项目中，包含与gradle配置相关的文件`项目名\gradle\wrapper\gradle-wrapper.properties`，内容如下
```
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.1.1-all.zip
```
Android Studio 编译时会自动解析`\gradle\wrapper\gradle-wrapper.properties`文件，并从distributionUrl中获取下载地址，自动下载并缓存到本地的对应的.gradle目录下，通常位于当前用户目录中，如`C:\Users\用户名\.gradle`，如果本地已存在缓存的文件，则直接使用，不会重新再次下载，所以往往在第一次下载时会很花时间

如果安装gradle时间太久，或下载失败，可以尝试如下的方案

## 使用本地离线Gradle压缩包

可先到官网[https://gradle.org/releases/](https://gradle.org/releases/)下载对应版本的压缩包，下载完整版本complete，下载完成后不用解压，有两种方法可以设置使用本地Gradle压缩包

方法一：

退出运行AndroidStudio

将下载的压缩包放在`C:\Users\用户名\.gradle\wrapper\dists\gradle-6.1.1-all\cfmwm155h49vnt3hynmlrsdst`目录下，删除该目录下的所有其他文件，注意目录中的版本号，版本号后面的目录名`cfmwm155h49vnt3hynmlrsdst`可能会不同

方法二：

修改`gradle-wrapper.properties`文件，将`distributionUrl`改为下载的路径。以下配置根据自己的下载路径做调整，不要放在中文或包含空格的目录下
```
distributionUrl=file:///E://Dev//Gradle//gradle-6.1.1-all.zip
```
修改文件后保存，点击右上角的`Sync Now`执行同步


## 使用国内maven仓库镜像

在工程中添加库引用时会通过gradle进行自动下载，默认的下载库是google()和jcenter()，可以修改为使用国内的仓库镜像
```
buildscript {
    repositories {
        maven { url 'https://maven.aliyun.com/repository/google/' }
        maven { url 'https://maven.aliyun.com/repository/public/' }
        mavenCentral()
    }
}

plugins{
    ....
}
...
```
> 在新的项目配置文件中，已经没有了allprojects配置