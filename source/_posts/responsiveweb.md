---
tags: [CSS,响应式] title: 响应式web开发 
---
> 响应式网页设计（英语：Responsive web design，通常缩写为RWD），或称自适应网页设计、回应式网页设计、对应式网页设计。 是一种网页设计的技术做法，该设计可使网站在多种浏览设备（从桌面电脑显示器到移动电话或其他移动产品设备）上阅读和导航，同时减少缩放、平移和滚动。	--Wikipedia

## 响应式设计要点
* flexble grid layout 弹性网格布局
* flexible image 弹性图片
* media queries 媒体查询

<!--more-->
## 响应式网站的优缺点
### 优点
* 减少工作量
	1. 	网站、设计、代码、内容都只需要一份
	2. 多出来的工作量只是JS脚本、CSS样式做一些改动
* 节省时间
* 每个设备都能得到正确的设计
* 搜索优化(SEO)

### 缺点
* 会加载更多的样式和脚本资源
* 设计比较精确定位和控制
* 老版本浏览器兼容不好

### 媒体查询
示例 @media all and (min-width:800px) and(orientation:landscape){...}

**连接符**  

and，or（可以用，代替），not，
only（防止老旧的浏览器，不支持带媒体属性的查询，而应用到给定的样式。） 

**属性**

* width: 视口(viewport)宽度
* height：视口高度
* device-witdh：渲染表面(设备)的宽度
* device-height：渲染表面的高度  
以上为常用属性，可添加前缀（min-,max-）

* orientation：检查设备处于横向还是纵向
* aspect-ratio:基于视口宽度和高度的宽高比。width/height 如16；9，4：3
* device-aspect-ratio:渲染表面的宽度，就是设备屏幕的宽度
* color:每种颜色的位数bits 如：min-color:16位，8位
* resolution:检测屏幕或打印机的分辨率。如：min-resolution:300dpi

## 响应式网站设计实践原则
* 渐进增强or优雅降级 => 推荐优雅降级
* PC优先or移动优先 => 取决于网站定位
* 断点的选择 => 针对屏幕的大小而不是特殊设备进行选择（480，800，1400），具体取决于产品说明。

## 相关知识点
### 单位
* px绝对的长度单位(1个px相当于1个像素)
* em 相对的长度单位
	1. em相对参照物为父元素的font-size
	2. em具有继承的特点，当父元素font-size没有设置的时候会层层查找，直至根元素html。
	3. 当没有设置font-size时，浏览器会有一个默认的em设置：1em=16px
	4. 容易造成混乱
* rem的相对参照物为根元素html，相对于参照固定不变，比较好计算，当没有设置font-size时，浏览器会有默认的rem，1rem = 16px,这点与em一致。当把html的font-size设置为62.5%(10/16)的时候，1rem=10px。
* vw,vh 因为兼容性的问题暂时不表，挖个坑先。。

### 清除浮动
1. 在浮动元素的parent最后加一个空的div，设置clear：both;缺点与html结构耦合，不利于后期维护；
2. 给父元素追加overflow：hidden，用来触发block formatting context，所以它的边框不会和浮动的box重叠，就达到了清除浮动的目的。缺点当然就是overflow本事带来的，比如你需要这个div内部有滚动条，或者有绝对定位的子元素在外边(轮播图利用的就是这个原理)。
3. 让父元素本身也浮动起来，同样是触发bfc。缺点也同样是设置浮动带来的副作用。
4. 目前通用的解决方案
	
		.clearfix:after{
			content:".";
			display:block;
			height:0;
			clear:both;
			visibility:hidden;
		}
		.cleafix{
			zoom:1;//ie6,7兼容触发haslayout;
		}
		
		或者
		.clearfix:after,
		.clearfix:before{//before用来处理margin重合的问题
			content:" ";
			display:table;
		}
		.clearfix:after{
			clear:both;		
		}
		.clearfix{
			zoom:1;
		}
			
			
			
		
### 引申出BFC的概念

#### BFC定义

BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。

#### BFC布局规则
* 内部的Box会在垂直方向，一个接一个地放置。
* Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
* 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
* BFC的区域不会与float box重叠。
* BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
* 计算BFC的高度时，浮动元素也参与计算

#### 哪些元素会生成BFC
* 根元素
* float属性不为none
* position为absolute或fixed
* display为inline-block, table-cell, table-caption, flex, 	inline-flex
* overflow不为visible

#### BFC的一些应用
* 基于float的自适应布局
* 清除内部浮动(通用做法见上面)
* 防止垂直margin重叠(一般的做法是统一只设置margin-top或margin-bottom)