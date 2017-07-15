title: How Browsers Work
speaker: lovefishs
url: https://github.com/lovefishs
transition: move
files: /js/zoom.js
theme: moon
hightStyle: monokai_sublime


<style>
  .fl { float: left; }
  .fr { float: right; }
  .left { text-align: left; }
  .center { text-align: center; }
  .right { text-align: right; }
</style>

[slide]
# How Browsers Work
## 浏览器的渲染原理简介
<small>演讲者：小黄</small>


[slide]
## 主要内容
----
* 背景介绍 {:&.rollIn}
* 浏览器工作主流程
* DOM 解析
* CSS 解析
* 渲染
* 回流/重绘 (reflow/repaint)


[slide]
## 背景介绍

[subslide]
### 我们要讨论的浏览器
----
* Internet Explorer (*win10 Edge*)
* **Firefox**
* **Safari(部分开源)**
* **Chrome**
* Opera

<p class="center">
  <img src="/img/StatCounter-browser-ww-monthly-200807-201508.png" width="400">
  <img src="/img/tongji.baidu.com.data.browser.png" width="400">
</p>

============

### 浏览器所渲染的网页
----
* HTML
* CSS
* JavaScript

<p class="center">
  <img src="/img/simile-html.jpg" width="200">
  <img src="/img/simile-html-css.jpg" width="200">
  <img src="/img/simile-html-css-javascript.gif" width="200">
</p>

============

### 浏览器的高层结构
----
* 用户界面 - 标题栏、书签菜单等
* 浏览器引擎 - 在用户界面和呈现引擎之间传送指令
* **呈现引擎** - 负责显示请求的内容
* 网络 - 用于网络调用，比如 HTTP 请求
* 用户界面后端 - 用于绘制基本的窗口小部件，比如组合框和窗口
* JavaScript 解释器 - 用于解析和执行 JavaScript 代码
* 数据存储 - 持久层，例如 Cookie 以及 HTML5 的 `localStorage` 和 `sessionStorage`

<p class="center">
  <img src="/img/layers.png" alt="浏览器的高层结构" width="400">
</p>
[/subslide]


[slide]
## 浏览器工作主流程

[subslide]

<p class="center">
  <img src="/img/render-process.jpg" alt="浏览器的高层结构" width="600">
</p>

1. Parse 解析 {:&.rollIn}
2. Construct Rendering Tree 构造渲染树
3. Paint 调用操作系统 Native GUI 的 API 绘制

============

### Parse
----
<p class="center">
  <img src="/img/render-process.jpg" alt="浏览器的高层结构" width="600">
</p>

* HTML/SVG/XHTML 解析这三种文件会产生一个 DOM Tree
* CSS 解析 CSS 会产生 CSS 规则树
* JavaScript 通过 DOM API 和 CSSOM API 来操作 DOM Tree 和 CSS Rule Tree

============

### Construct Rendering Tree
----
<p class="center">
  <img src="/img/render-process.jpg" alt="浏览器的高层结构" width="600">
</p>

* Rendering Tree 渲染树并不等同于 DOM Tree，因为一些像 `<head>` 或 `display: none` 的东西就没必要放在渲染树中了
* CSS 的 Rule Tree 主要是为了完成匹配并把 CSS Rule 附加上 Rendering Tree 上的每个 Element(webkit:DOM 结点, Gecko: Frame)
* 计算每个 Element 的位置，这又叫 layout 和 reflow 过程

============

### Paint
----
<p class="center">
  <img src="/img/render-process.jpg" alt="浏览器的高层结构" width="600">
</p>

* 调用操作系统 Native GUI 的 API 绘制
[/subslide]


[slide]
## DOM 解析
----
解析是呈现引擎中非常重要的一个环节，[详细介绍](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#Parsing_general)

```html
<html>
  <body>
    <p>
      Hello World
    </p>
    <div>
      <img src="example.png"/>
    </div>
  </body>
</html>
```

<p class="center">
  <img src="/img/parse-dom.png" alt="DOM 解析">
</p>


[slide]
## CSS 解析
[note]
解析器都会将 CSS 文件解析成 StyleSheet 对象，且每个对象都包含 CSS 规则。CSS 规则对象则包含选择器和声明对象，以及其他与 CSS 语法对应的对象。
[/note]

[subslide]
### CSS Rule Tree
----

```css
p, div { margin-top: 3px; }
.error { color: red; }
```

<p class="center">
  <img src="/img/parse-css.png" alt="CSS 解析">
</p>

浏览器引擎会通过 DOM Tree 与 CSS Rule Tree 来构造 Rendering Tree

============

### 举个栗子: HTML DOM Tree
----

```html
<doc>
  <title>A few quotes</title>
  <para class="emph">
    Franklin said that <quote>"A penny saved is a penny earned."</quote>
  </para>
  <para>
    FDR said <quote>"We have nothing to fear but <span class="emph">fear itself.</span>"</quote>
  </para>
</doc>
```
<p class="center">
  <img src="/img/DOM-Tree-Example.jpg" alt="DOM Tree 例子">
</p>

============

### 举个栗子: CSS Rule Tree
----

```css
/* rule 1 */ doc { display: block; text-indent: 1em; }
/* rule 2 */ title { display: block; font-size: 3em; }
/* rule 3 */ para { display: block; }
/* rule 4 */ [class="emph"] { font-style: italic; }
```
<p class="center">
  <img src="/img/CSS-Rule-Tree-Example.jpg" alt="CSS Rule Tree 例子">
</p>

============

### 举个栗子: Style Context Tree
----

通过这 DOM Tree 和 CSS Rule Tree 两个树，我们可以得到一个叫 Style Context Tree，也就是下面这样（把 CSS Rule 结点 Attach 到 DOM Tree 上）

<p class="center">
  <img src="/img/CSS-Content-Tree-Example.jpg" alt="CSS Content Tree 例子">
</p>
[/subslide]


[slide]
## 渲染
----
渲染的主流程

1. 计算 CSS 样式
2. 构建 Render Tree
3. Layout – 定位坐标和大小，是否换行，各种position, overflow, z-index属性
4. 绘制

<p class="center">
  <img src="/img/Render-Process-Skipping.jpg" alt="Render Process Skipping" width="600">
</p>

[note]
**注意: 上图流程中有很多连接线，这表示了Javascript动态修改了DOM属性或是CSS属会导致重新Layout，有些改变不会，就是那些指到天上的箭头，比如，修改后的CSS rule没有被匹配到，等。**
[/note]


[slide]
## 回流/重绘 (reflow/repaint)

[subslide]
### 定义
----

* Repaint - 屏幕的一部分要重画，比如某个CSS的背景色变了。但是元素的几何尺寸没有变 {:&.rollIn}
* Reflow  - 意味着元件的几何尺寸变了，我们需要重新验证并计算 Render Tree(是 Render Tree 的一部分或全部发生了变化，这就是 Reflow，或是 Layout)

============

### 视频
----

下面是一个打开 Wikipedia 时的 Layout/reflow 的视频（注：HTML在初始化的时候也会做一次reflow，叫 intial reflow），你可以感受一下:

<p class="center">
  <object width="480" height="400" classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0" align="middle">
    <param name="src" value="http://player.youku.com/player.php/sid/XMzI5MDg0OTA0/v.swf" />
    <param name="allowfullscreen" value="true" />
    <param name="quality" value="high" />
    <param name="allowscriptaccess" value="always" />
    <embed width="480" height="400" type="application/x-shockwave-flash" src="http://player.youku.com/player.php/sid/XMzI5MDg0OTA0/v.swf" allowfullscreen="true" quality="high" allowscriptaccess="always" align="middle" />
  </object>
</p>

Reflow的成本比Repaint的成本高得多的多。DOM Tree里的每个结点都会有reflow方法，一个结点的reflow很有可能导致子结点，甚至父点以及同级结点的reflow。

在一些高性能的电脑上也许还没什么，但是如果reflow发生在手机上，那么这个过程是非常痛苦和耗电的，比如说 css3 的 animate

============

### 分析
----
由定义可推导以下操作可能是成本很高的

* 当你增加、删除、修改DOM结点时，会导致Reflow或Repaint
* 当你移动DOM的位置，或是搞个动画的时候。
* 当你修改CSS样式的时候。
* 当你Resize窗口的时候（移动端没有这个问题），或是滚动的时候。
* 当你修改网页的默认字体时。

注: `display:none` 会触发 reflow，而 `visibility:hidden` 只会触发repaint，因为没有发现位置变化。

```javascript
var bstyle = document.body.style; // cache

bstyle.padding = "20px"; // reflow, repaint
bstyle.border = "10px solid red"; //  再一次的 reflow 和 repaint

bstyle.color = "blue"; // repaint
bstyle.backgroundColor = "#fad"; // repaint

bstyle.fontSize = "2em"; // reflow, repaint

// new DOM element - reflow, repaint
document.body.appendChild(document.createTextNode('dude!'));
```

[note]
当然，我们的浏览器是聪明的，它不会像上面那样，你每改一次样式，它就reflow或repaint一次。**一般来说，浏览器会把这样的操作积攒一批，然后做一次reflow，这又叫异步reflow或增量异步reflow**。但是有些情况浏览器是不会这么做的，比如：resize窗口，改变了页面默认的字体，等。对于这些操作，浏览器会马上进行reflow。

但是有些时候，我们的脚本会阻止浏览器这么干，比如：如果我们请求下面的一些DOM值：

1. offsetTop, offsetLeft, offsetWidth, offsetHeight
2. scrollTop/Left/Width/Height
3. clientTop/Left/Width/Height
4. IE中的 getComputedStyle(), 或 currentStyle

因为，如果我们的程序需要这些值，那么浏览器需要返回最新的值，而这样一样会 flush 出去一些样式的改变，从而造成频繁的 reflow/repaint。
[/note]

============

### 优化
----

最佳实践 Best Practices

* 不要一条一条地修改DOM的样式。与其这样，还不如预先定义好css的class，然后修改DOM的className {:&.rollIn}
  ```javascript
  // bad
  var left = 10,
  top = 10;
  el.style.left = left + "px";
  el.style.top  = top  + "px";

  // Good
  el.className += " theclassname";

  // Good
  el.style.cssText += "; left: " + left + "px; top: " + top + "px;";
  ```
* 把DOM离线后修改
  * 使用documentFragment 对象在内存里操作DOM
  * 先把DOM给display:none(有一次reflow)，然后你想怎么改就怎么改。比如修改100次，然后再把他显示出来。
  * clone一个DOM结点到内存里，然后想怎么改就怎么改，改完后，和在线的那个的交换一下。
* 不要把DOM结点的属性值放在一个循环里当成循环里的变量(不然这会导致大量地读写这个结点的属性)
* 尽可能的修改层级比较低的DOM
* 为动画的HTML元件使用 `fixed` 或 `absoult` 的 `position`(修改他们的 CSS 是不会 reflow 的)
* 不要使用 table 布局
[/subslide]


[slide]
## 参考资料

* [浏览器的工作原理：新式网络浏览器幕后揭秘](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/)
* [浏览器的渲染原理简介](http://coolshell.cn/articles/9666.html)
