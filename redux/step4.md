## compose
compose的功能：
> compose(fn1, fn2, fn3, fn4, fn5)(1);

翻译为：

> fn1(fn2(fn3(fn4(fn5(1)))));

redux的compose函数基于Array.prototype.reduce实现。

## reduce
参数如下（ts真是好东西）：
```typescript
Array.prototype.reduce(
  callbackfn: (
    accumulator: T,
    currentValue: T,
    currentIndex: number,
    array: T[]
  ) => T,
  initialValue?: T
): T;
```
用法其实不难，主要注意下是否存在initialValue的时候的情况：

回调函数第一次执行时，```accumulator```和```currentValue```的取值有两种情况：如果调用reduce()时提供了```initialValue```，```accumulator```取值为```initialValue```，```currentValue```取数组中的第一个值；如果没有提供```initialValue```，那么```accumulator```取数组中的第一个值，```currentValue```取数组中的第二个值。

> 注意：如果没有提供```initialValue```，reduce会从索引1的地方开始执行callback方法，跳过第一个索引。如果提供```initialValue```，从索引0开始。

如果数组为空且没有提供```initialValue```，会抛出TypeError 。如果数组仅有一个元素（无论位置如何）并且没有提供```initialValue```， 或者有提供```initialValue```但是数组为空，那么此唯一值将被返回并且callback不会被执行。

详细：[MDN--Array.prototype.reduce()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

## compose
简单分析下，compose函数返回一个函数，接受接受的参数类型和数目不定，和compose的参数函数的参数有关。
```javascript
function compose(...funcs) {
  return function() {}
}
```
funcs需要遍历、整合，可以利用reduce函数实现：
```javascript
function compose(...funcs) {
  return funcs.reduce(function(acc, current) {});
}
```
compose的返回值即reduce函数的返回值，为函数：
```javascript
function compose(...funcs) {
  return funcs.reduce(function(acc, current) {
    return function() {}
  });
}
```
该函数的参数不确定：
```javascript
function compose(...funcs) {
  return funcs.reduce(function(acc, current) {
    return function(...args) {}
  });
}
```
返回值为链式调用的函数的返回值
```javascript
function compose(...funcs) {
  return funcs.reduce(function(acc, current) {
    return function(...args) {
      return acc(current(...args));
    }
  });
}
```
reduce不接受空数组，并且funcs内只有一个函数时，不需要进reduce，优化下：
```javascript
function compose(...funcs) {
  if (funcs.length === 0) {
    return function(args) {
      return args;
    }
  }

  if (funcs.length === 1) {
    return funcs[0];
  }

  return funcs.reduce(function(acc, current) {
    return function(...args) {
      return acc(current(...args));
    }
  });
}
```
分析下不复杂，换成箭头函数后就成了reduce源码中的compose函数：
```javascript
function compose(...funcs) {
  if (funcs.length === 0) {
    return args => args;
  }

  if (funcs.length === 1) {
    return funcs[0];
  }

  return funcs.reduce((acc, current) => (...args) => acc(current(...args)));
}
```