# Iteration protocol & for-of

## 1. 이터레이션 프로토콜(Iteration protocol)

ES6에는 이터러블(iterable)과 이터레이터(iterator)를 정의한 이터레이션 프로토콜(iteration protocol)이 추가되었다.

> 이터러블(iterable)
> 이터러블은 **순회 가능한 자료 구조**이다. \*\*Symbol.iterator를 프로퍼티 key로 사용한 메소드를 구현하는 것에 의해 순회 가능한 자료 구조인 이터러블이 된다.
>
> 이터레이터
> Symbol.iterator를 프로퍼티 key로 사용한 메소드는 이터레이터를 반환한다. 이터레이터는 순회 가능한 자료구조인 이터러블의 요소를 탑색하기 위한 포인터로서 next() 메소드를 갖는 객체이다. next() 메소드는 value, done 프로퍼티를 갖는 객체를 반환하며 이 메소드를 통해 이터러블 객체를 순회할 수 있다.

![](http://poiemaweb.com/img/iteration-protocol.png)

ES6에서 제공하는 빌트인 이터러블은 아래와 같다. 아래의 객체들은 이터레이션 프로토콜을 준수하고 있으며 이터레이터를 반환한다.

> **Array**
> Array.prototype[Symbol.iterator]
>
> **String**
> String.prototype[Symbol.iterator]
>
> **Map**
> Map.prototype[Symbol.iterator]
>
> **Set**
> Set.prototype[Symbol.iterator]
>
> **DOM data structures**
> NodeList.prototype[Symbol.iterator]
> HTMLCollection.prototype[Symbol.iterator]

이터레이션 프로토콜은 이터레이터의 next() 메소드를 통해 다양한 데이터 소스에 순차적으로 접근할 수 있는 일관된 방법을 제시한다.

```js
// 이터러블
// Symbol.iterator를 프로퍼티 key로 사용한 메소드를 구현하여야 한다.
// 배열에는 Array.prototype[Symbol.iterator] 메소드가 구현되어 있다.

const iterable = ['a', 'b', 'c'];

// 이터레이터
// 이터러블의 Symbol.iterator를 프로퍼티 key로 사용한 메소드는 이터레이터를 반환한다.
const iterator = iterable[Symbol.iterator]();

// 이터레이터는 순회 가능한 자료 구조인 이터러블의 요소를 탐색하기 위한 포인터로서 value, done 프로퍼티를 갖는 객체를 반환하는 next() 함수를 메소드로 갖는 객체이다. 이터레이터의 next() 메소드를 통해 이터러블 객체를 순회할 수 있다.

console.log(iterator.next()); // { value: 'a', done: false }
console.log(iterator.next()); // { value: 'b', done: false }
console.log(iterator.next()); // { value: 'c', done: false }
console.log(iterator.next()); // { value: undefined, done: done }
```

아래는 for문을 사용하여 이터레이터의 next() 메소드로 이터러블 객체를 순회하는 예이다.

```js
// 이터러블
const iterable = ['a', 'b', 'c'];

// 이터레이터
const iterator = iterable[Symbol.iterator]();

// 이터레이터의 next() 메소드를 통해 이터러블 객체를 순회
for (;;) {
  const res = iterator.next();
  console.log(res);
  if (res.done) break;
}
```

## 2. for-of 루프

for-of 루프틑 이터러블 객체를 순회한다. for-of 루프는 이터레이터의 next() 메소드를 호출하고 next()메소드가 반환하는 done 프로퍼티가 true가 될 때까지 루핑한다.

```js
// 배열
for (const val of ['a', 'b', 'c']) {
  console.log(val);
}

// 문자열
for (const val of 'abc') {
  console.log(val);
}

// Map
for (const [key, value] of new Map([['a', '1'], ['b', '2'], ['c', '3']])) {
  console.log(`key : ${key} value : ${value}`); // key : a value : 1 ...
}

// Set
for (const val of new Set([1, 2, 3])) {
  console.log(val);
}
```

## 3. 커스텀 이터러블

객체는 이터러블이 아니다. 하지만 이터레이션 프로토콜을 준수하여 이터러블 객체를 만들 수 있다.

피보나치 수열을 구현한 간단한 이터르블 객체를 만들어 보자. Symbol.iterator를 프로퍼티 key로 사용한 메소드를 구현하는 것에 의해 순회 가능한 자료구조인 이터러블이 된다.

```js
const fibonacci = {
  [Symbol.iterator]() {
    let [prev, curr] = [0, 1];
    //순회 카운터
    let step = 0;
    // 최대 순회수
    const maxStep = 10;
    return {
      // fibonacci 객체가 순회할 때마다 next 함수가 호출된다.
      next() {
        [prev, curr] = [curr, prev + curr];
        return { value: curr, done: stepp++ >= maxStep };
      }
    };
  }
};

for (const num of fibonacci) {
  console.log(num);
}

// spread 연산자
const arr = [...fibonacci];
console.log(arr); // [ 1, 2, 3, 5, 8, 13, 21, 34, 55, 89 ]

// 디스트럭처링
const [first, second, ...rest] = fibonacci;
console.log(first, second, rest); // 1 2 [ 3, 5 , 8, 13, 21, 34, 55 ,89 ]
```

Symbol.iterator를 key로 사용한 메소드는 next() 함수를 프로퍼티로 가지는 객체를 반환하여야 한다. 그리고 next() 함수는 done과 value 프로퍼티를 가지는 객체를 반환한다. for-of 는 done 프로퍼티가 true가 될 때까지 반복하며 done 프로퍼티가 true 가 되면 반복을 중지한다.

이터러블 객체는 for-of 루프뿐만 아니라 디스트럭쳐링, spread 연산자, Map 과 Set 의 생성자에도 사용된다.
