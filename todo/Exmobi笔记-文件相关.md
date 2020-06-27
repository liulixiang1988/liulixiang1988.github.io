title:Exmobi笔记-文件相关
date:2015-05-13 15:39
category:移动开发
tags:Exmobi
author:刘理想

##文件下载

文件下载与打开

```
$("a").click(function(){
	if($(this).attr('href').match(/doc$|docx$|pdf$/)){
		alert("matched");
	}
	ClientUtil.download($(this).attr('href'),"res:"+$(this).text(), null,null,false);
  	NativeUtil.open("res:"+$(this).text());
});
```
