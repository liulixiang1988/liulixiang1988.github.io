Title: 使用Android开发一个博客阅读器-04 在List中展现数据
Date: 2014-09-27 11:30
Category: Android
Tags: Android学习笔记, Android, Androi博客阅读
Author: 刘理想
Summary: Android实战开发，使用Android开发一个博客阅读器，第四篇，在List中展现数据

##1 完成我们的异步任务AsyncTask

在[前一篇文章](http://liulixiang1988.github.io/shi-yong-androidkai-fa-yi-ge-bo-ke-yue-du-qi-03-cong-wang-luo-zhong-huo-qu-shu-ju.html)中，我们的JSON数据并不是从主线程中直接获取，而是使用了`AsyncTask`类。

`AsyncTask`类执行异步操作有4步：

1. `onPreExecute()`，在任务执行之前调用UI线程。主要用来设置任务，比如在用户界面中显示一个进度条。
2. `doInBackground(Params...)`, 在执行完`onPreExecute()`后立即调用后台任务线程。这个用来执行耗时的后台操作。异步任务的参数在这个步骤中传递。并且结果必须在此不返回，并且传递给最后一步（即第四部）。这一步也可以使用`publishProgress(Progress...)`，用来发布进度。这些值在`onProgressUpdate(Progress...)`步骤中被发布到UI线程。
3. `onProgressUpdate(Progress...)`在调用`publishPorgress(Porgress...)`后调用UI线程。用来展现后台计算的执行进度。比如，它可以用来展现一个进度条的动画或者在文本中显示日志。
4. `onPostExecute(Result)`，在后台计算完成后调用UI线程。在这步中，后台计算的结果作为参数传递进来。

根据上面的条件，我们对之前的异步任务类进行改造：
- 更改返回类型为`JSONObject`
- 添加`onPostExecute`方法，参数类型还是`JSONObject`，在这里通知UI线程进行更新列表
- 在MainListActivity中增加一个`JSONObject`字段，用来保存获取的JSON数据

代码如下：

```java
public class MainListActivity extends ListActivity {
	//...
	protected JSONObject blogData;

    //...

    public void updateList(){
    	if(blogData == null){
    		// TODO: handle error
    		
    	} else {
    		try {
				Log.d(TAG, blogData.toString(2));
			} catch (JSONException e) {
				Log.e(TAG, "Exception caught", e);
			}
    	}
    }

    
    private class GetBlogPostTasks extends AsyncTask<Object, Void, JSONObject>{

		@Override
		protected JSONObject doInBackground(Object... params) {
			int responseCode = 1;
			JSONObject jsonResponse = null;
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
	        		
	        		jsonResponse = new JSONObject(responseData);
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
	        
	        return jsonResponse;
		}
		
	   @Override
		protected void onPostExecute(JSONObject result) {
			blogData = result;
			updateList();
		}
    }
}
```

---

##2 填充字符串数组，并且创建Adapter适配器

```java
JSONArray jsonPosts = blogData.getJSONArray("posts");
blogPostTitles = new String[jsonPosts.length()];
for (int i = 0; i < jsonPosts.length(); i++){
	JSONObject post = jsonPosts.getJSONObject(i);
	String title = post.getString("title");
	title = Html.fromHtml(title).toString();
	blogPostTitles[i] = title;
}
ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, blogPostTitles);
setListAdapter(adapter);
```

**注意：**因为title中可能包含一些特殊的字符，比如"New Course: Treehouse Club &#8211; JavaScript"，如果直接显示，就太不友好了，因此，需要对它进行转化，使用`Html.fromHtml(title)`进行转化，但是`fromHtml()`返回的是`Spanned`,因此需要使用`toString()`方法返回字符串。

---

##3 使用对话窗处理错误

`AlertDialog.Builder`是工厂模式。

```java
AlertDialog.Builder builder = new AlertDialog.Builder(this);
builder.setTitle(R.string.error_title);
builder.setMessage(R.string.error_message);
builder.setPositiveButton(android.R.string.ok, null);
AlertDialog dialog = builder.create();
dialog.show();
```

`setPositiveButton(android.R.string.ok, null)`设置按钮与按钮事件，我们不需要设置按钮点击事件，仅仅是消失，所以传递null即可。

---

##4 清理展示

在展示具体应用之前，我们需要修改2个东西：第一，如果从网络中加载数据时间过长，我们的应用显示的是“没有数据”；第二，我们想要在list中添加作者。对于第一个问题，我们可以通过添加指示器来实现

###4.1 添加指示器

1. 设置`ListView`对应的empty `TextView`

首先，清空`TextView`的Text

```xml
<TextView
    android:id="@android:id/empty"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_alignParentLeft="true"
    android:layout_alignParentTop="true" />
```

然后，打开MainListActivity.java，在错误对话框代码下面添加如下代码：

```java
TextView textView = (TextView) getListView().getEmptyView();
textView.setText(getString(R.string.no_data));
```

**注意**,`ListView`对应的空`TextView`(`android:id="@android:id/empty"`)，获取它不是通过`getViewById()`，而是`getListView().getEmptyView()`。

获取strings.xml中的字符串：`getString(R.string.no_data)`

2. 添加指示器

```xml
<ProgressBar
    android:id="@+id/progressBar1"
    style="?android:attr/progressBarStyleLarge"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_centerInParent="true"/>
```

说明：

- `layout_centerInParent="true"`让指示器居中。

在程序中添加`protected ProgressBar progressBar;`

在`onCreate`中初始化`progressBar`并且使其可见：

```java
progressBar = (ProgressBar) findViewById(R.id.progressBar1);   

if (isNetworkAvailable()) {
	progressBar.setVisibility(View.VISIBLE); //设置可见
	GetBlogPostTasks getBlogPostTask = new GetBlogPostTasks();
	getBlogPostTask.execute();
}else{
	Toast.makeText(this, "网络连接不可用", Toast.LENGTH_LONG).show();
}
```

在`updateList`中将`progressBar`设置为不可见：

	progressBar.setVisibility(View.INVISIBLE);

3. 重构

修改`updateList`为`handleBlogResponse`，并将`blogData==null`时的内容提取到新方法`updateDisplayForError()`中。

---

##5 给List添加作者

```java
public static final String KEY_TITLE="title";
public static final String KEY_AUTHOR="author";

public void handleBlogResponse(){
	progressBar.setVisibility(View.INVISIBLE);
	if(blogData == null){
		updateDispalyForError();
	} else {
		try {
			JSONArray jsonPosts = blogData.getJSONArray("posts");

			//ArrayList就是一个数组List， HashMap类似字典
			ArrayList<HashMap<String, String>> blogPosts = 
					new ArrayList<HashMap<String, String>>();
			for (int i = 0; i < jsonPosts.length(); i++){
				JSONObject post = jsonPosts.getJSONObject(i);

				//获取title
				String title = post.getString(KEY_TITLE);
				title = Html.fromHtml(title).toString();

				//获取作者
				String author = post.getString(KEY_AUTHOR);
				author = Html.fromHtml(author).toString();
				
				//创建字典
				HashMap<String, String> blogPost = new HashMap<String, String>();
				blogPost.put(KEY_TITLE, title);
				blogPost.put(KEY_AUTHOR, author);

				//添加字典到ArrayList
				blogPosts.add(blogPost);
			}

			//映射form字段
			String[] keys = {KEY_TITLE, KEY_AUTHOR};
			//映射to字段
			int[] ids = {android.R.id.text1, android.R.id.text2};

			//使用SimpleAdapter代替ArrayAdapter，可以添加映射
			SimpleAdapter adapter = 
					new SimpleAdapter(this, blogPosts, android.R.layout.simple_list_item_2, keys, ids);
			setListAdapter(adapter);
		} catch (JSONException e) {
			Log.e(TAG, "Exception caught", e);
		}
	}
}
```

##6 总结

本节我们学习了如何处理异步的数据；Adapter适配器的使用；对话框的使用；进度指示器的使用；SimpleAdapter的使用。