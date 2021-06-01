## 更新sdk及gradle

版本发布比较早，现在有新的SDK版本和Gradle版本，可修改文件，以使用目前安装的编译工具，不做修改，则会使用工程中原有的配置重新下载指定版本的SDK和Gradle进行编译

> 以下修改参数根据文档编写时间，取当前开发环境中的版本，如果后续有更新，可参照修改为对应版本

需要修改如下文件

### app/build.gradle
```
android {
    //compileSdkVersion 24
    compileSdkVersion 30
    //buildToolsVersion "23.0.3"
    buildToolsVersion "30.0.3"

    defaultConfig {
        applicationId "com.example.android.pets"
        minSdkVersion 15
        //targetSdkVersion 24
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"
    }
    //...
}

dependencies {
    //compile 'com.android.support:appcompat-v7:24.1.1'
    //compile 'com.android.support:design:24.1.1'
    implementation 'androidx.appcompat:appcompat:1.1.0'
    implementation 'com.google.android.material:material:1.1.0'
}
```
根据当前电脑中已安装的版本进行修改，如果不清楚，可以创建一个新的工程，查看此文件配置

### build.gradle
```
buildscript {
    repositories {
        //加入google
        google()
        jcenter()
    }
    dependencies {
        //classpath 'com.android.tools.build:gradle:2.1.2'
        classpath 'com.android.tools.build:gradle:4.1.3'
    }
}

allprojects {
    repositories {
        //加入google
        google()
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```


### gradle/wrapper/gradle-wrapper.properties
此处为修改gradle工具版本
```
//distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip
distributionUrl=https\://services.gradle.org/distributions/gradle-6.5-all.zip
```
