---
title: Button 及点击事件
date: 2018-05-27 16:12:28
tags: Android
---

# 前言

最近有看关于 Android 开发入门的教程，觉得下面这篇关于 “Button 及点击事件” 的文章写的非常详细。

​                                                                                    关注该公众号

![](https://mp.weixin.qq.com/mp/qrcode?scene=10000004&size=102&__biz=MzU5NzQ5MzcyMw==&mid=100000158&idx=1&sn=39ead68eeeaefd63505bf96502e2e2b8&send_time=)

# Button 按钮

我们打开上一节的 UIText 项目中的 activity_main.xml (AIDE 为 main.xml)，删掉里面的 TextView ，加入一个 Button 。最后效果如下： 

```xml
<? xml version = "1.0" encoding = "utf-8" ?>
<LinearLayout 
              xmlns:android = "http://schemas.android.com/apk/res/android"
              android:layout_width = "match_parent" 
              android:layout_height = "match_parent" 
              android:orientation = "horizontal" >
    <Button 
            android:layout_width = "match_parent" 
            android:layout_height = "wrap_content"
            android:id = "@+id/button"
            android:text = "按钮" />
</LinearLayout>
```

这些属性都是昨天我们介绍过的，

| 属性          | 值           | 说明                         |
| ------------- | ------------ | ---------------------------- |
| layout_width  | match_parent | 将控件的宽指定为填充父布局   |
| layout_height | wrap_content | 将控件的高指定为适应控件大小 |
| id            | button       | 将控件的 ID 指定为 button    |
| text          | 按钮         | 将控件的文字指定为“按钮”     |



# 1.点击事件

但是我们这个按钮中看不中用，点击了什么反应都没有，那是因为还没有给他添加逻辑。如果需要按钮在点击时作出反应，我们就需要为 Button 注册一个 onClick（点击）事件监听器。打开 MainActivity.java 加入代码，如下所示：

```java
public class MainActivity extends AppCompatActivity {
	@Override 
    protected void onCreate (Bundle savedInstanceState) { 
        super . onCreate (savedInstanceState); 
        setContentView (R.layout.activity_main ); 
        Button mButton = (Button) findViewById(R.id.button); 
        mButton.setOnClickListener(new View.OnClickListener() { 
            @Override 
            public void onClick (View view) {       
			//在此处添加被点击时的逻辑
           }
        });
    }
}
```

在第 6 行，我们先是定义了一个 Button 类型的变量 mButton 。然后为了他赋一个初始值：我们的 Button，然后 mButton 就代表了我们这个代码了。但是怎么获取我们的 Button 呢？我们先通过 findViewById(R.id.xxx) 来获取我们的 Button 实例，其中xxx就是在布局里定义的 ID 。findViewById 返回的是个 View 对象，我们需要通过 (Button) 向下转型成一个 Button 对象。这个过程连起来就是第 8 行。这时我们的 mButton 就可以代表我们的按钮实例了。然后通过调用 mButton 的 .setonClickListener() 来为Button 注册一个点击监听器。在 .setonClickListener() 内需要传入一个 View.OnClickListener(){} 对象。然后在 View.OnClickListener(){} 里重写 onClick() 方法，在 onClick() 方法里编辑自己的逻辑。

是不是听懵了？？？没事，我当初也懵了很久，一直不明白为什么要这样。那时我查遍教程，由于我特别差的 Java 基础，根本看不透，看不懂，和你们现在一样。我的做法就是硬记下来，在需要用这段时就照猫画虎写下来。等我经验多了，基础知识多了，我不但背下来了，我还理解了。我知道这么多你背不下来，我也背不下来。我建议你去看看文末的视频，里面演示了如何使用代码补全功能，只要大体记住结构和关键词，就可与补全代码。但是我要求你没补全一次就默默背一次，这样才能早日修成正果。

## 1.1使用 Toast

接下来我们先小试牛刀，添加一个逻辑，就是弹出一个 Toast 。所谓的 Toast 就是在屏幕下方的一个小气泡.

Toast 的用法比较简单，通过静态方法 makeText() 创建出一个 Toast 对象，然后调用 show() 方法来把 Toast 显示出来。其中 makeText() 需要传入 3 个参数：

| 参数           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| Context        | Toast 的上下文，由于活动本身就是一个 Context 对象，这里直接传入 MainActivity.this 即可 |
| Text           | Toast 显示的文字内容                                         |
| Toast 显示时长 | 通常选择两个内置的常量：Toast.LENGTHSHORT （短）和 Toast.LENGTHLONG（长） |

 于是我们就在 onClick()里写一个 Toast :

```java
public class MainActivity extends AppCompatActivity {   
    @Override    
    protected void onCreate(Bundle savedInstanceState) { 
        super.onCreate(savedInstanceState);        
        setContentView(R.layout.activity_main);        
        Button mButton = (Button)findViewById(R.id.button);       
        mButton.setOnClickListener(new View.OnClickListener() {           
            @Override           
            public void onClick(View view) {    
                //在此处添加被点击时的逻辑              
                Toast.makeText(MainActivity.this, "点击了 Toast", Toast.LENGTH_SHORT).show();
            }        
        });  
    }
}
```

>  小TIPS：在 Android Studio 中快速插入一个Toast可以输入 Toast （注意大小写）之后按方向键选择列表里第二个，然后按回车，第一个参数通常会智能的帮我们生成，然后我们只需要写入我们要显示的文字就好。 

## 1.2通过接口注册点击事件

还有一种绑定点击事件的方法就是使用接口，接口的概念我并没有介绍，我觉得我也没那个本事也没必要仔细讲接口的概念，我就告诉你怎么做吧。看代码：

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener{   
    @Override    
    protected void onCreate(Bundle savedInstanceState) {   
        super.onCreate(savedInstanceState);     
        setContentView(R.layout.activity_main);    
        Button mButton = (Button)findViewById(R.id.button);       
        mButton.setOnClickListener(this);    
    }    
    @Override  
    public void onClick(View view) {      
        switch (view.getId()) {          
            case R.id.button:   
                Toast.makeText(MainActivity.this, "点击了 Button", Toast.LENGTH_SHORT).show();
                break;           
            default:           
                break;      
        }   
    }
}
```

就是在第一行的 AppCompatActivity 后面加上 implements 代表实现一个接口，然后接着写 View.OnClickListener 来代表实现一个 onClick 的接口。接着借鉴刚才的 setOnClickListener() ，只不过这里传入活动的实例 this 就可以了。最后在 onCreate 外重写 onClick() 方法，然后参照上面代码的格式写就可以了。如果实在不明白可以看我的视频，这里也有一些自动补全的技巧，在重写 onClick() 方法时只要输入 onClick 然后按方向键加回车确定就能自动补全所有的。实在还不会就用第一种吧，伴随着知识继续积累，慢慢那会懂的。



# 2.综合使用

每点击一次按钮 TextView 里的数字就加 1 。我们应该定义两个控件，一个是 Button 一个是 TextView ，然后注册 Button 的点击事件，在事件里获取 TextView 的值，然后把TextView 的值加 1 后，在修改 TextView 上的文字。

思路大体就是这样，我们在之前的基础上接着修改 activity_main.xml （AIDE 为 main.xml）加入一个 TextView ，让宽度充满父布局，高度适应控件大小：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"   
              android:layout_width="match_parent"  
              android:layout_height="match_parent"  
              android:orientation="horizontal"> 
    <Button       
            android:layout_width="match_parent"    
            android:layout_height="wrap_content"  
            android:id="@+id/button"    
            android:text="按钮"/> 
    <TextView       
              android:layout_width="match_parent"   
              android:layout_height="wrap_content"  
              android:id="@+id/textview"       
              android:text="这是 TextView"/>
</LinearLayout>
```

这些属性我相信你已经非常熟悉了，就不多赘述了，我们打开 MainActivity.java 修改逻辑，讲解都写在注释里了：

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener{   
    Button mButton;    //定义成全局变量，保存 Button 对象，便于全局调用   
    TextView mTextView;  //定义成全局变量，保存 TextView 对象，便于全局调用  
    int textInTextView;  //定义成全局变量，保存 TextView 中的值，便于调用   
    @Override   
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);    
        setContentView(R.layout.activity_main);   
        mTextView = (TextView)findViewById(R.id.textview);//绑定控件，获取实例        
        mTextView.setText("0"); //初始化 TextView 中的值为 0      
        mButton = (Button)findViewById(R.id.button);  //绑定控件，获取实例 
        mButton.setOnClickListener(this);  //注册点击事件   
    }   
    @Override 
    public void onClick(View view) { 
        switch (view.getId()){      
            case R.id.button:      
                textInTextView = Integer.parseInt(mTextView.getText().toString());                				 //我们要的时 int 值，因为我们要做数学运算            
                //而通过 getText() 获取的值是 byte 类型的，需要调用 .toString 转换成 String 型              	  //然后通过 Integer.getInteger() 方法将获取到的值转换成整型。   
                textInTextView++;  //自加
                mTextView.setText(Integer.toString(textInTextView));        
                //将结果显示到 TextView 上               
                //由于 setText() 需要传入一个字符串，          
                //所以要调用 Integer.toString 方法把数字转换成字符串，再传入               
                break;       
            default:          
                break;     
        }
    }
}
```

> Tips: 调用 TextVeiw 的 setText() 可以设置 TextView 的文字，同理可以用 getText() 获取到 TextView 的文字。