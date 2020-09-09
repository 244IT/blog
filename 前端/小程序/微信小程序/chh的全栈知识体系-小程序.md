

### 小程序笔记

### 01.小程序开发介绍

1.什么是小程序？

* 小程序是微信生态的一部分, 它提供了一种更加 **方便和高效** 的用户交互方式

2.小程序的好处？

* 便捷
  * 普通的APP我们需要的使用过程：了解APP - 下载 - 打开 - 注册 - 使用
  * 小程序版本的APP的使用过程：了解APP - 打开(扫码/搜索) - 使用
* 用户体验好，远好于公众号和H5页面的体验
* 释放手机内存空间，让手机桌面更加简洁
  * 对于手机空间不大的人来说, 安装很多App会带来灾难, 经常面临空间不足 
  * 小程序不需要安装, 而且有规定的大小(目前不能超过8M，包括在使用分包的情况下)

3.小程序开发的必要性？

* 小程序的用户量越来越多
* 用户体验好
* 一端开发，多端运行（iOS端、Android端）

4.WebView的飞速发展

* 随着公众号的出现和繁荣，WebView的使用频率越来越高，不少的企业或个人都需要做H5页面。
* H5页面的劣势很明显：体验太渣，而且无法调用很多底层App拥有的功能。 
* 微信团队也因此提供了一些JS-SDK给Web开发者使用，包括拍照、音频、地图、支付、分享等能力。
* 但是依然没法改变体验很渣这一点（比如加载某个页面经常是白屏、交互无反馈、页面切换生硬等）

5.Native的优势和劣势

|          | **Native**     | **Web**          | 期望         |
| :------- | -------------- | ---------------- | ------------ |
| 开发门槛 | 高             | 低               | 低           |
| 体验     | 好             | 白屏、交互反馈差 | 接近原生体验 |
| 版本更新 | 需审核，迭代慢 | 在线更新         | 在线更新     |
| 管控性   | 平台可管控     | 难管控           | 可管控       |

6.小程序的诞生

* 门槛低：基本上是类似于html+css+js
* 可直接云端更新：微信审核，无需经过App Store等平台
* 提升用户体验：通过提供基础能力、原生组件结合等方式，提升用户体验
* 平台管控能力：小程序提供云端更新，通过代码上传、审核等方式，增强对开发者的管控能力；
* 双线程模型：逻辑层和渲染层分开加载，提供了管控型和安全性（沙盒环境运行JS代码，不允许执行任何和浏览器相关的接口，比如跳转页面、操作DOM等）；

7.小程序的历史

* 2016年9月21日发布的小程序内测版

* 2017年1月9日正式上线

8.小程序预备知识

* 小程序的开发主要分成三部分：
  * 页面布局：WXML，类似HTML
  * 页面样式：WXSS，几乎就是CSS（某些不支持，某些进行了增强）
  * 页面脚本：JavaScript+WXS，（JS，以及WeixinScript后续学习）

### 02.开发前准备工作

>开发前准备工作主要分为两个部分：
>
>1.**申请AppID**
>
>2.**小程序开发工具**

1.注册账号 – 申请AppID

* 登录微信公众平台： https://mp.weixin.qq.com/

  * 一个账号对应一个小程序；

  * 企业、政府、媒体、其他组织主体可以注册50个小程序；

  * 个体户和个人类型主体可注册5个小程序；

* 注册成功后，查看AppID

  * 登录->点击开发->查看appid

2.小程序开发工具

* 开发工具的选择：官方微信Web开发者工具、VSCode
* 下载开发工具：https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html
* 选择稳定版下载安装

### 03.第一个小程序

1.应用目录的结构

* App

  * app.js：创建App实例的代码以及一些全局相关的内容
  * app.json：全局的一些配置，比如window/tabbar等
  * app.wxss：全局的一些样式配置

* Page

  * page.js：创建Page实例的代码，以及页面相关的内容
  * page.json：页面单独的配置，比如页面对应的window配置
  * page.wxml：页面的wxml布局代码
  * page.wxss：页面的样式配置

* Component

  * component.js：创建component实例的代码，以及组件内部的内容

  * component.json：组件内部的配置，比如当前组件使用了别的组件

  * component.wxml：组件的wxml布局代码
  * component.wxss：组件的样式配置

### 04.git tag的相关操作

1. 初始化项目提交

```
git add . 
git commit -m ''
git push 
```

2. 写完第一个知识点，进行第二次提交，并打上tag

```
git add . 
git commit -m ''
git tag 01_知识点01 //提交tag
git tag //查看目前的tag
```

3. 写完知识点一，准备写知识点二code，先回到初始化项目

```
git log //查看历史提交记录
git reset 哈希值 // 回退到指定的版本  参数： --hard 强制回退

//然后重复以上步骤
git add . 
git commit -m ''
git tag 01_知识点01 //提交tag

git push --tags //将tag都push到远程仓库
```

4. 读取github上tag的相关文件，先下载，再切换

```
git clone ...
git checkout 01_知识点01
```

### 05.小程序初体验

1.数据绑定

* test01/index.wxml

```
<view>hello {{name}}</view>
```

* test01/index.js

```
Page({
	data:{
		name: "chh"
	}
})
```

2.列表渲染

* test01/index.wxml

```
<view wx:for="{{student}}">{{index}}-{{item.name}}-{{item.age}}</view>
```

* test01/index.js

```
Page({
	data:{
		student: [
          {name: "rose", age: 12}, 
          {name: "jack", age: 33}, 
          {name: "jack", age: 10}
        ],
	}
})
```

3.事件绑定

* test01/index.wxml

```
<view>{{count}}</view>
<button size="mini" bindtap="add">+</button>
<button size="mini" bindtap="sub">-</button>
```

* test01/index.js

```
Page({
	data:{
		count: 0,
	},
	/* 加 */
    add() {
        this.setData({
          	count: this.data.count + 1
        })
    },
    /* 减 */
    sub() {
        this.setData({
          	count: this.data.count - 1
        })
    },
})
```

### 06.小程序的MVVM

2.编程方式：

* 命令式编程：原生操作dom
* 声明式编程：Vue/React

### 07.project.config和sitemap

>小程序的很多开发需求被规定在了配置文件中：
>
>**为什么这样做呢?** 
>
>* 这样做可以更有利于我们的开发效率； 
>
>* 并且可以保证开发出来的小程序的某些风格是比较一致的；
>
>* 比如导航栏 – 顶部TabBar，以及页面路由等等

1.project.config.json： 项目配置文件, 比如项目名称、appid等；

➢ https://developers.weixin.qq.com/miniprogram/dev/devtools/projectconfig.html

2.sitemap.json：小程序搜索相关的；

➢ https://developers.weixin.qq.com/miniprogram/dev/framework/sitemap.html

3.app.json：全局配置

4.page.json：页面配置

### 08.全局配置app.json和页面配置page.json

1.全局配置app.json

> 全局配置比较多, 我们这里将几个比较重要的. 完整的查看官方文档
>
> * https://developers.weixin.qq.com/miniprogram/dev/reference/configuration/app.html

| 属性   | 类型   | 必填 | 描述               |
| ------ | ------ | ---- | ------------------ |
| pages  | Array  | 是   | 页面路径列表       |
| window | Object | 否   | 全局的默认窗口表现 |
| tabBar | Object | 否   | 底部tab栏的表现    |

pages：页面路径列表

* 用于指定小程序由哪些页面组成，每一项都对应一个页面的 路径（含文件名） 信息。

* 小程序中所有的页面都是必须在pages中进行注册的。

window: 全局的默认窗口展示

* 用户指定窗口如何展示, 其中还包含了很多其他的属性

```
"window": {
    "navigationBarBackgroundColor" : "#FF5778",
    "navigationBarTextColor": "#fff",
    "navigationBarTitleText": "我的小程序",
    "enablePullDownRefresh": true,
    "backgroundColor": "#000",
    "backgroundTextStyle": "dark"
}
```

tabBar: 底部tab栏的展示

```
"tabBar": {
    "selectedColor": "#FF5778",
    "list": [
        {
        "pagePath": "pages/test01/index",
        "text": "首页",
        "iconPath": "assets/tabbar/cart.png",
        "selectedIconPath": "assets/tabbar/cart_active.png"
      },
      {
        "pagePath": "pages/test02/index",
        "text": "购物车",
        "iconPath": "assets/tabbar/category.png",
        "selectedIconPath": "assets/tabbar/category_active.png"
      }
    ]
 }
```

2.页面配置page.json

* 每一个小程序页面也可以使用 .json 文件来对本页面的窗口表现进行配置。

* 页面中配置项在当前页面会覆盖 app.json 的 window 中相同的配置项

### 09.小程序的双线程模型

1.谁是小程序的宿主环境呢？**微信客户端**

* 宿主环境为了执行小程序的各种文件：wxml文件、wxss文件、js文件。提供了小程序的双线程模型

2.双线程模型：

* WXML模块和WXSS样式运行于 渲染层，渲染层使

用WebView线程渲染（一个程序有多个页面，会使

用多个WebView的线程）。

* JS脚本（app.js/home.js等）运行于 逻辑层，逻辑

层使用JsCore运行JS脚本。

* 这两个线程都会经由微信客户端（Native）进行中转

交互

3.界面渲染整体流程：

* 在渲染层，宿主环境会把WXML转化成对应的JS对象； 

* 将JS对象再次转成真实DOM树，交由渲染层线程渲染；

* 数据变化时，逻辑层提供最新的变化数据，JS对象发生变化比较进行diff算法对比；

* 将最新变化的内容反映到真实的DOM树中，更新UI；

### 10.小程序的启动流程

1.启动流程

<img src="http://admin.chenhaohui.cn/img/weixin/note/note01.png"/>2.App生命周期函数

* onlaunch：初始化小程序
* onshow：小程序页面显示
* onhide：小程序页面隐藏
* onerror：小程序发送错误

### 11.注册App时做什么

1.注册App时，我们一般会做什么呢？

*  判断小程序的进入场景

* 监听生命周期函数，在生命周期中执行对应的业务逻辑，比如在某个生命周期函数中获取微信用户的信息。

* 因为App()实例只有一个，并且是全局共享的（单例对象），所以我们可以将一些共享数据放在这里。

2.小程序的打开场景较多：

* 常见的打开场景：群聊会话中打开、小程序列表中打开、微信扫一扫打开、另一个小程序打开

*  https://developers.weixin.qq.com/miniprogram/dev/reference/scene-list.html

3.如何确定场景?

*  在onLaunch和onShow生命周期回调函数中,会有options参数，其中有scene值

4.获取用户信息 – 保存全局变量

* wx.getUserInfo – 即将废弃的接口
* button组件 – 将open-type改成getUserInfo，并且绑定bindgetuserinfo事件去获取；
* 使用open-data组件展示用户信息（展示）

### 12.关于Page组件

**01.页面的生命周期**

| 属性     | 说明                 |
| -------- | -------------------- |
| onload   | 监听页面加载         |
| onShow   | 监听页面显示         |
| onReady  | 监听页面初次渲染完成 |
| onHide   | 监听页面隐藏         |
| onUnload | 监听页面卸载         |

**02.生命周期详解**

1.首先两个线程（视图线程：view Thread，逻辑线程：appService）同时开始执行start

2.视图线程开始初始化数据，之后会通知逻辑线程所需要的数据，逻辑线程开始监听页面加载（onLoad），然后监听页面显示（onShow），之后发生阻塞，发送数据给视图线程

3.逻辑线程向视图线程发送完数据后，视图层准备渲染数据（ready），然后通知逻辑层，随机逻辑层触发（onReady）

4.当页面修改数据的时候，逻辑视图要重新发送数据给视图层，视图层重新渲染

5.当页面退到后台的时候，逻辑线程触发onHide

6.当退出当前页面的时候，逻辑层触发onUnload

**03.事件属性**

| 属性              | 说明                         |
| ----------------- | ---------------------------- |
| onPullDownRefresh | 页面下拉触发                 |
| onReachBottom     | 页面上拉触底触发             |
| onShareTimeline   | 点击右上角转发朋友圈触发     |
| onAddToFavorites  | 点击右上角收藏触发           |
| onPageScroll      | 页面滚动触发                 |
| onResize          | 页面尺寸改变触发             |
| onTabItemTap      | 点击tab时触发（前提是tab页） |

**04.在Page页做什么**

1.在data中初始化一些数据

````
data: {
	list: []
}
````

2.在生命周期函数中发送网络请求，从服务器获取数据

```
onLoad() {
	wx.request({
		url: 'xxx',
		method: 'get'
	}).then(res => {
	 	const data = res.data.list
	 	this.setData({
	 		list: data
	 	})
	})
}
```

3.监听页面中的事件

```
onClick() {
	console.log("用户点击")
}
```

4.监听其他事件

```
onPullDownRefresh(){} // 下拉加载...
```

### 13.内置组件

#### 01.text

文本(Text组件用于显示文本, 类似于span标签, 是行内元素)

| 属性        | 类型    | 默认值 | 必填 | 说明                                                | 最低版本                                                     |
| :---------- | :------ | :----- | :--- | :-------------------------------------------------- | :----------------------------------------------------------- |
| selectable  | boolean | false  | 否   | 文本是否可选 (已废弃)                               | [1.1.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| user-select | boolean | false  | 否   | 文本是否可选，该属性会使文本节点显示为 inline-block | [2.12.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| space       | string  |        | 否   | 显示连续空格                                        | [1.4.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| decode      | boolean | false  | 否   | 是否解码                                            | [1.4.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |

**space 的合法值**

| 值   | 说明                   | 最低版本 |
| :--- | :--------------------- | :------- |
| ensp | 中文字符空格一半大小   |          |
| emsp | 中文字符空格大小       |          |
| nbsp | 根据字体设置的空格大小 |          |

#### 02.view

视图组件（块级元素，独占一行，通常用作容器组件)

| 属性                   | 类型    | 默认值 | 必填 | 说明                                                         | 最低版本                                                     |
| :--------------------- | :------ | :----- | :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| hover-class            | string  | none   | 否   | 指定按下去的样式类。当 `hover-class="none"` 时，没有点击态效果 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| hover-stop-propagation | boolean | false  | 否   | 指定是否阻止本节点的祖先节点出现点击态                       | [1.5.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| hover-start-time       | number  | 50     | 否   | 按住后多久出现点击态，单位毫秒                               | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| hover-stay-time        | number  | 400    | 否   | 手指松开后点击态保留时间，单位毫秒                           | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |

#### 03.button

> 基础库 1.0.0 开始支持，低版本需做[兼容处理](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html)。

按钮。

| 属性        | 类型    | 默认值       | 必填 | 说明                                                         | 最低版本                                                     |
| :---------- | :------ | :----------- | :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| size        | string  | default      | 否   | 按钮的大小                                                   | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| type        | string  | default      | 否   | 按钮的样式类型                                               | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| plain       | boolean | false        | 否   | 按钮是否镂空，背景色透明                                     | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| disabled    | boolean | false        | 否   | 是否禁用                                                     | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| loading     | boolean | false        | 否   | 名称前是否带 loading 图标                                    | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| form-type   | string  |              | 否   | 用于 [form](https://developers.weixin.qq.com/miniprogram/dev/component/form.html) 组件，点击分别会触发 [form](https://developers.weixin.qq.com/miniprogram/dev/component/form.html) 组件的 submit/reset 事件 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| open-type   | string  |              | 否   | 微信开放能力                                                 | [1.1.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| hover-class | string  | button-hover | 否   | 指定按钮按下去的样式类。当 `hover-class="none"` 时，没有点击态效果 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| ...         |         |              |      |                                                              |                                                              |

**size 的合法值**

| 值      | 说明     | 最低版本 |
| :------ | :------- | :------- |
| default | 默认大小 |          |
| mini    | 小尺寸   |          |

**type 的合法值**

| 值      | 说明 | 最低版本 |
| :------ | :--- | :------- |
| primary | 绿色 |          |
| default | 白色 |          |
| warn    | 红色 |          |

**form-type 的合法值**

| 值     | 说明     | 最低版本 |
| :----- | :------- | :------- |
| submit | 提交表单 |          |
| reset  | 重置表单 |          |

#### 04.image

图片。支持 JPG、PNG、SVG、WEBP、GIF 等格式，[2.3.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) 起支持云文件ID。

| 属性                   | 类型        | 默认值      | 必填 | 说明                                                   | 最低版本                                                     |
| :--------------------- | :---------- | :---------- | :--- | :----------------------------------------------------- | :----------------------------------------------------------- |
| src                    | string      |             | 否   | 图片资源地址                                           | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| mode                   | string      | scaleToFill | 否   | 图片裁剪、缩放的模式                                   | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| webp                   | boolean     | false       | 否   | 默认不解析 webP 格式，只支持网络资源                   | [2.9.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| lazy-load              | boolean     | false       | 否   | 图片懒加载，在即将进入一定范围（上下三屏）时才开始加载 | [1.5.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| show-menu-by-longpress | boolean     | false       | 否   | 开启长按图片显示识别小程序码菜单                       | [2.7.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| binderror              | eventhandle |             | 否   | 当错误发生时触发，event.detail = {errMsg}              | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindload               | eventhandle |             | 否   | 当图片载入完毕时触发，event.detail = {height, width}   | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |

注意：

* image组件默认宽度320px、高度240px
* image组件可以写成单标签，也可以写成双标签
* image组件是一个行内块元素

**栗子**

1.选择本地图片展示

```
// wxml
<button bindtap="chooseImage">选中图片</button>
<image src="{{imagePath}}"></image>

// js
chooseImage() {
  wx.chooseImage({
    success: (res) => {
      const imagePath = res.tempFilePaths[0]
      this.setData({
        imagePath
      })
    },
  })
}
```

2.图片懒加载

```
// wxml
<image wx:for="{{10}}" src="https://res.wx.qq.com/wxdoc/dist/assets/img/0.4cb08bb4.jpg" bindload="handleImgLoad" lazy-load ></image>
// js
handleImgLoad() {
	console.log("图片加载完成")
}
```

3.识别小程序码(肯德基小程序)

```
<image src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1599301587785&di=fd547c921a8aebc8b3229887c0cac2d0&imgtype=0&src=http%3A%2F%2Fimg.mp.itc.cn%2Fupload%2F20170421%2F4f3eda41d791411f80896005620554fd_th.jpeg" show-menu-by-longpress mode="aspectFit"/>
```

* show-menu-by-longpress长按弹出框包含以下功能：
  * 发送给朋友
  * 收藏
  * 保存图片
  * 识别图片的小程序码（前提是图片中有小程序码）

#### 05.input

| 属性                     | 类型        | 默认值            | 必填 | 说明                                                         | 最低版本                                                     |
| :----------------------- | :---------- | :---------------- | :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| value                    | string      |                   | 是   | 输入框的初始内容                                             | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| type                     | string      | text              | 否   | input 的类型                                                 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| password                 | boolean     | false             | 否   | 是否是密码类型                                               | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| placeholder              | string      |                   | 是   | 输入框为空时占位符                                           | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| placeholder-style        | string      |                   | 是   | 指定 placeholder 的样式                                      | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| placeholder-class        | string      | input-placeholder | 否   | 指定 placeholder 的样式类                                    | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| disabled                 | boolean     | false             | 否   | 是否禁用                                                     | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| maxlength                | number      | 140               | 否   | 最大输入长度，设置为 -1 的时候不限制最大长度                 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| cursor-spacing           | number      | 0                 | 否   | 指定光标与键盘的距离，取 input 距离底部的距离和 cursor-spacing 指定的距离的最小值作为光标与键盘的距离 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| auto-focus               | boolean     | false             | 否   | (即将废弃，请直接使用 focus )自动聚焦，拉起键盘              | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| focus                    | boolean     | false             | 否   | 获取焦点                                                     | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| confirm-type             | string      | done              | 否   | 设置键盘右下角按钮的文字，仅在type='text'时生效              | [1.1.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| always-embed             | boolean     | false             | 否   | 强制 input 处于同层状态，默认 focus 时 input 会切到非同层状态 (仅在 iOS 下生效) | [2.10.4](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| confirm-hold             | boolean     | false             | 否   | 点击键盘右下角按钮时是否保持键盘不收起                       | [1.1.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| cursor                   | number      |                   | 是   | 指定focus时的光标位置                                        | [1.5.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| selection-start          | number      | -1                | 否   | 光标起始位置，自动聚集时有效，需与selection-end搭配使用      | [1.9.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| selection-end            | number      | -1                | 否   | 光标结束位置，自动聚集时有效，需与selection-start搭配使用    | [1.9.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| adjust-position          | boolean     | true              | 否   | 键盘弹起时，是否自动上推页面                                 | [1.9.90](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| hold-keyboard            | boolean     | false             | 否   | focus时，点击页面的时候不收起键盘                            | [2.8.2](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindinput                | eventhandle |                   | 是   | 键盘输入时触发，event.detail = {value, cursor, keyCode}，keyCode 为键值，2.1.0 起支持，处理函数可以直接 return 一个字符串，将替换输入框的内容。 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindfocus                | eventhandle |                   | 是   | 输入框聚焦时触发，event.detail = { value, height }，height 为键盘高度，在基础库 1.9.90 起支持 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindblur                 | eventhandle |                   | 是   | 输入框失去焦点时触发，event.detail = {value: value}          | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindconfirm              | eventhandle |                   | 是   | 点击完成按钮时触发，event.detail = {value: value}            | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindkeyboardheightchange | eventhandle |                   | 是   | 键盘高度发生变化的时候触发此事件，event.detail = {height: height, duration: duration} | [2.7.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |

**type 的合法值**

| 值     | 说明               | 最低版本 |
| :----- | :----------------- | :------- |
| text   | 文本输入键盘       |          |
| number | 数字输入键盘       |          |
| idcard | 身份证输入键盘     |          |
| digit  | 带小数点的数字键盘 |          |

**confirm-type 的合法值**

| 值     | 说明                 | 最低版本 |
| :----- | :------------------- | :------- |
| send   | 右下角按钮为“发送”   |          |
| search | 右下角按钮为“搜索”   |          |
| next   | 右下角按钮为“下一个” |          |
| go     | 右下角按钮为“前往”   |          |
| done   | 右下角按钮为“完成”   |          |

**栗子**

1.获取用户输入的内容，当点击搜索后，下方的view显示对应的内容

```
// wxml
<view> 输入身份证
  <input type="text" 
         bindconfirm="handleConfirm" 
         confirm-type="searchValue"/>
</view>
<view>{{searchValue}}</view>

// js
data: {
  searchValue: ''
},
handleConfirm(res) {
  const searchValue = res.detail.value
  this.setData({
    searchValue
  })
}
```

#### 06.scroll-view

局部滚动

| 属性                    | 类型          | 默认值  | 必填 | 说明                                                         | 最低版本                                                     |
| :---------------------- | :------------ | :------ | :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| scroll-x                | boolean       | false   | 否   | 允许横向滚动                                                 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| scroll-y                | boolean       | false   | 否   | 允许纵向滚动                                                 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| upper-threshold         | number/string | 50      | 否   | 距顶部/左边多远时，触发 scrolltoupper 事件                   | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| lower-threshold         | number/string | 50      | 否   | 距底部/右边多远时，触发 scrolltolower 事件                   | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| scroll-top              | number/string |         | 否   | 设置竖向滚动条位置                                           | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| scroll-left             | number/string |         | 否   | 设置横向滚动条位置                                           | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| scroll-into-view        | string        |         | 否   | 值应为某子元素id（id不能以数字开头）。设置哪个方向可滚动，则在哪个方向滚动到该元素 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| scroll-with-animation   | boolean       | false   | 否   | 在设置滚动条位置时使用动画过渡                               | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| enable-back-to-top      | boolean       | false   | 否   | iOS点击顶部状态栏、安卓双击标题栏时，滚动条返回顶部，只支持竖向 | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| enable-flex             | boolean       | false   | 否   | 启用 flexbox 布局。开启后，当前节点声明了 `display: flex` 就会成为 flex container，并作用于其孩子节点。 | [2.7.3](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| scroll-anchoring        | boolean       | false   | 否   | 开启 scroll anchoring 特性，即控制滚动位置不随内容变化而抖动，仅在 iOS 下生效，安卓下可参考 CSS `overflow-anchor` 属性。 | [2.8.2](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| refresher-enabled       | boolean       | false   | 否   | 开启自定义下拉刷新                                           | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| refresher-threshold     | number        | 45      | 否   | 设置自定义下拉刷新阈值                                       | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| refresher-default-style | string        | "black" | 否   | 设置自定义下拉刷新默认样式，支持设置 `black | white | none`， none 表示不使用默认样式 | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| refresher-background    | string        | "#FFF"  | 否   | 设置自定义下拉刷新区域背景颜色                               | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| refresher-triggered     | boolean       | false   | 否   | 设置当前下拉刷新状态，true 表示下拉刷新已经被触发，false 表示下拉刷新未被触发 | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| enhanced                | boolean       | false   | 否   | 启用 scroll-view 增强特性                                    | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bounces                 | boolean       | true    | 否   | iOS 下 scroll-view 边界弹性控制 (同时开启 enhanced 属性后生效) | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| show-scrollbar          | boolean       | true    | 否   | 滚动条显隐控制 (同时开启 enhanced 属性后生效)                | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| paging-enabled          | boolean       | false   | 否   | 分页滑动效果 (同时开启 enhanced 属性后生效)                  | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| fast-deceleration       | boolean       | false   | 否   | 滑动减速速率控制 (同时开启 enhanced 属性后生效)              | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| binddragstart           | eventhandle   |         | 否   | 滑动开始事件 (同时开启 enhanced 属性后生效) detail { scrollTop, scrollLeft } | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| binddragging            | eventhandle   |         | 否   | 滑动事件 (同时开启 enhanced 属性后生效) detail { scrollTop, scrollLeft } | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| binddragend             | eventhandle   |         | 否   | 滑动结束事件 (同时开启 enhanced 属性后生效) detail { scrollTop, scrollLeft, velocity } | [2.12.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindscrolltoupper       | eventhandle   |         | 否   | 滚动到顶部/左边时触发                                        | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindscrolltolower       | eventhandle   |         | 否   | 滚动到底部/右边时触发                                        | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindscroll              | eventhandle   |         | 否   | 滚动时触发，event.detail = {scrollLeft, scrollTop, scrollHeight, scrollWidth, deltaX, deltaY} | [1.0.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindrefresherpulling    | eventhandle   |         | 否   | 自定义下拉刷新控件被下拉                                     | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindrefresherrefresh    | eventhandle   |         | 否   | 自定义下拉刷新被触发                                         | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindrefresherrestore    | eventhandle   |         | 否   | 自定义下拉刷新被复位                                         | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| bindrefresherabort      | eventhandle   |         | 否   | 自定义下拉刷新被中止                                         | [2.10.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |

**栗子**

1.垂直滚动

```
// wxml
<scroll-view scroll-y="true" class="scroll-y">
  <view wx:for="{{10}}" class="box"></view>
</scroll-view>

// js
.scroll-y{
  height: 400rpx;
  padding: 20rpx;
  background-color: lightcoral;
}
.box{
  width: 710rpx;
  height: 100rpx;
  margin: 10rpx 0;
  background-color: lightblue;
}
```

2.水平滚动

```
// wxml
<scroll-view scroll-x="true" class="scroll-x">
  <view wx:for="{{10}}" class="box"></view>
</scroll-view>

// wxss
.scroll-x{
  width: 400rpx;
  padding: 20rpx;
  background-color: lightcoral;
  white-space: nowrap;
}
.box{
  display: inline-block;
  width: 100rpx;
  height: 100rpx;
  margin: 0 10rpx;
  background-color: lightblue;
}
```

#### 07.swiper

#### 08.共同属性

| 属性名        | 类型         | 描述           | 注解                       |
| ------------- | ------------ | -------------- | -------------------------- |
| id            | String       | 组件的唯一标识 | 整个页面唯一               |
| class         | String       | 组件的样式类   | 在对应的WXSS中定义的样式类 |
| style         | String       | 组件的内联样式 | 可以动态设置的内联样式     |
| hidden        | Boolean      | 组件是否显示   | 所有组件默认显示           |
| data-*        | Any          | 自定义属性     |                            |
| bind*/catch\* | EventHandler | 组件的事件     |                            |

### 14.WXSS&WXML&WXS

#### 01.wxss

**页面样式写法**

* 页面样式的三种写法： 行内样式、页面样式、全局样式 
* 三种样式都可以作用于页面的组件 
* 优先级依次是：行内样式 > 页面样式 > 全局样式

行内样式

```
<view style="color: red;">test</view>
```

页面样式

```
// test.wxml
<view class="test">test</view>
// test.wxss
.test{color: red;}
```

全局样式

```
// test.wxml
<view class="test">test</view>
// app.wxss
.test{color: red;}
```

**选择器的权重**

* !important（∞） > 内联样式（1000）> id（100）> class（10）> element（1）

**尺寸单位**

* rpx（responsive pixel）: 可以根据屏幕宽度进行自适应。规定屏幕宽为750rpx
* 如在 iPhone6 上，屏幕宽度为375px，共有750个物理像素，则750rpx = 375px = 750物理像素，1rpx = 0.5px = 1物理像素。

**样式导入**

* 为什么使用样式导入？
  * 在某些情况下，我们可能会将样式分在多个wxss文件中，方便对 样式的管理。 
  * 这个时候，我们就可以使用样式导入，来让单独的wxss生效
* 我们可以在一个wxss中导入另一个wxss文件：
  * 1.使用@import进行导入 
  * 2.@import后跟需要导入的外联样式表的相对路径（或者绝对路 径也可以），用;表示语句结束。

```
import "/css/normal.wxss"
```

* 导入的位置在哪里？
  * 可以在app.wxss中导入这个样式 
  * 也可以在page.wxss导入这个样式

#### 02.wxml

**Mustache语法**

WXML基本格式：

* 类似于HTML代码：比如可以写成单标签，也可以写成双标签 
* 必须有严格的闭合：没有闭合会导致编译错误 
* 大小写敏感：class和Class是不同的属性 

开发中, 界面上展示的数据并不是写死的, 而是会根据服务器返回的数据, 或者用户的操作来进行改变.

*  如果使用原生JS或者jQuery的话, 我们需要通过操作DOM来进行界 面的更新
* 小程序和Vue/React一样, 提供了插值语法: Mustache语法(双大括号)

1. Mustache语法不仅仅可以直接显示数据, 也可以使用表达式：

```
<view>{{age >= 18 ? "成年人":"未成年人"}}</view>
<view>{{age + 20}}</view>
<view>{{age + '岁'}}</view>
```

2. 可以绑定到属性:

```
<view class="content {{isActive ? 'active' : ''}}">
```

**逻辑判断**

1. wx:if

某些时候, 我们需要根据条件来决定一些内容是否渲染：

* 当条件为true时, view组件会渲染出来 
* 当条件为false时, view组件不会渲染出来

```
<view wx:if="{{true}}">是否渲染</view>
```

根据按钮点击，决定是否渲染

```
<button size="mini" bind:tap="onToggle">切换</button>
<view wx:if="{{isShow}}">是否渲染</view>
```

也可以有多个条件

```
<button size="mini" bind:tap="onIncrement">+10</button>
<view wx:if="{{score > 90}}">优秀</view>
<view wx:elif="{{score > 80}}">良好</view>
<view wx:elif="{{score > 60}}">及格</view>
<view wx:else>不及格</view>
```

2. hidden

* hidden是所有的组件都默认拥有的属性
* 当hidden属性为true时, 组件会被隐藏 , 当hidden属性为false时, 组件会显示出来

hidden和wx:if的区别：

* hidden本质上是修改css属性display，拥有更高的初始渲染消耗，更适合频繁切换的场景
* wx:if本质上是元素节点的创建和销毁，拥有更高的切换开销，不适合频繁切换的场景

##### 01.列表渲染-wx:for

为什么使用wx:for？

* 我们知道，在实际开发中，服务器经常返回各种列表数据，我们不可能一一从列表中取出数据进行展示
* 需要通过for循环的方式，遍历所有的数据，一次性进行展示

在组件中，我们可以使用wx:for来遍历一个数组 （字符串 - 数字）

* 默认情况下，遍历后在wxml中可以使用一个变量index，保存的是当前遍历数据的下标值。 
* 数组中对应某项的数据，使用变量名item获取。

```
<!-- 1.1.遍历数组/字符串/数字 -->
<view wx:for="{{['abc', 'cba', 'nba']}}">{{item}} {{index}}</view>
<view wx:for="coderwhy">{{item}} {{index}}</view>
<view wx:for="{{9}}">{{item}}</view>
```

##### 02.列表渲染 -自定义item/index名称

但是某些情况下，我们可能想使用其他名称 ，或者当出现**多层遍历**时，名字会重复 

这个时候，我们可以指定item和index的名称：

```
<view wx:for="{{movies}}" wx:for-item="movie" wx:for-index="i">{{movie}} {{i}}</view>
```

##### 03.列表渲染 - key作用

key的作用主要是为了高效的更新虚拟DOM

##### 04.block标签

**什么是block标签？**

* 某些情况下，我们需要使用 wx:if 或 wx:for时，可能需要包裹一组组件标签 

* 我们希望对这一组组件标签进行整体的操作，这个时候怎么办呢？

  1. 方式一：使用一个view组件包裹：

  ```
  <view wx:if="{{isShow}}">
    <button>按钮</button>
    <view>呵呵呵</view>
    <text>我是内容</text>
  </view>
  ```

  2. 方式二：使用block标签

  ```
  <block wx:if="{{isShow}}">
    <button>按钮</button>
    <view>呵呵呵</view>
    <text>我是内容</text>
  </block>
  ```

**意义**

<block/> 并不是一个组件，它仅仅是一个包装元素，不会在页面中做任何渲染，只接受控制属性。 

**好处**

* 将需要进行遍历或者判断的内容进行包裹。
* 将遍历和判断的属性放在block便签中，不影响普通属性的阅读，提高代码的可读性

##### 05.模板

**01.模板基础使用**

WXML提供模板（template），可以在模板中定义代码片段，在不同的地方调用。(是一种wxml代码的复用机制，在小程序不支持组件化时用的较多，现在用的较少) 

* 使用 name 属性，作为模板的名字，然后在 <template/> 内定义代码片段
* 然后通过is指定name名使用

```
// 定义模板
<template name="contentItem">
  <button size='mini'>{{btnText}}</button>
  <view>{{content}}</view>
</template>
// 使用
<template is="contentItem" data="{{btnText: '按钮', content: '哈哈哈'}}"/>
<template is="contentItem" data="{{btnText: '警告', content: '呵呵呵'}}"/>
<template is="contentItem" data="{{btnText: '点击', content: '嘿嘿嘿'}}"/>
<template is="contentItem" data="{{btnText: '登录', content: '嘻嘻嘻'}}"/>
```

**02.模板导入**

> 小程序wxml中提供了两种引入文件的方式：import和include 

1.使用import

* 主要是导入template
* 特点: 不能进行递归导入

```
<import src="/wxml/template.wxml"/>
```

2.使用include

* 将公共的wxml中的组件抽取到一个文件中
* 特点: 不能导入template/wxs, 可以进行递归导入

```
<view>
  <include src="./nav.wxml"/>
  <view>标题</view>
</view>
```

##### 06.wxs模块

**认识wxs**

> WXS（WeiXin Script）是小程序的一套脚本语言，结合 WXML，可以构建出页面的结构

* 官方：WXS 与 JavaScript 是不同的语言，有自己的语法，并不和 JavaScript 一致。（不过基本一致）

**为什么要设计WXS语言呢**

* 在WXML中是不能直接调用Page/Component中定义的函数的. 
* 但是某些情况, 我们可以希望使用函数来处理WXML中的数据(类似于Vue中的过滤器)，这个时候就使用WXS了 

**WXS使用的限制和特点 **

*  WXS 的运行环境和其他 JavaScript 代码是隔离的，WXS 中不能调用其他 JavaScript 文件中定义的函数，也 不能调用小程序提供的API。
* WXS 函数不能作为组件的事件回调。 
* 由于运行环境的差异，在 iOS 设备上小程序内的 WXS 会比 JavaScript 代码快 2 ~ 20 倍。在 android 设备 上二者运行效率无差异。

**wxs的写法**

 WXS有两种写法：

* 写在\<wxs>标签中 

```
// wxml
<wxs module = "format">
  var name = "chh"

  module.exports = {
    name: name
  }
</wxs>

<view>{{format.name}}</view> 
```

* 写在以.wxs结尾的文件中

```
// wxs
var name = "chh"
module.exports = {
  name: name
}
// wxml
<wxs src="../../wxs/format.wxs" module="format"/>
<view>{{format.name}}</view> 
```



 \<wxs>标签的属性：

| 属性名 | 类型   | 默认值 | 说明                                                         |
| :----- | :----- | :----- | :----------------------------------------------------------- |
| module | String |        | 当前 `` 标签的模块名。必填字段。                             |
| src    | String |        | 引用 .wxs 文件的相对路径。仅当本标签为**单闭合标签**或**标签的内容为空**时有效。 |

**wxs的应用**

1.保留两位小数

假如服务器放回的数字是有多位小数，我们想对其进行保留两位小数的过滤操作，这个时候你可能会这么做：

```
<view>{{2.2222.toFixed(2)}}</view>
```

当然这是不起作用的，然后你还可能这么做

```
// js
numberToFixed(num) {
	return num.toFixed(num)
}
// wxml
<view>{{numberToFixed(2.2222)}}</view> 
```

当然这也是不起作用的哈

这时候wxs就派上用场了

```
<wxs module = "format">
	function numberToFixed(num) {
		return num.toFixed(2)
	}
	module.exports = {
		numberToFixed: numberToFixed
	}
</wxs>

<view>{{format.numberToFixed(2.2222)}}</view> 
```

注意：

* 不能使用ES6语法，只支持ES5
* 文件类型的引用必须使用相对路径

2.时间搓转换

```
// wxs
function dateFormat(timestamp, format) {
  if (!format) {
    format = "yyyy-MM-dd hh:mm:ss";
  }
  timestamp = parseInt(timestamp * 1000);
  var realDate = getDate(timestamp);
  function timeFormat(num) {
    return num < 10 ? '0' + num : num;
  }
  var date = [
    ["M+", timeFormat(realDate.getMonth() + 1)],
    ["d+", timeFormat(realDate.getDate())],
    ["h+", timeFormat(realDate.getHours())],
    ["m+", timeFormat(realDate.getMinutes())],
    ["s+", timeFormat(realDate.getSeconds())],
    ["q+", Math.floor((realDate.getMonth() + 3) / 3)],
    ["S+", realDate.getMilliseconds()],
  ];

  var regYear = getRegExp("(y+)", "i");
  var reg1 = regYear.exec(format);
  if (reg1) {
    format = format.replace(reg1[1], (realDate.getFullYear() + '').substring(4 - reg1[1].length));
  }
  for (var i = 0; i < date.length; i++) {
    var k = date[i][0];
    var v = date[i][1];

    var reg2 = getRegExp("(" + k + ")").exec(format);
    if (reg2) {
      format = format.replace(reg2[1], reg2[1].length == 1
        ? v : ("00" + v).substring(("" + v).length));
    }
  }
  return format;
}
// js
data: {
    time: 1560598160
}
// wxml
<wxs src="../../wxs/format.wxs" module="format"/>
<view>{{format.dateFormat(time, 'yyyy/MM/dd')}}</view>
```

### 15.事件

#### 01.绑定事件的方式

绑定事件有两种方式，bind/catch，每种绑定事件都有两种写法:

```
<button bindtap='handleBtnClick' size='mini'>按钮</button>
<button bind:tap='handleBtnClick' size='mini'>按钮</button>
<button catch:tap='handleBtnClick' size='mini'>按钮</button>
```

#### 02.常见事件

```
// wxml
<view class='box'
      bind:touchstart="handleTouchStart"
      bind:touchmove="handleTouchMove"
      bind:touchend="handleTouchEnd"
      bind:tap="handleTap"
      bind:longpress="handleLongpress">
</view>
// js
handleTouchStart() {
  console.log('handleTouchStart')
},
handleTouchMove() {
  console.log('handleTouchMove')
},
handleTouchEnd() {
  console.log('handleTouchEnd')
},
handleTap() {
  console.log('handleTap')
},
handleLongpress() {
  console.log('handleLongpress')
},
```

* 当点击一下触发事件：touchstart 、touchend、handleTap
* 当长按时触发事件：touchstart 、handleLongpress、touchend
* 点击移动触发事件：touchstart 、handleTouchMove、touchend

#### 03.事件对象

当某个事件触发时, 会产生一个事件对象, 并且这个对象被传入到回调函数中, 事件对象有哪些常见的属性呢?

| 属性           | 类型    | 说明                                         | 基础库版本                                                   |
| :------------- | :------ | :------------------------------------------- | :----------------------------------------------------------- |
| type           | String  | 事件类型                                     |                                                              |
| timeStamp      | Integer | 事件生成时的时间戳                           |                                                              |
| target         | Object  | 触发事件的组件的一些属性值集合               |                                                              |
| currentTarget  | Object  | 当前组件的一些属性值集合                     |                                                              |
| mark           | Object  | 事件标记数据                                 | [2.7.1](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| detail         | Object  | 事件标记数据                                 |                                                              |
| touches        | Array   | 触摸事件，当前停留在屏幕中的触摸点信息的数组 |                                                              |
| changedTouches | Array   | 触摸事件，当前变化的触摸点信息的数组         |                                                              |

**touches和changedTouches的区别**

* 在touched中不同

* 多手指触摸时不同

**currentTarget和target的区别**

使用下列demo，创建两个嵌套的元素，当点击里层元素时，查看currentTarget和target的信息。

```
// wxml
<view class='outer' id='outer' bindtap='handleOuter'>
  外层的view
  <view class='inner' id='inner' bindtap='handleInner'>内存的view</view>
</view>
// wxss
.outer {
  width: 400rpx;
  height: 400rpx;
  background: red;
  color: white;
}

.inner {
  width: 200rpx;
  height: 200rpx;
  background: blue;
  color: white;
}
// js
handleInner(event) {
  console.log(event)
},
handleOuter(event) {
  console.log(event)
},
```

* currentTarget触发事件的元素
* target产生事件的元素

#### 04.事件参数的传递

* 在元素中通过data-*定义传递的属性
* 在事件对象中的currentTarget.dataset获取

```
// wxml
<view class='container'>
  <block wx:for="{{titles}}" wx:key="{{index}}">
    <view class='item' 
          bindtap='handleItemClick'
          data-index="{{index}}"
          data-item="{{item}}">
      {{item}}
    </view>
  </block>
</view>
// js
handleItemClick(event) {
    console.log(event)
    // title - index
    const dataset = event.currentTarget.dataset;
    const title = dataset.item;
    const index = dataset.index;
    console.log(title, index)
},
```

#### 05.事件冒泡和捕获

当界面产生一个事件时，事件分为了捕获阶段和冒泡阶段。

* 事件冒泡：事件不仅仅在具体元素上触发，而是会沿着元素向外传递
* 事件捕获：事件不仅仅在具体元素上触发，而是会沿着元素向内传递，直到触发事件具体的元素身上

demo:

```
// wxml
<view class='view1' capture-bind:tap="handleCaptureView1" bindtap='handleBindView1'>
  <view class='view2' capture-bind:tap="handleCaptureView2" catchtap='handleBindView2'>
    <view class='view3' capture-bind:tap="handleCaptureView3" bindtap='handleBindView3'></view>
  </view>
</view>
// js
// ----------  事件冒泡和事件捕获
handleCaptureView1() {
  console.log('handleCaptureView1')
},
handleBindView1() {
  console.log('handleBindView1')
},
handleCaptureView2() {
  console.log('handleCaptureView2')
},
handleBindView2() {
  console.log('handleBindView2')
},
handleCaptureView3() {
  console.log('handleCaptureView3')
},
handleBindView3() {
  console.log('handleBindView3')
}
```

* 我们可以通过capture-bind:tap获取捕获阶段的事件，通过bindtap获取冒泡阶段的事件，通过catchtap终止事件冒泡，但是此事件依然会触发

### 16.组件化开发

#### 01.什么是组件化？

* 人面对复杂问题的处理方式： 
  * 任何一个人处理信息的逻辑能力都是有限的
  * 所以，当面对一个非常复杂的问题时，我们不太可能一次性搞定一大 堆的内容。
  * 但是，我们人有一种天生的能力，就是将问题进行拆解。 
  * 如果将一个复杂的问题，拆分成很多个可以处理的小问题，再将其放 在整体当中，你会发现大的问题也会迎刃而解。 

* 组件化也是类似的思想：
  * 如果我们将一个页面中所有的处理逻辑全部放在一起，处理起来 就会变得非常复杂，而且不利于后续的管理以及扩展。
  * 但如果，我们讲一个页面拆分成一个个小的功能块，每个功能块 完成属于自己这部分独立的功能，那么之后整个页面的管理和维 护就变得非常容易了。

#### 02.创建一个自定义组件

* 类似于页面，自定义组件由 json wxml wxss js 4个文件组成。 
  * 按照我的个人习惯, 我们会先在根目录下创建一个文件夹 components, 里面存放我们之后自定义的公共组件. 
  *  常见一个自定义组件 my-cpn: 包含对应的四个文件. 

* 自定义组件的步骤： 
  * 1.首先需要在 json 文件中进行自定义组件声明（将 component 字段设为 true 可这一组文件设为自定义组件）
  * 2.在wxml中编写属于我们组件自己的模板 
  * 3.在wxss中编写属于我们组件自己的相关样式 
  *  4.在js文件中, 可以定义数据或组件内部的相关逻辑(后续我们 再使用)

```
// 组件文件 .json
"component": true //一般会自动加上
// 组件文件 .wxml
<text>我的自定义组件</text>
// 引用页面 .json
"usingComponents": {
    "my-com": "/components/my-cpn/my-cpn"
}
// 引用页面 .wxml
<my-com></my-com>
```

#### 03.使用自定义组件和细节注意事项

1. 因为 WXML 节点标签名只能是 小写字母、中划线和下划线 的组合，所以自定义组件的标签名也只能包含这些 字符。
2. 自定义组件也是可以引用自定义组件的，引用方法类似于页面引用自定义组件的方式（使用 usingComponents 字段）。 
3. 自定义组件和页面所在项目根目录名 不能以“wx-”为前缀，否则会报错。 
4. 如果在app.json的usingComponents声明某个组件，那么所有页面和组件可以直接使用该组件。

#### 04.组件的样式

◼ 课题一：组件内的样式 对 外部样式 的影响 

* 结论一：组件内的class样式，只对组件wxml内的节点生效, 对于引用组件的Page页面不生效。 
* 结论二：组件内不能使用id选择器、属性选择器、标签选择器 

◼ 课题二：外部样式 对 组件内样式 的影响 

*  结论一：外部使用class的样式，只对外部wxml的class生效，对组件内是不生效的 
* 结论二：外部使用了id选择器、属性选择器不会对组件内产生影响 
* 结论三：外部使用了标签选择器，会对组件内产生影响

◼ 整体结论: 

* 组件内的class样式和组件外的class样式, 默认是有一个隔离效果的； 
* 为了防止样式的错乱，官方不推荐使用id、属性、标签选择器

**如何让组件样式相互影响**

* 在Component对象中，可以传入一个options属性，其中options属性中有一个styleIsolation（隔离）属性。 styleIsolation有三个取值： 
  * -isolated 表示启用样式隔离，在自定义组件内外，使用 class 指定的样式将不会相互影响（默认取值）； 
  * apply-shared 表示页面 wxss 样式将影响到自定义组件，但自定义组件 wxss 中指定的样式不会影响页面； 
  * shared 表示页面 wxss 样式将影响到自定义组件，自定义组件 wxss 中指定的样式也会影响页面和其他设置 

#### 05.组件和页面的通信

##### 01.传递数据和样式

```
// 组件 wxml
<view class='title titleclass'>{{title}}</view>
<view class='content'>我是组件的内容</view>
// 组件 js
Component({
  properties: {
    // title: String
    title: {
      type: String,
      value: '我是默认的标题',
      observer: function(newVal, oldVal) {
        console.log(newVal, oldVal)
      }
    }
  },
  externalClasses: ['titleclass']
})

// 页面 .wxml
<my-prop title="哈哈哈" titleclass="red"/>
<my-prop title="呵呵呵" titleclass="green"/>
// 页面 .wxss
.red {
  color: red;
}
.green {
  color: green;
}
```

##### 02.自定义事件传递数据

子组件可以通过this.triggerEvent(eventName, myEventDetail, myEventDetail)通知父组件/页面事件触发，也可以传递数据。其中eventName表示自定义事件名，myEventDetail为对象，表示传递的数据。myEventDetail表示触发事件的选项，包括：

| 选项名       | 类型    | 是否必填 | 默认值 | 描述                                                         |
| :----------- | :------ | :------- | :----- | :----------------------------------------------------------- |
| bubbles      | Boolean | 否       | false  | 事件是否冒泡                                                 |
| composed     | Boolean | 否       | false  | 事件是否可以穿越组件边界，为false时，事件将只能在引用组件的节点树上触发，不进入其他任何组件内部 |
| capturePhase | Boolean | 否       | false  | 事件是否拥有捕获阶段                                         |

用下面这个demo练习下子组件传递事件给页面吧：

```
// 组件 .wxml
<button size='mini' bind:tap="handleIncrement">+1</button>
// 组件 .js
Component({
  methods: {
    handleIncrement() {
      // console.log('---------')
      this.triggerEvent('increment', {name: 'why', age: 18}, {})
    }
  }
})

// 页面 .wxml
<view>当前计数: {{counter}}</view>
<my-event bind:increment="handleIncrement"/>
// 页面 .js
handleIncrement(event) {
  console.log('---------', event)
  this.setData({
    counter: this.data.counter + 1
  })
},
```

#### 03.页面/组件修改组件内数据

假如你在页面/组件中使用组件，想修改组件内数据（data）的时候，可以通过this.selectComponent(类/id名)获取组件对象，然后通过this.setData()修改组件数据

```
// 组件 .wxml
<view>组件内的计数: {{counter}}</view>
// 组件 .js
incrementCounter(num) {
  this.setData({
    counter: this.data.counter + num
  })
}
// 页面 .wxml
<!-- 6.直接选中组件修改数据/调用方法 -->
<button size='mini' bind:tap="handleIncrementCpn">修改组件内的数据</button>
<my-sel class="sel-class" id="sel-id"/>
// 页面 .js
handleIncrementCpn() {
  // 最终目的: 修改my-sel中的counter
  // 1.获取组件对象
  const my_sel = this.selectComponent('.sel-class')
  console.log(my_sel)

  // 2.通过setData修改组件中的数据(不合理)
  // my_sel.setData({
  //   counter: my_sel.data.counter + 20
  // })

  // 3.通过方法对数据进行修改
  my_sel.incrementCounter(10)
},
```

* 不直接通过this.setData修改，而是通过组件内方法来修改组件内数据更为合理。

#### 04.插槽的使用

##### 01.为什么使用插槽

 slot翻译为插槽： 

* 在生活中很多地方都有插槽，电脑的USB插槽，插板当中的电源插槽。 
* 插槽的目的是让我们原来的设备具备更多的扩展性。
*  比如电脑的USB我们可以插入U盘、硬盘、手机、音响、键盘、鼠标等等。

组件的插槽： 

* 组件的插槽也是为了让我们封装的组件更加具有扩展性。
* 让使用者可以决定组件内部的一些内容到底展示什么。 

 栗子：移动网站中的导航栏。

* 移动开发中，几乎每个页面都有导航栏。 
* 导航栏我们必然会封装成一个插件，比如nav-bar组件。
* 一旦有了这个组件，我们就可以在多个页面中复用了。  但是，每个页面的导航是不一样的，这时就需要用插槽根据需求插入对应的内容

##### 02.单插槽的使用

```
// my-slot组件 .wxml
<view>我是组件头部</view>
<slot />
<view>我是组件尾部</view>
// 页面 .wxml
<my-slot>
	<button size="mini">我是插入的按钮</button>
</my-slot>
```

##### 03.多插槽的使用

* 首先要在组件插槽通过name属性表示，使用时通过slot使用
* 其次在组件js文件配置multipleSlots: true（和data平级）

```
// 组件 .wxml
<view>我是mslot组件的开始</view>
<view class='slot1'><slot name="slot1"/></view>
<view class='slot2'><slot name="slot2"/></view>
<view class='slot3'><slot name="slot3"/></view>
<view>我是mslot组件的结尾</view>
// 组件 .js
options: {
    multipleSlots: true
},

// 页面 .wxml
<my-mslot>
    <button size='mini' slot="slot2">我是插入的按钮</button>
    <slider slot="slot1"/>
    <text slot="slot3">哈哈哈哈</text>
</my-mslot>
```

#### 05.Component构造器

`Component` 构造器可用于定义组件，调用 `Component` 构造器时可以指定组件的属性、数据、方法等。

```
Component({

  behaviors: [],
  // 1.父组件传入的数据
  properties: {
    myProperty: { // 属性名
      type: String,
      value: ''
    },
    myProperty2: String // 简化的定义方式
  },
  // 2.自身定义的数据
  data: {}, // 私有数据，可用于模板渲染
  // 3.组件生命周期
  lifetimes: {
    // 生命周期函数，可以为函数，或一个在methods段中定义的方法名
    attached: function () { },
    moved: function () { },
    detached: function () { },
  },

  // 生命周期函数，可以为函数，或一个在methods段中定义的方法名
  attached: function () { }, // 此处attached的声明会被lifetimes字段中的声明覆盖
  ready: function() { },
  // 4.页面生命周期
  pageLifetimes: {
    // 组件所在页面的生命周期函数
    show: function () { },
    hide: function () { },
    resize: function () { },
  },
  // 5.组件的方法
  methods: {
    onMyButtonTap: function(){
      this.setData({
        // 更新属性和数据的方法与更新页面数据的方法类似
      })
    },
    // 内部方法建议以下划线开头
    _myPrivateMethod: function(){
      // 这里将 data.A[0].B 设为 'myPrivateData'
      this.setData({
        'A[0].B': 'myPrivateData'
      })
    },
    _propertyChange: function(newVal, oldVal) {

    }
  }
  // 6.配置组件多插槽
  options: {
    multipleSlots: true
  },
})
```

### 17.网络请求

**01.网络请求入门**

微信提供了专属的API接口,用于网络请求: wx.request(Object object)

| 属性         | 类型                      | 默认值 | 必填 | 说明                                                         | 最低版本                                                     |
| :----------- | :------------------------ | :----- | :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| url          | string                    |        | 是   | 开发者服务器接口地址                                         |                                                              |
| data         | string/object/ArrayBuffer |        | 否   | 请求的参数                                                   |                                                              |
| header       | Object                    |        | 否   | 设置请求的 header，header 中不能设置 Referer。 `content-type` 默认为 `application/json` |                                                              |
| timeout      | number                    |        | 否   | 超时时间，单位为毫秒                                         | [2.10.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| method       | string                    | GET    | 否   | HTTP 请求方法                                                |                                                              |
| dataType     | string                    | json   | 否   | 返回的数据格式                                               |                                                              |
| responseType | string                    | text   | 否   | 响应的数据类型                                               | [1.7.0](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| enableHttp2  | boolean                   | false  | 否   | 开启 http2                                                   | [2.10.4](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| enableQuic   | boolean                   | false  | 否   | 开启 quic                                                    | [2.10.4](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| enableCache  | boolean                   | false  | 否   | 开启 cache                                                   | [2.10.4](https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html) |
| success      | function                  |        | 否   | 接口调用成功的回调函数                                       |                                                              |
| fail         | function                  |        | 否   | 接口调用失败的回调函数                                       |                                                              |
| complete     | function                  |        | 否   | 接口调用结束的回调函数（调用成功、失败都会执行）             |                                                              |

基础使用demo

```
// get请求
wx.request({
	url: 'http://httpbin.org/get',
	method: 'get',
	timeout: 5000,
	success(res) {
		console.log(res)
	},
	fail(res) {
		console.log(res)
	}
})
// post请求,并且携带参数
wx.request({
  url: 'http://httpbin.org/post',
  method: 'post',
  data: {
    name: 'coderwhy',
    age: 18
  },
  success: function(res) {
    console.log(res)
  },
  fail: function(err) {
    console.log(err)
  }
})
```

**02.网络请求的封装**

