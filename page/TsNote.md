# TypeScript

## 何为TypeScript

TypeScript（简称TS）是由微软公司研发的新型脚本语言，完全兼容JavaScript的语法，是JavaScript语言的超集。

## TypeScript是如何运作的？

TypeScript本身不具有直接运行的功能，需要通过转化为JavaScript文件来进行运行。所以TypeScript只是为了方便编码，而不是独特程序语言，使用TypeScript需要一定JavaScript基础。

## TypeScript有什么特别之处吗？

TypeScript 的拥有两个最重要的特性——类型系统、适用于任何规模。

- 类型系统

  JavaScript 是一门非常灵活的编程语言，如何JS属性、变量、对象都是没有任何类型约束的。也就是说在JS中定义一个文本变量，但是你依然将其赋予数字类型，不会报任何错误。

  而TypeScript则 拥有一套成熟的静态类型系统，它的任何变量、属性、对象都是拥有对应的类型的，这在其面向对象编程语言中十分常见。当你在将其string类型的变量赋予number时，编译器会报出错误。

  **TypeScript的类型系统提供的是一套静态类型。**

  类型系统分为两大类，动态类型和静态类型。区别是动态类型是编译后运行时才检查，JS就是这样的，而静态类型则是在编译前就会检查，这样在IDE也会提示报错。

  **TypeScript的类型不是强制的。**TypeScript属于弱类型，指虽然会在IDE中提示报错，但是编译依然会转为JS文件，运行时不会报错。而强类型指的是会阻止编译的类型，比如Java、Python、CPP等等。

- 适用于任何规模

  TypeScript 非常适用于大型项目，类型系统可以为大型项目带来更高的可维护性，以及更少的 bug。

  由于TypeScript是JS的超集，所以TypeScript 还可以和 JavaScript 共存。甚至可以直接将其JS文件修改后缀名改为TS文件，运行也不会报错误（当然这样做便失去了TS的意义了）。

  因为TS是基于类型的语言，所以在IDE编写TS时，会拥有补全提示功能，非常便于编程。在VSCode中编写JS文件时，弹出的代码补全也是基于TS语言服务实现的。


## 基础内容

### TypeScript的原始数据类型

JavaScript 的类型分为两种：原始数据类型（[Primitive data types](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)）和对象类型（Object types）。

而TypeScript中原始数据类型包括：布尔值、数值、字符串、`null`、`undefined` 以及 ES6 中的新类型 [`Symbol`](http://es6.ruanyifeng.com/#docs/symbol) 和 ES10 中的新类型 [`BigInt`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt)。

在TypeScript中，定义类型的方法就是在 对象名后 使用`:`(冒号) + 数据类型。

- 布尔值：

  ```typescript
  let isDone: boolean = false;
  // 注意，使用构造函数 Boolean 创造的对象不是布尔值：
  let createdByNewBoolean1: boolean = new Boolean(1); //对象不是boolean类型
  let createdByNewBoolean2: Boolean = new Boolean(1); //对象返回的是Boolean类型
  ```

  在 TypeScript 中，`boolean` 是 JavaScript 中的基本类型，而 `Boolean` 是 JavaScript 中的构造函数。
  
- 数值

  ```typescript
  let decLiteral: number = 6;
  let hexLiteral: number = 0xf00d;
  // ES6 中的二进制表示法
  let binaryLiteral: number = 0b1010;
  // ES6 中的八进制表示法
  let octalLiteral: number = 0o744;
  let notANumber: number = NaN;
  let infinityNumber: number = Infinity;
  ```

- 字符串

  ```typescript
  let myName: string = 'Tom';
  ```

- 空值(void)

  void主要用作与函数的返回值，一般不作为变量的类型。

  ```typescript
  function alertName(): void {
      alert('My name is Tom');
  }
  ```

  **void类型的变量，只能赋予undefined。**

- Null 和 Undefined

  ```typescript
  let u: undefined = undefined;
  let n: null = null;
  ```

  与`void`区别是，`undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，而 `void` 类型的变量不能赋值给 `number` 类型的变量。

### 任意值（any）

任意值（any）是TypeScript中的一个特有的类型，用来表示允许赋值为任意类型。常常用于还不明确内容的对象。

```typescript
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 47; //无报错
```

此外，`Any类型`的对象允许被访问任何属性、方法，即使是不存在的属性、方法，IDE都不会报错。

```typescript
let anyThing: any = 'hello';
console.log(anyThing.myName); //该对象不存在myName属性,但依然不会报错
something.setName('Tom'); //该对象不存在setName方法,但依然不会报错
```

PS：目前面临TypeScript最为严谨的问题就是any滥用。

“只要我对其不知道的类型通通定义为any，那么代码就没有任何错误了！” o(´^｀)o

“那为什么不回去用JavaScript呢？”  ┓(;´_｀)┏

### 类型自动判断

即使在TypeScript中不特别对对象进行明确类型，TypeScript也会会依照类型推论（Type Inference）的规则推断出一个类型。当然**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 `any` 类型而完全不被类型检查。**

### 联合类型

TypeScript支持一个对象为 多个不确定类型。即正常联合类型（Union Types），表示取值可以为多种类型中的一种。

```typescript
let myFavoriteNumber: string | number; //允许对象的类型是 string 或者 number，但是不能是其他类型。
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

当 一个函数 的接受值为 联合类型的对象时，那么函数内容中调用 它的时候，只能是 **所有类型里共有的属性或方法**，否则就会报错。

```typescript
// 报错。length 不是 string 和 number 类型的共有属性。
function getLength(something: string | number): number {
    return something.length;
}
//无错误,toString方法在string 和 number 类型都存在。
function getString(something: string | number): string {
    return something.toString();
}
```

当一个联合类型的变量在被赋值后，该变量就会为对应类型（但依然可以重新以联合类型赋值），就不能再使用另外一个类型的方法了。

```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错
```



### 接口(interface)

不同于JS，TS是完善的面向对象语言，它拥有面向对象概念中的 接口概率。

它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）。也就是接口只能定义，不能在内部输入逻辑代码。

```ts
interface Person {
    name: string;
    age: number;
}
// tom 实现了 Person 接口，自动拥有了Person的全部属性。
let tom: Person = {
    name: 'Tom',
    age: 25
};
```

**实现了对应接口的对象，必须进行实现其拥有的所有属性和方法！也不允许多实现其它属性。**否则会抛出错误。

```typescript
interface Person {
    name: string;
    age: number;
}
// 报错。Person接口的实现没被全部实现完。
let tom: Person = {
    name: 'Tom'
};
// 报错。Person未拥有的属性，不能拥有。
let Zoe: Person = {
    name: 'Tom'，
    age: 25，
    gender: 'male'
};
```

### 可选属性

当在TS中实现、定义class、interface时，如果某个属性为可选属性，在属性名为 加上`?` ，表示该属性为可选属性。

```typescript
interface Person {
    name: string;
    age?: number;
}
// Person接口的age属性为可选属性，所以不实现也不会报错。
let tom: Person = {
    name: 'Tom'
};
```

### 任意属性

常规实现接口下，不能额外实现其它属性。

但希望一个接口放开点的话，可以在该接口中定义`任意属性`。

任意属性使用 `[propName: 数据类型]` 定义，表示该接口可以拥有其它属性，并将其取为对应数据类型。

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}
// 由于Person定义了一个any的任意属性，它的要求取为string,gender作为string属性，恰好能被取为string属性,所以不会报错。
let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

但注意，这里有个坑，如果这个任意属性 类型不为any的话，那么就需要要求所有属性(包括接口已经定义的属性)都要满足这个类型，否则就会报错：

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}
// 这里 任意属性类型为string,而其age属性又是number,报错。
let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
```

为了解决避免这些自相矛盾，可以巧妙利用联合类型：

```ts
interface Person {
    name: string;
    age?: number;
    [propName: string]: string | number;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
```

### 只读属性

有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 `readonly` 定义只读属性：

```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}
let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};
// id为只读属性,一经赋值,就不能被修改了。
tom.id = 9527;
```

**只读属性可以不用在创建是强制要求实现。**

**只读的限制，是给予在对象创建的时候的，所以自作聪明，在其对象创建中忽略该属性，后续再赋值也是会报错的。**

### 数组

在 TypeScript 中，数组类型有多种定义方式。

最简单的就是 使用`数据类型[]`方式：

```ts
let fi: number[] = [1, 1, 2, 3, 5];
```

还可以使用 `Array<数据类型>`进行创建：

```ts
let fi: Array<number> = [1, 1, 2, 3, 5];
```

当然还可以使用歪门邪道，使用接口来创建数组 ：

```ts
interface NumberArray {
    // 利用了任意属性方法
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

当然这种方式不推荐使用，这会引起他人的困惑，小题大做。

结合`any`类型可以使得数组中出现任意类型，而不至于限制死类型：

```typescript
let list1: any[] = ['zssaer', 22, { website: 'https://zssaer.cn' }];
let list2: Array<any> = ['zssaer', 22, { website: 'https://zssaer.cn' }];
```

### 函数

众所周知，[函数是JavaScript重要的一部分](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch2.html#为何钟爱一等公民)，那么在TypeScript中函数也一样。

在JS中定义函数的方式主要为两种，函数声明（Function Declaration）和函数表达式（Function Expression）。

```js
// 函数声明（Function Declaration）
function sum(x, y) {
    return x + y;
}

// 函数表达式（Function Expression）
let mySum = function (x, y) {
    return x + y;
};
```

在TS的函数就和其他语言一样，也需要相应类型约束，主要表现在输入参数类型、输出值类型上。

```ts
// Function Declaration
function sum(x: number, y: number): number {
    return x + y;
}
// Function Expression
let sum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
}
```

这里的函数表达式，有点特别，其中`=>` 与ES6的`=>`的作用不一样。

ES6中叫做箭头函数，主要用作简化回调`return`的写法,直接回调。或者简化结构。可以参考 [ES6 中的箭头函数](http://es6.ruanyifeng.com/#docs/function#箭头函数)。

```javascript
// ES6箭头函数
var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```

而TS的`=>`则主要是用作函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。

在TS中除了直接定义函数外，还可以使用接口定义函数，这或许有点难以理解：

```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

采用函数表达式结合接口定义函数的方式时，对等号左侧进行类型限制，可以保证以后对函数名赋值时保证参数个数、参数类型、返回值类型不变。

上述定义的函数的输入参数都是固定的，在使用函数的时候，不能缺少响应的参数。有时对于输入参数也不必要这么苛刻，可以在定义时也使用可选参数。

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
```

如果可选参数在函数使用时未必 输入的话，那么函数内部处理中，这个可选参数将会以undefined值出现。利用这点可以在函数内部进行判断，从而实现各种要求。

当然，**在函数定义时，输入的 可选函数必须在所有正常函数定义后定义，否则会报错。**

ES6中，允许函数定义中设置入参的默认值，这点在TypeScript中也被允许：

```ts
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
```

ES6中，还允许定义函数时设置 入参接受其他参数（rest参数），利用`...函数名`表示。当然在TS中这个其他函数 也可以使用一个数组类型来定义：

```ts
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}
```

**函数定义时，rest参数只能位于最后一个参数。**

TypeScript中，函数允许根据内部逻辑，返回多种类型的返回值（利用联合类型），这种方式被称为*重载*。

```ts
function reverse(x: number | string): number | string | void {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

### 声明文件

#### 什么是TS声明文件

在进行TS编写时，常常遇见如下情况：

> 使用第三方库
>
> 需要使用一个参数，但这个参数定义在其他文件下

这时你就会发现，你不能在你的TS文件下随便使用外部库了，因为你按常规的方式引入进来后，TS会疯狂的报错，原因是TS不知道这个外部数据时是什么类型。这一点在JS修改为TS文件时的时候尤其明显。

这时你需要创建一个TS声明文件，用来声明XXX是什么类型，这样TS才能正常识别。

所谓TS声明文件，就是以`.d.ts`为后缀的文件。

一般来说，TS 会解析项目中所有的 `*.ts` 文件，当然也包含以 `.d.ts` 结尾的文件。所以当我们将 声明文件`xxx.d.ts` 放到项目中时，其他所有 `*.ts` 文件就都可以获得 到这个声明文件下 的类型定义了。

TS声明文件不仅用作与对其他外部JS文件的定义，也用作与对自己的全局定义，相当于一个全局文件，在内部定义一个变量，那么在自己的项目中所有TS文件都可以直接使用。

#### 定义TS声明文件

比如在项目中定义一个全局接口，在项目中创建一个`.d.ts`后缀文件：

```typescript
// src/index.d.ts
interface inputfile{
    fileName:string
    filedir:string
}
// src/index.ts
function getFile(file:inputfile):void{
    ...
}
```

`declare` 表示全局声明，它有很多种可声明的类型：

- declare var : 声明全局变量
- declare function：声明全局函数
- declare class：声明全局类
- declare enum：声明全局枚举
- declare namespace：声明全局对象（含有子属性）
- interface：声明全局接口
- declare module：声明全局模块

**注意interface接口的全局声明，前面没有declare。**

需要注意的是，**声明语句中只能定义类型，即使是全局函数也一样，切勿在声明语句中定义具体的实现。**

其中`namespace`是 TS 早期时为了解决模块化而创造的关键字，中文称为命名空间。

由于历史遗留原因，在早期还没有 ES6 的时候，TS 提供了一种模块化方案，使用 `module` 关键字表示内部模块。但由于后来 ES6 也使用了 `module` 关键字，TS 为了兼容 ES6，使用 `namespace` 替代了自己的 `module`，更名为命名空间。但随着 ES6 的广泛应用，现在已经不建议再使用 TS 中的 `namespace`，而推荐使用 ES6 的模块化方案了，故我们不再需要学习 `namespace` 的使用了。

虽然`namespace` 被淘汰了，但是在声明文件中，`declare namespace` 还是比较常用的，它用来表示全局变量是一个对象，包含很多子属性。

比如 `jQuery` 是一个全局变量，它是一个对象，提供了一个 `jQuery.ajax` 方法可以调用，那么就应该使用 `declare namespace jQuery` 来声明这个拥有多个子属性的全局变量：

```ts
// src/jQuery.d.ts
declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
}
// src/index.ts
jQuery.ajax('/api/get_something');
```

看上去是不是和定义全局class一样，当然除了一层的之外，`namespace`是可以在内部迭代的：

```ts
// src/jQuery.d.ts
declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
    namespace fn {
        function extend(object: any): void;
    }
}
// src/index.ts
jQuery.ajax('/api/get_something');
jQuery.fn.extend({
    check: function() {
        return this.each(function() {
            this.checked = true;
        });
    }
});
```

当然，这里如果Jquery下只使用`fn`下的属性的话，直接可以在写在外层：

```ts
// src/jQuery.d.ts
declare namespace jQuery.fn {
    function extend(object: any): void;
}
// src/index.ts
jQuery.fn.extend({
    check: function() {
        return this.each(function() {
            this.checked = true;
        });
    }
});
```

当不同类型的声明拥有同样的名称，也不会产生冲突。

```ts
declare function jQuery(selector: string): any;
declare namespace jQuery {
    function ajax(url: string, settings?: any): void;
}
```

#### 外部NPM包解决方案

当我们项目引入外部NPM包时，如果该包没有内置声明文件的话，该怎么办？

这时就需要自己为它写声明文件了。由于是通过 `import` 语句导入的模块，所以声明文件存放的位置也有所约束，一般有两种方案：

1. 创建一个 `node_modules/@types/外部项目包名/index.d.ts` 文件，存放 它的 模块的声明文件。这种方式不需要额外的配置，但是 `node_modules` 目录不稳定，代码也没有被保存到仓库中，无法回溯版本，有不小心被删除的风险，故不太建议用这种方案，一般只用作临时测试。
2. 创建一个 `types` 目录，专门用来管理自己写的声明文件，将它的放到 `types/外部包名/index.d.ts` 中。这种方式需要配置下 `tsconfig.json` 中的 `paths` 和 `baseUrl` 字段。

比如我们导入的是一个hook NPM项目，那么就应该在项目下创建 `types/hook/index.d.ts`文件。

随后在项目下`tsconfig.json`下添加如下内容，定义TS的文件范围：

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "baseUrl": "./",
        "paths": {
            "*": ["types/*"]
        }
    }
}
```

如此配置之后，通过 `import` 导入 `hook ` 的时候，也会去 `types` 目录下寻找对应的模块的声明文件了。

和一般的项目内的声明文件不一样，NPM包的声明文件主要使用导出相关的语句，一般不使用`declare`语句。

npm 包的声明文件与全局变量的声明文件有很大区别。在 npm 包的声明文件中，使用 `declare` 不再会声明一个全局变量，而只会在当前文件中声明一个局部变量。（ps：这也很好理解，不然都用declare的话，导入几百个包后，项目中全局变量岂不是一大堆...）

npm包的常用以下导出语句：

- export 导出变量
- export namespace 导出（含有子属性的）对象
- export default ES6 默认导出
- export = common js的导出模块

`export` 的语法与普通的 ts 中的语法类似，区别仅在于声明文件中禁止定义具体的实现。

比如hook中拥有name, getName, Animal, Directions, Options内容需要使用，那么在其声明文件中：

```ts
// types/hook/index.d.ts
export const name: string;
export function getName(): string;
export class Animal {
    constructor(name: string);
    sayHi(): string;
}
export enum Directions {
    Up,
    Down,
    Left,
    Right
}
export interface Options {
    data: any;
}
```

这样项目中导入模块时，就不会因为找不到这些内容的类型而报错了。

```ts
// src/index.ts
import { name, getName, Animal, Directions, Options } from 'hook';
console.log(name);
let myName = getName();
let cat = new Animal('Tom');
let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
let options: Options = {
    data: {
        name: 'hook'
    }
};
```

上面是导入外部NPM包的时候。

但如果碰巧你要自己创建一个包发布到NPM上的话，你要写一个全局变量声明文件，用作项目的编写使用。又有写一个导出声明文件，用作发布到NPM让其它人项目识别。这样就显得麻烦起来了，其实可以混用 `declare` 和 `export`，让其又为自己作为全局变量，又导出声明。

以上面编写hook项目为例：

```ts
// types/hook/index.d.ts
declare const name: string;
declare function getName(): string;
declare class Animal {
    constructor(name: string);
    sayHi(): string;
}
declare enum Directions {
    Up,
    Down,
    Left,
    Right
}
interface Options {
    data: any;
}
export default function foo(): string;
export { name, getName, Animal, Directions, Options };
```

这里的`export default`是ES6的默认导出，可以使导入方不再局限使用`{}`来选择而是直接输入任意名称来获取。但**只有 `function`、`class` 和 `interface` 可以直接在声明时默认导出，其他的变量需要先定义出来，再默认导出。**针对这种默认导出，我们一般会将导出语句放在整个声明文件的最前面（这是为了让浏览者更醒目查阅到）：

```ts
export default Directions;

declare enum Directions {
    Up,
    Down,
    Left,
    Right
}
```

当然 编写导出声明文件时，肯定不会只顾及自己的感受吧，也要考虑下他人使用吧（如果只是自用除外）。你使用ES6这样导入顾然没错，假如他人使用CommonJs导入不就会报错吗？

```typescript
// es6导入
import hook from 'xxx/hook'
// commonjs 导入
import hook = require('xxx/hook')
```

对于这种使用 commonjs 规范的库，假如要为它写类型声明文件的话，就需要使用到 `export =` 语法：

```ts
// types/foo/index.d.ts
export = foo;
declare function foo(): string;
declare namespace foo {
    const bar: number;
}
```

需要注意的是，上例中使用了 `export =` 之后，就不能再写单个导出 `export { bar}` 了。所以需要通过声明合并，使用 `declare namespace foo` 来将 `bar`合并，变相导出。

#### NPM包中扩展全局变量

上面说了，在NPM包中的声明文件只有导出声明是有效地，NPM包中的全局变量声明是不会对项目产生作用的。

那么如果你在编写一个准备发布到NPM的插件时，想给引用者（即安装此包）的项目中带来一些你的插件上的全局变量（即拓展他们的全局变量）怎么办？

TS中拥有一个`declare global`语句，它可以实现这样的效果：

```ts
// hook包中的types/hook/index.d.ts
declare global {
    interface String {
        prependHello(): string;
    }
}
export {};

// src/index.ts
'bar'.prependHello();
```

注意即使此声明文件不需要导出任何东西，仍然需要导出一个空对象，用来告诉编译器这是一个模块的声明文件，而不是一个全局变量的声明文件。

#### 声明文件依赖导入（模块插件）

声明文件中，可以导入其他声明文件中的类型。比如在自己的项目中重新扩展原有的导出模块声明：

```ts
// types/moment-plugin/index.d.ts
import * as moment from 'moment';

declare module 'moment' {
    export function foo(): moment.CalendarKey;
}

// src/index.ts
import * as moment from 'moment';
import 'moment-plugin';
moment.foo();
```

在上面例子中，我们声明了一个自己的moment模块，内部使用了 外部的一个类型。相当于我们在其moment导出拓展了一些内容，导入时需要在额外导入 这个声明文件，否则会报错。

#### 自动生成声明文件

如果本身项目使用的TS，那么对于自己的项目代码在进行编译的时候，可以自动生成声明文件。

方法就是在`tsconfig.json`文件下面添加`declaration` 选项。

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "outDir": "lib",
        "declaration": true,
    }
}
```

### 内置对象类型

TS除了内置了一些常用的原始数据类型以外，还内置了一些其他的对象类型，方便编写使用。

TS拥有ECMAScript定义的标准类型：`Boolean`、`Error`、`Date`、`RegExp` 等。更多的内置对象，可以查看 [MDN 的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)。

TS还定义了DOM、BOM类型：`Document`、`HTMLElement`、`Event`、`NodeList` 等：

```ts
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```





