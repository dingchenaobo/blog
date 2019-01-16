## combineReducers
reducer不总是一个，当出现多个reducer时，需要对reducer进行组合，reducx提供了combineReducers函数：
```javascript
const {
  combineReducers,
  createStore
} = Redux;

const reducer1 = function (state = { num: 0 }, action) {
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

const reducer2 = function (state = { num: 1 }, action) {
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

const reducers = combineReducers({
  reducer1,
  reducer2
});

const store = createStore(reducers);

store.subscribe(() => {
  console.log(store.getState());
});

// 触发state更新
store.dispatch({ type: 'INCREMENT', stage: 1 }); // log:  { reducer1: { num: 1 }, reducer2: { num: 2 } }

store.dispatch({ type: 'DECREMENT', stage: 1 }); // log: { reducer1: { num: 0 }, reducer2: { num: 1 } }
```

## 实现
createStore接受的reducer，也就是combineReducers返回的combination（reducers聚合）也是一个reducer函数。
```javascript
function combineReducers(reducers) {
  return function(state, action) {
  }
}
```
聚合reducers存在module，namespace为reducers的key，那么state自然也存在相同的module：
```javascript
function combineReducers(reducers) {
  const keys = Object.keys(reducers);
  const len = keys.length;
  return function(state, action) {
  }
}
```
reducer的职能就是更新state：
```javascript
function combineReducers(reducers) {
  const keys = Object.keys(reducers);
  const len = keys.length;
  return function(state, action) {
    const nextState = {};
    for (let i = 0; i < len; i += 1) {
      const namespace = keys[i];
      const tempState = state[namespace];
      const tempReducer = reducers[namespace];
      nextState[namespace] = tempReducer(tempState, action);
    }
    return nextState;
  }
}
```