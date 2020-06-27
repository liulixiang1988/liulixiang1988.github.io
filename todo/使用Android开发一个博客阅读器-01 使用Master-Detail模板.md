Title: 使用Android开发一个博客阅读器-01 使用Master-Detail模板
Date: 2014-09-19 21:36
Category: Android
Tags: Android学习笔记, Android, Androi博客阅读
Author: 刘理想
Summary: Android实战开发，使用Android开发一个博客阅读器

项目地址：https://github.com/liulixiang1988/android_demo/tree/master/MasterDetailExample

我们使用Master-Detail模板来做本应用的模板。

##建立Master-Detail模板

新建Android项目，命名为MasterDetailExample，模板类型选择为Master-Detail。


---

##理解Master-Details模板

ListFraments和DetailFragments

DummyContent用来作为MVC中的Model，保存List数据。

---

##Fragments中几种方法的调用顺序

###1.`onAttach(Activity activity)`第一个被调用

包含Fragment的Activity必须实现`Callbacks`接口。

```java
@Override
public void onAttach(Activity activity) {
    super.onAttach(activity);

    // Activities containing this fragment must implement its callbacks.
    if (!(activity instanceof Callbacks)) {
        throw new IllegalStateException("Activity must implement fragment's callbacks.");
    }

    mCallbacks = (Callbacks) activity;
}
```

###2. `onCreate(Bundle savedInstanceState)`第二个被调用

这里设置一些数据项。

```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // TODO: replace with a real list adapter.
    setListAdapter(new ArrayAdapter<DummyContent.DummyItem>(
            getActivity(),
            android.R.layout.simple_list_item_activated_1,
            android.R.id.text1,
            DummyContent.ITEMS));
}
```

###3.`onViewCreated(View view, Bundle savedInstanceState)`第三个被调用

```java
@Override
public void onViewCreated(View view, Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);

    // Restore the previously serialized activated item position.
    if (savedInstanceState != null
            && savedInstanceState.containsKey(STATE_ACTIVATED_POSITION)) {
        setActivatedPosition(savedInstanceState.getInt(STATE_ACTIVATED_POSITION));
    }
}
```

###4. 对于ListFragment，当点击时，`onListItemClick(ListView listView, View view, int position, long id)`被调用

```java
@Override
public void onListItemClick(ListView listView, View view, int position, long id) {
    super.onListItemClick(listView, view, position, id);

    // Notify the active callbacks interface (the activity, if the
    // fragment is attached to one) that an item has been selected.
    mCallbacks.onItemSelected(DummyContent.ITEMS.get(position).id);
}
```

---

##理解DetailActivity源码

DetailActivity中包含的Fragment是手工添加的，而非通过Layout文件添加。添加的代码在`onCreate`方法中。
我们使用`fragment.setArguments(arguments)`来设置参数，其中参数`arguments`是`Bundle`类型。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_blogpost_detail);

    // 添加返回按钮
    getActionBar().setDisplayHomeAsUpEnabled(true);

    // savedInstanceState is non-null when there is fragment state
    // saved from previous configurations of this activity
    // (e.g. when rotating the screen from portrait to landscape).
    // In this case, the fragment will automatically be re-added
    // to its container so we don't need to manually add it.
    // For more information, see the Fragments API guide at:
    //
    // http://developer.android.com/guide/components/fragments.html
    //
    if (savedInstanceState == null) {
        // Create the detail fragment and add it to the activity
        // using a fragment transaction.
        Bundle arguments = new Bundle();
        //传递列表行到Fragment中
        arguments.putString(BlogPostDetailFragment.ARG_ITEM_ID,
                getIntent().getStringExtra(BlogPostDetailFragment.ARG_ITEM_ID));
        //创建Fragment
        BlogPostDetailFragment fragment = new BlogPostDetailFragment();
        fragment.setArguments(arguments);
        getFragmentManager().beginTransaction()
                .add(R.id.blogpost_detail_container, fragment)
                .commit();
    }
}
```

##理解DetailFragment源码

DetailFragment中包含一个`mItem`，它是`DummyContent.DummyItem`类型。
在`onCreate`方法中，我们使用`getArguments`来获取传递的`Bundle`类型的参数，然后再调用`Bundle.getString()`方法就能获取`Bundle`中的数据。

```java
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        if (getArguments().containsKey(ARG_ITEM_ID)) {
            // Load the dummy content specified by the fragment
            // arguments. In a real-world scenario, use a Loader
            // to load content from a content provider.
            mItem = DummyContent.ITEM_MAP.get(getArguments().getString(ARG_ITEM_ID));
        }
    }
```

再来看看`onCreateView`方法，用`mItem.content`来填充数据，我们也可以换成其他的数据类型。

```java
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        View rootView = inflater.inflate(R.layout.fragment_blogpost_detail, container, false);

        // Show the dummy content as text in a TextView.
        if (mItem != null) {
            ((TextView) rootView.findViewById(R.id.blogpost_detail)).setText(mItem.content);
        }

        return rootView;
    }
```