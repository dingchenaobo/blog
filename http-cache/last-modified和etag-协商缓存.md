## Last-Modify && If-Modified-Since
在响应头中，根据文件的最后修改日期，加上Last-Modify字段，下一次的请求头中会带上If-Modified-Since，值就是上一次响应得到的Last-Modify，此时服务端就可以根据上一次的Last-Modify时间，和现在文件的修改时间对比，没有修改返回304，修改了200并且返回新文件。
```javascript
const maxAge = 300; // s
const lastModified = status.mtime; // 文件的最后修改日期
const ifModifiedSince = ctx.request.header['if-modified-since'] || 0;

/**
  * If-Modified-Since能检查到的粒度是s级，所以无法根据 lastModified 的时间戳判断
 */
const isModify = new Date(lastModified).toUTCString() === new Date(ifModifiedSince).toUTCString(); // 对比上次修改日期和最后修改日期是不是同一天

ctx.res.writeHead(isModify ? 304 : 200, {
  'Cache-Control': `max-age=${maxAge}`,
  'Last-Modified': lastModified
});
```

## Etag && If-None-Match
这个和 Last-Modify && If-Modified-Since 类似，但是不是根据文件的最后修改日期，http本身并没有给etag指定具体的算法，但是貌似根据文件本身的二进制数据hash一个hash字符串比较通用，然后在响应头中加入这个字符串，下次请求会在请求头中的 If-None-Match 带上这个字段，服务端再计算出文件的新 hash 值，再对比两个 hash 值是否相同来判断是否需要返回 304 。
