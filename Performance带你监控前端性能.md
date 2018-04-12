我们先看到的是navigation timing监测指标图,是HTML5提供的一组新API，已经在很多主流的浏览器中实现了,最后会有图说明支持的浏览器(不想承认IE是主流浏览器ing….

![RequestToDoWhat.jpg](https://upload-images.jianshu.io/upload_images/3290028-154cd7733a0a5486.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![navigationTime.png](https://upload-images.jianshu.io/upload_images/3290028-66f0a4ffe6b5a2e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到浏览器处理请求会经历了下面这些阶段：重定向→拉取缓存→DNS查询→建立TCP链接→发起请求→接收响应→处理HTML元素→元素加载完成，而这些时间数据都会Performance对象中体现出来

按触发顺序排列所有属性,详细的可以查看[Navigation Timing](https://www.w3.org/TR/navigation-timing/)
### 属性
```
window.performance //返回一个Performance对象

interface Performance {
  readonly attribute PerformanceTiming timing;
  readonly attribute PerformanceNavigation navigation;
};

interface PerformanceTiming {
  readonly attribute unsigned long long navigationStart;
  readonly attribute unsigned long long unloadEventStart;
  readonly attribute unsigned long long unloadEventEnd;
  readonly attribute unsigned long long redirectStart;
  readonly attribute unsigned long long redirectEnd;
  readonly attribute unsigned long long fetchStart;
  readonly attribute unsigned long long domainLookupStart;
  readonly attribute unsigned long long domainLookupEnd;
  readonly attribute unsigned long long connectStart;
  readonly attribute unsigned long long connectEnd;
  readonly attribute unsigned long long secureConnectionStart;
  readonly attribute unsigned long long requestStart;
  readonly attribute unsigned long long responseStart;
  readonly attribute unsigned long long responseEnd;
  readonly attribute unsigned long long domLoading;
  readonly attribute unsigned long long domInteractive;
  readonly attribute unsigned long long domContentLoadedEventStart;
  readonly attribute unsigned long long domContentLoadedEventEnd;
  readonly attribute unsigned long long domComplete;
  readonly attribute unsigned long long loadEventStart;
  readonly attribute unsigned long long loadEventEnd;
};

interface PerformanceNavigation {
  const unsigned short TYPE_NAVIGATE = 0; //普通进入，包括：点击链接、在地址栏中输入 URL、表单提交、或者通过除下表中 TYPE_RELOAD 和 TYPE_BACK_FORWARD 的方式初始化脚本
  const unsigned short TYPE_RELOAD = 1; // 通过刷新进入，包括：浏览器的刷新按钮、快捷键刷新、location.reload()等方法。
  const unsigned short TYPE_BACK_FORWARD = 2; //通过操作历史记录进入，包括：浏览器的前进后退按钮、快捷键操作、history.forward()、history.back()、history.go(num)
  const unsigned short TYPE_RESERVED = 255; // 其他
  readonly attribute unsigned short type;
  readonly attribute unsigned short redirectCount; //表示到达最终页面前，重定向的次数，但是这个接口有同源策略限制，即仅能检测同源的重定向。
}
```

由于这些内容都差不多是字面意思.而且W3C上解释的很清楚,所以就不展开了(其实就是我懒ORZ…当然.为了不浪费大家时间这个….

*列出PerformanceTiming中一些常用的计算*:
* DNS查询耗时 ：domainLookupEnd - domainLookupStart
* TCP链接耗时 ：connectEnd - connectStart
* request请求耗时 ：responseEnd - responseStart
* 解析dom树耗时 ： domComplete - domInteractive
* 白屏时间 ：responseStart - navigationStart
* domready时间(用户可操作时间节点) ：domContentLoadedEventEnd - navigationStart
* onload时间(总下载时间) ：loadEventEnd - navigationStart

在*PerformanceNavigation*中的属性查看上面接口代码后的注释,暂时在工作中没有使用到
ps: 如果你使用的是chrome, *MemoryInfo*是一个非标准属性
	* jsHeapSizeLimit: 内存大小限制
	* totalJSHeapSize: 可使用的内存
	* usedJSHeapSize: JS对象(包括V8引擎内部对象)占用的内存，不能大于totalJSHeapSize，如果大于，有可能出现了内存泄漏
	
### 方法
* 获取所有资源请求的时间数据,这个函数返回一个按startTime排序的对象数组
getEntries:  ƒ getEntries() 
* 返回值仍是一个数组,这个数组相当于getEntries()方法经过所填参数筛选后的一个子集
getEntriesByName:  ƒ getEntriesByName( name, type[optional] )
getEntriesByType:  ƒ getEntriesByType(type)
* 用于做标记和清除标记，供用户自定义统计一些数据，比如某函数运行耗时等
mark:  ƒ mark()
measure:  ƒ measure()
clearMarks:  ƒ clearMarks()
clearMeasures:  ƒ clearMeasures()
* 无返回值，可以清除目前所有entryType为"resource"的数据,用于单页应用的统计脚本非常有用
clearResourceTimings:  ƒ clearResourceTimings()
* 是当前时间与performance.timing.navigationStart的时间差,以微秒（百万分之一秒）为单位的时间，与 Date.now()-performance.timing.navigationStart的区别是不受系统程序执行阻塞的影响，因此更加精准
now:  ƒ now()

## 总结
Performance用来做前端性能监控，就2个字形容:利器...
当然这只是前端性能优化的第一步（性能的路上我也只看到了冰山一角），道阻且长 ~ 所以希望大家提出问题和指出疑问，一起进步~
