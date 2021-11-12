# Promise 에러처리

비동기에서 에러처리가 되면 언제 사고날지 모르기 때문에 주의해야한다
개발자가 의도적으로 에러가 발생한다는 상황을 가정해서 reject를 호출하거나
reject 호출해서 then의 두 번째 인자를 정의하는 것 대신, 가독성이 좋은 catch를 하는 경우가 대부분이다

## Promise 에러처리 방법

- reject
- catch

## catch를 통한 Promise 에러처리

```javascript
let promise = new Promise(function(resolve, reject) {
	setTimeout(function() {
        reject('으악!');
  }, 2000);
});

promise
	.then(function() {})
	.catch(**function(err) {
		console.log(err);
	}**);

```

## Promise 연습문제

```javascript
function job() {
    return new Promise(function(resolve, reject) {
        reject();
    });
}

job()
	.then(function() {
    console.log(1);
	})
	.then(function() {
    console.log(2);
	})
	.then(function() {
    console.log(3);
	})
	.catch(function() {
    console.log(4);
	})
	.then(function() {
    console.log(5);
	});

```

promise에서 reject를 호출했기 때문에 처음에 콘솔에 4가 찍히고 
한편으로는 catch 메서드에서 promise 를 리턴하기 때문에 아래의 then도 같이 실행되기 때문에 콘솔에 5도 찍힌다

```javascript
function job(state) {
    return new Promise(function(resolve, reject) {
        if (state) {
            resolve('success');
        } else {
            reject('error');
        }
    });
}

job(true)
	.then(function(data) {. // 첫 번째 then
    console.log(data);

    return job(false);
	})
	.catch(function(error) {  // 두 번째 catch
    console.log(error);

    return 'Error caught';
	})
	.then(function(data) {. // 세 번째 then
    console.log(data);

    return job(true);
	})
	.catch(function(error) { // 네 번째 catch
    console.log(error);
	});
```

1. 처음에 promise에서 state=true이기 때문에 resolve를 호출해서 첫 번째 then 콜백함수가 실행된다

    - 이 때 콘솔에 success가 찍히고
    - job(false)라는 promise를 반환하기 때문에 다시 promise를 실행하는데
    - 이번에는 state = false 이기 때문에 reject(‘error’)가 호출된다
  
2. reject(‘error’)가 호출되면 두 번째 catch의 콜백함수가 실행된다

    - 콘솔에 error가 찍히고
    - ‘Error caught’가 return값으로 나온다
    - 일반값이 반환되기 때문에 그 다음에 나오는 then의 콜백함수의 매개변수가 ‘Error caught’로 실행된다

3. 이전에 일반값이 리턴되었기 때문에 세 번째 then의 콜백함수의 매개변수 data = ‘Error caught’ 가 들어가게 된다

   - 콘솔에 ‘Error caught’ 가 찍히고
   - job(true)로, promise가 리턴되기는 하지만 이후에 `state=true 이기 때문에` 실행할 then이 없어서 더 이상 chaining되지 않는다



