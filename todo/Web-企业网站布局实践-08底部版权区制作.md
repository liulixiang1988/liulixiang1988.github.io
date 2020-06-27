Title: 企业网站布局实践-08 底部版权区制作
Date: 2015-04-04 21:31
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 结构分析

![底部版权区](images/qy-web-12.png)

##2. 文档结构

```
<div class="copyright">
    <div class="copyright_content">
        <ul>
            <li>关于
                <ul>
                    <li>品牌故事</li>
                    <li>联系我们</li>
                    <li>加入我们</li>
                    <li>版权声明</li>
                </ul>
            </li>
            <li>课程
                <ul>
                    <li>PHP开发</li>
                    <li>前端开发</li>
                    <li>JAVA开发</li>
                    <li>Android开发</li>
                </ul>
            </li>
            <li>关注
                <ul>
                    <li>新浪微博</li>
                    <li>新浪微博</li>
                    <li>企业微信</li>
                    <li>QQ空间</li>
                </ul>
            </li>
            <li>留言
                <ul>
                    <li>意见反馈</li>
                    <li>问题留言</li>
                    <li>媒体联络</li>
                    <li>在线客服</li>
                </ul>
            </li>
            <li><img src="images/weixin.png" alt="微信" width="30px" height="27px">微信
                <ul>
                    <li><img src="images/qrcode.jpg" alt="扫描关注慕课网官方微信" width="102" height="102" /></li>
                </ul>
            </li>
        </ul>
    </div><!-- copyright_content结束 -->
</div><!-- copyright结束 -->
```

##3. LESS实现

```
.copyright{
    clear: both;
    width:100%;
    height: 200px;
    background-color:#E8E8E8;
    margin-top: 10px;
}

.copyright_content{
    width: 1000px;
    margin: 0 auto;
    padding-top: 20px;
    height: 180px;

    li{
        list-style-type: none;
        float: left;
        width: 200px;
        text-align: center;
        background: url(../images/line.png) no-repeat right center;
        font-size: 18px;
        font-family: "微软雅黑";
        color: #999;
        line-height: 30px;

        ul li{
            font-size: 14px;
        }
    }
}
```