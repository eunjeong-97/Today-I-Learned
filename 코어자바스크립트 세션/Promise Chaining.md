# Promise Chaining

> Promise Chaining 사용하는 이유

여러 개의 비동기 작업을 `순차적` 으로 해야 하는 경우가 많다

예: 순차적으로 각각의 작업이 이전 단계 비동기 작업이 성공하고 나서 그 결과값을 이용해 다음 비동기 작업을 실행할 때

- 상품 호출
- 호출되었을 때의 id를 가지고 상품후기
- 상품후기를 통한 좋아요수

```javascript
getProducts()
	.then(getComments)
	.then(getLikes)
```

## Promise Chaining 형태

- then으로 계속 연결하면 된다
- then 안에 있는 함수 (=resolve 함수)에서 어떻게 반환하냐에 따라 다르게 처리할 수 있다

## then 내부 콜백함수의 반환값

then의 콜백함수 `handler function` 는 여러 타입의 값을 반환할 수 있는데 대표적으로 리턴값과 promise 형태이다

### 1. return 값

첫 번째 then의 return값이 그 다음 then의 매개변수로 들어간다

```javascript
let promise = new Promise(function(resolve, reject) {
    // 비동기상황
    setTimeout(function() {
        resolve(1); // 1초뒤에 성공하면 실행
    }, 1000);
});

promise
	.then(function(first) {
	  console.log('first', first);
	  return 2;
	}).then(function(second) { 
		console.log(second);
	});
```

### 2. promise 반환

반환된 promise 안에 있는 resolve는 그 다음에 있는 then 에 있는 콜백함수이다

```javascript
let promise = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve(1);
    }, 1000);
});

promise
	.then(function(first) {
	  console.log('first', first);
	  return 2;
	})
	.then(function(second) { 
		console.log('second', second);
		
    // 두 번째 then의 콜백함수에서 promise를 return 한다
		return new Promise(function(resolve, reject) {
        setTimeout(function() {
            resolve(3);
        }, 1000);
    });
	})
	.then(function(third) {
		console.log('third', third);
	});
```

> 정리

- 반환되는 것이 일반값이라면 다음 then의 콜백함수의 매개변수이고
promise이면 다음 then의 콜백함수이 resolve가 된다 
- then의 첫 번째 인자는 resolve인 것은 맞는데 누구의 resolve인지 아는 것이 중요하다

## 콜백함수를 Promise 형태로 반환

- 처음에 상품 API를 호출해서 응답이 오면 firstProductId 변수에 id 값 넣어주고 promise 리턴해서
- 상품후기 API를 호출했을 때의 응답값을 다음 then의 콜백함수의 매개변수로 전달한다
- 이 때 매개변수로 전달하는 로직이 없다고 이상하게 생각하는 것이 아니라 Promise Chaining 특성 상 계속 넘길 수 있다
- 단, then 이전에 아무것도 리턴하지 않았을 때 then 안에 있는 콜백함수의 매개변수는 undefined가 나온다

```javascript
axios('<https://api.test.com/proudcts>')
  .then(function(response) {
    let firstProductId = response.products[0].id;
		
		return axios('<https://api.test.com/proudct/comments?id='+firstProductId>);
  })
  .then(function(response) {
		let firstCommentId = response.comments[0].id;
    
		return axios('<https://api.test.com/proudct/comment/likes?id='+firstCommentId>)
  })
	.then(function(response) {
			let likes = response.likes;
			let likesCount = likes.length;
  });
```