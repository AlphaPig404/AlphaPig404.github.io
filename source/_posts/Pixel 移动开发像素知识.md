---
title: 移动端开发基础知识
---
## Pixel 移动开发像素知识
* px: CSS pixels逻辑像素，浏览器使用的抽象单位
* dp,pt: device independent pixels设备无关像素
* dpr: devicePxielRatio 设备像素缩放比
计算公式： 1px = (dpr)^2 * dp
维度上: 1px = 2 * dp

* DPI: 打印机每英寸可以喷的墨汁点(印刷行业)
* PPI: 屏幕每英寸的像素数量，即单位英寸内的像素密度  
目前，在计算机显示设备参数描述上，二者意思表达式一样的  

计算公式：以iPhone5为例子
ppi = 开方(1136^2+640^2)/4 = 326ppi (视网膜Retian屏)  

注意：单位为硬件像素，非px

<!--more-->
PPI越高，像素数越高，图像约清晰
Retina屏(高清屏)

## Viewport
手机浏览器默认为我们做了两件事情  
1. 页面渲染在一个980px(ios)的viewpoint上  
2. 缩放  
为什么不使用默认的980px的布局viewport
* 宽度不可控制，不同系统不同设备的默认值都可能不同
* 页面缩小版显示，交互不友好
* 链接不可点
* 有缩放，缩放后又有滚动  

font-size为40px等于PC上的12px同等物理大小，不规范  

一般是使用meta标签改变vieport  
\<meta name = "viewport" content="with=device-width,initial-scal=1"\>

## 设计方案
### 方案1
根据设备的实际宽度来设计(常用)，比如手机宽320px,我们就拿320px设计

### 方案2 1px=1dp
缩放0.5，根据设备的物理像素dp等于抽象像素px来设置。1像素边框和高清图片都不需要额外处理。淘宝订单方案，具体应该是使用媒体查询dp值来选择缩放比。

## 高效移动web布局
#### 弹性布局 flex  
#### 响应式布局  
媒体查询：  
类型：screen(屏幕)、print(打印机)、hangheld(手持设备)、all(通用)  
参数：width--视口宽度、height--视口宽度、device-width--设备的宽度、device-height--设备的高度、orientation：检查设备处于横向(landscape)还是竖屏(portrait)  
设计要点一: 百分比布局(建议)  
设计要点二：弹性图片  
设计要点三：重新布局，显示和隐藏


