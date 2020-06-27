Title: Android学习笔记-06 Android用户界面之布局
Date: 2014-08-28 08:36
Category: Android
Tags: Android学习笔记, Android
Author: 刘理想
Summary: Android学习笔记-06 Android用户界面之布局

##1 Relative Layout
根据子元素来调整位置

##2 Framely Layout
一直在左上角，速度快

##3 Linear Layout
一个挨着一个排列，可以水平排列，也可以垂直排列。

有一个weight属性，表示子对象分割父级对象分割的比例。

##4 Table Layout
最外层是Table Layout，垂直布局
里层是Table Row，水平布局

##5 通过应用程序动态的添加子对象

###5.1 创建一个Layout文件

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent"
              android:id = "@+id/linear_layout"
        >
</LinearLayout>
```

###5.2 在Activity中添加

```java
public class MainActivity extends Activity {
    private Button btn;
    private LinearLayout linearLayout;
    private View.OnClickListener onClickListern = new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            linearLayout.removeView(v);
        }
    };

    /**
     * Called when the activity is first created.
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        linearLayout = (LinearLayout) findViewById(R.id.linear_layout);
        for (int i = 0; i < 5; i ++){
            btn = new Button(this);
            btn.setText("点我"+i);
            btn.setOnClickListener(onClickListern);
            linearLayout.addView(btn);
        }
    }
}
```

其中，btn是在程序中声明的，Layout的addView方法添加子元素
