# 7. 符号

符号是ES6新增的一个数据类型，它通过使用函数 `Symbol(符号描述)` 来创建。

## 7-1. 普通符号

### 符号的创建与基本特点

符号设计的初衷，是为了给对象设置私有属性。

**私有属性**：只能在对象内部使用，外面无法使用。

符号具有以下特点：

- 没有字面量
- 使用 typeof 得到的类型是 symbol
- **每次调用 Symbol 函数得到的符号永远不相等，无论符号名是否相同**
- 符号可以作为对象的属性名存在，这种属性称之为符号属性
  - 开发者可以通过精心的设计，让这些属性无法通过常规方式被外界访问
  - 符号属性是不能枚举的，因此在 for-in 循环中无法读取到符号属性，Object.keys 方法也无法读取到符号属性
  - Object.getOwnPropertyNames 尽管可以得到所有无法枚举的属性，但是仍然无法读取到符号属性
  - ES6 新增 Object.getOwnPropertySymbols 方法，可以读取符号
- 符号无法被隐式转换，因此不能被用于数学运算、字符串拼接或其他隐式转换的场景，但符号可以显式的转换为字符串，通过 String 构造函数进行转换即可，console.log 之所以可以输出符号，是它在内部进行了显式转换

### 代码示例

#### 1. 创建符号

```javascript
//创建一个符号
const syb1 = Symbol();
const syb2 = Symbol("abc");

console.log(syb1, syb2);
console.log(typeof syb1 === "symbol", typeof syb2 === "symbol")
```

#### 2. 符号的唯一性

```javascript
//创建一个符号
const syb1 = Symbol("这是随便写的一个符号");
const syb2 = Symbol("这是随便写的一个符号");

console.log(syb1, syb2);
console.log(syb1 === syb2) // false，即使描述相同，符号也不相等
```

#### 3. 符号作为对象属性

```javascript
//创建一个符号
const syb1 = Symbol("这是用于对象的一个属性");

const obj = {
    a: 1,
    b: 2,
    [syb1]: 3  //符号属性
}

console.log(obj);
```

#### 4. 符号属性的不可枚举性

```javascript
const syb = Symbol();

const obj = {
    [syb]: 1,
    a: 2,
    b: 3
}

for (const prop in obj) {
    console.log(prop) // 只输出 a 和 b，不输出符号属性
}

console.log(Object.keys(obj)) // 只输出 ['a', 'b']
console.log(Object.getOwnPropertyNames(obj)) // 只输出 ['a', 'b']

//得到的是一个符号属性的数组
const sybs = Object.getOwnPropertySymbols(obj);
console.log(sybs, sybs[0] === syb)
```

#### 5. 使用符号实现私有属性

```javascript
const Hero = (() => {
    const getRandom = Symbol();

    return class {
        constructor(attack, hp, defence) {
            this.attack = attack;
            this.hp = hp;
            this.defence = defence;
        }

        gongji() {
            //伤害：攻击力*随机数（0.8~1.1)
            const dmg = this.attack * this[getRandom](0.8, 1.1);
            console.log(dmg);
        }

        [getRandom](min, max) { //根据最小值和最大值产生一个随机数
            return Math.random() * (max - min) + min;
        }
    }
})();

const h = new Hero(3, 6, 3);
console.log(h);
```

通过使用符号，`getRandom` 方法成为了真正的私有方法，外部无法直接访问。但如果我们知道符号，仍然可以访问：

```javascript
const Hero = (() => {
    const getRandom = Symbol();

    return class {
        constructor(attack, hp, defence) {
            this.attack = attack;
            this.hp = hp;
            this.defence = defence;
        }

        gongji() {
            //伤害：攻击力*随机数（0.8~1.1)
            const dmg = this.attack * this[getRandom](0.8, 1.1);
            console.log(dmg);
        }

        [getRandom](min, max) { //根据最小值和最大值产生一个随机数
            return Math.random() * (max - min) + min;
        }
    }
})();

const h = new Hero(3, 6, 3);
const sybs = Object.getOwnPropertySymbols(Hero.prototype);
const prop = sybs[0];
console.log(h[prop](3, 5))
```

## 7-2. 共享符号

根据某个符号名称（符号描述）能够得到同一个符号。

### 使用方法

```javascript
Symbol.for("符号名/符号描述")  //获取共享符号
```

### 代码示例

#### 1. 基本使用

```javascript
const syb1 = Symbol.for("abc");
const syb2 = Symbol.for("abc");
console.log(syb1 === syb2) // true，相同的描述返回同一个符号

const obj1 = {
    a: 1,
    b: 2,
    [syb1]: 3
}

const obj2 = {
    a: "a",
    b: "b",
    [syb2]: "c"
}

console.log(obj1, obj2);
```

#### 2. 在不同对象间共享符号属性

```javascript
const obj = {
    a: 1,
    b: 2,
    [Symbol.for("c")]: 3
}

console.log(obj[Symbol.for("c")]); // 3
```

#### 3. Symbol.for 的实现原理

```javascript
const SymbolFor = (() => {
    const global = {};//用于记录有哪些共享符号
    return function (name) {
        console.log(global)
        if (!global[name]) {
            global[name] = Symbol(name);
        }
        console.log(global);
        return global[name];
    }
})();

const syb1 = SymbolFor("abc");
const syb2 = SymbolFor("abc");

console.log(syb1 === syb2); // true
```

## 7-3. 知名（公共、具名）符号

知名符号是一些具有特殊含义的共享符号，通过 Symbol 的静态属性得到。

ES6 延续了 ES5 的思想：减少魔法，暴露内部实现！

因此，ES6 用知名符号暴露了某些场景的内部实现。

### 1. Symbol.hasInstance

该符号用于定义构造函数的静态成员，它将影响 instanceof 的判定。

```javascript
obj instanceof A

//等效于

A[Symbol.hasInstance](obj) // Function.prototype[Symbol.hasInstance]
```

#### 代码示例

```javascript
function A() {

}

Object.defineProperty(A, Symbol.hasInstance, {
    value: function (obj) {
        return false;
    }
})

const obj = new A();

console.log(obj instanceof A); // false
```

### 2. Symbol.isConcatSpreadable

该知名符号会影响数组的 concat 方法。

#### 代码示例

```javascript
const arr = [3];
const arr2 = [5, 6, 7, 8];

arr2[Symbol.isConcatSpreadable] = false;

const result = arr.concat(56, arr2)

//  [3, 56, [5,6,7,8]] - 因为 arr2 的 isConcatSpreadable 为 false
//  [3, 56, 5, 6, 7, 8] - 正常情况下会展开

console.log(result)
```

让类数组对象也可以被 concat 展开：

```javascript
const arr = [1];
const obj = {
    0: 3,
    1: 4,
    length: 2,
    [Symbol.isConcatSpreadable]: true
}

const result = arr.concat(2, obj)

console.log(result) // [1, 2, 3, 4]
```

### 3. Symbol.toPrimitive

该知名符号会影响类型转换的结果。

#### 代码示例

```javascript
class Temperature {
    constructor(degree) {
        this.degree = degree;
    }

    [Symbol.toPrimitive](type) {
        if (type === "default") {
            return this.degree + "摄氏度";
        }
        else if (type === "number") {
            return this.degree;
        }
        else if (type === "string") {
            return this.degree + "℃";
        }
    }
}

const t = new Temperature(30);

console.log(t + "!");     // 30摄氏度!
console.log(t / 2);       // 15
console.log(String(t));   // 30℃
```

### 4. Symbol.toStringTag

该知名符号会影响 Object.prototype.toString 的返回值。

#### 代码示例

```javascript
class Person {
    [Symbol.toStringTag] = "Person"
}

const p = new Person();

const arr = [32424, 45654, 32]

console.log(Object.prototype.toString.apply(p));  // [object Person]
console.log(Object.prototype.toString.apply(arr)) // [object Array]
```

### 5. 其他知名符号

ES6 还提供了其他知名符号，用于定制对象的各种行为，使得对象的行为更加可预测和可定制。