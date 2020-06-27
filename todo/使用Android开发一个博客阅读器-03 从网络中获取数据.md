Title: 使用Android开发一个博客阅读器-03 从网络中获取数据
Date: 2014-09-26 15:37
Category: Android
Tags: Android学习笔记, Android, Androi博客阅读
Author: 刘理想
Summary: Android实战开发，使用Android开发一个博客阅读器，第三篇，从网络中获取数据

##1 获取URL

在MainListActivity.java中添加两个常量：

```java
public static final int NUMBER_OF_POSTS = 20; //博客数目
public static final String TAG = MainListActivity.class.getSimpleName(); //用来做日志输出时调用
```

然后修改`onCreate`方法：

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main_list);
    try{
    	URL blogFeedUrl = new URL("http://blog.teamtreehouse.com/api/get_recent_summary/?count="+NUMBER_OF_POSTS);
    }
    catch(MalformedURLException e){
    	Log.e(TAG, "Eception Caught:", e);
    }
    
    ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, names);
    setListAdapter(adapter);
}
```

---

##2 从网上获取数据的错误方法

要让应用能够上网，首先，需要在AndroidManifest.xml中添加权限

```xml
<uses-sdk
    android:minSdkVersion="9"
    android:targetSdkVersion="21" />
<uses-permission android:name="android.permission.INTERNET"/>
```

然后在`onCreate`方法中添加如下方法：

```java
try{
	URL blogFeedUrl = new URL("http://blog.teamtreehouse.com/api/get_recent_summary/?count="+NUMBER_OF_POSTS);
	HttpURLConnection connection = (HttpsURLConnection) blogFeedUrl.openConnection();
	connection.connect();
	
	int responseCode = connection.getResponseCode();
	Log.i(TAG, "Response Code:"+responseCode);
	
} catch(MalformedURLException e){
	Log.e(TAG, "Eception Caught:", e);
} catch (IOException e) {
	Log.e(TAG, "Eception Caught:", e);
} catch (Exception e){
	Log.e(TAG, "Eception Caught:", e);
}
```

上面的方法错在哪呢？错在将网络访问放在主线程内进行。

---

##3. 异步请求

`android.os.AsyncTask`用来执行异步请求。要实现自己的异步请求，需要做下面的工作。

###3.1 继承`android.os.AsyncTask`，实现`doInBackground`方法

```java
private class GetBlogPostTasks extends AsyncTask<Object, Object, Object>{

	@Override
	protected Object doInBackground(Object... params) {
		return null
	}
	
}
```

`AsyncTask<Object, Object, Object>`的三个参数分别是：

1. 传递给`doInBackground`的参数类型
2. 后台执行的进度单位的类型
3. 返回值得类型

由此，我们进行改造，因为我们不用传递给`doInBackground`参数，所以不用对第一个参数进行修改。并且，我们也不用关心进度情况，可以将第二个参数类型修改为`Void`，最后，我们希望返回状态码，因此第三个类型，修改为`String`。

注意，`doInBackground`的返回值也要修改。

一般来说，异步的类直接放到要使用它的类里面，因此，我们在`MainListActivity`中添加的代码如下：

```java
private class GetBlogPostTasks extends AsyncTask<Object, Void, String>{

	@Override
	protected String doInBackground(Object... params) {
		int responseCode = 1;
        try{
        	URL blogFeedUrl = new URL("http://blog.teamtreehouse.com/api/get_recent_summary/?count="+NUMBER_OF_POSTS);
        	HttpURLConnection connection = (HttpURLConnection) blogFeedUrl.openConnection();
        	connection.connect();
        	
        	responseCode = connection.getResponseCode();
        	Log.i(TAG, "Response Code:"+responseCode);
        	
        } catch(MalformedURLException e){
        	Log.e(TAG, "Eception Caught:", e);
        } catch (IOException e) {
        	Log.e(TAG, "Eception Caught:", e);
		} catch (Exception e){
			Log.e(TAG, "Eception Caught:", e);
		}
        
        return "Response Status"+responseCode;
	}
	
}
```

###3.2 调用异步类

调用异步类的方法比较容易，在`MainListActivity`的`onCreate`方法中添加：

```java
GetBlogPostTasks getBlogPostTask = new GetBlogPostTasks();
getBlogPostTask.execute();
```

---

##4 处理无法联网的情况

在从网上获取数据时，我们还有一件事情要做，就是处理网络不可用的时候的情况。

要检查网络状况，也需要对应的权限，在AndroidManifest文件中添加对应的权限：

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```

然后在`MainListActivity`中添加方法用来检查网络情况：

```java
public boolean isNetworkAvailable(){
	ConnectivityManager manager = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
	NetworkInfo networkinfo = manager.getActiveNetworkInfo();
	
	boolean isAvailable = false;
	if (networkinfo != null && networkinfo.isConnected()){
		isAvailable = true;
	}
	return isAvailable;
}
```

- `getSystemService`获取的是`Object`类型，需要转换一下
- `Context`定义了一些系统自带Service的常量
- `ConnectivityManager`类的`getActiviteNetworkInfo`方法返回`NetworkInfo`类
- `NetworkInfo`的`isConnected`方法用来检查是否已经联网


最后，更改`onCreate`方法，在网络访问前先检查网络状况，如果网络状况不好提醒用户：

```java
if (isNetworkAvailable()) {
	GetBlogPostTasks getBlogPostTask = new GetBlogPostTasks();
	getBlogPostTask.execute();
}else{
	Toast.makeText(this, "网络连接不可用", Toast.LENGTH_LONG).show();
}
```

---

##5 从HTTP请求中获得JSON数据

获取JSON数据的网址是http://blog.teamtreehouse.com/api/get_recent_summary/?count=20 可以先在浏览器中打开看看数据的格式。

在前一节中，我们检查了网络情况，并打印了返回值。在本节中，我们将从结果中打印出返回的结果。

```java
try{
	URL blogFeedUrl = new URL("http://blog.teamtreehouse.com/api/get_recent_summary/?count="+NUMBER_OF_POSTS);
	HttpURLConnection connection = (HttpURLConnection) blogFeedUrl.openConnection();
	connection.connect();
	
	responseCode = connection.getResponseCode();
	
	if (responseCode == HttpURLConnection.HTTP_OK){
		InputStream inputStream = connection.getInputStream();
		Reader reader = new InputStreamReader(inputStream);
		int contentLength = connection.getContentLength();
		char[] charArray = new char[contentLength];
		reader.read(charArray);
		String resonseData = new String(charArray);
		Log.v(TAG, resonseData);
	} else {
    	Log.i(TAG, "Unsuccessful HTTP Response Code: "+responseCode);
	}
} catch(MalformedURLException e){
	Log.e(TAG, "Eception Caught:", e);
} catch (IOException e) {
	Log.e(TAG, "Eception Caught:", e);
} catch (Exception e){
	Log.e(TAG, "Eception Caught:", e);
}
```

我们主要看中间`if`判断这一部分。

- `responseCode == HttpURLConnection.HTTP_OK`用来判断数据是否成功获取
- 请求返回结果从`InputStream inputStream = connection.getInputStream();`中获取，然后使用`Reader reader = new InputStreamReader(inputStream);`来读取。
- `Reader`读取的是`char[]`类型，因此需要声明一个`char[]`数组，数组长度是返回内容的长度，通过`connection.getContentLength()`获取
- `char[]`转换为`String`，并调用`Log.v`输出，这里的`v`是指verbose

---

##6 从JSON格式中解析数据

Android提供了内建对象用来做JSON解析：

- `JSONObject`
- `JSONArray`：`JSONObject`数组

###6.1 声明JSONObject

```java
JSONObject jsonResponse = new JSONObject(responseData);
```

###6.2 获取JSONObject中的数据

`JSONObject`提供了很多获取数据的方法，比如`getString`, `getInt`, `getJSONArray`等。

**注意**，`getJSONArray`返回`JSONArray`对象。

```java
String status = jsonResponse.getString("status");
JSONArray jsonPosts = jsonResponse.getJSONArray("posts");
```

###6.3 使用JSONArray

```java
for (int i = 0; i < jsonPosts.length(); i++){
	JSONObject jsonPost = jsonPosts.getJSONObject(i);
	String title = jsonPost.getString("title");
	Log.i(TAG, "Post " + i + ": " + title);
}
```

整个方法如下：

```java
protected String doInBackground(Object... params) {
	int responseCode = 1;
    try{
    	URL blogFeedUrl = new URL("http://blog.teamtreehouse.com/api/get_recent_summary/?count="+NUMBER_OF_POSTS);
    	HttpURLConnection connection = (HttpURLConnection) blogFeedUrl.openConnection();
    	connection.connect();
    	
    	responseCode = connection.getResponseCode();
    	
    	if (responseCode == HttpURLConnection.HTTP_OK){
    		InputStream inputStream = connection.getInputStream();
    		Reader reader = new InputStreamReader(inputStream);
    		int contentLength = connection.getContentLength();
    		char[] charArray = new char[contentLength];
    		reader.read(charArray);
    		String responseData = new String(charArray);
    		
    		JSONObject jsonResponse = new JSONObject(responseData);
    		String status = jsonResponse.getString("status");
    		Log.v(TAG, status);
    		
    		JSONArray jsonPosts = jsonResponse.getJSONArray("posts");
    		
    		for (int i = 0; i < jsonPosts.length(); i++){
    			JSONObject jsonPost = jsonPosts.getJSONObject(i);
    			String title = jsonPost.getString("title");
    			Log.i(TAG, "Post " + i + ": " + title);
    		}
    	} else {
        	Log.i(TAG, "Unsuccessful HTTP Response Code: "+responseCode);
    	}
    } catch(MalformedURLException e){
    	Log.e(TAG, "Eception Caught:", e);
    } catch (IOException e) {
    	Log.e(TAG, "Eception Caught:", e);
	} catch (Exception e){
		Log.e(TAG, "Eception Caught:", e);
	}
    
    return "Response Status"+responseCode;
}
```
