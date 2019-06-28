---
title: 三、UI开发工具
---
# 三、UI开发工具
## 1、常用控件的使用方法
### 1.1TextView
主要用于在界面中显示一段文本信息。  
  
	<TextView
		android:id="@+id/text_view"   //定义控件唯一标识符
		android:layout_width="match_parent"   //控件宽度，和父布局大小一样
		android:layout_height="wrap_content"  //控件高度，控件大小刚好包含里面的内容
		android:text="This is TextView"  />    //显示的文本内容
所有控件都有2个属性，三种值：match_parent、fill_parent、wrap_content。
前两个一样，表示布局和父布局的大小一样，推荐match_parent; wrap_content表示当前控件的大小能够刚好包含住里面的内容就行。也可以对高度和宽度指定固定大小，但是这样会出现不同手机屏幕适配方面的问题。  
指定文字的对齐方式android:gravity  
  
	android:gravity="center"  //还有top、bottom、left、right，可以用“|”同时指定多个值
android:textSize 指定文字大小，android中以sp作为字体大小单位；  
android:textColor 指定文字颜色  
### 1.2Button
程序用于和用户进行交互的重要控件。  
  
	<Button
		android:id="@+id/button"   //定义控件唯一标识符
		android:layout_width="match_parent"   //控件宽度，和父布局大小一样
		android:layout_height="wrap_content"  //控件高度，控件大小刚好包含里面的内容
		android:text="Button"  />  //系统对Button中字母进行大写转换

android:textAllCaps="false" 禁用大写转换。
在MainActivity中为Button点击事件注册监听器：  
  
	public class MainActivity extends AppCompatActivity{
		@Override
		protected void onCreate(Bundle savedInstanceState){
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			Button button = (Button)findViewById(R.id.button);
			button.setOnclickListener(new View.OnClickListener){
				@Override
				public void onClick(View v){
					//此处添加逻辑
				}
			}
		}
	}
### 1.3EditText
用于和用户进行交互的一个重要控件，允许用户在控件里输入和编辑内容，并可以在程序中对这些内容进行处理。  
  
	<EditText
		android:id = "@+id/edit_text"
		android:layout_width = "match_parent"
		android:layout_height = "wrap_content"
	/>
在输入框中显示一些提示性的文字，用户输入任何内容，提示性文字就会消失：  
  
	android:hint = "Type something here" 
输入内容长度限制：  
  
	android:maxLines = "2"  //最大行数为两行,超过2行文本会向上滚动
### 1.4ImageView
用于在界面展示图片，图片通常放在以drawable开头的目录下。  
指定一张图片android:src  
  
	<ImageView
		...
		android:src="@drawable/img_1" />
代码中动态修改ImageView中的图片：  
  
	package com.example.xxxxx.uiwidgettest;

	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;
	import android.view.View;
	import android.widget.Button;
	import android.widget.EditText;
	import android.widget.ImageView;

	public class MainActivity extends AppCompatActivity implements View.OnClickListener{
    private ImageView imageView;
   	private EditText editText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button = (Button)findViewById(R.id.button);
        editText = (EditText)findViewById(R.id.edit_text);
        imageView = (ImageView)findViewById(R.id.image_view);
        button.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.button:
                imageView.setImageResource(R.drawable.img_2);//将图片改为img_2
        }
    	}
	}
### 1.5ProgressBar
用于在界面上显示一个进度条，表示程序正在加载一些数据。  
  
	  <ProgressBar
        android:id="@+id/progress_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
Android控件的可见属性，通过android:visibility进行指定，三种值：visible、invisible、gone  
visible：可见的，默认值  
invisible：不可见，任然占据原来的位置和大小，相当于透明  
gone：不可见也不占用屏幕空间  
通过代码设置控件的可见性，setVisibility()方法，传入View.VISIBLE、View.INVISIBLE、View.GONE  
判断ProgressBar的可见性：  
  
	progressBar.getVisibility() == View.GONE//判断是否是GONE
	progressBar.setVisibility(View.VISIBLE);//修改可见性为可见
指定ProgressBar不同的样式，通过style属性可以将它指定成水平进度条：  
  
	 <ProgressBar
        android:id="@+id/progress_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" 
        style="?android:attr/progressBarStyleHorizontal"//设置为水平进度条
        android:max="100"/>   //进度条最大值为100
### 1.6AlertDialog
在当前界面弹出一个对话框，这个对话框是置顶于所有界面元素之上的，能够屏蔽掉其他控件的交互能力。一般用于提示一些非常重要的内容或者警告信息。  
  
	public class MainActivity extends AppCompatActivity implements View.OnClickListener{
    	private ImageView imageView;
    	private EditText editText;

    	@Override
   	 protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_main);

       	 Button button = (Button)findViewById(R.id.button);
       	 editText = (EditText)findViewById(R.id.edit_text);
       	 imageView = (ImageView)findViewById(R.id.image_view);
        	button.setOnClickListener(this);
   	 }

    	@Override
    	public void onClick(View v) {
       	 switch (v.getId()){
           	 case R.id.button:
                AlertDialog.Builder dialog = new AlertDialog.Builder(MainActivity.this);//创建AlertDialog实例
                dialog.setTitle("this is dialog");//设置对话框标题
                dialog.setMessage("something important");//设置内容
                dialog.setCancelable(false);//是否可取消
                dialog.setPositiveButton("OK", new DialogInterface.OnClickListener() {//设置确定按钮的点击事件
                    @Override
                    public void onClick(DialogInterface dialog, int which) {

                    }
                });
                dialog.setNegativeButton("Cancle", new DialogInterface.OnClickListener() {//设置取消按钮的点击事件
                    @Override
                    public void onClick(DialogInterface dialog, int which) {

                    }
                });
                dialog.show();//调用show方法将对话框显示出来
                break;
            default:
                break;
        }
    }
}
### 1.7ProgressDialog
和AlertDialog类似，不过ProgressDialog会在对话框中显示一个进度条，一般用于标识操作比较耗时，让用户等待。  
  
	@Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.button:
                ProgressDialog processDialog = new ProgressDialog(MainActivity.this);
                processDialog.setTitle("This is ProgressDialog");
                processDialog.setMessage("Loading...");
                processDialog.setCancelable(true);
                processDialog.show();
                break;
            default:
                break;
        }
    }
如果setCancelable中传入false，则不能通过back键取消掉，那么在代码中需要加载完成后必须调用ProgressDialog的dimiss()方法关闭对话框，否则会一直存在。
## 2、四种基本布局
布局是一种可以放置很多空间的容器，可以按照一定的规律调整内部空间的位置。布局的内部出了放置控件外，也可以放置布局，通过多层布局的嵌套。  
### 2.1线性布局
排列方向：  
  
	android:orientation="vertical"  //垂直排列
	android:orientation="horizontal"  //水平排列
	
	android:layout_gravity =  "top"//控件在布局中的对齐方式
线性布局中可以用比例的方式指定控件的大小android:layout_weight，设置了这个属性控件的宽度就不应该在由android:layout_width来决定。LinearLayout下所有控件指定的layout_weight相加，得到一个总值，该控件的layout_weight值除以总值就是占屏幕的宽度的大小。
### 2.2相对布局
RelativeLayout是非常常用的布局，和LinearLayout排列规则不同，RelativeLayout更加随意，可以通过相对定位的方式让控件出现在布局的任何位置。  
  
	<RelativeLayout  ...
		...
		<Button
			android:id="@+id/button3"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:layout_centerInparent="true"
			android:text="Button3" />
		  <Button
			android:id="@+id/button2"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:layout_above="@id/button3" //在button3的上方
			android:layout_toLeftOf="@id/button3"  //在button3的左边
			android:text="Button2" />
		...
	</RelativeLayout>
一个控件去引用另外一个控件的id时，该控件一定要定义在引用控件的后面，不然会出现找不到id的情况。  
### 2.3帧布局
FrameLayout应用场景较少，所有的控件都默认摆放在布局的左上角。  
也可以使用android:layout_gravity来指定对齐方式。  
### 2.4百分比布局
之前的布局只要LineLayout支持使用layout_weight属性来实现比例指定控件大小的功能。引入百分比布局来解决，这种布局中不再使用wrap_content、match_parent等方式指定控件大小，而是直接指定控件在布局中所占的百分比，这样可以轻松实现任意比例分割布局。  
百分比布局只为FrameLayout和RelativeLayout进行功能扩展，提供了PercentFrameLayout和PercentRelativeLayout这两个全新布局。  
在项目的build.gradle中添加百分比布局库的依赖就能保证百分比布局在Android所有系统版本上兼容。 
  
	<android.support.percent.PercentFrameLayout
		...
		<Button
			...
			app:layout_widthPercent="50%"
			app:layout_heightPercent="50%" />
		...

	</android.support.percent.PercentFrameLayout>
## 3、创建自定义控件
所有控件都是直接或间接继承自View，所有布局都是直接或间接继承自ViewGroup。View是Android中最基本的一种UI组件，它可以在屏幕上绘制一块矩形区域，并能响应这块区域的各种事件，我们使用的控件就是在View的基础之上又添加各自特有的功能。  

### 3.1引入布局创建控件  
新建一个title.xml文件  
	  
  	<LinearLayout ...
		...
		<Button
			.. />
		<TextView
			... />
		...
	</LinearLayout>

	//部分属性说明
	android:background = "@drawable/edit_png"//为布局或控件指定背景，可以使用颜色或图片填充
	android:layout_margin = "5dp"//指定控件在各个方向上偏移的距离
	android:layout_marginLeft或者android:layout_marginTop
在程序中使用，在activity_main.xml中添加：  
  
	<LinearLayout ...
		...
		<include layout="@layout/title"/>  //include语句引入
	</LinearLayout>
### 3.2创建自定义控件
新建TitleLayout继承自LinearLayout，让它成为自定义的标题栏控件：  
  
	pulic class TitleLayout extends LinearLayout{
		public TitleLayout(Context context , AttributeSet attrs){//布局中引入TitleLayout控件就会调用这个构造函数
			super(context,attrs);
			//借助LayoutInflater实现标题栏布局多态加载，from方法构建一个LayoutInflater对象
			LayoutInflater.from(context).inflate(R.layout.title,this);//inflate方法可以多态加载一个布局文件。
			//inflate第一个参数是加载的布局文件的id，第二个参数是给加载好的布局在添加一个父布局
		}
	}
创建好布局文件后，在布局文件中添加自定义控件，修改activity_main.xml中的代码：  
  
	<LinearLayout ...
		...
		<com.example.uicustomviews.TitleLayout
			android:layout_width="match_parent"
			android:layout_height="wrap_content" />
给标题栏的按钮注册点击事件，修改TitleLayout代码：  
  
	pulic class TitleLayout extends LinearLayout{
		public TitleLayout(Context context , AttributeSet attrs){
			super(context,attrs);
			LayoutInflater.from(context).inflate(R.layout.title,this);
			Button titleBack =(Button)findViewByid(R.id.title_back);
			Button titleBack =(Button)findViewByid(R.ID.title_edit);
			titleBack.setOnClickListener(new OnClickListener){
				@Override
				public void onClick(View v){
					((Activity)getContext()).finish();
				}
			};
			titleEdit.setOnClickListener(new OnClickListener){
				@Override
				public void onClick(View v){
					Toast.makeText(getContext(),"You Clicked Edit button",Toast.LENGTH_SHORT).show();
				}
			};
		}
	}

## 4、滚动控件-ReyclerView(134页，未完成)
ReyclerView也属于新增的控件，为了让RecyclerView在所有Android版本上都能使用，将其定义在support库中。在app/build.gradle文件中添加：  
  
	dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:27.1.1'
    implementation 'com.android.support.constraint:constraint-layout:1.1.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
    implementation 'com.android.support:recyclerview-v7:27.1.1'  //添加内容
}
点击Sync Now进行同步。 修改activity_main.xml中的代码：  
  
	<android.support.v7.widget.RecyclerView  //添加RecyclerView控件
    	android:id="@+id/recycler_view"
  	  	android:layout_height="match_parent"
    	android:layout_width="match_parent" />

