Title: Python读取网络图片
Date: 2015-11-07 09:39
Category: Python
Tags: Python
Author: 刘理想
Summary: Python读取网络图片

##1. 使用urllib读取

```
try:
    from cStringIO import StringIO #python 2
except ImportError:
    from io import StringIO #python 3
import urllib
file = StringIO(urllib.urlopen(URL).read())
img = Image.open(file)
```

##2. 使用requests读取

```
from PIL import Image
import requests
try:
    from StringIO import StringIO #python 2
except ImportError:
    from io import StringIO #python 3

response = requests.get(url)
img = Image.open(StringIO(response.content))
```

关于`StringIO`和`cStringIO`，这些只是在python2中存在，在python3中导入`io`，使用`io.StringIO`或者`io.BytesIO`来使用文本或者数据

