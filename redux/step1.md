## 简单的使用redux
脱离react，简单的使用一下redux。
```javascript
const { createStore } = Redux;

// 初始state
const initState = {
  num: 0
};

// 创建一个reducer
const reducer = function (state = initState, action) {
  const { type, stage } = action;
  switch (type) {
    case 'INCREMENT':
      return { num: state.num + stage };
    case 'DECREMENT':
      return { num: state.num - stage };
    default:
      return { ...state };
  }
}

// 创建store
const store = createStore(reducer);

// 创建监听器
store.subscribe(() => {
  // 获取state
  store.getState();
});

// 触发state更新
store.dispatch({ type: 'INCREMENT', stage: 1 }); // log:  { num: 1 }

store.dispatch({ type: 'DECREMENT', stage: 1 }); // log: { num: 0 }
```

## createStore
初步看，createStore函数有三个返回值，分别是：
* dispatch函数：触发state更新。
* subscribe函数：加入新的监听器。
* getState函数：获取当前的state。

接受一个参数：
* reducer函数。
那么createStore骨架大概是：
```javascript
function createStore(reducer) {
  function subscribe() {
  }

  function getState() {
  }

  function dispatch() {
  }

  return {
    subscribe,
    getState,
    dispatch,
  };
}
```

## subscribe添加监听器
```javascript
function createStore(reducer) {
  const currentListeners = [];
  
  function subscribe(listener) {
    currentListeners.push(listener);
  }

  function getState() {
  }

  function dispatch() {
  }

  return {
    subscribe,
    getState,
    dispatch,
  };
}
```

## getState获取当前的state
```javascript
function createStore(reducer) {
  const currentListeners = [];
  let currentState;

  function subscribe(listener) {
    currentListeners.push(listener);
  }

  function getState() {
    return currentState;
  }

  function dispatch() {
  }

  return {
    subscribe,
    getState,
    dispatch,
  };
}
```

## dispatch触发state更新。
```javascript
function createStore(reducer) {
  const currentListeners = [];
  let currentState;

  function subscribe(listener) {
    currentListeners.push(listener);
  }

  function getState() {
    return currentState;
  }

  function dispatch(action) {
    // 更新state
    currentState = reducer(action);
    // 通知监听队列
    const len = currentListeners.length;
    for (let i = 0; i < len; i += 1) {
      currentListeners[i]();
    }
  }

  return {
    subscribe,
    getState,
    dispatch,
  };
}
```