# 一、Android SDK

## 1、安装

个人是倾向使用Intellij IDEA(以下简称IDEA)的，故计划使用IDEA上安装Android SDK开发Android APP。

打开IDEA，选择File-Project Structure-Platform Settings-SDK，下载Android SDK。

安装后，选择一个项目，注意修改语言为Java。

之后会导入gradle包。

### 创建AVD

运行时会提醒No Target Device，需要自己创建一个。创建虚拟设备，AVD， Android Virtual Device。

启动AVD管理器，会提示choose device definition。左边的五个种类有TV、Wear、Phone、Tablet、Automotive表示Android对电视、可穿戴设备、手机、平板和汽车提供了支持。中间一列为针对左侧的某个类别已经建立好的虚拟设备的配置文件，可**基于配置文件直接创建虚拟设备**。右侧为配置文件的图形化描述，包括屏幕尺寸(size、ratio)和清晰度(density)。默认是size:normal,ratio:long,density:420dpi。

~~~
dpi是每英寸点数
~~~

点击next后，出现的是系统映像(system image)文件选择文件。在这里决定虚拟手机的Android系统版本、系统架构和API等。

选择R，APIlevel为30，架构为x86，点击Next。

~~~
x86标识一套通用的计算机指令的集合。
~~~

可以选择对虚拟设备的参数进行修改。按下finish，就能创建成功了。

### AVD与真机的区别

* AVD 不支持真实的电话接听和呼叫，但是可以通过控制台模拟电话呼叫。
* AVD 不支持USB连接。
* AVD 不支持相机/视频捕捉（输入）。
* AVD 不支持耳机。
* AVD 不支持蓝牙。
* AVD 不能在运行时确认SD卡的插入和弹出状态。
* AVD 不能确定电池的电量多少和充电状态。
* AVD 不能确定连接状态。

## 2、SDK相关

SDK(software development kits)软件开发工具包，是软件开发工程师用于为特定的软件包、软件框架、硬件平台、操作系统等建立应用软件的开发工具的集合。

首先分析SDK的目录

* extras
  * 用于存放 Android 附加支持文件，主要包含 Android 的 support 支持包、Google 的几个工具和驱动、Intel 的 IntelHaxm
* platforms
  * 用于存放 Android SDK Platforms 平台相关文件，包括字体、res 资源、模板等
* platform-tools
  * 主要包含各个平台工具，具体为
  * api目录
    * 里面有**api-versions.xml文件，指明所需类的属性、方法、接口等**
  * adb.exe
    * 连接Android手机与PC端，可以在PC端上控制手机的操作
  * fastboot.exe
    * 可以进行重启系统、重写内核、查看连接设备、写分区、清空分区等操作
  * NOTICE.txt
    * 给出提示信息
  * source.properties
    * 资源属性信息文件，主要显示该资源生成时间、系统类型、资源 URL 地址等
* system-images
  * 用于存放系统需要的所有映像
* .temp
* tools
  * 包含重要的工具

## 3、Android.jar

Java项目引入的工具类，也就是jar包

|       名称       | 作用                                                     |
| :--------------: | -------------------------------------------------------- |
|   Android.app    | 提供高层的程序模型和基本的运行环境                       |
| Android.content  | 包含各种对设备上的数据进行访问和发布的类                 |
| Android.database | 通过内容提供者浏览和操作数据库                           |
| Android.graphics | 底层的图形库                                             |
| Android.location | 定位和相关服务的类                                       |
|  Android.media   | 提供一些类管理多种音频、视频的媒体接口                   |
|   Android.net    | 提供帮助网络访问的类，超过通常的 java.net.* 接口         |
|    Android.os    | 提供系统服务、消息传输、IPC 机制                         |
|  Android.openg   | 提供 OpenGL 的工具                                       |
| Android.provider | 提供类，访问 Android 的内容提供者                        |
|   Android.view   | 提供基础的用户界面接口框架                               |
|   Android.util   | 涉及工具性的方法，例如时间日期的操作                     |
|  Android.webkit  | 默认浏览器操作接口                                       |
|  Android.widget  | 包含各种 UI 元素（大部分是可见的）在应用程序的屏幕中使用 |

## 4、Android API

* Location-Based Services（定位服务）

通过集成GPS芯片来接收卫星信号，通过GPS全球定位系统中至少3个卫星和原子钟来获取手机当前的坐标数据，通过转换成为地图上的具体位置。

* Media APIs（多媒体接口）

集成影音解码器以及相关的多媒体API，可以使手机支持MP3，MP4

* 3D Graphics with OpenGL（3D 图形处理 OpenGL）

游戏娱乐功能，支持3D游戏或应用场景的就需要使用3D技术。手机厂商根据手机的屏幕和定位集成不同等级的3D加速图形芯片来加强手机的娱乐性。

* Low-Level Hardware Access（低级硬件访问）

主要用于控制手机底层方面的操作，支持不同设备的操作管理，如蓝牙及WiFi

# 二、第一个app

在new-project中选择Android-basic activity指定工程文件存放的位置，选择运行平台和语言，finish后就会完成工程的创建。

选择要运行的AVD，可查看运行效果。

## 1、分析

这是原有的代码

~~~java
package com.example.myapplication;

import android.os.Bundle;
import com.google.android.material.floatingactionbutton.FloatingActionButton;
import com.google.android.material.snackbar.Snackbar;
import androidx.appcompat.app.AppCompatActivity;
import androidx.appcompat.widget.Toolbar;
import android.view.View;

import android.view.Menu;
import android.view.MenuItem;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Toolbar toolbar = findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        FloatingActionButton fab = findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                        .setAction("Action", null).show();
            }
        });
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Inflate the menu; this adds items to the action bar if it is present.
        getMenuInflater().inflate(R.menu.menu_main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        int id = item.getItemId();

        //noinspection SimplifiableIfStatement
        if (id == R.id.action_settings) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }
}
~~~

MainActivity代码简单，表明了MainActivity是继承AppCompatActivity，并重写了onCreate方法。

onCreate方法调用了父类的onCreate方法，调用setContentView方法显示视图。Android工程中使用XML来设计视图界面。R.layout.activity_main 是 Android 工程中默认的布局文件的名字，即 activity_main.xml。

activity_main.xml

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="introduction.android.helloworld.MainActivity">
    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme.AppBarOverlay">
        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:popupTheme="@style/AppTheme.PopupOverlay" />
    </android.support.design.widget.AppBarLayout>
    <include layout="@layout/content_main" />
    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="@dimen/fab_margin"
        app:srcCompat="@android:drawable/ic_dialog_email" />
</android.support.design.widget.CoordinatorLayout>
~~~

CoordinatorLayout 布局是 support v7 系统新增的布局，具有便于调度协调子布局的特点。该布局可看作是增强版的 FrameLayout，通常与 ToolBar 和 FloatingActionButton 合用。

ToolBar 是图 5 中显示 HelloWorld 的蓝色工具栏，具有承载系统菜单的功能。相关代码

~~~xml
<android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme.AppBarOverlay">
        
    	<android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:popupTheme="@style/AppTheme.PopupOverlay" />
    
</android.support.design.widget.AppBarLayout>
~~~

FloatingActionButton 是图 5 中右下侧的邮箱图标的按钮，布局相关代码如下

~~~xml
<android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="@dimen/fab_margin"
        app:srcCompat="@android:drawable/ic_dialog_email" />
~~~

接下来的是按钮代码，实现的功能是点击按钮后，显示replace with your own action

activity_main.xml中的

~~~xml
<include layout ="@layout/content_main" />
~~~

这行代码将 content_main.xml 的布局嵌入 activity_main 布局中。content_main.xml 的代码为：

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="A@string/appbar_scrolling_view_behavior"
    tools:context="introduction.android.helloworld.MainActivity"
    tools:showIn="@layout/activity_main">
    
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</android.support.constraint.ConstraintLayout>
~~~

表示当前的布局文件使用 LinearLayout 布局，该布局中仅有一个 TextView 组件用于显示信息，显示的内容为 "Hello World!"。

**鼓励用户将所有组件放置到 content_main.xml 中，而对 activity_main 中的代码尽量不做修改**。

## 2、文件结构解析

project的主要目录及存放的内容

* .gradle

  *  Gradle在构建工程的过程中生成的文件 

* .idea

  * Android Studio生成的工程配置文件，类似Eclipse的project.properties

* build

  * 相当于Eclipse工程的bin目录，**用于存放生成的文件，包括APK**

* gradle

  * 用于存放Gradle构建工具系统的JAR和Wrapper等，以及配置文件 

* external libraries

  * **工程依赖的LIB文件，如SDK等 **

* app

  * Android Studio创建工程中的一个Module，是程序开发者的主要工作目录

app目录的主要目录及存放的内容

* src
  * 包含应用程序的所有源代码。在 src 用的 Activity，MainActivity.java 就是在创建项目的时候创建的一个 Activity，在 Activity 中可以编写控制 View 的逻辑
* build
  * R 类中包含 4 个静态内部类：attr、drawable、layout 和 string，分别代表属性、图片资源、布局文件及字符串的声明。
* assets
  *  通常放置一些原始资源文件，会在 Android 打包的时候原封不动地一起打包，安装时会直接解压到对应的 Android 打包的时候原封不动地一起打包，安装时会直接解压到对应的 assets 目录中。这里**通常放置一些项目中用到的多媒体资源等**
* res
  * 通常放置的是 Android 要用到的各种程序资源。
  * 常用的子目录有
    * drawable 目录放置应用到的图片资源；
    * layout 目录放置一些与 UI 相关的布局文件，都是以 XML 文件方式保存；
    * values 目录中放置的是一些字符串、数组、颜色、样式和动画等资源，values 目录中的每一个文件都会转化成 R.java 中的一个静态类，文件中的每一个资源都会转化成 R.java 中对应静态类的静态整型常量，这样 Activity 中通过一个解析器就可以获取对应的资源。
* AndroidManifest.xml
  * 是整个项目的配置资源，里面配置的内容包括当前应用程序所在的包、应用程序中的 Activity、应用程序的访问权限等。
