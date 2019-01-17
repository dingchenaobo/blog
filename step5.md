## isPlainObject
本来是要看applyMiddleware的，但是看了一眼这个
```javascript
function dispatch(action) {
  if (!isPlainObject(action)) {
    throw new Error(
      'Actions must be plain objects. ' +
        'Use custom middleware for async actions.'
    );
  }
}
```
这个isPlainObject是个什么东西呢？

## isPlainObject
看看源码：
```javascript
function isPlainObject(obj) {
  if (typeof obj !== 'object' || obj === null) return false;

  let proto = obj;
  while (Object.getPrototypeOf(proto) !== null) {
    proto = Object.getPrototypeOf(proto);
  }

  return Object.getPrototypeOf(obj) === proto;
}
```
看出来redux对action是有一些要求的，比如这个函数的意思，大致是需要action是字面量声明或者Object.create创建的函数，也就是action的__proto__指向的是Object.prototype。

那为啥不直接这么写的呢？
```javascript
function isPlainObject(obj) {
  if (typeof obj !== 'object' || obj === null) return false;

  return Object.getPrototypeOf(obj) === Object.prototype;
}
```

## 跨域访问变量（cross-realm）
如果出现页面嵌套，即iframe的时候，在frame页面中打印：
```javascript
// frame
console.log(window.parent.Array === Array) // false
```
这两个Array已经不是一个构造函数了。同理，如果在父页面中使用isPlainObject，在子页面中调用，直接使用
```javascript
Object.getPrototypeOf(obj) === Object.prototype
```
的判读，其实就是父页面的Object.prototype和子页面的Object.prototype比较，结果就成了false了：
```javascript
// parent
function isPlainObject(obj) {
  if (typeof obj !== 'object' || obj === null) return false;

  let proto = obj;
  while (Object.getPrototypeOf(proto) !== null) {
    proto = Object.getPrototypeOf(proto);
  }

  return Object.getPrototypeOf(obj) === proto;
}

function myIsPlainObject(obj) {
  if (typeof obj !== 'object' || obj === null) return false;

  return Object.getPrototypeOf(obj) === Object.prototype;
}

function test(obj) {
  console.log(isPlainObject(obj), myIsPlainObject(obj));
}

// frame
window.parent.test({});
// log: true false
```
学到了。

### 参考
* [redux的isPlainObject源码](https://blog.csdn.net/juzipidemimi/article/details/81814425)

* [Optimize dispatch plain object check #2599](https://github.com/reduxjs/redux/pull/2599#issuecomment-342849867)
