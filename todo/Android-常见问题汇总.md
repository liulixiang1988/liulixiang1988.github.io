Title: Android-常见问题汇总
Date: 2015-06-26 8:30
Category: Android
Tags: Android
Author: 刘理想

##使用Android兼容库时ToolBar不显示

这种情况是因为使用错了兼容库的版本，请使用v7版本的兼容库：

```
import android.support.v7.app.AppCompatActivity;
```

##层级导航（Up导航）

- 层级导航(Up导航)是在app内导航到上一级别，始终是在同一个app内，是逐级向上的应用内导航。
- 后退键导航(Back导航)则是根据用户的历史记录进行导航，只能返回到上一次的用户界面，比如导航到桌面或者其他应用中。

给一个应用添加逐级导航的方法是在**AndroidManifest.xml**中的Activity中添加`android:parentActivityName`属性:

```
<activity
    android:name=".CrimePagerActivity"
    android:label="@string/app_name"
    android:parentActivityName=".CrimeListActivity">
</activity>
```

效果如图：

![img](images/android-2015-06-26-1.png)

