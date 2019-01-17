## bindActionCreators
同样，action也是可以聚合的，redux提供了bindActionCreators方法：
```javascript
const {
  bindActionCreators,
  createStore
} = Redux;

const reducer = function (state = { num: 0 }, action) {
  const { type, step } = action;
  switch (type) {
    case 'increment':
      return { num: state.num + step };
    case 'decrement':
      return { num: state.num - step };
    default:
      return { ...state };
  }
};

const store = createStore(reducer);

store.subscribe(() => {
  console.log(store.getState());
});

function increment() {
  return { type: 'increment', step: 1 };
}

function decrement() {
  return { type: 'decrement', step: 1 };
}

const actions = bindActionCreators({
  increment,
  decrement
}, store.dispatch);

actions.increment(); // log: { num: 1 }
actions.decrement(); // log: { num: 0 }
```

## 实现
bindActionCreators方法接受的参数为对象（action聚合）和dispatch函数，返回同样是一个对象，原理比较简单：
```javascript
function bindActionCreators(actionCreators, dispatch) {
  const keys = Object.keys(actionCreators);
  const len = keys.length;
  const boundActionCreators = Object.create(null);

  for (let i = 0; i < len; i += 1) {
    const namespace = keys[i];
    boundActionCreators[namespace] = function() {
      dispatch(actionCreators[namespace].call(this, arguments));
    }
  }

  return boundActionCreators;
}
```
bindActionCreators的第一个参数还可以是函数，如果是函数，那就和普通action一样：
```javascript
function bindActionCreator(actionCreator, dispatch) {
  return function() {
    dispatch(actionCreator.call(this, arguments));
  }
}

function bindActionCreators(actionCreators, dispatch) {
  if (typeof actionCreators === 'function') {
    return bindActionCreator(actionCreators, dispatch);
  }
  
  const keys = Object.keys(actionCreators);
  const len = keys.length;
  const boundActionCreators = Object.create(null);

  for (let i = 0; i < len; i += 1) {
    const namespace = keys[i];
    boundActionCreators[namespace] = bindActionCreator(actionCreators[namespace], dispatch);
  }

  return boundActionCreators;
}
```