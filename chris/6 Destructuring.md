# Destructuring

디스트럭쳐링(Destructuring)은 기존에 구조로 가지고 있던 객체(배열 또는 객체)를 분석하여 개별적인 변수에 할당하는 것이다. 배열 또는 객체 리터럴에서 필요한 값만을 추출하여 변수에 할당하거나 반환할 때 유용하다.

## 1. 배열 디스트럭처링 (Array Destructuring)

ES5의 경우, 배열의 각 요소를 배열로부터 분리하여 변수에 할당하기 위한 방법은 아래와 같다.

```js
// ES5
var arr = [1, 2, 3];

var one = arr[0];
var two = arr[1];
var three = arr[2];

console.log(one, two, three); // 1 2 3
```

배열 디스트럭처링은 배열의 각 요소를 배열로부터 추출하여 변수 리스트에 할당한다. 이때 추출/할당 기준은 **인덱스**이다.

```js
// ES6 Destucturing
const arr = [1, 2, 3];

// 인덱스를 기준으로 배열로부터 요소를 추출하여 변수에 할당
const [one, two, three] = arr;

console.log(one, two, three); // 1 2 3
```

배열 디스트럭처링을 위해서는 할당 연산자 왼쪽에 배열 형태의 변수 리스트가 필요하다.

```js
let x, y, z;
[x, y, z] = [1, 2, 3];

// 위의 구문과 동치이다.
let [x, y, z] = [1, 2, 3];
```

왼쪽의 변수 리스트와 오른쪽의 배열은 인덱스를 기준으로 할당된다.

```js
let x, y, z;

[x, y] = [1, 2];
console.log(x, y); // 1 2

[x, y] = [1];
console.log(x, y); // 1 undefined

[x, y] = [1, 2, 3];
console.log(x, y); // 1 2

[x, , z] = [1, 2, 3];
console.log(x, z); // 1 3

// default value
[x, y, z = 3] = [1, 2];
console.log(x, y, z); // 1 2 3

[x, y = 10, z = 3] = [1, 2];
console.log(x, y, z); // 1 2 3

// spread operator
[x, ...y] = [1, 2, 3];
console.log(x, y); // 1 [ 2, 3 ]
```

배열 디스트럭처링은 배열에서 필요한 요소만 추출하여 변수에 할당하고 싶은 경우에 유용하다.

```js
const arr = [1, 2, 3, 4];
const [one, , three] = arr;

console.log(one, three); // 1 3
```

## 2. 객체 디스트럭처링 (Object destructuring)

ES5의 경우, 객체의 각 프로퍼티를 객체로부터 분리하여 변수에 할당하기 위해서는 프로퍼티 명(키)를 사용하여야 한다.

```js
// ES5
var obj = { firstName: 'Ungmo', lastName: 'Lee' };
var name = {};

name.firstName = obj.firstName;
name.lastName  = obj.lastName;

console.log(name); // { firstName: 'Ungmo', lastName: 'Lee' }
```

객체 디스트럭처링은 객체의 각 프로퍼티를 객체로부터 추출하여 변수 리스트에 할당한다. 이때 할당 기준은 **프로퍼티 이름**(키)이다.

```js
// ES6 Destructuring
const obj = { firstName: 'Ungmo', lastName: 'Lee' };

const { firstName, lastName } = obj;

console.log(firstName, lastName); // Ungmo Lee
```

객체 디스트럭처링을 위해서는 할당 연산자 왼쪽에 객체 형태의 변수 리스트가 필요하다.

```js
const { prop1: p1, prop2: p2 } = { prop1: 'a', prop2: 'b' };
console.log(p1, p2); // 'a' 'b'
console.log({ prop1: p1, prop2: p2 }); // { prop1: 'a', prop2: 'b' }

// 아래는 위의 축약형이다
const { prop1, prop2 } = { prop1: 'a', prop2: 'b' };
console.log({ prop1, prop2 }); // { prop1: 'a', prop2: 'b' }

// default value
const { prop1, prop2, prop3 = 'c' } = { prop1: 'a', prop2: 'b' };
console.log({ prop1, prop2, prop3 }); // { prop1: 'a', prop2: 'b', prop3: 'c' }
```

객체 디스트럭처링은 객체에서 프로퍼티 이름(키)으로 필요한 프로퍼티 값만을 추출할 수 있다.

```js
function margin() {
  const left = 1, right = 2, top = 3, bottom = 4;
  return { left, right, top, bottom };
}
const { left, bottom } = margin();
console.log(left, bottom); // 1 4
```

중첩 객체의 경우는 아래와 같이 사용한다.

```js
function settings() {
  return { display: { color: 'red' }, keyboard: { layout: 'qwerty'} };
}
const { display: { color: displayColor }, keyboard: { layout: keyboardLayout }} = setting();

console.log(displayColor, keyboardLayout); // red qwerty
```
