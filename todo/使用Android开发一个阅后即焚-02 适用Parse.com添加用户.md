title:使用Android开发一个阅后即焚-02 使用Parse.com添加用户
date:2014-10-05 10:12
category:Android
tags:Android
author:刘理想
summary:使用Parse.com添加用户

##1. 创建Parse.com账户和应用

###1.1 创建Parse.com的账户

###1.2 添加Parse.com的SDK

直接将Paser.com的SDK添加到app/libs文件夹内即可。

###1.3 创建一个继承自Application的对象

```java
public class RabbitApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        Parse.initialize(this, "6wZyARdh21YE1SFqceoIh24beo9WCzpYnjMCgqCV", "9KT8jUIBizQbLh2yo5QLUWz74LJcRnIUcorTDuN8");
    }
}
```

Application通常在app启动的时候就会自动创建。Application在app中是一个单例模式，而且Application也是整个app生命周期最长的对象。所有的Activity和Service都是共用着一个Application，所以Application通常用来共享数据，数据传递和数据缓存。

其中的`Parse.initialize()`时根据每个账户不同而不同，具体参见https://parse.com/apps/quickstart#parse_data/mobile/android/native/existing 

仅仅自定义一个Application类时不行的，还需要在AndroidManifest.xml中的`<Application>`节点添加`android:name=".RabbitApplication"`属性.

###1.4 添加Parse.com所需要的权限

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

###1.5 测试Parse

在RabbitApplication中的`onCreate`方法中添加

```java
ParseObject testObject = new ParseObject("TestObject");
testObject.put("foo", "bar");
testObject.saveInBackground();
```

此时，点击运行程序。然后在Parse.com中的core里查看，我们可以看到一个表格。

![Parse.com后台测试数据](images/Snip20141005_1.png)

##2. 使用对话框处理错误

在SignUpActivity中的`onCreate`添加：

```java
mUserName = (EditText) findViewById(R.id.usernameField);
mPassword = (EditText) findViewById(R.id.passwordField);
mEmail = (EditText) findViewById(R.id.emailField);
mSendBtn = (Button) findViewById(R.id.btnSignup);

mSendBtn.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        String username = mUserName.getText().toString().trim();
        String password = mPassword.getText().toString().trim();
        String email = mEmail.getText().toString().trim();

        if (username.isEmpty() || password.isEmpty() || email.isEmpty()){
            AlertDialog.Builder builder = new AlertDialog.Builder(SignUpActivity.this);
            builder.setTitle(R.string.signup_error_title)
                    .setMessage(R.string.signup_error_message)
                    .setPositiveButton(android.R.string.ok, null);
            AlertDialog alertDialog = builder.create();
            alertDialog.show();
        }
        else{
            //todo: new user here
        }
    }
});
```

##3. 使用Parse创建用户

创建用户很简单，文档在https://parse.com/docs/android_guide#users-signup

```java
mSendBtn.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
		//...
        if (username.isEmpty() || password.isEmpty() || email.isEmpty()){
        	//...
        }
        else{
            ParseUser user = new ParseUser();
            user.setUsername(username);
            user.setPassword(password);
            user.setEmail(email);

            user.signUpInBackground(new SignUpCallback() {
                @Override
                public void done(ParseException e) {
                    if (e == null){
                        Intent intent = new Intent(SignUpActivity.this, MainActivity.class);
                        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
                        startActivity(intent);
                    } else {
                        AlertDialog.Builder builder = new AlertDialog.Builder(SignUpActivity.this);
                        builder.setTitle(R.string.signup_error_title)
                                .setMessage(e.getMessage())
                                .setPositiveButton(android.R.string.ok, null);
                        AlertDialog alertDialog = builder.create();
                        alertDialog.show();
                    }
                }
            });
        }
    }
});
```

运行程序并注册，然后到https://www.parse.com/apps/rabbit--45/collections#class/_User 可以看到已经注册了一个用户

![用户注册](images/Snip20141005_2.png)

##4. 用户登录

###4.1 用户登录代码

使用Parse.com登录的文档在：https://parse.com/docs/android_guide#users-login

打开LoginActivity，添加如下代码

```java
protected EditText mUserName;
protected EditText mPassword;
protected Button mLoginBtn;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_login);

    //...

    mUserName = (EditText) findViewById(R.id.usernameField);
    mPassword = (EditText) findViewById(R.id.passwordField);
    mLoginBtn = (Button) findViewById(R.id.btnLogin);

    mLoginBtn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            String username = mUserName.getText().toString().trim();
            String password = mPassword.getText().toString().trim();
			
			//用户名密码不能为空
            if (username.isEmpty() || password.isEmpty()){
                AlertDialog.Builder builder = new AlertDialog.Builder(LoginActivity.this);
                builder.setTitle(R.string.login_error_title)
                        .setMessage(R.string.login_error_message)
                        .setPositiveButton(android.R.string.ok, null);
                AlertDialog alertDialog = builder.create();
                alertDialog.show();
            }
            else{
            	//登录
                ParseUser.logInInBackground(username, password, new LogInCallback() {
                    @Override
                    public void done(ParseUser parseUser, ParseException e) {
                        if (e == null){
                        	//登录成功
                            Intent intent = new Intent(LoginActivity.this, MainActivity.class);
                            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
                            startActivity(intent);
                        } else {
                        	//登录失败
                            AlertDialog.Builder builder = new AlertDialog.Builder(LoginActivity.this);
                            builder.setTitle(R.string.login_error_title)
                                    .setMessage(e.getMessage())
                                    .setPositiveButton(android.R.string.ok, null);
                            AlertDialog alertDialog = builder.create();
                            alertDialog.show();
                        }
                    }
                });
            }
        }
    });
}
```

###4.2 修改MainActivity

Parse获取当前用户的方法：https://parse.com/docs/android_guide#users-current

打开MainActivity.java，修改`onCreate`方法：

```java
ParseUser currentUser = ParseUser.getCurrentUser();
if (currentUser != null) {
    Log.v(TAG, currentUser.getUsername());
} else {
    // show the signup or login screen
    Intent intent = new Intent(this, LoginActivity.class);
    //第1种方法
    //intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_CLEAR_TASK);
    //第2种方法
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
    startActivity(intent);
}
```

##5. 用户登出

Parse登出用户的方法也在：https://parse.com/docs/android_guide#users-current

同时，我们还需要考虑一个问题，在哪里添加登出按钮比较合适呢？一般我们可以在菜单栏中添加退出按钮。

###5.1 添加菜单

Android的菜单是在`onCreateOptionsMenu`方法中定义，打开MainActivity.java，我们看到它的菜单定义是：

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    // Inflate the menu; this adds items to the action bar if it is present.
    getMenuInflater().inflate(R.menu.main, menu);
    return true;
}
```

从上面的代码我们知道，MainActivity对应的菜单是在`res/menu/main.xml`中定义。打开这个文件，我们在里面定义菜单如下：

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    tools:context=".MainActivity" >
    <item android:id="@+id/action_logout"
        android:title="@string/menu_logout_label"
        android:orderInCategory="100"
        android:showAsAction="never"/>
</menu>
```

说明：
- orderInCategory:定义了一个顺序，值越小越在上面，值越大越在下面
- showAsAction:定义了是否在菜单栏中显示该动作，`always`表示一直显示，`ifRoom`表示有足够空间时显示， `never`表示不显示.

###5.2 菜单事件处理方法

Android的菜单事件处理方法是在`onOptionsItemSelected`中处理的。比如我们要处理登出事件，在`onOptionsItemSelected`中添加：

```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    // Handle action bar item clicks here. The action bar will
    // automatically handle clicks on the Home/Up button, so long
    // as you specify a parent activity in AndroidManifest.xml.
    int id = item.getItemId();
    if (id == R.id.action_logout) {
        //登出
        ParseUser.logOut();
        navigateToLoginScreen();
        return true;
    }
    return super.onOptionsItemSelected(item);
}
```

其中`navigateToLoginScreent()`方法如下：

```java
public void navigateToLoginScreen(){
    // show the signup or login screen
    Intent intent = new Intent(this, LoginActivity.class);
    //第1种方法
    //intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_CLEAR_TASK);
    //第2种方法
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
    startActivity(intent);
}
```

##6. 添加指示器表示登录状态

之前在使用Android创建博客阅读器的过程中，我们使用在layout中添加指示器的方法来添加指示器。除了这种方法之外，我们还可以使用另外一种方法在菜单中添加指示器。

打开SignUpActivity.java，在`onCreate`中添加：

```java
requestWindowFeature(Window.FEATURE_INDETERMINATE_PROGRESS);
setContentView(R.layout.activity_login);
```

注意这两个的顺序，不能搞混，否则会导致程序崩溃。

后面就可以使用`setProgressBarIndeterminateVisibility();`来显示和隐藏指示器了。比如

```java
setProgressBarIndeterminateVisibility(true);
ParseUser.logInInBackground(username, password, new LogInCallback() {
    @Override
    public void done(ParseUser parseUser, ParseException e) {
        setProgressBarIndeterminateVisibility(false);
        //...
    }
}
```

##7. 总结

本节，我们学习了如何使用Parse，如何将Parse SDK添加到现有项目中，如何创建用户、用户登录、用户登出、使用对话框处理错误和使用指示器来显示注册、登录状态等等。


