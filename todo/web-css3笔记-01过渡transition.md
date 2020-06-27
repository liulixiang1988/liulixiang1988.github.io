Title: CSS3笔记01-过渡transition
Date: 2015-06-13 20:31
Category: Web
Tags: css3,compass,sass
Author: 刘理想

使用compass:

```
@import "compass/css3/transition";
div.foo{
    width: 50px;
    padding: 5px 10px;
    background: #9f3;
    @include transition-property(background, width);
    @include transition-duration(0.5s);
    @include transition-timing-function(ease-in);
}

div.foo:hover{
    background: #690;
    width: 100px;
}
```

对应的css3:
```
div.foo {
  width: 50px;
  padding: 5px 10px;
  background: #9f3;
  -moz-transition-property: background, width;
  -o-transition-property: background, width;
  -webkit-transition-property: background, width;
  transition-property: background, width;
  -moz-transition-duration: 0.5s;
  -o-transition-duration: 0.5s;
  -webkit-transition-duration: 0.5s;
  transition-duration: 0.5s;
  -moz-transition-timing-function: ease-in;
  -o-transition-timing-function: ease-in;
  -webkit-transition-timing-function: ease-in;
  transition-timing-function: ease-in;
}

/* line 13, ../sass/transition.scss */
div.foo:hover {
  background: #690;
  width: 100px;
}
```

- `transition-property`:代表过渡的属性，可以使`background`,`opactiy`, `font-size`和`width`等,也可以使用`all`代表全部属性；
- `transition-duration`：代表过渡的持续时间，注意结尾要有单位，比如`3s`等；
- `transition-timing-function`: 过渡的时间函数，有六种`[ ease | linear | ease-in | ease-out | ease-in-out | cubic-bezier(x1, y1, x2, y2)]`平滑、线性、由慢到快、由快到慢、由慢到快再到慢，以及三次贝赛尔曲线类型；
- `transition-delay`: 延迟过渡，注意要有单位。

也可以使用`transition`一次写完：
compass:
`single-transition($property, $duration, $function, $delay)`
scss:
`transition:background 0.3s ease 0.3s`


