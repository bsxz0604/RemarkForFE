# 从前端的角度出发 - web调起APP的

![日常上图.png](https://upload-images.jianshu.io/upload_images/3290028-df09884f65edb0bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 背景
对于APP来说，回流分享页是最好的最便宜的也是最病毒式的拉新方式。让新用户去下载APP是重要的。对老用户来说，可以直接调起APP也是提升用户体验和让用户有侵入式体验的重要手段。所以我们一起来看看有哪些方式可以唤起APP的

### 概念叙述
调起APP在不同平台用不同的方式，主要就分3个
	* URI Scheme
	* universal Link
	* Android App Links
现在还是有很多第三方来协助你处理这个事情，通过接入他们的SDK和客户端代码来处理，但是万变不离其宗，所有的第三方也离不开这3种方式。

1. URI Scheme:
	* URI Scheme 是iOS，Android平台都支持，只需要原生APP开发时注册 scheme ， 用户点击到此类链接时，会自动唤醒APP，借助于 URL Router 机制，则还可以跳转至指定页面。
	* \<scheme name\> : \<hierarchical part\> \[ ? \<query\> \] \[ # \<fragment\> \]
		* <scheme name>：是scheme的名称，代表着协议名称。
		* <hierarchical part>：它包含 authority 和 path。 
		* <query>：可选项目，隔开或&隔开的键值对<key>=<value>
		* <fragmentg> ：可选项目包，其它额外的标识信息
例如：
	git://github.com/user/project-name.git
	ftp://user1:1234@地址
	musically://musical?id=xxxx&key=xxxx
2. universal Link:
	* iOS9 后推出的一项功能，通用链接，对于前端即访问一个https的url,如果这个url带有你提交给开发平台的配置文件中匹配规则的内容，iOS系统会去尝试打开你的app，如果打不开，系统就会在浏览器中转向你要访问的链接。
	* universal Link 工作方式如下：
		1. 访问web link
		2. iOS访问 https://xxxxxxx/apple-app-site-association 并解析，获取文件中的信息(App的Team ID和Bundle ID)
		3. 通过Bundle ID 检查本地是否存在对应app，和检查PATH信息等，如果有app打开app，如果没有则跳转对应web link(可通过代码实现跳去app Stroe)
3. Android App Links:
	* 在2015年的Google I/O大会上，Android M宣布了一个新特性：App Links让用户在点击一个普通web链接的时候可以打开指定APP的指定页面，前提是这个APP已经安装并且经过了验证，否则会显示一个打开确认选项的弹出框，只支持Android M以上系统。
	* 简单的说就是建立APP和某个链接的关联，避免系统在处理该类型链接时弹出选择框。弹框最常见的就是浏览器打开时的选择框。弹出选择框是应用注册了相应scheme，applinks的作用是避免在打开自己域名的链接时弹出选择（前提是注册了相应scheme），可以实现直接打开自己关联的app。
	

### 对比/优劣
![vs.png](https://upload-images.jianshu.io/upload_images/3290028-06dc41494f53c0c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
1.  URI Scheme:
根据图片可以看到URI Scheme的兼容性是最高，在使用的过程中，会发现有很多限制:
	* 当要被唤起的app没有安装时，这个链接就会出错。在国内非常杂乱的浏览器中，会出错的现象会很多种类型。
	* 当注册有多个scheme相同的时候，目前没有办法区分。
	* 不支持从其他app中的UIWebView中跳转到目标app
也就因为会有这些原因，apple和android都出现了自己第二套解决方案。
2. universal Link 从链接上看来，是一个web link，所以也就解决了当没有app时，跳转也不会出现报错，所以相对Scheme优势就提现出来了.
	* 当已经安装app，不需要加载任何web页面，app就会立即启动；app没有安装，就会跳去对应的web link。
	* universal Link 是从服务器上查询是哪个app需要被打开，所以不会存在冲突问题
	* universal Link 支持从其他app中的UIWebView中跳转到目标app
	* 隐私性，提供universal Link给别的app进行app间的交流，然而对方并不能够用这个方法去检测你的app是否被安装。
当然universal Link也不是十全十美的,缺陷也是存在的:
	* 会记住用户的选择：在用户点击了Universal link之后，iOS会去检测用户最近一次是选择了直接打开app还是打开网站。一旦用户点击了这个选项，他就会通过safiri打开你的网站。并且在之后的操作中，默认一直延续这个选择，除非用户从你的webpage上通过点击Smart App Banner上的OPEN按钮来打开。
![记住用户的选择.png](https://upload-images.jianshu.io/upload_images/3290028-dd4e861ed0d13624.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. app link 和 universal Link 差异不大。也是为了更好的提供调起app出现的google的方案。优点与 universal Link 差不多，缺点主要如下：
	* 国内的支持相对较差，在有的浏览器或者手机ROM中并不能链接至APP，而是在浏览器中打开了对应的链接。 
	* 在询问是否用APP打开对应的链接时，如果选择了“取消”并且“记住选择”被勾上，那么下次你再次想链接至APP时就不会有任何反应
![app_link记住用户的选择.png](https://upload-images.jianshu.io/upload_images/3290028-13873a5f4ea698a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

无论哪一种方式目前都没有解决几个问题：
	* 如果设备上没有安装这个app的时候，安装完毕后，无法保留住此时用户停留的上下文。
	* 因为web没有办法监听到APP是否安装，所以都需要通过一些手段来兼容调起app或者是去下载页


### 使用 & 需要注意的内容

1. URI Scheme：
	* 使用:  这种方式是当期使用最广泛，也是最简单的，但是需要手机，APP支持 URI Scheme 。
	*  需要注意的内容 & 遇到的问题：
	其实使用URI Scheme 部分前端没有太多可以排查的问题，会遇到的问题主要是两个部分。1. 在android的兼容性处理（国内的浏览器无力吐槽ing），2. 当没有安装app的情况，URI Scheme 会有各种报错，也需要处理…
```
<!-- 唤醒APP并跳转至指定的path页面 -->
<a href="<scheme>://<path>?<params>=<value>">打开APP</a>

<!-- JS设置iframe src跳转至指定的path页面 -->
//创建一个隐藏的iframe
var ifr = document.createElement('iframe');
ifr.src = '<scheme>://<path>?<params>=<value>';
ifr.style.display = 'none';
document.body.appendChild(ifr);
//记录唤醒时间
var openTime = +new Date();
window.setTimeout(function(){
    document.body.removeChild(ifr);
    //如果setTimeout 回调超过2500ms，则弹出下载
    if( (+new Date()) - openTime > 2500 ){
        window.location = '指定的下载页面';
    }
},2000)
```
2. universal Link & app Links
	* 使用：对于有app的用户，只是打开一个连接，但是需要注意的是需要考虑到没有APP的用户。(个人的解决方案：针对域名来判断，当域名为特定的universal Link 的域名，则跳转去下载页面）
	* 需要注意的内容 & 遇到的问题：
		1. apple-app-site-association 和  assetlinks.json 的配置
		2. 需要保证使用的链接跨域(universal Link)
		3. 直接将universal Link 贴入浏览器的url中不会生效
		4. window.onload 或者用户没有任何事件触发的情况下，universal Link也不会生效
	

### 两大平台的特殊处理(facebook & twitter)
facebook 和 twitter 作为国外的两大信息聚合平台，对于在他们app中调起app也有自己的一套方式。
根据要求通过添加META头来处理打开APP
facebook:
```
<meta property="fb:app_id" content="xxxxxx" />
<meta property="og:type" content="xxxx"/>
<meta property="og:title" content="xxx" />
<meta property="al:ios:url" content="{{ uri scheme }}" />
<meta property="al:android:url" content="{{ uri scheme }}" />
<meta property="al:ios:app_store_id" content="{{app_store_id}}" />
<meta property="al:ios:app_name" content="{{xxx}}" />
<meta property="al:android:app_name" content="{{xxx}}" />
<meta property="al:android:package" content="{{android:package}}" />
``` 
twitter:
```
<meta name="twitter:card" content="app" />
<meta name="twitter:site" content="xxxxx" />
<meta name="twitter:title" content="xxxxx" />
<meta name="twitter:description" content="xxxxxxx" />
<meta name="twitter:image" content="xxxx" />
<meta name="twitter:app:name:iphone" content="xxx">
<meta name="twitter:app:id:iphone" content="xxx">
<meta name="twitter:app:url:iphone" content="{{Scheme}}">
<meta name="twitter:app:name:ipad" content="xxx">
<meta name="twitter:app:id:ipad" content="xxx">
<meta name="twitter:app:url:ipad" content="{{Scheme}}">
<meta name="twitter:app:name:googleplay" content="xxx">
<meta name="twitter:app:id:googleplay" content="xxx">
<meta name="twitter:app:url:googleplay" content="{{Scheme}}">
```

###  使用 checkList(前端)
1. scheme
	- iOS 和 android 是否已经支持 此scheme	
	- js处理兼容代码

2. universal Link ([apple-app-site-association 官方文档](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1))
	- HTTPS的域名
	- iOS9 以上
	- universal Link 是否跨域
	- universal Link的落地页是否是下载页面
	- apple-app-site-association 配置在 host的根目录和.well-known下
		* 官方检测: [apple-app-site-association 检测](https://search.developer.apple.com/appsearch-validation-tool/)
	- apple-app-site-association会在第一次打开app或者更新app时候会去拉去,所以确认是否更新了apple-app-site-association后没有更新过app
	- 检查apple-app-site-association   paths 大小写敏感 支持通配符
	- 该设备的用户选择了直接打开app还是打开网站，如果选择打开网站，需要通过smart banner 重新启用
	- 跳转处理是否是在用户事件中触发，而不是进入页面后直接触发

3.  app links ([android app links官方文档](https://developer.android.com/training/app-links/))
	- HTTPS的域名
	- 	跳转后的落地页是否是下载页面
	- assetlinks.json 配置在 host的.well-known下
		* 官方生成/检测: [android app links检测](https://developers.google.com/digital-asset-links/tools/generator)

4. facebook ([facebook app link官方文档]( https://developers.facebook.com/docs/applinks))
		- 将需要的meta头信息填充完毕
		- 检测链接 [分享调试器 - Facebook for Developers](https://developers.facebook.com/tools/debug/sharing/) , 确认分享链接中获取到了所需要的meta头
		- 分享过的链接会有缓存，在检测中清楚缓存
		- 如果web和wap链接一致，确认在web中也添加了相同的meta头，facebook会默认从web中获取

5. twitter ([Twitter app card官方文档]( https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/app-card))
		- 将需要的meta头信息填充完毕
		- 检测链接 [Twitter app card 检测](https://cards-dev.twitter.com/validator)
		- 如果web和wap链接一致，确认在web中也添加了相同的meta头，facebook会默认从web中获取
		
### 链接
1. [webview如何屏蔽universal Link](https://stackoverflow.com/questions/38450586/prevent-universal-links-from-opening-in-wkwebview-uiwebview)
2. [apple-app-site-association 官方文档](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1)
3. [apple-app-site-association 检测](https://search.developer.apple.com/appsearch-validation-tool/)
4. [android app links官方文档](https://developer.android.com/training/app-links/)
5. [android app links检测](https://developers.google.com/digital-asset-links/tools/generator)
6. [facebook app link官方文档]( https://developers.facebook.com/docs/applinks)
7. [分享调试器 - Facebook for Developers](https://developers.facebook.com/tools/debug/sharing/) 
8. [Twitter app card官方文档]( https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/app-card)
9. [Twitter app card 检测](https://cards-dev.twitter.com/validator)

