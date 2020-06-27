title:JS补遗-jQuery篇
date:2015-05-13 15:08
category:Web
tags:jQuery
author:刘理想

##文件相关

###[jQuery获取当前input file里的值](http://stackoverflow.com/questions/740114/how-to-use-jquery-to-get-the-current-value-of-a-file-input-field)：

```
$("#fileinput").val();
```

###[jQuery获取input file里的多个值](http://stackoverflow.com/questions/10703102/jquery-get-all-filenames-inside-input-file)

```
var files = $('#basicUploadFile').prop("files"); //files是FileList对象

var names = $.map(files, function(val) { return val.name; }); //names是字符串数组
```

###[jQuery文件上传](http://stackoverflow.com/questions/5392344/sending-multipart-formdata-with-jquery-ajax)

###[文件跨域提交](http://stackoverflow.com/questions/20035101/no-access-control-allow-origin-header-is-present-on-the-requested-resource)



