## 建静态资源服务器
借助koa搭建静态资源服务器，验证的逻辑主要在这里:
```javascript
// ...
if (mime.includes('image/') || mime.includes('video/')) {
  ctx.res.writeHead(200);
  ctx.res.write(content, 'binary');
  ctx.res.end();
} else if (mime.includes('text/javascript')) {
  const maxAge = 50; // s
  ctx.res.writeHead(200, {
    'cache-control': `max-age=${maxAge}`,
  });
}
ctx.body = content;
// ...
```
## expires
指定资源的过期时间，在过期时间之前，使用`memory cahce`:
```javascript
// 过期时间
const expires = new Date('2019/1/23 11:50:00');

ctx.res.writeHead(200, {
  'expires': expires,
});
```
![desc](https://img.alicdn.com/tfs/TB1FfzdDCrqK1RjSZK9XXXyypXa-1122-680.jpg)

但是缺陷也非常明显，当过了这个期限后，需要重制expires字段，否则不会再从缓存读取：
![desc](https://img.alicdn.com/tfs/TB19UrmDAvoK1RjSZFNXXcxMVXa-1146-574.jpg)

## cache-control
和expires相反，cache-control指定的是资源的有效时间，既从第一次请求到开始，在cache-control指定的时间内，均从缓存获取数据。

常见的取值有private、no-cache、max-age、must-revalidate等，默认为private。其作用根据不同的重新浏览方式分为以下几种情况：

| 常用的取值 | 说明 |
| ------ | ------ |
| private（default） | 内容只缓存到私有缓存中(仅客户端可以缓存，代理服务器不可缓存) |
| public	 | 所有内容都将被缓存(客户端和代理服务器都可缓存) |
| no-cache | 必须先与服务器确认返回的响应是否被更改，然后才能使用该响应来满足后续对同一个网址的请求。因此，如果存在合适的验证令牌 (ETag)，no-cache 会发起往返通信来验证缓存的响应，如果资源未被更改，可以避免下载。 |
| no-store | 所有内容都不会被缓存到缓存或 Internet 临时文件中 |
| must-revalidation/proxy-revalidation | 如果缓存的内容失效，请求必须发送到服务器/代理以进行重新验证 |
| max-age=xxx (number, seconds) | 缓存的内容将在 xxx 秒后失效, 这个选项只在HTTP 1.1可用, 并如果和Last-Modified一起使用时, 优先级较高 |

## cache-control: max-age
```javascript
const maxAge = 10; // s

ctx.res.writeHead(200, {
  'cache-control': `max-age=${maxAge}`,
});
```
```max-age```的单位是```s```，即该资源的缓存有效期是10s，第一次请求：
![第一次请求](https://img.alicdn.com/tfs/TB10n2kDxTpK1RjSZR0XXbEwXXa-1814-94.jpg)

缓存时间内请求:

![缓存时间内请求](https://img.alicdn.com/tfs/TB1JMTtDwHqK1RjSZFkXXX.WFXa-1828-66.jpg)

过期后:

![过期后](https://img.alicdn.com/tfs/TB18EvmDyrpK1RjSZFhXXXSdXXa-1926-42.jpg)

比 expires 好一点的是，过期后再次请求会更新缓存，也就是重新从缓存拿去（expires失效后除非重新设置，否则已经无效了），知道再次过期，简单点说就是```每隔max-age的时间向后台请求，拿新数据并更新缓存```。

> 如果 expires 和 cache-control: max-age 同时存在，则 expires 被 cache-control: max-age 覆盖