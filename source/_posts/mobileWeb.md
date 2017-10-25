---
tag: [Mobil] title: 移动端常见问题集锦 
---
## 关于移动端适配的问题

		<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no"/>
		目前的做法是设置meta标签，用750/640的设计稿来布局，简单粗暴。也有推崇用rem来布局的，感觉不太好操作。设置如下：
		html { font-size: 100px; }
		@media(min-width: 320px) { html { font-size: 100px; } }
		@media(min-width: 360px) { html { font-size: 112.5px; } }
		@media(min-width: 400px) { html { font-size: 125px; } }
		@media(min-width: 640px) { html { font-size: 200px; } }
		给手机设置100px的字体大小；对于320px的手机匹配是100px，其他手机都是等比例匹配，因此设计稿上是多少像素的话，那么转换为rem的时候，rem = 设计像素/100即可

## 禁用a,button,input,optgroup,select,textarea等标签背景变暗  
在移动端使用a标签做按钮的时候或者文字连接的时候，点击按钮会出现一个 "暗色的"背景，比如如下代码：  

<!--more-->
	<a href="">button1</a>  
	<input type="button" value="提交"/>
在移动端点击后 会出现"暗色"的背景，这时候我们需要在css加入如下代码即可：

	a,button,input,optgroup,select,textarea{
		 -webkit-tap-highlight-color: rgba(0,0,0,0);
	}
		
## meta基础知识点：  
1. 页面窗口自动调整到设备宽度，并禁止用户及缩放页面。
		
		<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0,maximum-scale=1.0, user-scalable=0" />
属性基本含义：  
  content="width=device-width：  
  控制 viewport 的大小,device-width 为设备的宽度  
  initial-scale - 初始的缩放比例  
  minimum-scale - 允许用户缩放到的最小比例  
  maximum-scale - 允许用户缩放到的最大比例  
  user-scalable - 用户是否可以手动缩放  

2. 忽略将页面中的数字识别为电话号码  
  
  		<meta name="format-detection" content="telephone=no" />

3. 忽略Android平台中对邮箱地址的识别  
   		
   		<meta name="format-detection" content="email=no" />


4. 当网站添加到主屏幕快速启动方式，可隐藏地址栏，仅针对ios的safari  
		
		<meta name="apple-mobile-web-app-capable" content="yes" />


5. 将网站添加到主屏幕快速启动方式，仅针对ios的safari顶端状态条的样式  

		<meta name="apple-mobile-web-app-status-bar-style" content="black" />

6. 需要在网站的根目录下存放favicon图标，防止404请求(使用fiddler可以监听到)，在页面上需加link如下：
		
		<link rel="shortcut icon" href="/favicon.ico">

因此页面上通用的模板如下：
	
	<!DOCTYPE html>
	 <html>
	    <head>
	        <meta charset="utf-8">
	        <meta content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" name="viewport">
	        <meta content="yes" name="apple-mobile-web-app-capable"> 
	        <meta content="black" name="apple-mobile-web-app-status-bar-style">
	        <meta content="telephone=no" name="format-detection">
	        <meta content="email=no" name="format-detection">
	        <title>标题</title>
	        <link rel="shortcut icon" href="/favicon.ico">
	    </head>
	    <body>
	        这里开始内容
	    </body>
	</html>

## 移动端如何定义字体font-family
1. IOS系统
	* 	默认中文字体是Heiti SC
	*  默认引文字体Helvetica
	*  默认数字字体是HelveticaNeue
	*  无微软雅黑字体
2. Android系统
	* 	默认中文字体是Droidssansfallback
	*  默认英文和数字字体是Droid Sans
	*  无微软雅黑字体  
3. Winphone系统
	* 	默认中文字体是Dengxian(方正等线体)
	*  默认英文和数字字体是Segoe
	*  无微软雅黑字体

各个手机系统都有自己的默认字体，且都不支持微软雅黑，如无特殊需求，手机端无需定义中文字体，使用系统默认英文字体和数字字体可使用Helevetica，三种系统都支持。
	
	html{ 
			color: #333;
			font-family:'Helvetica Neue',Tahoma,Arial,PingFangSC-Regular,'Hiragino Sans GB','Microsoft Yahei',sans-serif;
			line-height: 1.2;
			user-select: none;
			-webkit-font-smoothing: antialiased;
			touch-action: manipulation;
			text-size-adjust: none;
		}
	以上配置来自于饿了么H5
	或者直接body{font-family: "Helvetica Neue", Helvetica, sans-serif;}

## 其他
### 点击链接可拨打电话
 	<a href="tel:13412341234">打电话给：13412341234</a>
### 点击链接可以发短信
	<a href="sms:10086">发送短信给10086</a>
### 调用手机系统自带的邮件功能
	<a href="mailto:fulun2017@gmail.com">发送电子邮件</a>
### webkeit表单输入框placeholder的颜色值改变：
如果想要默认的颜色显示红色，代码如下：  
		
	input::-webkit-input-placeholder{color:red;}  
	
如果想要用户点击变为蓝色，代码如下：
	  
	input:focus::-webkit-input-placeholder{color:blue;}
	
### 移动端IOS手机下清除输入框内阴影：
	
	input,textarea{
		-webkit-appearance:none;
	}

### 在IOS中禁止长按链接与图片弹出菜单
	a, img {
	   -webkit-touch-callout: none; 
	}
### 不显示webkit的滚动条

	element::-webkit-scrollbar{display:none;}
	// 测试在微信端无效（iPhone6）
	目前来看的话，移动端局部滚动需要引入第三方库iscroll或者better-scroll
	
### H5页面内容不想被人选中
	-webkit-user-select: none;
	user-select: none;
### H5页面想禁止长按链接或长按图片弹出菜单
	-webkit-touch-callout: none;
### 想取消IOS中button、input上的默认样式
	-webkit-appearance: none;
### 想改变Input里 placeholder属性的样式

	::-webkit-input-placeholder{color:#ccc}

### H5页面input type=”num”时想去掉右边的上下箭头

	input::-webkit-outer-spin-button,input::-webkit-inner-spin-button{-webkit-appearance:none !important; margin: 0;}

### H5页面加载的图片太大了，怎么办？

	JPG图片用JPEGmini压缩，PNG可在线用http://tinypng.org/压缩

### 在iOS系统中键盘输入时不想首字母为大写

	<input type="text" autocapitalize="off" />

### 在IOS系统中键盘输入关闭自动修正

	<input type="text" autocorrect="off" /> 

### 屏幕旋转横屏竖屏切换时，想禁止文本缩放

	-webkit-text-size-adjust: 100%;

### H5页面想有快速回弹滚动的效果
	
	overflow: auto; /* auto | scroll */
	
	-webkit-overflow-scrolling: touch;
	
	
## 参考链接
[H5移动端知识点总结](http://www.cnblogs.com/tugenhua0707/p/5180841.html)  
[移动前端系列——移动端页面坑与排坑技巧](http://tgideas.qq.com/webplat/info/news_version3/804/808/811/m579/201411/290576.shtml)  
[H5手机移动端WEB开发资源整合 常用的标签及注意事项](http://blog.csdn.net/u012118993/article/details/56023399)