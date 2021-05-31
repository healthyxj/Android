# 三、Android核心组件

四大核心组件：Activity、Service、BroadcastReceiver、ContentProvider

软件层次结构：OS、Middle Layer、Application

**Android没有使用常见的应用程序入口点的方法(main方法)**，应用程序是由组件构成的，组件之间可以调用相互独立的基本功能模块，根据完成的功能不同，划分为4类核心组件，各组件之间的消息通过Intent完成。

## 1、Activity

是应用程序交互的窗口

**一个Activity通常对应一个窗口**，可以理解为web网页。用于显示信息，并且相互之间能够跳转。与网页不同，跳转可以有返回值。

新打开一个视图时，之前的视图就会被设置为暂停状态，并压入历史堆栈or后退栈(Back Stack)中，用户可以通过回退操作返回打开过的视图。Activity是由Android进行维护的，有自己的生命周期，即产生、运行、销毁。

每个Activity都提供一个用户界面窗口。一般情况下，该界面窗口会填满整个屏幕，也可能比屏幕小。

一个Android应用程序由多个Activity组成，其中有一个mainActivity，作用相当于Java程序中的main函数。当应用程序启动时，作为应用程序的入口首先呈现给用户。

当新的 Activity 被启动的时候，之前的 Activity 会停止，但是不会被销毁，而是**被压入“后退栈(Back Stack)”的栈顶，新启动的 Activity 获得焦点，显示给用户**。“后退栈”遵循“后入先出”的原则。当新启动的 Activity 被使用完毕，用户单击“Back”按钮时，当前的 Activity 会被销毁，而原先的 Activity 会被从“后退栈”的栈顶弹出并且激活。

### 生命周期

调用的常见方法有

* 创建：onCreate(Bundle savedInstanceState)
  * 创建 Activity 时调用。
  * 以 Bundle 的形式提供对以前储存的任何状态的访问。
  * 其中参数 savedInstanceState 对象是用于保存 Activity 的对象的状态。
* 激活：onStart()
  * Activity 变为在屏幕上对用户可见时调用。
* 恢复：onResume()
* 暂停：onPause()
* 停止：onStop()
* 销毁：onDestroy()
  * Activity 被完全从系统内存中移除时调用。
* 重启：onRestart()



activity主要有三个状态：

* 运行态
* 暂停态
* 停止态

### 创建

创建Activity的步骤

* 新建类

  * 创建一个 Activity，必须**创建 Android.app.Activity（或者它的一个已经存在的子类）的一个子类，并重写 onCreate() 方法**。

* 关联布局XML文件

  * 在新建的 Activity 中设置其布局方式，需要在 res/layout 目录中新建一个 XML 布局文件，可以通过 setContentView() 来指定 Activity 的用户界面的布局文件。

* 注册

  * 在 AndroidManifest.xml 文件中对建立的 Activity 进行注册，即在 <application> 标签下添加 <activity> 标签。

  * ~~~xml
    <manifest ... >
        <application ... >
            <activity android:name = ".ExampleActivity" />
            ...
        </application ... >
        ...
    </manifest >
    ~~~

  * 主Activity，需要添加<intent-filter>

  * ~~~xml
    <activity Android:name = ".ExampleActivity" Android:icon = "@drawable/app_icon">
        <intent-filter>
            <action Android:name = "Android.intent.action.MAIN" />
            <category Android:name = "Android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
    ~~~

  * 对代码的解释:

* <action Android:name = "Android.intent.action.MAIN" /> 表示该 Activity 作为主 Activity 出现。
  <category Android:name = "Android.intent.category.LAUNCHER" /> 表示该 Activity 会被显示在最上层的启动列表中。

### 启动

除了主Activity由系统启动外，其他Activity由应用程序启动。

通常情况下，通过startActivity() 方法来启动 Activity，而要启动的 Activity 的信息由 Intent 对象来传递

~~~java
Intent intent = new Intent(this,AnotherActivity.class);
startActivity(intent);
~~~

表示通过当前的 Activity 启动名为 AnotherActivity 的 Activity。

有时，用户不需要知道要启动的 Activity 的名字，而可以仅制定要完成的行为，**由 Android 系统来为用户挑选合适的 Activity**

~~~java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_EMAIL,recipientArray);
startActivity(intent);
~~~

其中，Intent.EXTRA_EMAIL 放置的是 recipientArray 中存储的要发送的 E-mail 的目标地址。该 Intent 对象被 startActivity() 启动后，Android 系统会启动相应的 E-mail 处理应用程序，并将 Intent.EXTRA_EMAIL 中的内容放置到邮件的目标地址中。

有时，当**需要从启动的 Activity 获取返回值的时候，需要使用 startActivityForResult() 方法**代替 startActivity() 方法，并实现 onActivityResult() 方法来获取返回值。

~~~java
Intent intent = new Intent(Intent.ACTION_PICK,Contacts.CONTENT_URI);
startActivityForResult(intent,PICK_CONTACT_REQUEST);
~~~

### 关闭Activity

关闭 Activity 使用 finish() 方法。关闭之前启动的其他 Activity 可以使用 finishActivity() 方法。但是**通常情况下，程序员不应该使用这些方法去强制关闭 Activity**。

因为 Android 系统在为用户维护 Activity 的生命周期，并且提供了完备的资源回收机制和资源重建机制，可以动态地回收和重建 Activity，因此 Activity 应用交由 Android 系统来管理，除非已确定用户不再需要当前的 Activity，并且不允许用户回退到当前 Activity。

### Activity数据传递

有三种传递方式

* 通过 Intent 传递一些简单的数据。
* 通过 Bundle 传递相对复杂的数据或者对象。
* 通过 startActivityForResult 可以更方便地进行来回传递，当然前两种方法也可以来回传递。

利用intent传递数据

~~~java
//activity1传递数据
Intent intent = new Intent(Activity1.this,Activity2.class);
intent.putExtra("author","leebo"); //在 Intent 中加入键值对数据，键为 "author"，值为 "leebo"
Activity1.this.startActivity(intent);

//activity2取出数据
Intent intent = getIntent(); //获得传过来的 Intent
String value = intent.getStringExtra("author");
//根据键名 author 取出对应键值为 "leebo"
~~~

利用bundle传递数据

~~~java
//activity1传递数据
Intent intent = new Intent(Activity1.this,Activity2.class);
Bundle myBundle = new Bundle();
myBundle.putString("author","leebo");
intent.putExtras(myBundle);
Activity1.this.startActivity(intent);

//activity2取出数据
Intent intent = getIntent();
Bundle myBundle = intent.getExtras();
String value = myBundle.getString("author"); //根据键名 author 取出对应键值为 "leebo"
~~~

利用startActivityForResult()传递数据

不仅可以把数据从Activity1传递给Activity2，还可以把数据传回Activity1

~~~java
//Activity1的发送数据
final int REQUEST_CODE = 1;
Intent intent = new Intent(Activity1.this,Activity2.class);
Bundle mybundle = new Bundle();
mybundle.putString("author","leebo"); //把数据传过去
intent.putExtras(mybundle);
startActivityForResult(intent,REQUEST_CODE);

//重载onActivityResult 方法，用来接收传过来的数据
protected void onActivityResult(int requestCode,int resultCode,Intent intent){
    if(requestCode == this.REQUEST_CODE){
        switch(resultCode){
            case RESULT_OK:
                Bundle b = intent.getExtras();
                    String str = b.getString("Result"); //获取 Result 中的值，为 "from Activity2"
                    break;
                default:
                    break;
        }
    }
}

//Activity2
Intent intent = getIntent();
Bundle myBundle = getIntent().getExtras();
String author = getBundle.getString("author");
Intent intent = new Intent();
Bundle bundle = new Bundle();
bundle.putString("Result","from Activity2");
intent.putExtras(bundle）;
// RESULT_OK 这个值相当于 onActivityResult 方法里面的 resultCode
Activity02.this.setResult(RESULT_OK,intent); //通过 intent 将数据返回给 Activity1，RESULT_OK
finish(); // 结束当前的 Activity
~~~



## 2、Service

没有用户界面，相当于操作系统中的一个服务。

Android定义了两种Service，本地Service和远程Service。**本地 Service 是只能由承载该 Service 的应用程序访问的组件，而远程 Service 是供在设备上运行的其他应用程序远程访问的 Service**。

通过 Context.startService(Intent service) 可以启动一个 Service，通过 Context. bindService() 可以绑定一个 Service。

## 3、BroadcastReceiver

用于接收来自系统和其他应用的广播。

BroadcastReceiver 不能生成 UI，通过 NotificationManager 来通知用户有事件发生，对于用户来说是隐式的。

BroadcastReceiver 的 2 种注册方式：

- 在 AndroidManifest. xml 中进行静态注册；
- 在运行时的代码中使用 Context.registerReceiver() 进行动态注册。

只要注册了 BroadcastReceiver，即使对应的事件广播来临时应用程序并未启动，系统也会**自动启动该应用程序对事件进行处理**。另外，用户还可以通过Context.sendBroadcast() 将自己的 Intent 对象广播给其他的应用程序。

## 4、ContentProvider

文件、数据库等数据在 Android 系统内是私有的，仅允许被特定应用程序直接使用。**在两个程序之间，数据的交换或共享由 ContentProvider 实现**。

ContentProvider 类实现了一组标准方法的接口，从而能够让其他的应用保存或读取 ContentProvider 提供的各种数据类型。

# 四、Android其他组件

## 1、资源

Android层次结构中，资源扮演着重要的角色

这里的Android资源主要指的是res和assets目录。

一般来讲，共有3种类型的资源：**XML文件、位图文件和RAW文件(声音)**。

Android工程目录中，用于存放资源的文件夹有两个，res和assets

| res                                                          | assets                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 不支持深度子目录                                             | 可以使用任意深度的子目录进行存储                             |
| 资源最终将被打包到编译后的 [Java](http://c.biancheng.net/java/) 文件中，可以直接通过 R 资源类访问 | 资源打包到应用程序中的静态文件，这些文件不会被编译，最终会直接部署到目标设备中；另外，不能直接通过 R 资源类读取，只能使用流的形式读取。 |
| 利用率较高                                                   |                                                              |

## 2、AndroidManifest.xml

每一个Android项目都包含了一个清单(Manifest)文件，**AndroidManifest.xml，是xml格式的Android程序声明文件**，每个 Android 程序必须在根目录下包含一个 AndroidManifest.xml。

AndroidManifest.xml 包含 Android 系统运行程序前所必须掌握的重要信息，这些信息包含应用程序名称(label)、图标(icon or roundIcon)、包名称(package)、模块组成、授权和 SDK 最低版本等。

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.example.myapplication">

    <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:roundIcon="@mipmap/ic_launcher_round"
            android:supportsRtl="true"
            android:theme="@style/AppTheme">
        <activity
                android:name=".MainActivity"
                android:label="@string/app_name"
                android:theme="@style/AppTheme.NoActionBar">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>

</manifest>
~~~

除了manifest和application标签都是必需的。

### manifest标签

manifest标签是AndroidManifest.xml的根标签。

manifest 标签用于设置与项目相关的一些属性，比如**用于唯一标识应用程序的 package 属性**，用于记录应用程序版本的 Android:versionName 属性等等。其中的 xmlns:Android 属性必须被定义为“http://schemas.Android.com/apk/res/Android”。

### application标签

manifest 标签仅能包含一个 application 标签。

manifest 标签使用各种属性来指定应用程序的各种元数据（包括标题、图标和主题）。它还可以作为一个包含活动（Activity）、服务（Service）、内容提供器（Provider）和广播接收器（Broadcast Receiver）标签的容器，用来指定应用程序组件。

application可以有很多子标签

| 名称          | 作用                                                         |
| ------------- | ------------------------------------------------------------ |
| activity 标签 | 应用程序显示的**每一个 Activity 都要求有一个 activity 标签，并使用 Android:name 属性来指定类的名称**。这必须包含核心的启动 Activity 和其他所有可以显示的屏幕或者对话框。启动任何一个没有在清单中定义的 Activity 时都会抛出一个运行时异常。每一个 Activity 节点都允许使用 intent-filter 子标签来指定哪个 Intent 启动该活动。 |
| service 标签  | 和 activity 标签一样，应用程序中使用的每一个 Service 类都要创建一个新的 service 标签。（Service 标签也支持使用 intent-filter 子标签来允许后面的运行时绑定。） |
| provider 标签 | provider 标签用来说明应用程序中的每一个内容提供器。内容提供器是用来**管理数据库访问以及程序内和程序间共享的**。 |
| receiver 标签 | 通过添加 receiver 标签，可以注册一个广播接收器，而不用事先启动应用程序。广播接收器就像全局事件监听器一样，一旦注册了之后，无论何时，只要与它相匹配的 Intent 被应用程序广播出来，它就会立即执行。通过在声明中注册一个广播接收器，可以使这个进程实现完全自动化。如果一个匹配的 Intent 被广播了，应用程序就会自动启动，并且你注册的广播接收器也会开始运行。 |

### user-permission标签

作为安全模型的一部分，uses-permission 标签声明了那些自己定义的权限，而这些权限是应用程序正常执行所必需的。**在安装程序时，设定的所有权限将会告诉用户，由他们来决定同意与否**。

### permission标签

在可以限制访问某个应用程序组件之前，需要在清单中定义一个 permission。

可以使用 permission 标签来创建这些权限定义。然后，应用程序组件就可以**通过添加 Android: permission 属性来要求这些权限**。其他的应用程序需要在它们的清单中包含 uses-permission 标签（并且通过授权），之后才能使用这些受保护的组件。

可以详细的指定允许访问权限的级别(normal、dangerous、signature和signatureOrSystem)、一个 label 属性和一个外部资源，这个外部资源应该包含对授予这种权限的风险的描述。

### instrumentation标签

instrumentation 类提供一个框架，用来在应用程序运行时在活动或者服务上运行测试。

## 3、App Widgets

**App Widgets 是指能够嵌入其他应用程序中的小组件，并且能够周期性地进行更新**。可以通过 App Widgets 使我们的 UI 界面更多样化，也可以通过 App Widget Provider 发布我们自己开发的 App Widgets 组件。**一个能够用于容纳 App Widgets 组件的应用程序组件被称为 App Widgets Host（App Widgets 宿主）**

### 创建前

需要完成4个步骤

* AppWidgetProviderInfo元数据
  * 定义在 XML 文件中的用于描述 App Widget 的元数据对象，比如 App Widget 的**布局、更新频率**以及相关的 AppWidgetProvider 类。

* 实现AppWidgetProvider类
  * 当 App Widget 的状态发生改变，例如更新、启用、禁用和删除的时候，你都会接收到相应的广播通知

* 视图布局
  * 在XML文件中为App Widget定义初始布局

* 为AppWidget配置Activity
  * 用户添加App Widget时会启动Activity，允许用户在创建App Widget进行修改。

### App Widget的声明

需要在Manifest中声明App Widget

~~~xml
<receiver android:name = "ExampleAppWidgetProvider">
    <intent-filter>
    	<action android:name = "android.addwidget.action.APPWIDGET_UPADTE"
                />
    </intent-filter>
    <meta-data android:name = "android.appwidget.provider"
               android:resource = "@xml/example_appwidget_info" />
</receiver>
~~~

<receiver> 元素必须要指定 android:name 属性，它指定了 App Widget 使用的 AppWidgetProvider 的名字。

<intent-filter> 元素必须包括一个含有 android:name 属性的 <action> 元素。该元素指定 AppWidgetProvider 接受 ACTION_APPWIDGET_UPDATE 广播。这是唯一一个必须被显式声明的广播。当有必要的时候，AppWidgetManager 会自动发送所有其他App Widget 广播给 AppWidgetProvider。

<meta-data> 元素指定了 AppWidgetProviderInfo 资源并需要以下属性。

- android:name：指定元数据名称。
- android:resource：指定 AppWidgetProviderInfo 资源路径。

### 增加元数据

AppWidgetProviderInfo 用于定义 App Widget 的一系列基本特性，例如最小布局的尺寸、初始的布局资源、刷新频率以及创建时要加载的配置 Activity 等。

使用 <appwidget-provider> 元素标签在 XML 中定义 AppWidgetProviderInfo 对象并保存到项目的 res/xml/ 目录下

~~~xml
<appwidget-provider xmlns:android = "http://schemas.android.com/apk/res/android" 
    android:minWidth = "294dip"  
    android:minHeight = "72dip" 
    android:updatePeriodMillis = "86400000" 
    android:initialLayout = "@layout/example_appwidget"
    android:configure = "com.example.android.ExampleAppWidgetConfigure" 
    > 
</appwidget-provider> 
~~~

其中：

- minWidth 和 minHeight 属性的值指定了这个 App Widget 布局需要的最小区域。
- updatePerdiodMillis 属性定义了 App Widget 框架调用 onUpdate() 方法来从 AppWidgetProvider 请求一次更新的频率。实际上更新的时间并不精准。建议更新频率越低越好，比如一小时更新一次，这样可以节省电力，或者根据用户的配置调整更新频率，比如有个人每 15 分钟想查看一下股票的报价，这样可以将频率设置为一小时更新 4 次。
- initialLayout 属性指向 App Widget 使用的布局的资源。
- configure 属性定义了该 App Widget 被加载时使用的配置 Activity。

### 创建App Widget布局

App Widget 的布局是基于 RemoteViews 对象的，而 RemoteViews 对象可以支持以下布局：

- Framelayout
- LinearLayout
- RelativeLayout
- GridLayout

### 为App Widget添加边界

如果没有为自定义的 Widget 定义边界，它就会自动扩展到屏幕大小。因此，我们需要为自定义的 App Widget 定义边界。

自 Android 4.0 开始，App Widget 会自动在 Widget 的边界环绕盒之间添加空隙，以便为 Widget 和其他小组件以及屏幕上的图标提供更好的排列组合方式。为实现这个行为，我们需要将应用程序中的 "targetSdkVersion" 属性设置为大于 14。

定义边界

~~~xml
<FrameLayout
    android:layout_width = "match_parent"
    android:layout_height = "match_parent"
    android:padding = "@dimen/widget_margin">
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal">
        ...
    </LinearLayout>
</FrameLayout>
~~~



