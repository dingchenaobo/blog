## defineProperty
api介绍不多说，简单的数据监听:
```javascript
function definedReactive(obj, key) {
  let value = obj[key];
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get() {
      console.log('getter');
      return value;
    },
    set(newValue) {
      if (newValue === value) {
        return;
      }
      console.log('setter');
      value = newValue;
    }
  });
}

const test = { x: 1 };

definedReactive(test, 'x');

test.x = 2; // log: setter
test; // log getter
```
如何监听对象呢？遍历：
```javascript
function definedReactive(obj, key) {
  /// ...
}

class Observer {
  constructor(value) {
    this.walk(value);
  }

  walk(obj) {
    for (let k in obj) {
      definedReactive(obj, k);
    }
  }
}

const test = { x: 1, y: 1 };
new Observer(test);
```
如何添加回调函数呢？借用一个变量：
```javascript
const Dep = {
  target: null
};

class Observer {
  constructor(value) {
    this.walk(value);
  }

  walk(obj) {
    for (let k in obj) {
      definedReactive(obj, k);
    }
  }
}

function definedReactive(obj, key) {
  let value = obj[key];
  const dep = [];
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get() {
      if (Dep.target) {
        dep.push(Dep.target);
      }
      return value;
    },
    set(newValue) {
      if (newValue === value) {
        return;
      }
      dep.forEach(cb => cb(newValue, value));
      value = newValue;
    }
  });
}

// 也并不复杂
const test = { x: 1 };

new Observer(test);

Dep.target = function(nv, ol) {
  console.log(`${ol} -> ${nv}`);
}
test.x;

Dep.target = function(nv, ol) {
  console.log(`render`);
}
test.x;

test.x = 2;
```
值得注意的点就是，设置回调后，需要手动触发一次get函数，把target回调保存入dep数组中，考虑到监听回调不止一个，所以dep是一个数组。