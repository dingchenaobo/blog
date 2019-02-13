## di(依赖注入)
[维基描述](https://zh.wikipedia.org/wiki/%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5)

> 在软件工程中，依赖注入是种实现控制反转用于解决依赖性设计模式。一个依赖关系指的是可被利用的一种对象（即服务提供端） 。依赖注入是将所依赖的传递给将使用的从属对象（即客户端）。该服务是将会变成客户端的状态的一部分。 传递服务给客户端，而非允许客户端来建立或寻找服务，是本设计模式的基本要求。

其实挺常见的，angular 中大量的使用了 di，vue 中的 vue.use，都属于di。

## 简单实现(一种方式)
```typescript
// injector.ts
var injector = {
  dependencies: {},

  register(key: string, value: Function) {
    this.dependencies[key] = value;
  },

  resolve(deps: Array<string>, func: Function, scope = {}) {
    let params = this.getParamNames(func); // 取得参数名
    const $params = [];
    for (let i = 0, len = params.length; i < len; i += 1) {
      const param = params[i];
      if (deps.includes(param)) {
        if (this.dependencies.hasOwnProperty(param)) {
          $params.push(this.dependencies[param]);
        } else {
          throw new Error(`未注册的模块：${param}`);
        }
      } else {
        throw new Error(`未注入的模块：${param}`);
      }
    }
    return func.apply(scope, $params);
  },

  getParamNames(func: Function): string[] {
    let paramNames: string = func.toString().match(/^function\s*[^\(]*\(\s*([^\)]*)\)/m)[1];
    paramNames = paramNames.replace(/ /g, '');
    return paramNames.split(',');
  }
}

export default injector;
```
```typescript
// index.ts
injector.register('Player', Player);
injector.register('Team', Team);

injector.resolve(['Player', 'Team'], function (Player: any, Team: any) {
  const teamRocket = new Team('rocket');
  const player = new Player('ym', teamRocket);
  console.log(player);
});
```

## 参考
* [[译]javascript中的依赖注入](https://juejin.im/post/5bd177806fb9a05d30179925)

* [javascript依赖注入详解](https://blog.csdn.net/nongshuqiner/article/details/78792174)