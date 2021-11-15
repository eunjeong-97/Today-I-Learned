# this

다른언어에서 this는 클래스 안에서 인스턴스 가리킬 때만 사용하는데 자바스크립트에서는 상황에 따라 다르기 때문에 헷갈릴 수 있다

## 함수와 메서드의 차이

- 함수와 메서드는 모두 function 키워드로 함수를 정으한 것을 말한다
- 메서드는 객체의 프로퍼티로 함수가 정의되었을 때 말한다
  - `객체.메서드()` 형태로 호출 : 객체가 함수를 호출해야 메서드이다
  
## this

- this란, this가 바라보고 있는 `객체`인데 상황에 따라 대상이 달라지는 것이다
- 실행컨텍스트가 생성될 때 결정된다
- 실행컨텍스트는 함수를 호출할 때 생성되서 콜스택에 쌓이기 때문에, `this는 함수를 호출할 때 결정`된다

## this의 동작 방식

### 1. 전역 공간에서 this가 바라보는 대상

- client(브라우저)에서는 `this = window`
- Node.js에서는 `this = blobal`

### 2. 메서드로 호출될 때 this가 바라보는 대상 (암시적 binding)

- 객체의 프로퍼티에 할당된 함수를 `호출하면`, this는 해당 객체를 바라본다
- 그렇기 때문에 `this.메서드` 자체를 보면 this가 무엇을 가리키는 건지 모르고 호출해야 확인이 가능하다

```javascript
var name = 'lee';

var user = {
	name: 'kim',
	getName: function() {
		console.log(this.name); // this가 무엇을 가리키는건지 모름
	},
	age: 50,
	child: {
		age: 15,
		underTwenty: function() {
			console.log(this.age); // this가 무엇을 가리키는건지 모름
			return this.age < 20
		}
	}
}

// === 호출하면서 this가 가리키는 것 확인가능 ==
user.getName();  // kim
user.child.underTwenty();  // 15
```

> #### 메서드를 호출할 때, `.` 앞에 어떤 객체가 호출하느냐에 따라 this가 달라진다

- getName 메서드는 user 객체를 바라보기 때문에 `this = user`
underTwenty 메서드는 child 객체를 바라보기 때문에 `this = underTwenty`

```javascript
user.parentUnderTwenty = user.child.underTwenty;
user.parentUnderTwenty(); // 50
```

- user 객체 안에 parentUnderTwenty라는 프로퍼티를 새로 생성하려고 한다.
- parentUnderTwenty 메서드는 user 객체를 바라본다.

```javascript
var name = 'lee';

var user = {
	name: 'kim',
	getName: function() {
		console.log(this.name); // kin | this = user
	
		var inner = function() {
			console.log(this.name); // ✅ 이 부분만 봐서는 모른다
		}

		inner(); // 호출할 때, 앞에 아무것도 없으면 전역으로 찾아야함
	}
}

user.getName();
```


- inner() 함수를 호출할 때 앞 `누가 호출했는지` 에 아무것도 없었기 때문에 window가 객체가 된다
- 따라서 전역변수인 'lee'가 된다

## 원하는 대상으로 this binding (명시적 binding)

이렇게 this가 가리키는 것이 상황에 따라 `호출할 때` 다른데 내 짐작대로 this를 추측해서 코드를 작성하면  `정의할 때` 문제가 발생할 수 있다
이러한 문제를 해결 하기 위해 아래의 함수를 많이 사용한다

### 1. call()

- 함수를 호출할 때, `원하는 대상의 객체` 를 인자로 넘겨준다

```javascript
var user = {
	name: 'kim',
	getName: function() {
		console.log(this.name);
	},
	age: 50,
	child: {
		age: 15,
		underTwenty: function() {
			console.log(this.age);
			return this.age < 20
		}
	}
}

user.child.underTwenty.call(user); // false
```

### 2. apply()

call 메서드와 완전히 같은 기능이나, 호출할 함수에 인자를 배열로 넘기느냐 or not

### 3. bind()

call과 비슷하게 어떤 객체를 사용한다고 명시하기는 하지만, ~~바로 호출하는 것이 아니라~~ 대상을 묶어놓기 (binding) 하는 것

```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    this.handleClick = this.handleClick.bind(this); // 호출하지만, 대상을 묶어놓기만 한다
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```

> #### onClick={this.handleClick}

- 원래 이벤트핸들러의 this는 이벤트를 발생시킨 엘리먼트(요소)를 가리킨다
- 유저가 클릭할 때 자바스크립트 안에서 handleClick를 호출하기 때문에 우리가 통제하지 못하기 때문에 this로 묶어둬야 한다.

## arrow function

this는 해당 메서드를 호출했을 때 결정되는데, arrow function에서의 this는 undefined이며 스코프 한 단계 밖의 스코프 체인 객체를 나타낸다

- this의 대상이 어떤 객체가 호출했느냐로 결정되지 않는다
- 함수 내부에 this는 없으며, scope chain의 가장 가까운 객체를 this의 대상을 결정한다.
- arrow function 내부의 this를 console해보면 undefined가 나온다

```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};
}

  handleClick = () => {
    // this = Toggle Component
    this.setState(prevState => ({ 
      isToggleOn: !prevState.isToggleOn
    }));
  }
  

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```



