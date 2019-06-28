---
title: 一、Android简介
---
# 一、Android简介
## 1、目录文件介绍
### 1)、Project目录下：
.gradle和.idea：这两个目录放置Android Studio自动生成的一些文件，无须关心，也无须编辑；  
app ： 项目中的代码、资源等内容都是放置在这个目录；  
build ：主要包含一些在编译时自动生成的文件，无须过多关心；  
gradle ： 这个目录包含gradle wrapper的配置文件；  
.gitignore ： 这个文件用来将指定的目录或文件排除在版本控制之外；  
build.gradle：项目全局的gradle构建脚本，通常无须修改；  
  
	// Top-level build file where you can add configuration options common to all sub-projects/modules.

	buildscript {
    repositories {
        google()
        jcenter()  //一个代码托管仓库，很多Android开源项目托管到jcenter上，声明之后就可以使用上面的开源项目
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.2'   //声明一个Gradle插件，
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
   	 }
	}

	allprojects {
   	 repositories {
        google()
        jcenter()
  	  }
	}

	task clean(type: Delete) {
   	 delete rootProject.buildDir
	}
gradle.properties：这个文件是全局的gradle配置文件，这里的配置熟悉将影响到项目中所有的gradle编译脚本；  
gradlew和gradlew.bat：这两个文件是用来在命令行界面执行gradle命令，前者是Linux或Mac系统使用，后者是Windows系统使用；  
HelloWorld.iml：iml文件是所有Intellij IDEA项目都会自动生成的一个文件，用于标识一个Intellij IDEA项目，无须修改；  
local.properties：这个文件用于指定本机中Android SDK路径，通常自动生成无须修改，若本机SDK路径发生变化，替换成新的路径；  
settings.gradle：这个文件用于指定项目中所有引入的模块。  
  
### 2)、App目录分析：  
build：编译时自动生成的文件，无须关心；  
libs：项目中使用到了第三方jar包，就需要将这些jar包都放在libs目录下，放在这个目录下的jar包都会被自动添加到构建路径中去；  
androidTest：此处是用来变形AndroidTest测试用例的，可以对项目进行一些自动化测试；  
java：放置java代码的地方；  
res：项目中使用到的所有图片、布局、字符串等资源都要存放在这个目录下。图片放在drawable目录，布局放在layout目录，字符串放在value目录；   
  
	//  MyApplication程序res/values/strings.xml
	<resources>
		<string name = "app_name">My Application</string>
	</resources>
	//定义的字符串有2种方式引用
	//1、代码中用R.string.MyApplication
	//2、XML中用@string/MyAppliaction 
AndroidMainfest.xml：整个Android项目的配置文件，程序中定义的四大组件都需要在这个文件里注册，还可以在这个文件中给应用程序添加权限声明；  
test：编写Unit Test测试用例，是对项目进行自动化测试的另一种方式；  
.gitignore：将app模块内的指定目录或文件排除在版本控制之外；  
app.iml：IntelliJ IDEA项目自动生成的文件；  
build.gradle：app模块的gradle构建脚本，文件中指定很多项目构建相关的配置；  gradle是先进的项目构建工具  
  
	apply plugin: 'com.android.application' //表示应用程序模块，可以直接运行，另外一个是com.android.library，需要依附于别的应用程序模块来运行

	android {//这个闭包中配置项目构建中的各种属性
    compileSdkVersion 27     //编译版本
    defaultConfig {
        applicationId "com.example.zhangqin07.myapplication"
        minSdkVersion 15
        targetSdkVersion 27
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes { //用于指定安装文件的相关配置，一般有debug和release
        release {  //正式安装文件的配置
            minifyEnabled false   //是否对项目的代码进行混淆，true表示混淆，false表示不混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
		//用于指定混淆时的规则文件，proguard-android.txt是Android SDK目录下通用的混淆规则
		//proguard-rules.pro是当前项目的根目录下的，编写当前项目的特有混淆规则
        }
    }
}

	dependencies {//这个闭包指定当前项目所有的依赖关系，本地依赖、库依赖、远程依赖
    implementation fileTree(dir: 'libs', include: ['*.jar']) //本地依赖声明，将libs目录下的所有jar后缀的 文件添加到项目的构建路径中去
    implementation 'com.android.support:appcompat-v7:27.1.1' //远程依赖，com.android.suppor域名部分，用于和其它公司库区分；appcompat-v7组名称，用于同一公司不同库区分；27.1.1版本号，同一库不同版本区分。
    implementation 'com.android.support.constraint:constraint-layout:1.1.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}
 
proguard-rules.pro：用于指定项目代码的混淆规则。  
## 2、Android日志工具
Android中的日志工具类是Log(Android.util.Log)，这个类有5种方法打印日志：  
Log.v() :用于打印最琐碎、最小的日志信息，级别为verbose，Android日志最低级；  
Log.d()：用于打印一些调试信息，级别为debug，比verbose高一级；  
Log.i()：打印一些比较重要的数据，这些数据可以帮助分析用户行为数据，级别为info，比debug高一级；  
Log.w()：用于打印警告信息，提示程序在这个地方可能存在潜在风险，最好去修复，级别为warm，比info高一级；  
Log.e()：用于打印程序的错误信息，比如程序进入到了catch语句当中，当有错误信息打印出来的时候代表程序出现了严重问题，必须修复，级别为error，比warm高一级  
  
	public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //添加一行打印日志的语句
		Log.d("MainActivity","onCreate execute");
 	   }
	}
logcat中的输出结果如下：  
  
	06-03 10:44:12.695 6769-6769/com.example.XXXXX.myapplication D/MainActivity: onCreate execute
