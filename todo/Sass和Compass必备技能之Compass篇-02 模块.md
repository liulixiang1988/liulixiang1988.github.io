Title: Sass和Compass必备技能之Compass篇-02 模块简介
Date: 2015-04-20 21:43
Category: Web
Tags: CSS, SASS
Author: 刘理想

##1. Layout简介

http://compass-style.org/reference/compass/layout/

```
@import "compass/layout"
@import "compass/layout/grid-background"
@import "compass/layout/sticky-footer"
@import "compass/layout/stretching"
```

##2. CSS3模块

使用率最高的模块，提高跨浏览器的CSS3效果
http://compass-style.org/reference/compass/css3/

```
@import "compass/css3";
.webdemo-sec{
    @include box-shadow(1px 1px 3px 2px #cfcecf);
}
```

##3. Browser模块
有时，compass希望只支持特定的browser，使用browser即可。
```
@import "compass/support";
$supported-browsers:chrome;
//最低版本
$browser-minimum-versions: ("ie": "8");
```

##4. Typography模块
http://compass-style.org/reference/compass/typography/

```
@import "compass/typography"
```



