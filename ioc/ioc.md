## ioc
[官方解释](https://zh.wikipedia.org/wiki/%E6%8E%A7%E5%88%B6%E5%8F%8D%E8%BD%AC)
> 控制反转（Inversion of Control，缩写为IoC），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。其中最常见的方式叫做依赖注入（Dependency Injection，简称DI），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中。

## 问题
比如，球员可以抽象成一个类，球员有一个属性team，队伍也可以抽象成一个类：
```typescript
// type.ts
export interface ITeam {
  name: string;
}

export interface IPlayer {
  name: string;
  team: ITeam;
}

// team.ts
import { ITeam } from './type';

export default class Team implements ITeam {
  public name: string;

  constructor(name: string) {
    this.name = name;
  }
}

// palyer.ts
import { IPlayer, ITeam } from './type';
import Team from './team';

export default class Player implements IPlayer {
  public name: string;
  public team: ITeam;

  constructor(name: string, teamName: string) {
    this.name = name;
    this.team = new Team(teamName);
  }
}

// index.ts
import Player from './ioc/player';

const ym = new Player('ym', 'hj');
```
这样看似没问题，但是还是有一点问题的。如果由于业务需求，需要改 ```Team``` 类，这时候就需要在 team.ts 和 player.ts 两个文件里面去改动，因为我们在设计 ```Player``` 类的时候，直接依赖了 ```Team``` 类的实现，如果 Team 需要在构造函数中多加一个参数的时候，```player``` 就需要改动。

## 解决
那在实现 ```Player``` 类的时候，就不能直接显式的依赖 ```Team``` 类，简单的修改下 ```Player``` 类和 index 文件：
```typescript
// palyer.ts
import { IPlayer, ITeam } from './type';

export default class Player implements IPlayer {
  public name: string;
  public team: ITeam;

  constructor(name: string, team: ITeam) {
    this.name = name;
    this.team = team;
  }
}

// index.ts
import Player from './ioc/player';
import Team from './ioc/team';

const team = new Team('hj');
const ym = new Player('ym', team);
```
非常简单的修改，但是这个时候 ```Player``` 类的实现不再依赖于 ```Team``` 类，Team 的修改就不会再印象 Player 中的代码，这就是ioc。

维基的描述：
> Class A中用到了Class B的对象b，一般情况下，需要在A的代码中显式的new一个B的对象。采用依赖注入技术之后，A的代码只需要定义一个私有的B对象，不需要直接new来获得这个对象，而是通过相关的容器控制程序来将B对象在外部new出来并注入到A类里的引用中。而具体获取的方法、对象被获取时的状态由配置文件（如XML）来指定。

## 实现方法
实现控制反转主要有两种方式：依赖注入和依赖查找。两者的区别在于，前者是被动的接收对象，在类A的实例创建过程中即创建了依赖的B对象，通过类型或名称来判断将不同的对象注入到不同的属性中，而后者是主动索取相应类型的对象，获得依赖对象的时间也可以在代码中自由控制。