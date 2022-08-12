<!-- TOC -->

- [性能优化](#性能优化)
  - [参考资料](#参考资料)
  - [HTTP 缓存](#http-缓存)
    - [HTTP 缓存的相关首部字段](#http-缓存的相关首部字段)
    - [缓存命中和未命中](#缓存命中和未命中)
    - [新鲜度检测](#新鲜度检测)
      - [是否过期](#是否过期)
      - [服务器再验证](#服务器再验证)
    - [缓存（副本）会储存在哪里](#缓存副本会储存在哪里)
    - [私有缓存和公有缓存](#私有缓存和公有缓存)
    - [缓存的处理流程](#缓存的处理流程)
    - [强缓存和协商缓存](#强缓存和协商缓存)
    - [用户刷新/访问方式](#用户刷新访问方式)
    - [from ServiceWorker、from disk cache 与 from memory cache](#from-serviceworkerfrom-disk-cache-与-from-memory-cache)
    - [使用 express 来验证](#使用-express-来验证)
    - [参考资料](#参考资料)
  - [前端性能优化的方法](#前端性能优化的方法)
  - [gzip](#gzip)

<!-- /TOC -->
# 性能优化

## 参考资料

- [Web开发的各种性能工具](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651551342&idx=1&sn=80571f7f6fc11f55bf6d04c812e3a0bb&chksm=8025a1afb75228b9e0b1a057f4dbffe896d32805ad03719dac781894a7046aeb7bc49bc71c0c&scene=0&key=cc7d6364edc5eb3ef56772becb427538fa84605fe0766a293c436a943e3924573fa6866883fdd7ab575a9092deaf27e80515ae63a8516b18c57f6f045fc2db86607628c4ebae2dcff8ab60e2a132bb55&ascene=14&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=ZoqXnMgi0AAEna540iG5qPugeLgO5W7T7qkyPIntCT9Pu1xlW6JZWbtLnqd%2FcXea)
- [如何增强单页应用的体验](https://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=2651551146&idx=2&sn=6c52bae8df63c20cbad4df66ae1d6214&scene=0&key=84de4b77008f9e09b297db1dfef96925d8b863c63ed7a5e8bc4af3b8c8c9f8075727f77db7e8eeede79057dec53ea6179dc9aac9650607c2bb2dfc3466e2a04838178a4aff82ff96397fe5a948fef842&ascene=14&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=ZoqXnMgi0AAEna540iG5qPugeLgO5W7T7qkyPIntCT9Pu1xlW6JZWbtLnqd%2FcXea)
- [【未读】Web前端性能优化进阶——完结篇](https://www.cnblogs.com/MarcoHan/p/5297798.html)
- [【未读】从输入 URL 到展现涉及哪些缓存环节(非常详细)](https://mp.weixin.qq.com/s?__biz=MzA5NzkwNDk3MQ==&mid=2650589480&idx=1&sn=8ca0cc5ced48ba0d465283fe4a01eaac&chksm=8891d90cbfe6501a2e4df23eff5cb2be33d1b8aa5a3fd7f67ac4b7f24fe44716a18b7db0921f&scene=0&xtrack=1&key=00a8e91eefd868fcec8a9a88d2833741f9ce5d6929e71d82f811bc0e967646d878b5ba4312df29ff5c3de01e15384cb547570e56cd58b49288137b3d939cbe6fb9938d92db25976282aac39b11a9099e&ascene=14&uin=MjcwNDcxNzQyMQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=ZoqXnMgi0AAEna540iG5qPugeLgO5W7T7qkyPIntCT9Pu1xlW6JZWbtLnqd%2FcXea)
- [【阮一峰】网页性能管理详解](http://www.ruanyifeng.com/blog/2015/09/web-page-performance-in-depth.html)
- [（译）2019年前端性能优化清单 — 上篇](https://juejin.cn/post/6844903765682683911)

## HTTP 缓存

> Web缓存是可以自动保存常见文档副本的HTTP设备。当Web请求抵达缓存时，如果本地有“已缓存的”副本，就可以从本地存储设备而不是原始服务器中提取这个文档。 《HTTP权威指南》

- 节省网络费用。
- 加快页面的加载速度。
- 降低服务器的压力。

### HTTP 缓存的相关首部字段

通用首部字段（能分别在请求报文和响应报文中使用）

- Cache-Control （常用，设置是否开启缓存和使用时间）
- Pragma （设置是否开启缓存。过时，为了兼容低版本）

> 优先级从高到低分别是 Pragma -> Cache-Control -> Expires

请求首部字段

- if-Match
- if-None-Match （常用）
- if-Modified-Since （常用）
- if-Unmodified-Since

响应首部字段

- Etag （常用，用于服务器再验证，给服务器对象设置一个唯一的标识）

实体首部字段（请求和响应中的实体部分所使用的首部）

- Expires （设置过期时间。过时，为了兼容低版本）
- Last-Modified （常用，资源最后一次修改的时间）

> 由于请求和响应报文中都可能包含实体部分，所以在这两种类型的报文中都可能出现实体首部。——《HTTP权威指南》

### 缓存命中和未命中

![缓存命中和未命中](./images/%E7%BC%93%E5%AD%98%E5%91%BD%E4%B8%AD%E5%92%8C%E6%9C%AA%E5%91%BD%E4%B8%AD.png)

缓存命中（cache hit），有找到缓存副本就是命中，没有找到缓存副本就是未命中。

### 新鲜度检测

> 检测缓存副本跟服务器对象是否相同。

包括两点：

1. **是否过期**，没有过期说明足够新鲜。
2. **再验证**。判断文档是否修改过。因为过期的文件不一定修改过。

浏览器表现形式：

1. 如果没有过期则不发送请求，直接从本地缓存取数据。（在chrome下表现为 200 from cache）
2. 如果过期，则进行服务器再验证。
    - 服务器对象跟缓存副本一样。（在chrome下表现为 304 Not Modified，**实体为空**）
    - 服务器对象跟缓存副本不一样。（在chrome下表现为 200 OK，**有实体返回**）


#### 是否过期

使用 `Cache-Control:max-age` 首部和 `Expires` 首部来设定过期日期，同时还会带有响应主体。

- `Expires` 设置**过期时间**。是一个绝对时间，以秒为单位。
    - 服务器时间跟客户端时间不一致时，过期时间会失效，所以用 `Cache-Control:max-age` 来解决这个问题。
- `Cache-Control:max-age` 设置**使用时间**，是一个相对时间，以秒为单位。

![缓存是否过期](./images/%E7%BC%93%E5%AD%98%E6%98%AF%E5%90%A6%E8%BF%87%E6%9C%9F.png)

- 服务器返回响应数据时会在 Response Headers 中加上 `Last-Modified` 或 `expires`  首部，两者可以同时设置，值为最后一次修改时间。
- 客户端在发送请求时会在 Request Headers 中加上 `If-Modified-Since` 首部，值是 `Last-Modified` 的值。
- 判断 `If-Modified-Since` 是否过期。
    - 没有过期。不发送请求，直接从缓存副本中拿数据。
    - 过期。发请求，进行服务器再验证。

> 需要注意的是，响应报文中Expires所定义的缓存时间是相对服务器上的时间而言的，其定义的是资源“失效时刻”，如果客户端上的时间跟服务器上的时间不一致（特别是用户修改了自己电脑的系统时间），那缓存时间可能就没啥意义了。

> 针对上述的“Expires时间是相对服务器而言，无法保证和客户端时间统一”的问题，http1.1新增了 Cache-Control 来定义缓存过期时间。注意：若报文中同时出现了 Expires 和 Cache-Control，则以 Cache-Control 为准。

用时间来验证缓存副本的新鲜度有两个问题：

- 时间不新鲜，但是服务器资源没修改。
- 只能精确到秒。所以无法处理1秒内多次修改文件的情况。

所以需要进行服务器再验证。

#### 服务器再验证

> 只有在文档过期时才需要与服务器进行再验证。

![缓存服务器再验证](./images/%E7%BC%93%E5%AD%98%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%86%8D%E9%AA%8C%E8%AF%81.png)

- 再验证命中，返回304，服务器对象跟缓存副本相同。
- 再验证未命中，返回200，服务器对象跟缓存副本不相同，返回新的服务器对象，保存到缓存。
- 再验证对象被删除，返回404，缓存也将其副本删除。

![缓存服务器再验证1](./images/%E7%BC%93%E5%AD%98%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%86%8D%E9%AA%8C%E8%AF%811.png)

- 再验证是一定会发请求。
- 服务器返回响应数据时会在 Response Headers 中加上 `Etag`首部，值为一个唯一的标识符（MD5）或者唯一标识组合(`"V2.4","V2.5","V2.6"`)。
- 客户端在发送请求时会在 Request Headers 中加上 `If-None-Match` 首部，值是 `Etag` 的值。
- 服务器判断 `If-None-Match` 和服务器端最新的唯一表示是否一致。
    - 一致。返回304，没有实体返回。
    - 不一致。返回200，有实体返回。
    - 没有找到资源。返回404，客户端删除缓存副本。


### 缓存（副本）会储存在哪里

> 本地副本可能存储在内存、本地磁盘，甚至附近的另一台计算机中。——《HTTP权威指南》

### 私有缓存和公有缓存

- 私有缓存，浏览器缓存将文档存放在磁盘或者内存当中。
- 公有缓存，使用代理服务器来保存文档。公有缓存会接受来自多个用户的访问，所以通过它可以更好地减少冗余流量。
### 缓存的处理流程

![简化缓存请求流程](./images/%E7%AE%80%E5%8C%96%E7%BC%93%E5%AD%98%E8%AF%B7%E6%B1%82%E6%B5%81%E7%A8%8B.png)

### 强缓存和协商缓存

《HTTP权威指南》没有强缓存和协商缓存的概念，但是网上能搜到。

- 强制缓存就是只使用 `Expires` 或 `Cache-Control` 进行时间过期验证。
- 协议缓存就是结合时间过期验证和服务器再验证。
- 强制缓存优先级高于协商缓存。

所以这个定义有啥意义吗？不懂

当浏览器要请求资源时

1. 调用 Service Worker 的 fetch 事件响应
2. 查看 memory cache
3. 查看 disk cache。这里又细分：
    1. 如果有强制缓存且未失效，则使用强制缓存，不请求服务器。这时的状态码全部是 200
    2. 如果有强制缓存但已失效，使用对比缓存，比较后确定 304 还是 200

浏览器

1. 发送网络请求，等待网络响应
2. 把响应内容存入 disk cache (如果 HTTP 头信息配置可以存的话)
3. 把响应内容 的引用 存入 memory cache (无视 HTTP 头信息的配置)
4. 把响应内容存入 Service Worker 的 Cache Storage (如果 Service Worker 的脚本调用了 cache.put())

### 用户刷新/访问方式

用户不同方式刷新页面会有不同的效果：

- 在URI输入栏中输入然后回车/通过书签访问。
    - 如果没有缓存没有过期，则不会发请求。
- F5/点击工具栏中的刷新按钮/右键菜单重新加载。
    - 无论如何都会给服务器发送请求。请求头上会带上 `If-Modified-Since`或者 `If-None-Match` 首部字段。所以不一定有实体返回。
- Ctl+F5
    - 会在 请求头上加上 `Cache-Control: no-cache` 和 `Pragma: no-cache` 两个首部，所以一定会从服务器获取最新的数据，一定有实体返回。

### from ServiceWorker、from disk cache 与 from memory cache
- 细节参考：[from disk cache 与 from memory cache](https://www.cnblogs.com/jpfss/p/9242797.html)
- [一文读懂前端缓存](https://zhuanlan.zhihu.com/p/44789005)

它们的优先级是：(由上到下寻找，找到即返回；找不到则继续)

1. Service Worker
2. Memory Cache
3. Disk Cache
4. 网络请求

### 使用 express 来验证

- 一定要使用工具（express+node或Fiddler）来验证。详细验证过程参考：[前端也要懂Http缓存机制](https://juejin.cn/post/6844903655619969038)
- Fiddler 抓包工具

### 参考资料
- [HTTP缓存控制小结](https://imweb.io/topic/5795dcb6fb312541492eda8c)。这篇文章讲解了跟缓存相关的首部字段，简单易懂。
- 《HTTP权威指南》作为权威，其他资料作为辅助。
- [前端缓存最佳实践](https://zhuanlan.zhihu.com/p/52916788)


## 前端性能优化的方法

- 减少http请求次数：CSS Sprites, JS、CSS源码压缩、图片大小控制合适；网页Gzip，CDN托管，data缓存 ，图片服务器。
- 前端模板 JS+数据，减少由于HTML标签导致的带宽浪费，前端用变量保存AJAX请求结果，每次操作本地变量，不用请求，减少请求次数
- 用innerHTML代替DOM操作，减少DOM操作次数，优化javascript性能。
- 当需要设置的样式很多时设置className而不是直接操作style。
- 少用全局变量、缓存DOM节点查找的结果。减少IO读取操作。
- 避免使用CSS Expression（css表达式)又称Dynamic properties(动态属性)。
- 图片预加载，将样式表放在顶部，将脚本放在底部  加上时间戳。
- 避免在页面的主体布局中使用table，table要等其中的内容完全下载之后才会显示出来，显示比div+css布局慢。

对普通的网站有一个统一的思路，就是尽量向前端优化、减少数据库操作、减少磁盘IO。向前端优化指的是，在不影响功能和体验的情况下，能在浏览器执行的不要在服务端执行，能在缓存服务器上直接返回的不要到应用服务器，程序能直接取得的结果不要到外部取得，本机内能取得的数据不要到远程取，内存能取到的不要到磁盘取，缓存中有的不要去数据库查询。减少数据库操作指减少更新次数、缓存结果减少查询次数、将数据库执行的操作尽可能的让你的程序完成（例如join查询），减少磁盘IO指尽量不使用文件系统作为缓存、减少读写文件次数等。程序优化永远要优化慢的部分，换语言是无法“优化”的。

## gzip

**参考资料**：

[前端性能优化之gzip](https://segmentfault.com/a/1190000012571492)
