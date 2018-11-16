## 1.JNI介绍
### 1.1.简介
* 定义：Java Native Interface，即Java本地接口
* 作用：使得Java与本地其他类型语言（如C、C++）交互
即在Java代码里调用C、C++等语言的代码或C、C++代码调用Java代码
* 特别注意：
JNI是Java调用Native语言的一种特性
JNI是属于Java的，与Android无直接关系
### 1.2 为什么要有 JNI
* 背景：实际使用中，Java 需要与本地代码进行交互
* 问题：因为 Java 具备跨平台的特点，所以Java与本地代码交互的能力非常弱
* 解决方案： 采用 JNI特性 增强 Java 与 本地代码交互的能力
### 1.3 实现步骤
* 在Java中声明Native方法（即需要调用的本地方法）
* 编译上述 Java源文件javac（得到 .class文件）
* 通过 javah 命令导出JNI的头文件（.h文件）
* 使用 Java需要交互的本地代码实现在 Java中声明的Native方法
* 如 Java 需要与 C++ 交互，那么就用C++实现 Java的Native方法
* 编译.so库文件
* 通过Java命令执行 Java程序，最终实现Java调用本地代码
## 2.NDK介绍
### 2.1 简介
* 定义：Native Development Kit，是 Android的一个工具开发包 
NDK是属于 Android 的，与Java并无直接关系
* 作用：快速开发C、 C++的动态库，并自动将so和应用一起打包成 APK 
即可通过 NDK在 Android中 使用 JNI与本地代码（如C、C++）交互
* 应用场景：在Android的场景下 使用JNI
即 Android开发的功能需要本地代码（C/C++）实现
![ndk.png](http://upload-images.jianshu.io/upload_images/944365-f66342df568ef7aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 2.1 使用步骤
* 配置 Android NDK环境
* 创建 Android 项目，并与 NDK进行关联
* 在 Android 项目中声明所需要调用的 Native方法
* 使用 Android需要交互的本地代码 实现在Android中声明的Native方法 
* 比如 Android 需要与 C++ 交互，那么就用C++ 实现 Java的Native方法
* 通过 ndk - bulid 命令编译产生.so库文件
* 编译 Android Studio 工程，从而实现 Android 调用本地代码
## 3. NDK与JNI关系
![jk.png](http://upload-images.jianshu.io/upload_images/944365-6607e9321d3cbddd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 4. 具体使用
### 4.1 配置 Android NDK环境
### 4.2 关联 Andorid Studio项目 与 NDK
* 1.在Gradle的 local.properties中添加配置
ndk.dir=/Users/suxi/Documents/android-sdk-macosx/ndk-bundle
* 2.在libs文件夹下添加对应平台的.so文件，在cpp文件夹下添加.h文件
在CMakeLists.txt文件里配置.so库路径，.h文件路径，native-lib.cpp文件路径
```java
cmake_minimum_required(VERSION 3.4.1)

add_library( native-lib SHARED src/main/cpp/native-lib.cpp )

include_directories(src/main/cpp)

add_library(bipay SHARED IMPORTED)
set_target_properties(bipay PROPERTIES IMPORTED_LOCATION
                      ${PROJECT_SOURCE_DIR}/libs/${ANDROID_ABI}/libbipay.so )

find_library( log-lib log )

target_link_libraries(
                        native-lib
                        bipay
                        ${log-lib}
                        )

```
* 3.在Gradle的build.gradle配置节点
 
 ```java
 externalNativeBuild {
            cmake {
                cppFlags ""
                abiFilters "armeabi-v7a"
            }
        }


sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }


  externalNativeBuild {
        cmake {
            path "CMakeLists.txt"
        }
    }  
 ```
* 4.在native-lib.cpp文件里写调用C++的方法，方法名为java文件里定义此方法的路径加方法名
```java
Java_com_spark_ndkdemo_MainActivity_getMnemonic(){}

public native String getMnemonic(int language);
```
