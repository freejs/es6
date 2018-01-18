# Promis

## 1. Promise란?

자바스크립트는 비동기 처리를 위한 하타의 패턴으로 콜백을 사용한다. 하지만 전통적인 콜백 패턴은 비동기 처리중 발생한 **에러의 예외 처리하기 곤란**하고 여러 갠의 비동기 로직을 한번에 처리하는 것도 한계가 있다. ES6에서 비동기 처리를 위한 또 다른 패턴으로 Promise가 등장했다. Promise는 전통적인 콜백 패턴이 가진 단점을 일부 보완하며 비동기 처리 시점을 명확하게 표현한다.

## 2. 콜백 패턴의 단점

### 2.1 콜백 헬(Callback Hell)

JavaScript에서 빈번히 사용되는 비동기 처리 모델은 요청을 병렬로 처리하여 다른 요청이 blocking(작업 중단)되지 않는 장점이 있지만 단점도 가지고 있는데 그것은 여러개의 콜백함수가 순서를 보장하기 위해 nesting되어 복잡도가 높아지는 **Callback Hell**이다.

![](http://poiemaweb.com/img/callback-hell.png)

비동기 함수는 실행 완료를 기다리지 않고 다음 task를 실행한다. 따라서 비동기 함수 내에서 처리 결과를 return(또는 전역변수에 할당)하면 기대한 대로 동작하지 않는다. 다음 코드를 살펴보자.

```js
function get(url) {
  // XMLHttpRequest 객체 생성
  var req = new XMLHttpRequest();
  // 비동기 방식으로 Request를 오픈
  req.open('GET', url);
  // Request를 전송
  req.send();

  // Event Handler
  req.onreadystatechange = function () {
    // 서버 응답 완료 && 정상 응답
    if (req.readyState === XMLHttpRequest.DONE) {
      if(req.status == 200) {
        console.log(req.response);
        // 비동기 함수는 실행 완료를 기다리지 않고 다음 task를 실행한다. 따라서 비동기 내에서 처리 결과를 return(또는 전역변수에 할당)하면 기대한 대로 동작하지 않는다.
        return req.response;
        // 비동기 함수의 결과에 대한 처리는 이곳에서 진행하여야 한다.
      } else {
        // 서버의 응답이 정상이 아니면
        console.log(Error(req.statusText));
      }
    }
  };
}

var url = 'http://jsonplaceholder.sypicode.com/posts/1';

// get 함수는 비동기 함수이므로 실행 완료를 기다리지 않고 바로 다음 task를 수행한다.
// 즉, 함수의 실행이 완료하여 함수의 반환값을 받기 이전에 다음 task로 진행된다. 따라서 res는 undefined이다.
var res = get(url);
console.log(res); // undefined
```

위 코드를 살펴보면 비동기 함수의 처리 결과를 반환하는 경우, 순서가 보장되지 않기 때문에 그 반환 결과를 가지고 후속 처리를 할 수 없다. 즉, 비동기 함수의 처리 결과에 때한 처리는 비동기 함수의 콜백 함수내에서 처리하여야 한다.

만일 비동기 함수의 처리 결과를 가지고 다른 비동기 함수를 호출해야 하는 경우, 함수의 호출이 nesting이 되어 복잡도가 높아지는 현상이 발생하는데 이를 **Callback Hell**이라 한다.

Callback Hell은 코드의 가독성을 나쁘게 하고 복잡도를 증가시켜 실수를 유발시킬 확률이 높아지며 **에러처리가 곤란**하다.

### 2.2 에러 처리의 한계

에러 처리가 곤란한 것은 콜백 방식 비동기 처리가 갖는 문제이다. 예외(exception)은 caller 방향으로 전파된다. 그리고 비동기처리의 콜백함수는 해당 이벤트가 발생하면 이벤트 큐에 들어가 있다가 Call Stack이 비어졌을 때, 순차적으로 Call Stack으로 이동되어 실행된다. (이것에 대한 상세한 내용은 이벤트 루프와 동시성(Concurrency)를 참고하기 바란다)

이때 콜백함수를 호출한 것은 콜백함수를 갖는 비동기 함수가 아니가 때문에 아래와 같은 에러는 catch되지 않아 프로세스가 종료된다.

> throw 구문은 사용자 정의 예외를 throw한다. 현재의 함수 실행을 중지하고(throw 이후의 구문은 실행되지 않는다) Call Stack의 최초 catch 블록으로 제어를 이동시킨다. Call Stack에 catch 블록이 존재하지 않으면 애플리케이션은 종료한다.

```js
try {
  setTimeout(() => { throw 'Error!'; }, 1000);
} catch (e) {
  console.log('에러를 캐치하지 못한다..');
  console.log(e);
}
```

이러한 문제를 극복하기 위해 Promise가 제안되었다. Promise는 ES6에 정식 채택되어 2017년 1월 현재 **IE를 제외한** 대부분의 브라우저가 지원하고 있다.

## 3. Promise의 상태(State)

Promise는 비동기처리가 성공(fulfilled)하였는지 또는 실패(rejected)하였는지 등의 상태(state) 정보를 갖는다.

상태 - 의미 - 구햔
pending - 비동기 처리가 아직 수행되지 않은 상태 - resolve 또는 reject 함수가 아직 호출되지 않은 상태

fulfilled - 비동기 처리가 수행되 상태 (성공) - resolve 함수가 호출된 상태

rejected - 비동기 처리가 수행된 상태 (실패) - reject 함수가 호출된 상태

settled - 비동기 처리가 수행된 상태 (성공 또는 실패) - resolve 또는 reject 함수가 호출된 상태

## 4. Promise의 생성

Promise는 Promise 생성자를 통해 인스턴스화한다. Promise 생성자는 비동기 작업을 수행할 콜백함수를 인자로 전달받는데 이 콜백함수는 resolve와 reject 콜백함수를 인수로 전달받는다.

```js
var promise = new Promise((resolve, reject) => {
  // 비동기 작업을 수행한다.

  if(/* 비동기 작업 수행 성공*/) {
    resolve('resolved!');
  } else {
    reject(Error('rejected!'));
  }
});
```

Promise 생성자가 인자로 전달받는 콜백 함수는 내부에서 비동기 작업을 수행한다. 이때 비동기 작업이 성공하면 콜백함수의 인자로 전달받은 resolve를 호출하고 실패하면 reject를 호출한다.

## 5. Promise 후속 처리 함수 then, catch

Promise 생성자가 인자로 전달받은 콜백 함수에서 비동기 작업(Timer 함수)을 실행하도록 해보자.

```js
// 비동기 함수
function asyncFunc(param) {
  // Promise 객체 선언과 반환
  return new Promise((resolve, reject) => {
    // 비동기 함수
    setTimeout(() => (param ? resolve('resolved!') : reject('rejected!')), 1000);
  });
}
```

asyncFunc 함수는 함수 내부에서 Promise 객체를 생성하고 반환한다. asyncFunc 함수를 실행하면 asyncFunc 함수는 Promise 객체를 반환하는데 이 Promise 객체는 상태를 갖는다고 하였다. Promise 객체의 상태에 따라 후속 처리 함수(then, catch)를 체이닝 방식으로 호출한다.

> **then**
> then 메소드는 두 개의 콜백 함수를 인자로 전달 받는다. 첫번째 함수는 성공(fulfilled) 시 호출되는 함수이고 두번째 함수는 실패(rejected) 시 호출된다.
>
> **catch**
> 예외 발생 시 호출된다.

```js
// 비동기 함수
function asyncFunc(param) {
  // Promise 객체 선언과 반환
  return new Promise((resolve, reject) => {
    // 비동기 함수
    setTimeout(() => (param ? resolve('resolved!') : reject('rejected!')),1000);
  });
}

// asyncFunc 함수를 호출하면 Promise 객체를 생성하고 반환한다.
// 인자에 true를 전달 : resolve 메소드 호출
asyncFunc(true)
  .then(
    // resolve가 실행된 경우(성공), resolve 함수에 전달된 값이 result에 저장된다.
    result => console.log(result), // resolved!
    // reject가 실행된 경우(실패), reject 함수에 전달된 값이 reason에 저장된다.
    reason => {
      console.log(reason); // rejected!
      throw 'Error: ' + reason;
    }
  )
  .catch(err => console.log(err));

// asyncFunc 함수를 호출하면 Promise 객체를 생성하고 반환한다.
asyncFunc(false)
  .then(
    // resolve가 실행된 경우(성공), resolve 함수에 전달된 값이 result에 저장된다
    result => console.log(result), // resolved!
    // reject가 실행된 경우(실패), reject 함수에 전달된 값이 reason에 저장된다
    reason => {
      console.log(reason); // rejected!
      throw 'Error:' + reason;
    }
  )
  // 예외 발생 시 호출된다.
  .catch(err => console.log(err));
```

위 예제는 비동기적 상황을 만들기 위해 Timer 함수를 사용하였지만 Promise는 XMLHttpRequest를 순서대로 처리하거나 처리 직훔 다른 처리를 해야 할 때 유용하게 사용된다.

then 메소드가 Promise를 반환하도록 하면 이어지는 then 메소드를 Promise chaining할 수 있다. 그리고 then 메소드의 콜백 함수가 반환하는 값은 자동으로 다음에 오는 then 또는 catch 메소드로 전달된다.

아래는 서버로부터 특정 포스트를 취득한 후, 포스트의 작성자의 아이디로 작성된 다른 포스트를 검색하는 예제이다.

```html
asyncFunc(false)
  .then(
    // resolve가 실행된 경우(성공), resolve 함수에 전달된 값이 result에 저장된다
    result => console.log(result), // resolved!
    // reject가 실행된 경우(실패), reject 함수에 전달된 값이 reason에 저장된다
    reason => {
      console.log(reason); // rejected!
      throw 'Error:' + reason;
    }
  )
  // 예외 발생 시 호출된다.
  .catch(err => console.log(err));
위 예제는 비동기적 상황을 만들기 위해 Timer 함수를 사용하였지만 Promise는 XMLHttpRequest를 순서대로 처리하거나 처리 직후 다른 처리를 해야 할 때 유용하게 사용된다.

then 메소드가 Promise를 반환하도록 하면 이어지는 then 메소드를 Promise를 chaining할 수 있다. 그리고 then 메소드의 콜백 함수가 반환하는 값은 자동으로 다음에 오는 then 또는 catch 메소드로 전달된다.

아래는 서버로 부터 특정 포스트를 취득한 후, 포스트 작성자의 아이디로 작성된 다른 포스트를 검색하는 예제이다.

<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Promise example</title>
</head>
<body>
  <h1>Promise example</h1>
  <h3>Result 1</h3>
  <pre id="demo1"></pre>
  <h3>Result 2</h3>
  <pre id="demo2"></pre>
  <script>
    function get(url) {
      // promise 생성과 반환
      return new Promise((resolve, reject) => {
        // XMLHttpRequest 객체의 생성
        var req = new XMLHttpRequest();
        // 비동기 방식으로 Request를 오픈한다
        req.open('GET', url);
        // Request를 전송한다
        req.send();

        // Event Handler
        req.onreadystatechange = function () {
          // 서버 응답 완료 && 정상 응답
          if (req.readyState === XMLHttpRequest.DONE) {
            if (req.status == 200) {
              // resolve 메소드에 전달한 처리 결과는 then 메소드의 첫번째 콜백함수에서 취득 가능
              resolve(req.response);
            } else {
              // 서버의 응답이 정상이 아니면
              // reject 메소드에 전달한 처리 결과는 then 메소드의 두번째 콜백함수에서 취득 가능
              reject(req.statusText);
            }
          }
        };
      });
    }

    const url = 'http://jsonplaceholder.typicode.com';

    get(`${url}/posts/1`)
      .then(response => {
        console.log('Success 1', response);
        document.getElementById('demo1').innerHTML = response;
        // Ajax 요청 결과에 의해 또 다른 Ajax 요청을 실행한다.
        // Request: /posts?userId=1
        // JSON.parse(): JSON 문자열 => 객체.
        console.log(JSON.parse(response).userId);

        return get(`${url}/posts?userId=${JSON.parse(response).userId}`);
        // then 메소드의 콜백 함수가 반환하는 값은 자동으로 다음에 오는 then 또는 catch 메소드로 전달된다.
      })
      .then(response => {
        // Request: /posts?userId=1의 처리 결과를 수신
        console.log('Success 2', response);
        document.getElementById('demo2').innerHTML = response;
      });
  </script>
</body>
</html>
```
