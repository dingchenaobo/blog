## mocha单元测试
单元测试对代码的健壮性和可靠性有很大帮助。
```javascript
// step1 全局安装
npm i -g mocha

// step2 项目内安装
npm i --save-dev mocha expect.js mochawesome
```

* mocha：测试框架，[github地址](https://github.com/mochajs/mocha).
* expect.js：断言库，[github地址](https://github.com/Automattic/expect.js).
* mochawesome：html可视化测试报告，[github地址](https://github.com/adamgruber/mochawesome).

## 简单的测试
```javascript
// func.js
module.exports = function test(a, b) {
  return a + b;
}

// func.test.js
const test = require('./index');
const expect = require('expect.js');

module.exports = function () {
  describe('`testA`函数单元测试结果如下：', () => {
    it('测试用例：1, 2', () => {
      expect(test(1, 2)).to.equal(3);
    });

    it('测试用例：3, 4', () => {
      expect(test(3, 4)).to.equal(7);
    });
  });
}

// package.json: script
"test": "mocha --reporter mochawesome"
```
运行脚本会生成```mochawesome-report```文件夹，打开```mochawesome.html```，可以看到可视化测试结果：

![result](https://gw.alicdn.com/tfs/TB13mh_DmzqK1RjSZPxXXc4tVXa-2878-1452.jpg)