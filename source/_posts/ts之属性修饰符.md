---
title: ts之属性修饰符
date: 2023-12-13
tags: [ts, 修饰符]
---

## 一、public

> `public` 修饰的属性可以在任意位置访问和修改

```js
class Person {
  name: string; // 等同于 public name: string; public是默认修饰符
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
```

也可以换成如下写法：

```js
class Person {
  constructor(public name: string, public age: number) {
  }
}

const kv = new Person('kv', 18)
console.log(kv); // Person {name: 'kv', age: 18}
```

<!-- more -->

## 二、private

> `private` 修饰的属性是私有属性，私有属性只能在类的内部访问和修改，它的子类无法访问和修改，它的实例也无法访问

```js
class Person {
  private name: string
  private age: number

  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
}
```

如果想要访问 `private` 修饰的属性，可以通过 `set/get`

```js
class Person {
  private _name: string
  private _age: number

  constructor(name: string, age: number) {
    this._name = name
    this._age = age
  }

  get age() {
    return this._age
  }

  set age(value) {
    if(value >= 0) {
      this._age = value
    }
  }
}

const kv = new Person('kv', 18)
console.log(kv.age); // 18
kv.age = -33
console.log(kv.age); // 18
kv.age = 19; // 18
console.log(kv.age); // 19
```

## 三、protect

> `protect` 受保护的属性，只能在当前类和它的子类中访问和修改，但是它们的实例都不能访问

```js
class Person {
  protected _name: string
  protected _age: number

  constructor(name: string, age: number) {
    this._name = name
    this._age = age
  }
}

class Male extends Person{
  test() {
    console.log(this._age);
  }
}

const kv = new Male("kv", 18)
kv.test() // 18
```
