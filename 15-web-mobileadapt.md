title: 移动端Web页面适配

author:
  name: LI YANG
  url: http://mooc1.chaoxing.com/course/87155873.html
output: 14-web-viewport.html

--
# 移动端Web页面适配
## Moile Adaption

--
### viewport视口
viewport是严格的等于浏览器的窗口。
> `visual viewport` 可见视口 屏幕宽度  
> `layout viewport` 布局视口 DOM宽度  
> `ideal viewport` 理想适口：使布局视口就是可见视口  
>
> 设备宽度(visual viewport)与DOM宽度(layout viewport), scale的关系为：  
> `（visual viewport）= （layout viewport）* scale`
> 
> 获取 `屏幕宽度(visual viewport)` 的尺寸：`window. innerWidth/Height`  
> 获取 `DOM宽度(layout viewport)` 的尺寸：`document. documentElement. clientWidth/Height`

设置理想视口：把默认的 ` layout viewport ` 的宽度设为移动设备的屏幕宽度，得到理想视口 ` ideal viewport`

```html
<meta name="viewport" content="width=device-width,initial-scale=1">
```

--
### 物理像素(physical pixel)
物理像素又被称为设备像素，他是显示设备中一个最微小的物理部件。每个像素可以根据操作系统设置自己的颜色和亮度。所谓的一倍屏、二倍屏(Retina)、三倍屏，指的是设备以多少物理像素来显示一个CSS像素，也就是说，多倍屏以更多更精细的物理像素点来显示一个CSS像素点，在普通屏幕下1个CSS像素对应1个物理像素，而在Retina屏幕下，1个CSS像素对应的却是4个物理像素。关于这个概念，看一张"田"字示意图就会清晰了。

--
### CSS像素
CSS像素是一个抽像的单位，主要使用在浏览器上，用来精确度量Web页面上的内容。一般情况之下，CSS像素称为与设备无关的像素(device-independent pixel)，简称DIPs。CSS像素顾名思义就是我们写CSS时所用的像素。

--
### 设备像素比dpr(device pixel ratio)
设备像素比简称为dpr，其定义了物理像素和设备独立像素的对应关系。它的值可以按下面的公式计算得到：
```
设备像素比 ＝ 物理像素 / 设备独立像素
```
在Retina屏的iphone上，devicePixelRatio的值为2，也就是说1个css像素相当于2个物理像素。通常所说的二倍屏(retina)的dpr是2, 三倍屏是3。

> viewport中的scale和dpr是倒数关系。获取当前设备的dpr：
> 
> - JavaScript: window.devicePixelRatio  
> - CSS: -webkit-device-pixel-ratio, -webkit-min-device-pixel-ratio, -webkit-max-device-pixel-ratio。不同dpr的设备，可根据此做一些样式适配(这里只针对webkit内核的浏览器和webview)


--
### 设备独立像素dip或dp
dip或dp,（device independent pixels，设备独立像素）与屏幕密度有关。dip可以用来辅助区分视网膜设备还是非视网膜设备。

> 安卓设备根据屏幕像素密度可分为ldpi、mdpi、hdpi、xhdpi等不同的等级。  
> 
> - 规定以160dpi为基准，1dp=1px。
> - 如果密度是320dpi，则1dp=2px，以此类推。IOS设备：从IPhone4开始为Retina屏
> - CSS像素与设备独立像素之间的关系依赖于当前的缩放等级。

--
### 屏幕像素密度PPI(pixel per inch)
屏幕像素密度是指一个设备表面上存在的像素数量，它通常以每英寸有多少像素来计算(PPI)。屏幕像素密度与屏幕尺寸和屏幕分辨率有关，在单一变化条件下，屏幕尺寸越小、分辨率越高，像素密度越大，反之越小。

```
屏幕密度 = 对角线分辨率/屏幕尺寸
```

--
### 概念关系图
```
屏幕尺寸、屏幕分辨率-->对角线分辨率/屏幕尺寸-->屏幕像素密度PPI  
                                                |
              设备像素比dpr = 物理像素 / 设备独立像素dip(dp)
                                                |
                                       viewport: scale
                                                |
                                            CSS像素px
```

--
### 前端实现方式

**1 . 设置理想视口**
设置理想视口，使得DOM宽度(layout viewport)与屏幕宽度(visual viewport)一样大,DOM文档主宽度即为屏幕宽度。1个CSS像素(1px)由多少设备像素显示由具体设备而不同。
```html
<meta name="viewport" content="width=width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
```

**2 . 动态设置视口缩放为1/dpr**
对于安卓，所有设备缩放设为1,对于IOS，根据dpr不同，设置其缩放为dpr倒数。
在iPhone6上，缩放为0.5，即CSS像素2px最终显示效果为1px，而在scale=1的设备，CSS像素1px显示效果为1px，那么，为了达到显示效果一致，以px为单位的元素(比如字体大小)，其样式应有适配不同dpr的版本，为了方便写，在动态设置viewport: scale的时候，同时在html根元素上加上data-dpr=[dpr]，样式示例：
```css
.p {
    font-size: 14px;

  [data-dpr="2"] & {
    font-size: 14px * 2;
  }

  [data-dpr="3"] & {
    font-size: 14px * 3;
  }
}
```
为写样式方便，可以借助sass的mixin写代码片段：
```css
// 适配dpr的字体大小
@mixin font-dpr($font-size){
  font-size: $font-size;

  [data-dpr="2"] & {
      font-size: $font-size * 2;
  }

  [data-dpr="3"] & {
      font-size: $font-size * 3;
  }
}
@mixin px-dpr($property, $px) {
  #{$property}: $px;

  [data-dpr="2"] & {
    #{$property}: $px * 2;
  }

  [data-dpr="3"] & {
    #{$property}: $px * 3;
  }
}

// 使用
@include font-dpr(14px);
@include px-dpr(width, 40px); @include px-dpr(height, 40px);
```

若无指定`viewport scale`，则将IOS设备根据其`1/dpr`缩放，同时在根元素上加上`data-dpr=[dpr]`
```js
(function (doc, win) {
  var doc = win.document;
  var docEl = doc.documentElement;
  var resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize';
  var metaEl = doc.querySelector('meta[name="viewport"]');
  var metaCtt = metaEl ? metaEl.content : '';
  var dpr = 0;
  var scale = 0;
  var matchScale = metaCtt.match(/initial\-scale=([\d\.]+)/);
  var matchWidth = metaCtt.match(/width=([^,\s]+)/);

  /**
    * ================================================
    *   设置data-dpr和viewport
    × ================================================
    */
  if (matchScale) {
    console.warn('将根据已有的meta标签来设置缩放比例');
    scale = parseFloat(matchScale[1]);
    dpr = parseInt(1 / scale);
  }

  // 对iOS设备进行dpr的判断，对于Android系列，始终认为其dpr为1
  if (!dpr && !scale) {
    var isAndroid = win.navigator.appVersion.match(/android/gi);
    var isIPhone = win.navigator.appVersion.match(/[iphone|ipad]/gi);
    var devicePixelRatio = win.devicePixelRatio;
    if (isIPhone) {
        // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
        if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {                
            dpr = 3;
        } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)){
            dpr = 2;
        } else {
            dpr = 1;
        }
    } else {
        // 其他设备下，仍旧使用1倍的方案
        dpr = 1;
    }
    scale = 1 / dpr;
  }

  docEl.setAttribute('data-dpr', dpr);
  // 动态改写meta:viewport标签
  if (!matchScale) { 
    var metaEl = doc.createElement('meta');
    metaEl.setAttribute('name', 'viewport');
    metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
    if (docEl.firstElementChild) {
      document.documentElement.firstElementChild.appendChild(metaEl);
    } else {
      var wrap = doc.createElement('div');
      wrap.appendChild(metaEl);
      documen.write(wrap.innerHTML);
    }
  }

})(document, window);
```

> 设置缩放视口与设置理想视口有什么不同  
> 问题：`viewport`设为理想视口 `scale=1`，基本已经满足适配，为什么要动态设置 `viewport` 缩放?  
> 推测原因：iPhone6为例，dpr为2，缩放设为0.5，则DOM宽度为750，缩放后显示刚好为屏幕宽度375，而总的CSS像素其实是750，与设备像素一致，这样1px的CSS像素，占用的物理像素也是1； 而`viewport`设置缩放为1的理想视口情况下，DOM宽度为375，显示也刚好是屏幕宽度，然而1px的CSS像素，占用的物理像素是2。这样说来，这样设置可以实现1px的线条在二倍屏的显示。
> 
> 因为： CSS像素与设备像素的关系依赖于屏幕缩放。 验证：设备iPhone6  
> 
> - 在`scale=0.5`时，`1px边框`显示效果;
> - 在`scale=1.0`时，`1px边框`显示效果;
> - 在`scale=0.5`时，`2px边框`显示效果;
> 
> 通过对比后发现，在` scale=0.5 `时，1px的线比` scale=1.0 `要细，这也就解决了` 1px线条 `的显示问题。


**3 . rem(一个CSS单位)**
这个单位的定义和em类似，不同的是em是相对于父元素，而rem是相对于根元素。rem定义是根元素的font-size, 以rem为单位，其数值与px的关系，需相对于根元素<html>的font-size计算，比如，设置根元素font-size=16px, 则表示1rem=16px。

根据这个特点，可以根据设备宽度动态设置根元素的font-size，使得以rem为单位的元素在不同终端上以相对一致的视觉效果呈现。

选取一个设备宽度作为基准，设置其根元素大小，其他设备根据此比例计算其根元素大小。比如使得iPhone6根元素font-size=16px。

| 设 备 | 设备宽度 | 根元素font-size/px | 宽度/rem |
|---------|----------|--------------------|----------|
| iPhone5 | 320 | js计算所得 | -- |
| iPhone6 | 375 | 16 | 23.4375 |
| i6 Plus | 414 | js计算所得 | -- |
| - | 360 | js计算所得 | -- |

> 根元素fontSize公式  
> baseWidth, baseFontSize是选为基准的设备宽度及其根元素大小  
> width, fontSize为所求设备的宽度及其根元素大小
> 
> `width / fontSize = baseWidth / baseFontSize`

```js
/**
  * 以下这段代码是用于根据移动端设备的屏幕分辨率计算出合适的根元素的大小
  * 当设备宽度为375(iPhone6)时，根元素font-size=16px; 依次增大；
  * 限制当为设备宽度大于768(iPad)之后，font-size不再继续增大
  * scale 为meta viewport中的缩放大小
  */
(function (doc, win) {
  var docEl = doc.documentElement;
  var resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize';
  /**
    * ================================================
    *   设置根元素font-size
    * 当设备宽度为375(iPhone6)时，根元素font-size=16px; 依次增大；
    * 当为设备宽度大于768(iPad)之后，font-size不再继续增大
    × ================================================
    */
    var refreshRem = function () {
    var clientWidth = docEl.clientWidth; // var clientWidth = docEl.getBoundingClientRect().width;
    if (!clientWidth) return;
    var fz;
    var maxWidth = 768;
    var width = clientWidth;
    if(clientWidth/dpr > maxWidth) {
      width = maxWidth*dpr;
    }
    fz = 16 * (width / 375);
    docEl.style.fontSize = fz + 'px';
  };

  if (!doc.addEventListener) return;
  win.addEventListener(resizeEvt, refreshRem, false);
  doc.addEventListener('DOMContentLoaded', refreshRem, false);
  refreshRem();

})(document, window);
```

对于需要使用rem来适配不同·屏幕的元素，使用rem来作为CSS单位，为了方便，可以借助sass写一个函数计算px转化为rem, 写样式时不必一直手动计算。

```bash
@function px2rem($px, $base-font-size: 16px) {
  @if (unitless($px)) {
    @warn "Assuming #{$px} to be in pixels, attempting to convert it into pixels for you";
    @return px2rem($px + 0px); // That may fail.
  } @else if (unit($px) == rem) {
    @return $px;
  }
  @return ($px / $base-font-size) * 1rem;
}
// 使用，eg:
font-size: px2rem(18px);

```


**4 . flex布局**
flex布局对于屏幕适配也很有帮助，有些地方通过flex布局的实现方式，效果会比较合理。


**5 . vm/vh:CSS单位**
vw(view-width), vh(view-height) 这两个单位是CSS新增的单位，表示视区宽度/高度，视区总宽度为100vw, 总高度为100vh。