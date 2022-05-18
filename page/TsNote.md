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

### 数值

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



