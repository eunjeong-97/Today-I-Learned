# async & await

이전에는 Promise를 사용했는데 마찬가지로 비동기처리를 하기위한 것이다

- async function은 ES8데 도입되었으며, 비동기 함수를 선언한다
- async function은 promise를 반환하기 때문에 Promise 또한 잘 알아야 한다
- await를 쓰려면 async 함수 안에 꼭 있어야 한다

async 함수 안에 즉시실행함수를 통해 바로 await 함수를 호출한다

```javascript
(async function() {
	// await 함수 호출
})();

(async () => {
	// await 함수 호출
})();
```

> 즉시 실행함수 IIFE (Immediately invoked function expression)

스코프를 한정짓기 위해 소괄호 안에 넣어서 바로 실행시키는 것이다

```javascript
(function () {
	// 내용
})();

(() => {
	// 내용
})();
```

## async & await 예외처리

`try catch` 를 통해 예외처리를 한다
비동기처리를 할 때에는 예외처리를 잘 해줘야 한다

## async & await 활용

이전에 비동기적으로 처리하는 로직을 구현하기 위해 아래와 같이 Promise Chaining을 이용해서 구현한 것도 

```javascript
axios('https://api.test.com/proudcts')
  .then(function(response) {
    let firstProductId = response.products[0].id;
		return axios('https://api.test.com/proudct/comments?id='+firstProductId);
  })
  .then(function(response) {
		let firstCommentId = response.comments[0].id;
    return axios('https://api.test.com/proudct/comment/likes?id='+firstCommentId)
  })
	.then(function(response) {
			let likes = response.likes;
			let likesCount = likes.length;
  });
```

async & await 형태로 가독성 좋게 변환할 수도 있다

```javascript
(async () => {
  let productResponse = await axios('https://api.test.com/proudcts');
  let firstProductId = productResponse.products[0].id;

  let commentResponse = await axios('https://api.test.com/proudct/comments?id='+firstProductId);
	let firstCommentId = commentResponse.comments[0].id;

  let likeResponse = await axios('https://api.test.com/proudct/comment/likes?id='+firstCommentId);
	let likesCount = likeResponse.likes.length;
})();
```