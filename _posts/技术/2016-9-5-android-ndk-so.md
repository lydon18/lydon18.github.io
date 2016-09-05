---
layout: post
title: AndroidStudio 下用ndk 生成so包
category: 技术
keywords: java
---

## 安装ndk
打开androidstudio，点击sdkManager,点击sdktool,选择ndk点击install
![安装ndk](/lydon18.github.io/assets/img/0069.png)

## 生成so
1.	第一次生成so文件的时候，我们先使用NDK的sample下的hello-jni的例子。						
2.	通过Create project  from existing source，选择hello-jni的目录，按默认选项创建工程即可；						
3.	观察hello-jni的工程目录结构，发现根目录下多了一个名叫jni的文件夹，该文件夹下面有两个文件，Android.mk和hello-jni.c。				
     Android.mk是用来生成so文件的配置文件，我们来看看里面是什么：
     
     ```					
	LOCAL_PATH := $(call my-dir)					
	include $(CLEAR_VARS)
	LOCAL_MODULE    := hello-jni
	LOCAL_SRC_FILES := hello-jni.c
	include $(BUILD_SHARED_LIBRARY)
	include $(BUILD_SHARED_LIBRARY)表示会生成一个动态链接库，即so文件，生成的库文件名称为lib*.so
	LOCAL_MODULE    := hello-jni表示将要生成一个名称为libhello-jni.so的库文件
	LOCAL_SRC_FILES := hello-jni.c表示生成库文件的源文件是hello-jni.c
	```
	
	    打开hello-jni.c文件，会看到里面有一个函数：	
	    
	    ```		
	jstring Java_com_example_hellojni_HelloJni_stringFromJNI( JNIEnv* env, jobject thiz )				
	{			
	    return (*env)->NewStringUTF(env, "Hello from JNI !");				
	}
	```
	
	这个函数从根据什么原则生成的呢？是根据src下的HelloJni.Java文件中的函数 public native String  stringFromJNI();生成的。命名规则为：
	Java_(固定开头)com_example_hellojni(用"_"连接的包名)_HelloJni(类名)_stringFromJNI(函数名)
	    这个函数的生成可以使用javah的命令：用命令行方式进入工程目录的bin\classes目录下，运行javah com.example.hellojni.HelloJni，就会把所
	    有native的函数都按规则生成在一个名为HelloJni.h的头文件里。
	    
4.	在同模块build.gradle defaultConfig节点中添加				
	```
	 defaultConfig {
        ndk{
            moduleName "hello-jni"  //设置库(so)文件名称
        }
    }
    ```
5.	生成so文件，点击project buildproject 或者refresh项目就会自动生成libhello-jni.so的文件
6.	so文件的调用			
      在HelloJni.java文件中有一段代码：
      				
      ```
    static {
        System.loadLibrary("hello-jni");
    }
    ```
    使用loadLibrary就可以加载该so文件了，加载的时候不需要写libhello-jni.so，只要写hello-jni就可以了。
	
	