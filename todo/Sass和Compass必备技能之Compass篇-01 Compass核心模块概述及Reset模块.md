Title: Sass和Compass必备技能之Compass篇-01 Compass核心模块概述及Reset模块
Date: 2015-04-20 20:51
Category: Web
Tags: CSS, SASS
Author: 刘理想

##1. Compass核心模块

![images/compass-1.png](images/compass-1.png)

Reset(`@import "compass/reset";`)和Layout(`@import "compass/layout";`)并没有包含在compass的核心模块中，需要单独引用。

`@import "compass";`包含了CSS3/Helpers/Typography/Utilities模块：
- CSS3模块

##2. 使用normalize.css替换reset

《grunt beginer》

http://necolas.github.io/normalize.css/

可以使用compass插件：

###2.1 安装

`gem install compass-normalize`

###2.2 在config.rb中引入

`require 'compass-normalize'`
**注意：config.rb中的require 'compass/import-once/activate'是防止多次引入，如果需要多次引入，使用@import 'compass/reset!',即后面加上叹号**
如果输出格式是压缩的，在注释的最前面加一个感叹号`/*!注释*/`.

有时，你并不想全部使用normalize所有的模块，那么可以分别引入normalize的模块，normalize有以下8个模块：

![images/compass-2.png](images/compass-2.png)

引入方式：先引入`@import "normalize-version";` 然后引入要使用的模块 `@import "normalize/base";`

```
//@import "normalize";
//or
@import "normalize-version";
//base
@import "normalize/base";
/*link*/
@import "normalize/links";
```

###2.3 在sass页面中使用

reset的mixin和normalize里相对应

![images/compass-3.png](images/compass-3.png)

使用方法：
```
@import "compass/reset/utilities";

.test-reset-box-model {
    @include reset-box-model;
}
```

文档：http://compass-style.org/







