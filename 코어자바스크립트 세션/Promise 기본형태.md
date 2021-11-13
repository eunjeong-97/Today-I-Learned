# Promise 기본형태

## Promise

- ES6에서 도입된 개념으로
비동기처리를 통한 콜백지옥같은 자바스크립트의 비효율적인 코드를 극복하기 위해 탄생했다
- 자바스크립트의 내장 클래스이다.

> 클래스와 인스턴스화
**클래스**는 객체를 만들려는 공장 같은것으로, 이러한 공장에서 객체를 만들 때 **인스턴스화**한다고 한다

- Promise 클래스를 인스턴스화해서 promise 객체를 만든다
- 이렇게 만든 promise 객체를 통해 비동기 작업을 할 것이다

> 대표적으로 state, resolve, reject 함수를 먼저 이해하면 된다

## promise 구현하기

Promise 는 클래스이기 때문에 new 라는 생성자를 통해 만들면 된다

```javascript
let promise = new Promise (function(resolve, reject) {
  // 이 자리에 비동기 로직 (즉, 나중에 처리하고 싶은 로직)을 작성하면 된다
  // 시켜놓고 언제 완료될지 모르는 로직
});
```

여기서 resolve, reject 또한 콜백함수인데
resolve 는 성공했을 때 실행할 함수
reject 는 실패했을 때 실행할 함수

여기서 resolve, reject 또한 함수이기 때문에 미리 정의를 해야한다고 생각하겠지만,
미리 정의하지 않아도 자바스크립트 엔진에서 정의해놓기 때문에 에러가 발생하지 않는다.

```javascript
let promise = new Promise(function(resolve, reject) {
  resolve(); // 에러 발생 안함
  reject();
  console.log(‘실행 됨’);
});
```

## resolve

Promise 에서 비동기로직이 성공했을 때 실행할 로직

```javascript
let promise = new Promise(function(resolve, reject) {
  // 비동기 로직 작성
  // 언제 완료될지 모르는 로직
  // 이러한 로직이 완료되면 resolve 호출
  // 비동기 로직이 실패되면 reject 호출
});

promise.then(function() {
  // resolve 함수
  // 위의 비동기 로직이 성공하면 이 함수가 호출된다
```
만약 API호출 성공하면 차트를 그리겠다고 하면 
반환된 `promise 객체에 then() 메서드` 를 통해 차트를 그리는 로직을 써넣으면 된다

> resolve 함수의 매개변수 전달

```javascript
let promise = new Promise(function(resolve, reject) {
setTimeout(function() {
    // resolve 함수에 인자를 넘긴다
    resolve(‘hello world’);
  }, 2000);
});

promise.then(function(msg) {
  console.log(msg); // 2초뒤에 hello world 라는 콘솔이 찍힌다
});
```

## reject 

Promise 에서 비동기로직이 실패했을 때 실행할 로직

```javascript
let promise = new Promise(function(resolve, reject) {
  // 비동기로직
});

// then 인자 둘다 모두 cb 함수
// 첫 번째는 resolve 함수정의
// 두 번째는 reject 함수정의
promise.then(function() {}, function() { // 실패했을 때의 로직}); 
```

```javascript
let promise = new Promise(resolve, reject) {
  setTimeout(function() {
    reject(‘실패!’);  // 실패하지 않니지만, 실패했다고 가정하고 reject 함수를 호출한다
}, 2000);

promise.then(function() {
  console.log(‘resolve’,);
}, function(msg) {
  console.log(‘reject’, msg);
});
```

## Promise의 그 밖의 처리

### finally

promise에서 로직이 실행이 되던 안되던 상관없이 항상 실행하는 것

### all

순차적으로 실행하고 싶은 것이 아니라 비동기처리를 동시에 3개 호출하고 한 번에 응답받고 싶을 때 사용한다

예를 들어 이미지 10개를 순차적으로 보여주는 것이 아니라 10개의 이미지를 모두 다 받으면 실행되도록


## Promise 주의점

resolve, reject 는 async (비동기) 콜백함수이기 때문에 처음에 호출된 한 문장만 실행한다

```javascript
let promise = new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve(1); // 1초뒤에 console.log(1) 실행됨
    resolve(2); // 실행되지 않음
    reject(3); // 실행되지 않음
    reject(
  }, 1000);
});

promise.then(function(msg) {
  console.log(‘resolve’, msg);
}, function(msg) {
  console.log(‘reject’, msg);
);
```	

### 1. 동기함수 비동기 함수 혼용

동기함수 먼저 실행하고 비동기함수 실행되기 때문에 콘솔창에 1,3,2 순서로 찍힌다

```javascript
let promise = new Promise(function(resolve, reject) {
    setTimeout(function() {
        console.log(1);
        resolve(2);
        console.log(3);
        resolve(4);
    }, 1000);
});

promise.then(function(data) {
    console.log(data);
});
```

### 2. Promise 내부의 resolve, reject 함수

Promise 바디 내부에서 resolve, reject 콜백 함수는 첫번째 문장만 실행되고 종료한다

```javascript
let promise = new Promise(function(resolve, reject) {
  resolve(1); // Promise 바디 내부에서 첫번째 문장만 실행되고 종료한다

  setTimeout(function () { 
		resolve(2);
	}, 1000);
});

promise.then(function(data) {
    console.log(data);
});
```

> 생성자 특징

생성자의 특징을 통해 아래와 같은 코드를 실행하면 바로 콘솔에 1이 찍힌다

```javascript
let promise = new Promise(function(resolve, reject) {
  console.log(1); // 클래스 생성자를 통해 인스턴스화를 하게 되면 .then 메서드가 따로 없어도 바로 실행이 된다

setTimeout( function() {
  resolve(2);
}, 1000)l;
});

promise.then(function(data) {
  console.log(data);
});
```

### 3. Promise는 인터넷 익스플로어에서 사용할 수 없다

- 이러한 이유로 순수자바스크립트에서 개발을 해야할 때 바벨을 이용하면 좋다
- 바벨이란, 고차언어를 컴퓨터가 이해할 수 있도록 ES5이하의 저차언어로 변환시켜주는 것이라 라이브러리를 설치할 때, 필수적으로 바벨을 설치하도록 한다

## state

promise 객체는 3가지 상태를 가지고 있다

- Pending (대기) : 비동기 처리 로직이 아직 완료되지 않음
- Fulfilled (이행) : 비동기 처리가 완료되어 promise가 결과 값을 반환해준 상태
- Rejected (실패) : 비동기 처리가 실패하거나 오류가 발생한 상태

Promise 상태에 따라 일을 조정할 일은 거의 없다