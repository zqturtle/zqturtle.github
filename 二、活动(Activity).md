---
title: 二、活动（Activity）
---
# 二、活动（Activity）
活动（Activity）是一种可以包含用户界面的组件，主要用于和用户进行交互，一个应用程序可以包含零个或者多个活动。
## 1、活动基本用法
### 1.1、手动创建活动
Activity Name：FirstActivity；  
Generate Layout File：表示会自动为FirstActivity创建一个对应的布局文件；  
Launcher Activity：表示自动将FirstActivity设置为当前项目的主活动；  
Backwards Compatibility：表示为项目启用向下兼容的模式。  
  
项目的任何活动都应该重写Activity的onCreate()方法，如FirstActivity中Android Studio的默认实现：  
  
	package com.example.xxxxx.activitytest;

	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;

	public class FirstActivity extends AppCompatActivity {

  	  @Override
    	protected void onCreate(Bundle savedInstanceState) {//重写onCreate方法,主要是加载一些组件
        	super.onCreate(savedInstanceState);          //调用父类的onCreate方法，参数的作用是保持
    	}
	}
### 1.2、创建和加载布局
Android程序的设计讲究逻辑和视图分离，最好每一个活动都能对应一个布局，布局就是用来显示界面内容。  
#### 1.2.1创建布局  
在app/src/main/res目录右击->New->Directory创建layout目录。layout目录右击->New->Layout resource file,命名布局文件为first_layout，根元素选择LinearLayout。创建完成。  
在Android Studio的下方的Design是可视化布局编辑器，不仅可以预览当前的布局，还可以通过拖放的方式编辑布局；Text是通过XML文件的方式来编辑布局的，其代码如下：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  	  android:orientation="vertical" android:layout_width="match_parent"
    	android:layout_height="match_parent">

	</LinearLayout>  
布局文件中已经有一个LinearLayout元素，添加一个按钮：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   	 android:orientation="vertical" android:layout_width="match_parent"
    	android:layout_height="match_parent">
    
    	<Button
      //android:id是给当前元素定义一个唯一标识符，代码可以对其进行操作； 	//在XML中引用一个id就用@id/id_name语法；在XML中定义一个id就用@+id/id_name语法
	android:id="@+id/button_1"
      	 android:layout_width="match_parent"  //当前元素宽度，match_parent表示和父元素一样宽
        android:layout_height="wrap_content" //当前元素高度，wrap_content表示高度更好包含里面的内容就行
        android:text="Button 1"  //指定元素中显示的文字内容
        />

	</LinearLayout>
#### 1.2.2在活动中加载布局
在FirstActivity中的onCreate方法中加入代码：  
  
	package com.example.xxxxx.activitytest;

	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;

	public class FirstActivity extends AppCompatActivity {

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.first_layout);//给当前活动加载布局，一般传入布局文件的id
    	}
	}
项目的添加的任何资源都会在R文件中生成一个相应的资源id，first_layout.xml布局的id也添加到R文件中了，R.layout.first_layout就可以得到first_layout.xml布局的id。  
#### 1.2.3在AndroidManifest文件中注册
所有的活动都要在AndroidManifest.xml文件中注册才能生效，FirstActivity在AndroidManifest.xml中注册过了，app/src/main/AndroidManifest.xml：  
  
		<?xml version="1.0" encoding="utf-8"?>
	<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    		package="com.example.xxxxxx.activitytest">

   	 <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".FirstActivity"></activity>
    </application>

	</manifest>
活动的注册在<application>标签内，通过<Activity>注册，android:name来指定具体注册哪一个活动，.FirstActivity是一个缩写，<manifest>标签的package指定了包名。  
仅仅注册不能运行，还需要为程序配置主活动，在AndroidManifest.xml中使用<intent-filter>标签：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<manifest xmlns:android="http://schemas.android.com/apk/res/android"
   	 package="com.example.xxxxx.activitytest">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".FirstActivity"
            android:label="This is FirstActivity">//指定活动标题栏内容，会话顶部，还是启动器中应用程序的名称
            <intent-filter>  //标签内配置主活动
                <action android:name = "android.intent.action.MAIN"/>  //使用这两句声明来配置
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>

	</manifest>
#### 1.2.4在活动中使用Toast
Toast是Android系统提供的一种非常好的提醒方式，在程序中可以使用它将一些短小的信息通知给用户，这些信息一段时间后会自动消失，不会占用任何屏幕空间。  
定义一个Toast触发点，使用界面中的按钮，在onCreate方法中添加代码：  
  
	package com.example.xxxxx.activitytest;

	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;
	import android.view.View;
	import android.widget.Button;
	import android.widget.Toast;

	public class FirstActivity extends AppCompatActivity {

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.first_layout);

        Button button1 = (Button) findViewById(R.id.button_1);//获取布局文件中定义的元素实例，first_layout.xml中通过android:id指定的
        button1.setOnClickListener(new View.OnClickListener() {//这个方法为按钮注册一个监听器，点击按钮就会执行onClick方法
            @Override
            public void onClick(View v) {//弹出Toast的功能
			//通过静态方法makeText创建Toast对象，然后调用show显示出来
			//第一个参数Context，Toast要求的上下文;第二个参数显示的文本内容；第三个参数显示时长
                Toast.makeText(FirstActivity.this,"you clicked Button 1",Toast.LENGTH_SHORT).show();
            }
        });
   	 }
	}
  
#### 1.2.5在活动中使用Menu
使得菜单能得到展示并不占用屏幕。  
在res目录右击->New->Directory,输入menu；文件夹下新建main菜单，menu右击->New->Menu resource file，在main.xml中添加代码：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<menu xmlns:android="http://schemas.android.com/apk/res/android">
   		 <item   //item标签用来创建某一个菜单项
        		android:id="@+id/add_item"  //给菜单项指定一个唯一标识符
        		android:title="Add"/>     //给菜单项指定一个名称
    		<item
        		android:id="@+id/remove_item"
       		 android:title="Remove"/>
	</menu>
在FirstActivity中重写onCreateOptionMenu()方法（MAC中control+O）:  
  
	    	@Override
    		public boolean onCreateOptionsMenu(Menu menu) {
       	 		getMenuInflater().inflate(R.menu.main , menu);//获得MenuInflater对象，调用inflate方法给当前活动创建菜单
			//inflate()方法2个参数，第一个指定通过哪一个资源文件来创建菜单；第二个参数指定我们的菜单项添加到哪一个Menu对象中，此处使用了传入的参数
        		return true;
    		}
要菜单真正可用，还要在定义菜单响应事件，通过在FirstActivity中重写onOptionItemSelected()方法：
  
	    @Override
    	public boolean onOptionsItemSelected(MenuItem item) {
        	switch (item.getItemId()){//判断点击的是哪一个菜单项
           	 case R.id.add_item:
                Toast.makeText(this,"You clicked Add",Toast.LENGTH_SHORT).show();
                break;
           	case R.id.remove_item:
                	Toast.makeText(this,"You clicked Remove",Toast.LENGTH_SHORT).show();
                	break;
            default:
        }
        return true;
    }
#### 1.2.6销毁一个活动
除了返回键销毁活动外，在代码中销毁可以使用Activity类提供的finish()方法，例如修改按钮监听器中的代码：  
  
	button1.setOnClickListener(new View.OnClickListener() {
           	@Override
            public void onClick(View v) {
                //Toast.makeText(FirstActivity.this,"you clicked Button 1",Toast.LENGTH_SHORT).show();
                finish();
            }
        });
点击Button 1就销毁了活动。  
## 2、使用Intent在活动中切换
点击启动器只会进入到应用的主活动中，所以需要从主活动跳转到其他活动。  
Intent是Android程序中各组件交互的一种重要方式，不仅可以指明当前组件想要执行的动作，还可以在不同组件直接传递数据。一般可用于启动活动、启动服务以及发送广播等。  
### 2.1使用显示Intent
#### 2.1.1创建另外一个活动
右击包名->New->Activity->Empty Activity,创建SecondActivity，布局为second_layout，不选Launcher Activity。编辑second_layout.xml如下：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:orientation="vertical" android:layout_width="match_parent"
    	android:layout_height="match_parent">

    <Button
        android:id="@+id/button_2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button 2"
        />

	</LinearLayout>  
任何一个活动都需要在AndroidManifest.xml中注册，Android Studio已自动完成，由于不是主活动，不需要配置<intent-filter>标签里的内容。FirstActivity和SecondActivity注册对比如下：  
  
	<activity
            android:name=".FirstActivity"
            android:label="This is FirstActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity android:name=".SecondActivity"></activity>
#### 2.1.2显示Intent
Intent的构造函数之一：  
  
	Intent(Context packageContext , Class<?>cls)
第一个参数表示启动活动的上下文，第二个指想要启动的目标活动。  
Activity类中提供的一个startActivity()方法接收一个Intent参数，专门用于启动活动。修改FirstActivity中按钮的点击事件：  
  
	 button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Toast.makeText(FirstActivity.this,"you clicked Button 1",Toast.LENGTH_SHORT).show();
                Intent intent = new Intent(FirstActivity.this , SecondActivity.class);
                startActivities(intent);
            }
        });
### 2.2隐式Intent
不明确指出想要启动哪一个活动，而是指定一系列更为抽象的action和category等信息，然后交由系统分析这个Intent。  
通过AndroidManifest.xml文件中的<activity>标签下配置<intent-filter>的内容，指定当前活动能够响应action和category：  
  
	<activity android:name=".SecondActivity">
            <intent-filter>
                <action android:name="com.example.xxxxx.activitytest.ACTION_START"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
只有<action>和<category>中的内容同时匹配上Intent中指定的action和category时，这个活动才响应该Intent。  
修改FirstActivity中按钮的点击事件：  
  
	button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Toast.makeText(FirstActivity.this,"you clicked Button 1",Toast.LENGTH_SHORT).show();
                //Intent intent = new Intent(FirstActivity.this , SecondActivity.class);
		//使用了Intent的另外一个构造函数，传递action字符串
                Intent intent = new Intent("com.example.xxxxx.activitytest.ACTION_START");
                startActivity(intent);
            }

一个Intent只能指定一个action，但是能指定多个category。在AndroidManifest.xml的<intent-filter>中添加一个category：  
	  
	<activity android:name=".SecondActivity">
            <intent-filter>
                <action android:name="com.example.xxxxx.activitytest.ACTION_START"/>
               	<category android:name="android.intent.category.DEFAULT"/>
				<category android:name="android.intent.category.MY_CATEGORY"/>//声明自定义的category
            </intent-filter>
        </activity>
---------------------
	       	@Override
            public void onClick(View v) {
                //Toast.makeText(FirstActivity.this,"you clicked Button 1",Toast.LENGTH_SHORT).show();
                //Intent intent = new Intent(FirstActivity.this , SecondActivity.class);
                Intent intent = new Intent("com.example.xxxxx.activitytest.ACTION_START");
                intent.addCategory("com.example.xxxxx.activitytest.MY_CATEGORY");//添加一个category
                startActivity(intent);
            }
        });
#### 2.2.1隐式Intent的多种用法
隐式Intent不仅可以起到自己程序内的活动，还可以起到其他程序的活动，使得Android多个应用程序之间功能共享。例如，调用系统浏览器打开网页，修改FirstActivity中按钮事件代码：  
  
	 button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_VIEW);//Intent的action是Intent.ACTION_VIEW,系统内置的动作
                intent.setData(Uri.parse("http://www.baidu.com"));//Uri.parse方法将网址字符串解析成一个Uri对象，setData将对象传递进去
                startActivity(intent);
            }
        });
还可以在<intent-fileter>标签中配置<data>标签，更精确的指定当前活动响应什么类型数据。<data>标签主要可以配置以下内容：  
android:scheme   指定数据的协议部分，如http  
android:host    指定数据的主机部分，如www.baidu.com  
android:port    指定数据的端口部分  
android:path   指定主机名和端口之后的部分  
android:mineType   指定可以处理的数据类型，允许使用通配符方式指定  
<data>标签指定的内容和Intent携带的data完全一致时，当前活动才能够响应Intent。  
  
拨打电话：  
  
	     button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_DIAL);
                intent.setData(Uri.parse("tel:10086"));
                startActivity(intent);
            }
        });
### 2.3向下一个活动传递数据
Intent中提供了一系列putExtra()方法的重载，可以把我们需要传递的数据暂存在Intent中，启动另一个活动后，将数据从Intent中取出。例如FirstActivity中的一个字符串传递到SecondActivity中去：  
  
	 button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String data = "Hello SecondActivity";
                Intent intent = new Intent(FirstActivity.this ,SecondActivity.class);
                intent.putExtra("extra_data" , data); //通过putExtra传递一个字符串，第一个参数是键，用于后面从Intent中取值，data是要传的数据
                startActivity(intent);
            }
        });
---------------------
	//在SecondActivity中将传递的数据取出，并打印出来
	public class SecondActivity extends AppCompatActivity {

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.second_layout);

        	Intent intent = getIntent();//次方法获取用于启动SecondActivity的Intent
        	String data = intent.getStringExtra("extra_data");//获取传递的数据
        	Log.d("SecondActivity",data);
        
   	 	}
	}
### 2.4返回数据给上一个活动
使用Activity中的一个startActivityForResult(）方法启动活动，这个方法当活动销毁时能够返回一个结果给上一个活动。两个参数，第一个参数Intent，第二个是请求码，用于在之后回调中判断数据的来源。  
  
	button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            	Intent intent = new Intent(FirstActivity.this ,SecondActivity.class);
				startActivityForResult(intent , 1);//请求码是唯一值就可以
		}
	}
在SecondActivity中给按钮注册点击事件，并在点击事件中添加数据的逻辑：  
  
	public class SecondActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.second_layout);
   	Button button2 = (Button)findViewById(R.id.button_2);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();//构建一个Intent，仅仅传递数据，没指向任何“意图”
                intent.putExtra("data_rerutn" , "Hello FirstActivity");
                setResult(RESULT_OK,intent);//向上一活动返回数据,第一个参数返回处理结果，_OK和_CANCELED两个值，第二个参数把带有数据的intent传递回去
                finish(); //销毁当前活动
            }
        });
    	}
	}
startActivityForResylt()方法启动SecondActivity后，在SecondActivity被销毁后回调上一个活动的onActivityResult()方法，因此需要重写FirstActivity中的这个方法得到返回的数据：  
  
	@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        switch (requestCode){
            case 1:
                if(resultCode == RESULT_OK){
                    String returnedData = data.getStringExtra("data_return");
                    Log.d("FirstActivity" , returnedData);

                }
                break;
            default:
        }
    }
三个参数，第一个requestCode是启动活动时传入的请求码；resultCode返回数据时传入的处理结果；data是携带返回数据的Intent。由于一个活动可能启动多个活动，每个活动返回的数据都回调到onActivityResult()方法中，因此，首先检查requestCode的值判断数据来源。  
  
还可以通过Back键回到FirstActivity，需要重写onBackPressed()方法来解决：  
  
	@override
	public void onBackPressed(){
		Intent intent = new Intent();
		intent.putExtra("data_return","Hello FirstActivity");
		setResult(RESULT_OK,intent);
		finish();
	}
## 3、活动的生命周期
Android中的活动是可以层叠的，使用任务(Task)管理活动，一个任务就是一组存放在栈里的活动的集合，这个栈称为返回栈。启动一个新的活动就会在返回栈中入栈，处于栈顶；当调用finish()或者按back键销毁活动后就出栈。
### 3.1活动状态
**运行状态：**活动处于返回栈的栈顶  
**暂停状态：**活动不处于栈顶，但任然可见；这是因为不是每一个活动都会占满整个屏幕，暂停状态的活动仍然完全存活，如果内存极低系统会考虑回收这种活动  
**停止状态：**活动不处于栈顶，并且完全不可见；系统会保存相应的状态和成员变量，但是也有可能被回收  
**销毁状态：**活动从返回栈移除  
### 3.2活动的生存期
Activity类定义了7个回调方法，覆盖生命周期的每一个环节。  
**onCreate()：**每个活动都要重写这个方法，会在活动第一次被创建的时候调用；在方法中完成活动的初始化，如加载布局、绑定事件  
**onStart():**由不可见变为可见的时候调用  
**onResume()：**活动准备好和用户进行交互的时候调用，此时活动处于栈顶运行状态  
**onPause()：**在系统准备去启动或者恢复另外一个活动的时候调用  
**onStop():**活动完全不可见的时候调用，如果启动的是另外一个活动是对话框式的那么执行onPause而不是onStop  
**onDestroy()：**活动呗销毁之前调用，之后活动变为销毁状态  
**onRestart()：**活动由停止活动变为运行状态前调用，活动被重新启动  
以上方法除了onRestart方法，其他都是两两相对的，因此活动又可以分为3种生存期：  
**完整生存期：**活动在onCreate和onDestroy之间所经历的  
**可见生存期：**活动在onStart和onStop之间所经历的，此期间活动对用户总是可见  
**前台生存期：**活动在onResume和onPause之间所经历的，活动总是处于运行状态，可以和用户进行交互  
### 3.3活动被回收了咋办
应用场景：应用中有一个活动A，活动A启动了活动B，活动A进入停止状态，这个时候由于系统内存不足活动A被回收了，然后back键返回活动A时，活动A还是要会正常显示，此时并不会执行onRestart方法，而是会执行onCreate方法，活动A呗重新创建一次。但是，活动A中如果有一个文本框，你输入了一段文字，如果被回收了，返回到活动A时出入的文字没了。  
为了解决这个问题，Activity中还提供了一个onSaveInstance()回调方法，这个方法可以保证还活动被回收前一定会被调用，可以通过这个方法来解决活动被回收时零时数据得不到保存的问题。  
  
onSaveInstanceState()方法携带一个Bundle类型参数。  
Bundle提供了一系列保存数据的方法，puString() 、putInt()，每个方法2个参数，第一个参数是键，用于后面从Bundle中取值，第二个参数是真正要保存的内容。  
MainActivity中将临时数据保存：  
  
	@Override
	protected void onSaveInstanceState(Bundle outState){
		super.onSaveInstanceState(outState);
		String tempData = "Something you just typed";
		outState.putString("data_key" , tempData);
	}
保存之后恢复，onCreate()方法也有一个Bundle类型参数，一般为null，如果活动被回收之前通过onSaveInstanceState()方法来保存数据，这个参数就会带有之前所保存的全部数据。修改MainActivity的onCreate()方法：  
  
	@Override
	protected vodi onCreate(Bundle savedInstanceState){
		super.onCreate(savedInstanceState);
		Log.d(TAG , "onCreate");
		setContentView(R.layout.activity_main);
		if(savedInstanceState != null){
			String tempData = savedInstanceState.getString("data_key");
			Log.d(TAG , tempData);
		}
	}
取出之后就可以做相应的恢复操作，此处只是简单打印。  
## 4、活动的启动模式
启动模式一共有4种：standard  singleTop  singleTask  singleInstance  
可以在AndroidManifest.xml中通过给<activity>标签指定android:launchMode属性来选择启动模式。  
### 4.1standard
默认的启动模式，不显示指定的情况下，所有活动都会自动使用这种模式。使用这种模式的活动，系统不会在乎这个活动是否已经在返回栈中存在，每次启动都会创建该活动的一个新的实例。每当启动一个新的活动，就会在返回栈中入栈，并处于栈顶位置。
### 4.2singleTop
启动活动时如果发现返回栈的栈顶已经是该活动，则认为可以直接使用它，不会再创建新的活动实例。
### 4.3singleTask
每次启动该活动系统只会首先在返回栈中检查是否存在该活动的实例，如果发现已经存在则直接使用该活动实例，并把这个活动之上的所有活动统统出栈，如果没有发现就会创建一个新的活动实例。
### 4.4singleInstance
指定为singleInstance模式的活动会启动一个新的返回栈来管理这个活动。如果程序中的一个活动是允许其他程序调用的，要实现其他程序和我们的程序共享这个活动的实例，就需要这个模式创建一个单独的返回栈管理这个活动，不管哪个应用程序来访问这个活动，都共用的同一个返回栈。
## 5、活动的最佳实践技巧
### 5.1获取当前是哪一个活动
在ActivityTest项目中新建一个BaseActivity类，右击包->New->Java Class，不需要注册，只需要创建普通的Java类，让BaseActivity继承自AppCompatActivity，并重写onCreate方法：  
  
	public class BaseActivity extends AppCompatActivity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.d("BaseActivity" , getClass().getSimpleName());//获取当前实例的类名，通过Log打印出来
   	 }
	}
让BaseActivity成为ActivityTest项目中所有活动的父类，继承自BaseActivity，而BaseActivity也是继承自AppCompatActivity的，所以所有活动的现有功能并不受影响。运行后每当进入一个界面，就会在Logcat中打印信息，从而知道当前界面对应哪个活动。  
### 5.2随时随地退出程序
不需要按几次返回键退出，思路是用一个专门的集合类对所有活动进行管理。新建一个ActivityCollector类作为活动管理器：  
  
	public class ActivityCollector{
		public static List<Activity> activities = new ArrayList<>();//通过List暂存活动
		public static void addActivity(Activity activity){ //向List中添加活动
			activities.add(activity);
		}
		public static void removeActivity(Activity activity){//从List中移除活动
			activities.remove(activity);
		}
		public static void finishAll(){   //将List中存储的活动全部销毁
			for(Activity activity: activities){
				if(!activity.isFinishing()){
					activity.finish();
				}
			}
		}
	}
--------------------
	//修改BaseActivity中的代码
	public class BaseActivity extends AppCompatActivity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.d("BaseActivity" , getClass().getSimpleName());
		ActivityCollector.addActivity(this);//将当前创建的活动添加到活动管理器
  	  }
		@Override
		protected void onDestroy(){//重写onDestroy方法
			super.onDestroy();
			ActivityCollector.removeActivity(this);//将马上销毁的活动从活动管理器中移除
		}
	}
---------------------
	//调用ActivityCollector.finishAll()不管在什么地方都可以退出程序
	public class SecondActivity extends BaseActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.second_layout);

        Button button2 = (Button)findViewById(R.id.button_2);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
               ActivityCollector.finishAll();
            }
        });
    }
}

保证程序完全退出可以杀掉进程：  
  
	android.os.Process.killProcess(android.os.Process.myPid());//myPid获取当前进程的id，killProcess只能杀掉当前程序的进程，不能用来杀掉其他程序
### 5.3启动活动的最佳写法
一般方法通过Intent构建当前“意图”，然后调用startActivity()或者startActivityForResult()方法将活动启动，如果有数据传递使用Intent完成。如：  
  
	Intent intent = new Intent(FirstActivity.this , SecondActivity.class);
	intent.putExtra("param1" , "data1");
	intent.putExtra("param2" , "data2");
	startActivity(intent);
但是有一个对接问题，你不清楚这个活动需要传递哪些数据。修改SecondActivity中代码：  
  
	public class SecondActivity extends BaseActivity{
		public static void actionStart(Content context , String data1 , String data2){
			Intent intent = new Intent(FirstActivity.this , SecondActivity.class);
			intent.putExtra("param1" , "data1");
			intent.putExtra("param2" , "data2");
			startActivity(intent);
		}
	}
添加一个actionStart(）方法完成Intent构建，SecondActivity中需要的数据通过此方法的参数传递过来。启动SecondActivity：  
  
	button1.setOnClickListener(new OnClickListener){
		@Override
		public void onClick(View v){
			SecondActivity.actionStart(FirstActivity.this , "data1","data2");
		}
	}

