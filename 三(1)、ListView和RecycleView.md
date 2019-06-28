---
title: 三(1)、ListView和RecyclerView
---
# 1、ListView
程序中有大量数据需要展示的时候就可以借助ListView来实现，ListView允许用户通过手指上下滑动的方式将屏幕外的数据滚动到屏幕内，同时屏幕上原有的数据则会滚动出屏幕。  
## 1.1ListView简单用法
新建ListViewTest项目，修改activity_main.xml代码,在布局中加入ListView控件：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ListView
        android:id="@+id/list_view"    //指定id
        android:layout_width="match_parent"    //占满整个布局空间
        android:layout_height="match_parent"></ListView>

	</android.support.constraint.ConstraintLayout>
修改MainActivity中的代码：  
  
	package com.example.xxxxx.listviewtest;

	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;
	import android.widget.ArrayAdapter;
	import android.widget.ListView;

	public class MainActivity extends AppCompatActivity {

    	private String[] data = {"Apple" , "Banana" , "Orange" , "Watermelon" , "Pear" ,"Grape" ,
       		"Pineapple" , "Strawberry" , "Cherry" , "Mango" ,"Apple" , "Banana" , "Orange" ,
            "Watermelon" , "Pear" ,"Grape" , "Pineapple" , "Strawberry" , "Cherry" , "Mango"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //数组中的数据无法直接传递给ListView，需要借助适配器完成，此处使用ArrayAdapter
        //通过泛型指定要适配的数控类型，然后在构造函数中传入要适配的数据
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(MainActivity.this,android.R.layout.simple_list_item_1,data);

        ListView listView = (ListView)findViewById(R.id.list_view);
        //将构建好的适配器对象传递进去，使ListView和数据之间建立关联
        listView.setAdapter(adapter);
    }
	}
ArrayAdapter的构造函数中依次传入当前上下文、ListView子项布局的id，以及要适配的数据。使用android.R.layout.simple_list_item_1作为ListView子项布局的id，这是一个Android内置的布局文件，里面只有一个TextView，可用于简单地显示一段文本。  
## 1.2定制ListView的界面
让它显示更加丰富的内容，不仅仅是一段文本。为水果配置图片。  
新建一个实体类Fruit，作为ListView适配器的适配类型。代码如下：  
  
	package com.example.xxxxxxxxxx.listviewtest;
	public class Fruit {
    	private String name;
    	private  int imageId;

    public Fruit(String name , int imageId){
        this.name = name;
        this.imageId = imageId;
    }

    public String getName(){
        return name;
    }
    public int getImageId(){
        return imageId;
    }
	}
name表示水果的名字，imageId表示水果对应图片的资源id。  
然后需要为ListView的子项指定一个我们自定义的布局，在layout目录下新建fruit_item.xml，代码如下：  
  
	<?xml version="1.0" encoding="utf-8"?>
	<android.support.constraint.ConstraintLayout
    	xmlns:android="http://schemas.android.com/apk/res/android" 
    	android:layout_width="match_parent"
    	android:layout_height="match_parent">
    
    <ImageView
        android:id="@+id/fruit_image"
        android:layout_width="60dp"
        android:layout_height="60dp" />
    
    <TextView
        android:id="@+id/fruit_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" 
        android:layout_gravity = "center_vertical"  //垂直居中显示
        android:layout_marginLeft="10dp"/>

	</android.support.constraint.ConstraintLayout>
接下来需要创建一个自定义的适配器，这个适配器继承自ArrayAdapter，并将泛型指定为Fruit。新建FruitAdapter，代码如下：  
  
	package com.example.xxxxx.listviewtest;

	import android.content.Context;
	import android.support.annotation.NonNull;
	import android.support.annotation.Nullable;
	import android.view.LayoutInflater;
	import android.view.View;
	import android.view.ViewGroup;
	import android.widget.ArrayAdapter;
	import android.widget.ImageView;
	import android.widget.TextView;
	import java.util.List;

	public class FruitAdapter extends ArrayAdapter<Fruit> {
    	private int resourceId;
    	//重写父类的一组构造函数，用于将上下文、ListView子项布局id和数据传递进来。
    	public FruitAdapter(Context context, int textViewResourceId, List<Fruit>objects){
        	super(context, textViewResourceId, objects);
        	resourceId = textViewResourceId;
    	}

    @Override
    //重写getView方法，在每个子项被滚动到屏幕的时候就会被调用此方法
    public View getView(int position, @Nullable View convertView, @NonNull ViewGroup parent) {
        //获取当前项的Fruit实例
        Fruit fruit = getItem(position);
        //使用LayoutInflater为每个子项加载我们传入的布局
        //第三个参数false表示只让我们在父布局中声明的layout属性生效，不为这个view添加父布局，因为一旦添加父布局
        //之后，它就不能再添加到ListView中了（ListView的标准写法）
        View view = LayoutInflater.from(getContext()).inflate(resourceId, parent, false);

		//获取ImageView和TextView的实例
        ImageView fruitImage = (ImageView)view.findViewById(R.id.fruit_image);
        TextView fruitName = (TextView)view.findViewById(R.id.fruit_name);
		//设置显示的图片和文字
        fruitImage.setImageResource(fruit.getImageId());
        fruitName.setText(fruit.getName());
        return view;  //返回布局
    }
	}
修改MainActivity中的代码：  
  
	package com.example.xxxxx.listviewtest;

	import android.support.annotation.Nullable;
	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;
	import android.widget.ArrayAdapter;
	import android.widget.ListView;
	import java.util.ArrayList;
	import java.util.List;

	public class MainActivity extends AppCompatActivity {

    	private List<Fruit> fruitList = new ArrayList<>();

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //初始化水果数据
        initFruits();
        
        //构建FruitAdapter对象，并将FruitAdapter作为适配器传递给ListView。
        FruitAdapter adapter = new FruitAdapter(MainActivity.this, R.layout.fruit_item, fruitList);
        ListView listView = (ListView)findViewById(R.id.list_view);
        listView.setAdapter(adapter);
    }

    //初始化水果数据，传入水果的名字和图片id，并将对象添加到水果列表中。
    private void initFruits(){
        for (int i = 0; i<2; i++){
            Fruit apple = new Fruit("Apple",R.drawable.apple_pic);
            fruitList.add(apple);
            Fruit banana = new Fruit("Banana", R.drawable.banana_pic);
            fruitList.add(banana);
            Fruit orange = new Fruit("Orange",R.drawable.orange_pic);
            fruitList.add(orange);
            Fruit watermelon = new Fruit("Watermelon", R.drawable.watermelon_pic);
            fruitList.add(watermelon);
            Fruit pear = new Fruit("Pear",R.drawable.pear_pic);
            fruitList.add(pear);
            Fruit grape = new Fruit("Grape", R.drawable.grape_pic);
            fruitList.add(grape);
            Fruit pineapple = new Fruit("Pineapple", R.drawable.pineapple_pic);
            fruitList.add(pineapple);
            Fruit strawberry = new Fruit("Strawberry", R.drawable.strawberry_pic);
            fruitList.add(strawberry);
            Fruit cherry = new Fruit("Cherry", R.drawable.cherry_pic);
            fruitList.add(cherry);
            Fruit mango = new Fruit("Mango", R.drawable.mango_pic);
            fruitList.add(mango);
        }
    }
	}
## 1.3ListView的点击事件
ListView如何响应用户的点击事件，修改MainActivity中代码：  
  
	...
	@Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //初始化水果数据
        initFruits();

        //构建FruitAdapter对象，并将FruitAdapter作为适配器传递给ListView。
        FruitAdapter adapter = new FruitAdapter(MainActivity.this, R.layout.fruit_item, fruitList);
        ListView listView = (ListView)findViewById(R.id.list_view);
        listView.setAdapter(adapter);
        //响应点击事件
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                Fruit fruit = fruitList.get(position);
                Toast.makeText(MainActivity.this, fruit.getName(),Toast.LENGTH_SHORT).show();
            }
        });
    }
	...
# 2、RecyclerView
新建RecyclerView项目。  
## 2.1RecyclerView基本用法  

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
新建Fruit类和fruit_item.xml都和ListViewTest的一样。  
然后为RecyclerView准备一个适配器，新建FruitAdapter类，让这个适配器继承自RecyclerView.Adapter，并将泛型指定为FruitAdapter.ViewHolder。ViewHolder是在FruitAdapter中定义的一个内部类。准备适配器代码如下：  
  
	package com.example.xxxxx.recyclerview;

	import android.support.annotation.NonNull;
	import android.support.v7.widget.RecyclerView;
	import android.view.LayoutInflater;
	import android.view.View;
	import android.view.ViewGroup;
	import android.widget.ImageView;
	import android.widget.TextView;
	import java.util.List;

	public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {
    	private List<Fruit> mFruitList;

    //定义一个内部类，继承RecyclerView.ViewHolder
    static class ViewHolder extends RecyclerView.ViewHolder{
        ImageView fruitImage;
        TextView fruitName;

        //构造函数的参数view就是RecyclerView子项的最外层布局，然后就可以获取ImageView和TextView实例
        public ViewHolder(View view){
            super(view);
            fruitImage = (ImageView)view.findViewById(R.id.fruit_image);
            fruitName = (TextView)view.findViewById(R.id.fruit_name);
        }
    }

    //构造函数用于将要展示的数据源传进来，并赋值给全局变量，方便后续对其操作
    public FruitAdapter(List<Fruit> fruitList){
        mFruitList = fruitList;
    }

    //继承自RecyclerView.ViewHolder就必须冲写这三个方法
    @Override
    //将fruit_item加载进来，然后创建一个ViewHolder实例
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.fruit_item, parent, false);
        ViewHolder holder = new ViewHolder(view);
        return  holder;
    }

    //对RecyclerView子项的数据进行赋值，在每个子项滚动到屏幕内的时候执行，通过position参数得到当前的fruit实例
    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        Fruit fruit = mFruitList.get(position);
        holder.fruitImage.setImageResource(fruit.getImageId());
        holder.fruitName.setText(fruit.getName());
    }

    //RecyclerView一共有多少子项
    @Override
    public int getItemCount() {
        return mFruitList.size();
    }
	}

  准备好适配器之后就可以使用RecyclerView，修改MainActivity中代码：  
  
	package com.example.xxxxx.recyclerview;

	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;
	import android.support.v7.widget.LinearLayoutManager;
	import android.support.v7.widget.RecyclerView;
	import java.util.ArrayList;
	import java.util.List;

	public class MainActivity extends AppCompatActivity {

    private List<Fruit> fruitList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initFruits();
        //获取RecyclerView实例
        RecyclerView recyclerView = (RecyclerView)findViewById(R.id.recycler_view);

        //LinearLayoutManager是线性布局的意思，实现和ListView类似的效果
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        recyclerView.setLayoutManager(layoutManager);
        FruitAdapter adapter = new FruitAdapter(fruitList);
        recyclerView.setAdapter(adapter);
    }

    //初始化水果数据，传入水果的名字和图片id，并将对象添加到水果列表中。
    private void initFruits(){
        for (int i = 0; i<3; i++){
            Fruit apple = new Fruit("Apple",R.drawable.apple_pic);
            fruitList.add(apple);
            Fruit banana = new Fruit("Banana", R.drawable.banana_pic);
            fruitList.add(banana);
            Fruit orange = new Fruit("Orange",R.drawable.orange_pic);
            fruitList.add(orange);
            Fruit watermelon = new Fruit("Watermelon", R.drawable.watermelon_pic);
            fruitList.add(watermelon);
            Fruit pear = new Fruit("Pear",R.drawable.pear_pic);
            fruitList.add(pear);
            Fruit grape = new Fruit("Grape", R.drawable.grape_pic);
            fruitList.add(grape);
            Fruit pineapple = new Fruit("Pineapple", R.drawable.pineapple_pic);
            fruitList.add(pineapple);
            Fruit strawberry = new Fruit("Strawberry", R.drawable.strawberry_pic);
            fruitList.add(strawberry);
            Fruit cherry = new Fruit("Cherry", R.drawable.cherry_pic);
            fruitList.add(cherry);
            Fruit mango = new Fruit("Mango", R.drawable.mango_pic);
            fruitList.add(mango);
        }
    }
	}  
## 2.2实现横向滚动
修改fruit_item布局，目前这个布局里面的元素是水平排列的，适用于纵向滚动的场景，如果要实现横向滚动，应该把fruit_item里的元素改成垂直排列才合理。修改fruit_item.xml中的代码：  
  
	<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="100dp"
    android:layout_height="wrap_content">

	<ImageView
    android:id="@+id/fruit_image"
    android:layout_width="50dp"
    android:layout_height="50dp"
    android:layout_gravity="center_horizontal"/>
	<TextView
    android:id="@+id/fruit_name"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center_horizontal"
    android:layout_marginTop="10dp"/>

	</LinearLayout>
将LinearLayout改成垂直方向排列，并把宽度设置为100dp。将ImageView和TextView设置成在布局中水平居中，使用layout_marginTop属性让文字和图片直接保持一些距离。  
修改MainActivity中的代码：  
  
	...
	public class MainActivity extends AppCompatActivity {

    private List<Fruit> fruitList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initFruits();
        //获取RecyclerView实例
        RecyclerView recyclerView = (RecyclerView)findViewById(R.id.recycler_view);

        //LinearLayoutManager是线性布局的意思，实现和ListView类似的效果
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        //recyclerView.setLayoutManager(layoutManager);
        //FruitAdapter adapter = new FruitAdapter(fruitList);
        //实现横向滚动
        layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);
        recyclerView.setLayoutManager(layoutManager);
        FruitAdapter adapter = new FruitAdapter(fruitList);
        recyclerView.setAdapter(adapter);
    }
	}
## 2.3其它布局
除了LinearLayoutManager之外，RecyclerView还提供了GridLayoutManager和StaggeredGridLayoutManager这两种内置的布局排列方式。GridLayoutManager可以用于实现网络布局，StaggeredGridLayoutManager可以用于实现瀑布流布局。  
## 2.4RecyclerView的点击事件
RecyclerView的所有点击事件都是由具体的View去注册的。在RecyclerView中注册点击事件，首先修改FruitAdapter中的代码：  
  
	public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {
    	private List<Fruit> mFruitList;

    //定义一个内部类，继承RecyclerView.ViewHolder
    static class ViewHolder extends RecyclerView.ViewHolder{
        View fruitView;  //新增代码
        ImageView fruitImage;
        TextView fruitName;

        //构造函数的参数view就是RecyclerView子项的最外层布局，然后就可以获取ImageView和TextView实例
        public ViewHolder(View view){
            super(view);
            fruitView = view; //新增代码
            fruitImage = (ImageView)view.findViewById(R.id.fruit_image);
            fruitName = (TextView)view.findViewById(R.id.fruit_name);
        }
    }

    //构造函数用于将要展示的数据源传进来，并赋值给全局变量，方便后续对其操作
    public FruitAdapter(List<Fruit> fruitList){
        mFruitList = fruitList;
    }

    //继承自RecyclerView.ViewHolder就必须冲写这三个方法
    @Override
    //将fruit_item加载进来，然后创建一个ViewHolder实例
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.fruit_item, parent, false);

        //新增代码
        final ViewHolder holder = new ViewHolder(view);
        //ViewHolder holder = new ViewHolder(view);
        holder.fruitView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(v.getContext(),"you clicked view"+fruit.getName(),Toast.LENGTH_SHORT).show();
            }
        });
        return  holder;
    }

    //对RecyclerView子项的数据进行赋值，在每个子项滚动到屏幕内的时候执行，通过position参数得到当前的fruit实例
    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        Fruit fruit = mFruitList.get(position);
        holder.fruitImage.setImageResource(fruit.getImageId());
        holder.fruitName.setText(fruit.getName());
    }

    //RecyclerView一共有多少子项
    @Override
    public int getItemCount() {
        return mFruitList.size();
    }
	}




