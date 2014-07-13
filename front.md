# 途风网移动站点前端规范 #

|  作者  | 版本号 | 描述 |    日期   |
|:------:|:------:|------|-----------|
| robert |   0.1  | 初稿 | 2014-07-10|

>适用范围：主要针对老版M站中文站出现的问题整理，同时适用于M站英文站，及部分适用于WebApp的开发。

----------------
## Html&Css规范  ##

#### 1.Meta信息规范 ####

```html
<!--版本信息-->
<meta content="0.1" name=version"/>
<!--viewport设定-->
<meta name="viewport" content="initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no, width=device-width" />
<!--IOS设备webapp的全屏样式设置-->
<meta content="yes" name="apple-mobile-web-app-capable"/>
<meta content="yes" name="apple-touch-fullscreen"/>
<meta content="black" name="apple-mobile-web-app-status-bar-style">

```

#### 2、为移动站设置桌面Icon ####

```html
<!--
建议模式：
1、PNG尺寸114x114。
2、不使用系统自动对桌面ICON渲染圆角及高光时，rel值用apple-touch-icon-precomposed替代apple-touch-icon。
-->
<link rel="apple-touch-icon"  href="touch-icon-iphone.png" />  

```



#### 3.样式表兼容规范 ####

设备兼容W3C浏览器，样式表CSS3属性声明-moz、-webkit及无前缀标准模式，如：

```css
-webkit-box-sizing:border-box;
-moz-box-sizing:border-box;
box-sizing:border-box;

```
#### 4.初始化样式控制 ####

```css
html { 
    /*兼容弹性布局，单倍单位10px*/
    font-size: 62.5%;
    /*font-size: 10px;*/
    width: 100%;
    height: 100%;
}

body {
    font-size: 12px;
    padding: 0;
    margin: 0;
    /*禁止用户选择和复制*/
    /*
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    */
}

form, p, h1, h2, h3, h4, ul, dl, ol  {
    font-size: 100%;
    padding: 0;
    margin: 0;
}

/*格式化手机默认的控件样式渲染*/
a, input, textarea, select {
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0); 
    -webkit-appearance: none;
}
input {
    vertical-align: middle;
}

```

#### 5.弹性布局单位使用rem ####

```css
html{
    font-size:62.5%;
}
.font12{
    font-size:1.2rem;
}
.box100{
    width:10rem;   
}

```

#### 6.使用流体布局增强盒模型性能 ####


|       方法       |                    参数             |                 说明                  |
|------------------|-------------------------------------|---------------------------------------|
|box-orient        | horizontal,vertical,inline-axis,block-axis,inherit   | 子元素排列 vertical or horizontal     |
|box-flex          | <number>                            | 兄弟元素之间比例，仅作一个系数        |
|box-align         | start,end,center,baseline,stretch   | box 排列                              |
|box-direction     | normal,reverse                      | box 方向                              |
|box-flex-group    | <integer>                           | 以组为单位的流体系数                  |
|box-lines         | single,multiple                     | 列超出了父框中的空间，是否要换行显示  |
|box-ordinal-group | <integer>                           | 以组为单位的子元素排列方向            |
|box-pack          | start,center,end,justify            | 元素排列方式                          |

左右布局列表示例：
```html
<ul id="list">
    <li>
        <div class="pic">左图片位</div>
        <div class="desc">右描述</div>
    </li>
    ......
<ul>

<style>
#list > li{
    height:100px;
    display:-webkit-box;
    display:-moz-box;
    display:box;
    -webkit-box-orient:horizontal;
    -moz-box-orient:horizontal;
    box-orient:horizontal;
    -webkit-box-align:start; 
    -moz-box-align:start;
    box-align:start;
}
#list > li .pic{
    width:100px;
}
#list > li .desc{
    -webkit-box-flex:1;
    -moz-box-flex:1;
    box-flex:1;
}
</style>
```

#### 4、动画处理 ####

* 使用流体布局或position脱离的元素作为载体。
* 优先使用CSS3属性transform控制运动行为。
* 使用CSS3属性transition及animation及keyframes控制动画时间轴，禁止使用setTimeout、setInterval、requestAnimationFrame等。
* JQuery下slideDown、SlideUp、show、hide、fadeIn、fadeOut、animate等均非CSS3动画实现，需谨慎使用。
* 为前端添加CSS3动画函数接口的示例。

`JQuery`
```html
<div class="box"></div>

<script>
//将BOX 4秒内从无逐渐展开
$("#box").transition({
    opacity: ['0.0', '1.0'],
    width: ["0px", "100px"],
    height: ["0px", "100px"]
}, 4000);
    
//$.transition实现
$.fn.extend({
    transition: function (params, time, effect, callback) {
        callback = (typeof(effect) === "function") ? effect : callback;
        effect = (typeof(effect) === "string") ? effect : undefined;
        var key, style = "all " + time + "ms " + ( effect || "linear");
        for (key in params) {
            $(this).css(key, params[key][0]);
        }
        $(this).css({
            "-webkit-transition":style,
            "-moz-transition":style,
            "transition":style
        })
        $(this).focus();
        for (key in params) {
            $(this).css(key, params[key][1])
        }
        setTimeout(function () {
            $(this).css({
                "-webkit-transition":"none",
                "-moz-transition":"none",
                "transition":"none"
            })
            if (callback) {
                callback();
            }
        }, time + 20);
    }
});
</script>

```

`Javascript`

```html
<div id="box"></div>

<script>
//将BOX 4秒内从无逐渐展开
transition.play(document.getElementById("box"), {
    opacity: ['0', '1.0', 'ease'],
    width: ["0px", "100px"],
    height: ["0px", "100px"]
}, 4000);

//transition.play实现
var transition = (function () {
    "use strict";
    function _string2hump(str) {
        return str.replace(/\-(\w)/g, function (all, letter) {
            return letter.toUpperCase();
        });
    }
    
    function _transition(dom, params, time, effect, callback) {
        callback = (typeof(effect) === "function") ? effect : callback;
        effect = (typeof(effect) === "string") ? effect : undefined;
        var style, key;
        for (key in params) {
            dom.style[_string2hump(key)] = params[key][0];
        }
        effect = ( effect ) ? effect : "linear";
        style = "all " + time + "ms " + effect;
        dom.style.WebkitTransition = style;
        dom.style.mozTransition = style;
        dom.style.transition = style;
        dom.focus();
        for (key in params) {
            dom.style[_string2hump(key)] = params[key][1];
        }
        setTimeout(function () {
            dom.style.WebkitTransition = "none";
            dom.style.msTransition = "none";
            dom.style.transition = "none";
            if (callback) callback();
        }, time + 20);
    }

    return {
        play: function (dom, params, time, effect, callback) {
            _transition(dom, params, time, effect, callback);
        }
    }
})();
</script>
```

## Javascript 规范##

#### 1.禁止使用ontouchstart、ontouchmove、ontouchend事件替代onclick事件####
错误的示例：
```js
$(".button").bind("touchstart",function(){
    location = "page.html"
})

```

#### 2.使用JavaScript严谨模式(Strict Mode)，只能在私有作用域中申明，禁止在window顶级对象下做申明 ####

```js
function method(){
     "use strict"
     //your codes
}

(function(){
    "use strict"
     //your codes 
}());


```
#### 3、动态列表事件使用委托###
JQuery 禁止使用live推荐使用delegate，合理使用on 
```html
<ul id="list">
    <li>序列1</li>
    <li>序列2</li>
    <li>序列3</li>
    .....
</ul>

<script>
/* JQuery禁止的方式 */
$("#list li").live("click",function(){
   //function code here.
});
$(document).on("click","#list li",function(){
  //function code here.
});

/* JQ推荐的方式 */
$('#list').delegate('li', 'click', function() {
   //function code here.
});
$('#list').on('click', 'li', function() {
    //function code here.
});

/* JS方式 */
document.getElementById("list").addEventListener("click",function(e){
    if (  e.target.tagName === "LI") {
       //function code here. 
    }
},false);
</script>
```

#### 4.AJAX使用 ####
 

* 优先使用异步模式，尽量避免同步模式，防止阻塞。
* 对重复使用的数据读取后进行Cache。
* 避免使用间隔小而多次的AJAX请求，对大量请求不能避免的情况使用队列，示例：

```js
//初始化队列
$("body").queue([]);
//添加一条任务到队列
$("body").queue("ajax", function () {
    $.ajax({
        timeout:1000,
        type:"POST",
        url: "test.json",
        data: {},
        success: function (data) {
            //your callback
        },
        complete:function(){
            //继续执行队列
            if ($("body").queue().length > 0) {
                $("body").dequeue("ajax");
            }
        }
    });
})

//开始队列
if ($("body").queue().length > 0) {
    $("body").dequeue("ajax");
}
```






 
 
#### 5.使用压缩格式的JS文件及样式表文件####

## 图片规范##
##### 1.图片编码规范 #####

* 图片格式使用JPG、GIF、PNG，透明图片使用PNG24，禁止使用PNG8及Gif格式透明。
* 图片大小按双倍尺寸原图并在CSS中设定bankground-size:50%缩小显示。
* 单色或渐变色图片使用iconfont矢量图, 在线工具 http://iconfont.cn/。
* 复杂图片使用CSSS prites。
* 超小图片使用base64流定义，禁止使用PNG8。

```html
<style>
background-image:url(data:image/gif;base64,R0lGODlhMAAfAOYAAP88A.......);
</style>
<img width="40" height="30" src="data:image/jpg;base64,/9j/4QMZRXhpZgAASUkqAAgAAAAL...." />
```



