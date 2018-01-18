# Class

JavaScript는 \*\*프로토타입 기반(prototype-based) 객체지향형 언어다. 비록 다른 객체지향 언어들과의 차이점에 대한 논쟁들이 있긴 하지만, JavaScript는 강력한 객체지향 프로그래밍 능력들을 지니고 있다.

프로토타입 기반 프로그래밍은 클래스가 필요없는(class-free) 객체지향 프로그래밍 스타일로 프로토타입 체인과 클로저 등으로 객체지향 언어의 상속, 캡슐화(정보 은닉) 등의 개념을 구현할 수 있다.

-   JavaScript Object-Oriented Programming

ES5에서는 생성자 함수와 프로토타입을 사용하여 객체 지향 프로그래밍을 구현하였다.

```js
// ES5
var Person = (function () {
  // Constructor
  function Person(name) {
    this._name = name;
  }

  // method
  Person.prototype.sayHi = function () {
    console.log('Hi! ' + this._name);
  };

  // return constructor
  return Person;
})();

var me = new Person('Lee');
me.sayHi(); // Hi! Lee

console.log(me instanceof Person); // true
```

![](http://poiemaweb.com/img/prototype-class.png)

하지만 클래스 기반 언어에 익숙한 프로그래머들은 프로토타입 기반 프로그래밍 방식이 혼란스러울 수 있으며 JavaScript를 어렵게 느끼게하는 하나의 장벽처럼 인식되었다.

ES6의 클래스는 기존 프로토타입 기반 객체지향 프로그래밍보다 클래스 기반 언어에 익숙한 프로그래머가 보다 빠르게 학습할 수 있는 단순명료한 새로운 문법을 제시하고 있다. ES6의 클래스가 새로운 객체지향 모델을 제공하는 것이 아니며 사실 **클래스도 함수**이고 기존 프로토타입 기반 패턴의 Syntactic sugar일 뿐이다.

## 1. 클래스 정의 (Class Definition)

ES6의 클래스는 class 키워드를 사용하여 정의한다. 위에서 살펴본 Person 생성자 함수를 Person 클래스로 정의 해 보자.

```js
class Person {
  constructor(name) {
    this._name = name;
  }

  sayHi() {
    console.log(`Hi ! ${this._name}`);
  }
}

const me = new Person('Lee');
me.sayHi(); // Hi! Lee

console.log(me instanceof Person); // true
```

표현식으로도 클래스를 정의할 수 있다. 함수와 마찬가지로 클래스는 이름을 가질 수도 갖지 않을 수도 있다. 이때 클래스가 할당된 변수를 사용해 클래스를 생성하지 않고 기명 클래스의 클래스명을 사용해 클래스를 생성하면 에러가 발생한다. 이는 함수와 마찬가지로 클래스 표현식에서 사용한 클래스명은 외부 코드에서 접근 불가능하기 때문이다. 자세한 내용은 함수표현식(Function expression)을 참조하기 바란다.

```js
const Foo = class MyClass{};

const Foo = new Foo();
console.log(foo); // MyClass {}

new MyClass(); // ReferenceError: MyClass is not defined
```

## 2. 인스턴스의 생성

클래스의 인스턴스를 생성하려면 new 연산자와 함께 생성자를 호춣한다.

```js
class Foo {}
const foo = new Foo();
```

**new 연산자**를 사용하지 않고 생성자를 호출하면 에러가 발생한다.

```js
class Foo {}

const foo = Foo(); // TypeError: Class constructor Foo cannot be invoked without 'new'
```

## 3. constructor

**constructor**는 인스턴스를 생성하고 초기화하기 위한 특수한 메소드이다. constructor는 클래스 내에 한 개 만 존재할 수 있으며 만약 클래스가 2개 이상의 constructor 메소드를 포함하면 SyntaxError가 발생한다.

인스턴스를 생성할 때 new 연산자와 함께 호출한 것이 바로 constructor이며 constructor의 파라미터에 전달한 값은 멤버 변수에 할당한다.

constructor는 생략 가능하다. constructor를 생략하면 클래스에 `constructor() {}`를 포함한 것과 동일하게 동작한다. 따라서 빈 객체가 생성되기 때문에 인스턴스의 생성과 동시에 멤버 변수의 생성 및 초기화를 실행할 수 없다.

```js
class Foo {}

const foo = new Foo();
console.log(foo); // Foo {}

foo.num = 1;      // 동적 프로퍼티 추가
console.log(foo); // Foo { num: 1 }

class Bar {
  // constructor는 인스턴스의 생성과 동시에 멤버 변수의 생성 및 초기화를 실행할 수 있다.
  constructor(num) {
    this.num = num;
  }
}

console.log(new Bar(1)); // Bar { num: 1 }
```

## 4. 멤버 변수

클래스 바디에는 메소드만을 포함할 수 있다. 클래스 바디에 멤버 변수를 선언하면 SyntaxError가 발생한다.

```js
class Foo {
  let name = ''; // SyntaxError

  constructor() {}
}
```

따라서 멤버 변수의 선언과 초기화는 반드시 constructor 내부에서 실시한다.

```js
class Foo {
  constructor(name) {
    this.name = name; // 멤버변수의 선언과 초기화
  }
}

console.log(new Foo('Lee')); // Foo { name: 'Lee' }
```

constructor 내부에서 선언한 멤버 변수 name은 this(클래스의 인스턴스)에 바인딩되어 있으므로 언제나 `public`이다. 즉 외부에서 참조 가능하다.

```js
class Foo {
  constructor(name) {
    this.name = name; // 멤버변수의 선언과 초기화
  }
}

const foo = new Foo('Lee');
console.log(foo.name); // Lee
```

ES6 class는 private, public, protected 키워드와 같은 접근 제한자(Access modifier)를 지원하지 않는다.

## 5. 호이스팅(Hoisting)

클래스는 let, const와 같이 호이스팅되지 않는 것처럼 동작한다. 즉 class 선언문 이전에 class를 참조하면 referenceError가 발생한다.

```js
const foo = new Foo(); // ReferenceError: Foo is not defined
class Foo {}
```

자바스크립트는 ES6의 class를 포함하여 모든 선언(var, let, const, function, function\*, class)를 호이스팅한다. 하지만 클래스는 스코프의 선두에서 class의 선언까지 **일시적 사각지대(Temporal Dead Zone; TDZ)**에 빠지게 되기 때문에 class 선언문 이전에 class를 참조하면 ReferenceError가 발생한다.

ES6 Class도 사실은 함수이다. function 키워드로 선언한 함수와 같은 방식으로 호이스팅하지 않고 let, const 키워드로 선언한 함수표현식과 같이 호이스팅한다.

## 6. getter, setter

### 6.1 getter

getter는 어떤 멤버 변수에 접근할 때마다 멤버 변수의 값을 조작하는 행위가 필요할 때 사용한다. 사용 방법은 아래와 같다.

```js
class Foo {
  constructor(arr = []) {
    this._arr = arr;
  }

  // getter: firstElem은 멤버 변수 이름과 같이 사용된다.
  // getter는 반드시 무언가를 반환하여야 한다.
  get firstElem() {
    return this._arr.length ? this._arr[0] : null;
  }
}

const foo = new Foo([1, 2]);
// 프로퍼티 firstElem에 접근하면 getter가 호출된다.
console.log(foo.firstElem); // 1
```

### 6.2 setter

setter는 어떤 멤버 변수에 값을 할당할 때마다 멤버 변수의 값을 조작하는 행위가 필요할 때 사용한다. 사용 방법은 아래와 같다.

```js
class Foo {
  constructor(arr = []) {
    this._arr = arr;
  }

  // getter: firstElem은 멤버 변수 이름과 같이 사용된다.
  // getter는 반드시 무언가를 반환하여야 한다.
  get firstElem() {
    return this._arr.length ? this._arr[0] : null;
  }

  // setter: firstElem은 멤버 변수 이름과 같이 사용된다.
  set firstElem(elem) {
    // ...this._arr은 this._arr를 개별 요소로 분리한다
    this._arr = [elem, ...this._arr];
  }
}

const foo = new Foo([1, 2]);

// 멤버 변수 lastElem에 값을 할당하면 setter가 호출된다.
foo.firstElem = 100;

console.log(foo.firstElem); // 100
```

## 7. 정적 메소드 (Static method)

**static** 키워드는 클래스의 정적(static) 메소드를 정의한다. 정적 메소드는 클래스의 인스턴스화(instantiating)없이 호출하며 클래스의 **인스턴스로 호출할 수 없다**. 정적 메소드는 Math객체의 메소드와 같이 애플리케이션 전역에서 사용할 유틸리티(utility) 함수를 생성하는데 주로 사용된다.

```js
class Foo {
  constructor(prop) {
    this.prop = prop;
  }
  static staticMethod() {
    return 'staticMethod';
  }
  prototypeMethod(){
    return 'prototypeMethod';
  }
}

const foo = new Foo(123);

console.log(Foo.staticMethod());
console.log(foo.staticMethod()); // Uncaught TypeError: foo.staticMethod is not a function
```

위에서도 언급했지만 사실 Class도 함수이고 기존 prototype 기반 패턴의 Syntactic sugar일 뿐이다.

위 예제를 ES5로 표현해보면 아래와 같다.

```js
var Foo = (function () {
  function Foo(prop) {
    this.prop = prop;
  }
  Foo.staticMethod = function () {
    return 'staticMethod';
  };
  Foo.prototype.prototypeMethod = function () {
    return 'prototypeMethod';
  };
}());

var foo = new Foo(123);

console.log(Foo.staticMethod());
console.log(foo.staticMethod()); // Uncaught TypeError: foo.staticMethod is not a function
```

ES5로 표현한 위 코드는 ES6 Class로 표현한 코드와 정확히 동일하게 동적한다.

정적 메소드는 클래스의 인스턴스화(instantiating)없이 호출하며 클래스의 인스턴스로 호출할 수 없는 이유에 대해 알아보자. prototype과 JavaScript OOP에 대한 사전 지식이 필요하므로 아직 이에 대한 학습이 안되어 있으면 skip하기 바란다.

우선 Foo는 **함수**이다. Class도 사실 함수라고 위에서 언급하였다.

```js
class Foo {
  constructor() {}
}

console.log(typeof Foo); // function
```

함수 객체는 prototype 프로퍼티를 갖는데 일반 객체의 \[[Prototype]] 프로퍼티와는 다른것이며 일반 객체는 prototype 프로퍼티를 가지지 않는다.

함수 객체만이 가지고 있는 **prototype 프로퍼티는 함수 객체가 생성자로 사용될 때 이 함수를 통해 생성된 객체의 부모 역할을 하는 객체**를 가리킨다. 즉 Foo는 함수이고 생성자 함수로 사용되므로 함수 Foo의 prototype은 함수 Foo로 생성되는 객체 foo의 부모 역할을 한다.

```js
console.log(Foo.prototype === foo.__proto__); // true
```

그리고 prototype이 가지고 있는 constructor 프로퍼티는 함수 객체 자신을 가리킨다.

```js
console.log(Foo.prototype.constructor === Foo); // true
```

**정적 메소드인 staticMethod는 함수 객체 Foo의 member, 프로토타입 메소드인 prototypeMethod는 Foo.prototype의 member가 되므로 staticMethod는 foo에서 호풀할 수 없게 된다.**

```js
class Foo {
  constructor(prop) {
    this.prop = prop;
  }
  static staticMethod() {
    return 'staticMethod';
  }
  prototypeMethod() {
    return 'prototypeMethod';
  }
}
const foo = new Foo(123);

console.log(typeof Foo.staticMethod); // function
console.log(Foo.staticMethod());      // staticMethod

console.log(typeof Foo.prototype.prototypeMethod); // function
console.log(foo.prototypeMethod());                // prototypeMethod

console.log(foo.staticMethod()); // TypeError: foo.staticMethod is not a function
```

![](http://poiemaweb.com/img/class-prototype.png)

## 8. 클래스 상속 (Class Inheritance)

상속(또는 확장)은 코드 재사용의 관점에서 매우 유용하다. 새롭게 정의할 클래스가 기존에 있는 클래스와 매우 유사하다면, 동일한 구현은 상속을 통해 그대로 사용하고 다른 점만 구현하면 된다. 코드 재사용은 개발 비용을 현저히 줄일 수 있는 잠재력이 있기 때문에 매우 중요하다.

### 8.1 extends 키워드

**extends** 키워드는 부모 클래스(Base class)를 상속하는 자식 클래스(Sub class)의 생성을 위해 클래스 선언에 사용된다.

부모 클래스 Animal을 상속받는 Human 클래스를 정의해 해보자.

```js
// Base Class
class Animal {
  constructor(weight) {
    this._weight = weight;
  }

  weight() {
    console.log(this._weight);
  }

  eat() { console.log('Animal eat.'); }
}

// Sub Class
class Human extends Animal {
  constructor(weight, language) {
    super(weight);
    this._language = lanuage;
  }

  // 부모 클래스의 eat 메소드를 오버라이드하였다
  eat { console.log('Human eat.'); }

  speak() {
    console.log(`Koreans speak ${this._language}.`);
  }
}

const korean = new Human(70, 'Korean');
korean.weight(); // 70
korean.eat();    // Human eat.
korean.speak();  // Koreans speak Korean.

console.log(korean instanceof Human);  // true
console.log(korean instanceof Animal); // true
```

> **오버로딩(Overloading)**
> 매개변수의 타입 또는갯수가 다른, 같은 이름의 메소드를 구현하고 매개변수에 의해 메소드를 구별하여 호출하는 방식이다. 자바스크립트는 오버로딩을 지원하지 않지만 arguments 객체를 사용하여 구현할 수는 있다.
>
> **오버라이딩(Overriding)**
> 상위 클래스가 가지고 있는 메소드를 하위 클래스가 재정의하여 사용하는 방식이다.

위 코드를 프로토타입 관점으로 표현하면 아래와 같다. 인스턴스 korean은 프로토타입 체인에 의해 부모 클래스의 메소드를 사용할 수 있다.

> **프로토타입 체인**
> 특정 객체의 프로퍼티나 메소드에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티 또는 메소드가 없다면 \[[Prototype]] 프로퍼티가 가리키는 링크를 따라 자신의 부모 역할을 하는 프로토타입 객체의 프로퍼티나 메소드를 차례대로 검색한다.

![](http://poiemaweb.com/img/class-extends.png)

### 8.2 super 키워드

**super 키워드는 부모 클래스의 프로퍼티를 참조(Reference)할 때 또는 부모 클래스의 constructor를 호출할 때 사용한다.**

아래 예제의 super는 Child의 부모클래스인 Parent를 가리킨다.

```js
class Parent {
  construcgtor(x, y) {
    this._x = x;
    this._y = y;
  }

  toString() {
    return `${this._x}, ${this._y}`;
  }
}

class Child extends Parent {
  constructor(x, y, z) {
    // super 메소드는 자식 class의 constructor 내부에서 부모 클래스의 constructor(super-constructor)를 호출한다.
    super(x, y);
    this._z = z;
  }

  toString() {
    // super 키워드는 부모 클래스(Base Class)에 대한 참조이다. 부모 클래스의 프로퍼티 또는 메소드를 참조하기 위해 사용한다.
    return `${super.toString()}, ${this._z}`; // B
  }
}

const child = new Child(1, 2, 3);
console.log(child.toString()); // 1, 2, 3
```

super()는 부모 클래스의 constructor를 호출한다. 즉, 부모 클래스를 생성한다. **자식 클래스의 constructor에서 super()를 호출하지 않으면 ReferenceError가 발생한다**.

```js
class Parent {}

class Child extends Parent {
  constructor() {} // ReferenceError: this is not defined
}

const child = new Child();
```

**super()를 호출하기 이전에는 this를 참조할 수 없다.**

```js
class Parent {
  constructor(x) {
    this._x = x;
  }
}

class Child extends Parent {
  constructor(x, y) {
    // console.log(this); // ReferenceError: this is not defined
    super(x);
    this._y = y;
    console.log(this); // Child { _x: 1, _y: 2 }
  }
}

const child = new Child(1, 2);
```

### 8.3 static 메소드와 prototype 메소드의 상속

prototype 관점에서 바라보면 자식 클래스의 \[[prototype]]은 부모 클래스이다.

```js
class Parent {}

class Child extends Parent {}

console.log(Child.__proto__ === Parent); // true
console.log(Child.prototype.__proto__ === Parent.prototype); // true
```

자식 클래스 Child의 \[[prototype]]은 부모 클래스 Parent이다. 그림으로 표현해보면 아래와 같다.

![](http://poiemaweb.com/img/class-prototype-relation.png)

이것은 Prototype chain에 의해 부모 클래스의 정적 메소드도 상속됨을 의미한다.

```js
class Parent {
  static staticMethod() {
    return 'staticMethod';
  }
}

class Child extends Parent {}

console.log(Parent.staticMethod()); // 'staticMethod'
console.log(Child.staticMethod());  // 'staticMethod'
```

자식 클래스의 정적 메소드 내부에서도 super를 사용하여 정적 메소드를 호출할 수 있다. 이는 자식 클래스는 프로토타입 체인에 의해 부모 클래스의 정적 메소드를 참조할 수 있기 때문이다.

하지만 자식 클래스의 일반메소드(프로토타입 메소드) 내부에서는 super를 사용하여 정적 메소드를 호출할 수 없다. 이는 자식 클래스의 인스턴스는 프로토타입 체인에 의해 부모 클래스의 정적 메소드를 참조할 수 없기 때문이다.

```js
class Parent {
  static staticMethod() {
    return 'Hello';
  }
}

class Child extends Parent {
  static staticMethod() {
    return `${super.staticMethod()} wolrd`;
  }

  prototypeMethod() {
    return `${super.staticMethod()} wolrd`;
  }
}

console.log(Parent.staticMethod()); // 'Hello'
console.log(Child.staticMethod());  // 'Hello wolrd'
console.log(new Child().prototypeMethod());  // TypeError: (intermediate value).staticMethod is not a function
```

![](http://poiemaweb.com/img/class-prototype-chain.png)
