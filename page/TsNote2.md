# TypeScript（2-高级）

## 前言

本章内容为高级内容，主要有高级数据类型、类、代码检测等。

上片[内容](.\TsNote.md)

## 高级

### 元组（tuple）

元组这一数据类型普遍存在于函数编程语言中。

*元组类型*是另一种类型`Array`，它确切地知道它包含多少个元素，以及它在特定位置包含哪些类型。

定义一对值分别为 `string` 和 `number` 的元组：

```ts
const tom: [string, number] = ['Tom', 25];
```

一般而言，元组都是定义为const 常量的，不被允许修改，但是TypeScript中，tuple是可以被修改的，可以通过pop 、push、[]定位修改，这看上去就是一个新的存储容器。

但其实tuple的`"pop" | "push" | "reverse" | "shift" | "sort" | "splice" | "unshift" | "fill" | "copyWithin"`这些方法都是目前TypeScript的不能解决的问题，TypeScript尝试提供这些方法给元组类型，但是这些方法使用却会破坏元组的特性。而且目前还不能被解决，具体内容可以访问：https://github.com/microsoft/TypeScript/issues/40316

### 枚举（Enum）

#### 普通枚举

枚举用作归纳一些常量，用于快速的反复使用。比如一周只能有七天，颜色限定为红绿蓝等。

枚举存储方式是以 key-value键值对进行存储的，以键取值，或者以值取键。其中key 只能为数字，默认可以不进行设置，将会自动以递增方式作为key：

```ts
// 其中枚举成员会被赋值为从 0 开始递增的数字，同时也会对枚举值到枚举名进行反向映射
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
// 手动赋值，如果后接着为赋值的内容，将会自动赋值为 从前一项加1
enum Days {Sun = 7, Mon = 1, Tue= 5，Fri};
```

使用枚举只需要`枚举名[值]`就可以获取到键，当然反过来`枚举名[键]`也可以取到值。

```ts
console.log(Days["Sun"] === 0); 
console.log(Days["Mon"] === 1); 
console.log(Days["Tue"] === 2);
console.log(Days["Sat"] === 6);

console.log(Days[0] === "Sun"); 
console.log(Days[1] === "Mon"); 
console.log(Days[2] === "Tue"); 
console.log(Days[6] === "Sat"); 
```

除了放置普通的常数，enum枚举还允许 另外一种形式，计算所得项。

```ts
enum Color {Red, Green, Blue = "blue".length};
// 输出: 4
console.log(Color["Blue"]);
```

这里的Blue的内容为 一个计算式，输出了“blue”这个字符串的长度。

由于枚举的键默认自动递增的，所以**计算式必须位于所有未手动赋值的内容 后，否则就会报错。**

```ts
enum Color {Red = "red".length, Green, Blue};
// index.ts(1,33): error TS1061: Enum member must have initializer.
// index.ts(1,40): error TS1061: Enum member must have initializer.
```

#### 其余枚举

除了普通枚举以外还处在 `常数枚举`、`外部枚举`。

*常量枚举*

常数枚举就是在枚举前加`const`定义的枚举。

```ts
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

它与普通枚举的区别就是，常数枚举在编译为JS阶段会被删除，并且不能有计算式成员。

上述操作编译为JS为：

```js
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```

*外部枚举（Ambient Enum）*

外部枚举是使用`declare enum`定义的枚举类型，一般在声明文件中定义：

```ts
declare enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

之前提到过，`declare` 定义的类型只会用于编译时的检查，编译结果中会被删除。

所以上面编译js后结果为：

```js
var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
```

当然也可以同时使用`declare`和`const`:

```ts
declare const enum Directions {
    Up,
    Down,
    Left,
    Right
}
```

那样编译js结果就和常量枚举一样:

```js
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
```



### 类（class）

类这一概念在各个面向对象编程语言中都有出现，且为关键概率。但是JavaScript作为面向对象语言，却对其类这个概念一直处于抛弃状态，近二十年来，JavaScript一直通过构造函数实现类的概念。直到近些年的ES6规范推出，才使JavaScript这个语言迎来了`class`。

#### ES6中的类

在ES6中使用 `class` 定义类，使用 `constructor` 定义构造函数。

同样和 其他的语言一样，使用`new`进行生成新的实例对象。

```typescript
class Animal {
    public name;
    constructor(name) {
        this.name = name;
    }
    sayHi() {
        return `My name is ${this.name}`;
    }
}

let a = new Animal('tony');
console.log(a.sayHi()); // My name is tony
```

类还支持继承概率，使用`extends`关键字实现，子类中利用`super`来调用父类构造函数或者方法：

```typescript
class Cat extends Animal {
  constructor(name) {
    super(name); // 调用父类的 constructor(name)
    console.log(this.name);
  }
  sayHi() {
    return 'Meow, ' + super.sayHi(); // 调用父类的 sayHi()
  }
}
```

使用getter、setter（被称为存取器）来进行赋值、取值操作:

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  get name() {
    return 'Jack';
  }
  set name(value) {
    console.log('setter: ' + value);
  }
}
let a = new Animal('Kitty'); // setter: Kitty
a.name = 'Tom'; // setter: Tom
```

**其中setter、getter不同于java中那样是一种方法，ES6的setter、getter是一种计算属性，是直接引用的，而不是使用带有`()`的方法**

上述类中并没有定义自己的属性，实际上生产中我们都不怎么干，而是在类中定义属性，用作存储相关内容。通常在其余语言中使用`private`来定义类的私有属性，但是在ES6中，private 变为了`#属性名`。

```js
class Person {
  #age
  constructor(name) {
    this.name = name; // this is public
    this.#age = 20; // this is private
  }
  greet() {
    // here we can access both name and age
    console.log(`name: ${this.name}, age: ${this.#age}`);
  }
}
let joe = new Person('Joe');
joe.greet();
```

这种写法不仅可以写私有属性，还可以用来写私有方法。之所以要引入一个新的前缀`#`表示私有属性，而没有采用`private`关键字，是因为 JavaScript 是一门动态语言，没有类型声明，使用独立的符号*似乎*是唯一的比较方便可靠的方法。

#### TS中类

虽然ES6完成了对JS的类的实现，但是可以看见，其实这个实现是十分的难用，并且不好理解，在国外的评价是十分糟糕，一些人直接说ES6最糟糕的设计，认为这个类概率其实就是一种语法糖，在技术和概念上并不准确。

而TS的类 引入了一系列的概率，变得完善起来了（吗？）

不同于ES6下的类，TypeScript 可以使用三种访问修饰符（Access Modifiers），分别是 `public`、`private` 和 `protected`。变得和 java一样了...（TypeScript应该改名RealJavaScript才对）

- `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public` 的
- `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问
- `protected` 修饰的属性或方法是受保护的，它和 `private` 类似，区别是它在子类中也是允许被访问的

这里简单区分下，public的属性和private属性区别，public属性在其对象下可以直接使用`=`赋值，可以直接获取到值；而private属性只能在getter、setter方法下获值、赋值。

当private关键字位于构造函数时，那么该类就不能被实体化和被继承了，一般用于单利模式、工厂模式等设计模式下。

此外，属性支持直接赋默认值。

```ts
class Animal {
  private _name: string = "dog";
  public constructor() {}
  get name() {
    return this._name;
  }
  set name(name: string) {
    this._name = name;
  }
}

let dog = new Animal();
// setter call
dog.name = "ss";
// getter call
console.log(dog.name);
```

在函数编程语言中，有一个统一规定，那就是私有变量名前缀带有`_`下划线，表示这是私有变量。正是因为这样，setter和getter直接再去掉下划线即可统一规范，而不是某些语言的`getXXX`、`setXXX`那样。

**抽象类**

`abstract` 用于定义抽象类和其中的抽象方法。

抽象方法不能被实例化，只能用作被继承。抽象类中，可以内置抽象方法，而抽象方法必须要求其继承类去实现，并且本身不能有内容。

```typescript
abstract class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  abstract sayHi();
  run() {
    console.log("This Animal is running");
  }
}
class Cat extends Animal {
  sayHi() {
    console.log(this.name + " is say hi to you!");
  }
}
```



