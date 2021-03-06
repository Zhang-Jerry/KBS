## HTTP缓存的分类

分为强制缓存（也称强缓存）和协商缓存。强缓存，在缓存失效时间之前，都不会再向服务端请求数据；协商缓存，每次请求都需要进行比较判断是否可以使用缓存。    
两种缓存规则可以同时存在，但强缓存优先级高于协商缓存，即有强缓存机制时，协商缓存机制将失效。

## 强缓存

相关头字段按优先级排序：

| 字段 | 归属 | 常用值 | 含义 | 其它
|--------|--------|--------|--------|--------
| Pragma | 响应头 | no-cache（可以使cache-control的设置失效） | 不使用缓存，每次都向服务器请求 | http1.0，不常用 
| Cache-Control | 请求/响应头 | no-cache/max-age=10 | 控制缓存策略/设置缓存有效时间长度 | http1.1 
| Expires | 请求/响应头 | GMT格式时间 | 缓存过期时间截点(服务端时间，精确到1s) | http1.0

缺点：缓存过期后，无论资源是否变化，都要重新发送；

## 协商缓存

相关头字段：

| 字段 | 归属 | 常用值 | 含义 | 其它
|--------|--------|--------|--------|--------
| Etag | 响应头 | 资源内容的唯一标识（指纹机制） | 资源内容不变，该标识就不变 | 对应请求头的 If-None-Match ，http1.1
| If-None-Match | 请求头 | Etag的值 | 用于服务端判断资源是否有变化 |  http1.1
| Last-Modified | 响应头 | GMT格式时间 | 最新资源变化时间 | 对应请求头的 If-Modified-Since  http1.0
| If-Modified-Since | 请求头 | Last-Modified的值 | 用于服务端判断资源是否有变化 | http1.0

注：Etag/If-None-Match 优先级高于 Last-Modified/If-Modified-Since

缺点：每次都要发送请求。

## 最佳方案

浏览器渲染页面过程中，先请求 html 文件，遇到 link、script再去加载 css、js 文件。

可以通过为 css、js 请求添加版本号或 MD5hash，来控制缓存，具体方案为：   
1. html 文件，采用协商缓存(Etag)方式，每次渲染都发送请求，如果文件无变化则使用缓存；
2. css、js 采用强缓存方式，仅在版本号或 MD5hash变化时，才重新请求资源。