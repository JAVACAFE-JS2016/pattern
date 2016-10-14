5장 객체 생성 패턴
------------------

### 네임 스페이스

전역 변수를 기초로 하는 JavaScript의 단점 때문에 여러 스크립트가 한 페이지 안에 함께 있는 소스코드에서는 전역 변수가 많아질 수록 이름이 겹칠 우려가 있다

```javascript
// 안티 패턴
function Parent() = {}
function Child() = {}
// 변수
var some_var = 1;
// 객체
var module1 = {};
module1.data = {a: 1, b: 2};
var module2 = {};
```

네임스페이스를 적용

```javascript
// 전역 객체
var MYAPP = {};
MYAPP.Parent = function () {};
MYAPP.Child = function () {};
// 변수
MYAPP.some_var = 1;
// 객체
MYAPP.module1 = {};
MYAPP.module1.data = {a : 1, b: 2};
MYAPP.module2 = {};
```

네임스페이스를 생성하거나 프로퍼티를 추가하기 전에 먼저 이미 존재하는지 여부를 확인 하는 것이 최선이다.

```javascript
// 위험하다
var MYAPP = {};
// 개선안
if (typeof MYAPP === "undefined") {
  var MYAPP = {};
}
// 또는 더 짧게 쓸 수 있다.
var MYAPP = MYAPP || {};
```

네임스페이스 함수를 만들어 편리하게 생성할 수 있다.

```javascript
// 네임스페이스 함수를 사용한다.
MYAPP.namespace('MYAPP.modules.module2');

var MYAPP = MYAPP || {};

MYAPP.namespace = function(ns_string) {
  var parts = ns_string.split('.'),
      parent = MYAPP,
      i;

  // 처음 중복되는 전역 객체명은 제거
  if(parts[0] === "MYAPP") {
    parts = parts.slice(1);
  }

  for(i = 0 ; i< parts.length ; i++) {
    if (typeof parent[parts[i]] === "undefined") {
      parent[parts[i]] = {};
    }
    parent = parent[parts[i]];    
  }
};
```

### 의존관계 선언

### 비공개 프로퍼티와 메서드

자바 등 다른 언어와는 달리 Javascript에는 객체의 모든 멤버는 public으로 되어있다. 비공개 멤버에 대한 별도의 문법은 없지만 클로저를 사용해서 구현할 수 있다.

```javascript
function Gadget() {
  // 비공개 멤버
  var name = 'iPod' ;
  // 공개된 함수
  this.getName = function () {
    return name;
  }
};

var toy = new Gadget();
// name은 비공개이므로 undefined가 출력
console.log(toy.name);
// 공개 메서드에서는 'name'에 접근 할 수 있다.
console.log(toy.getName());
```

비공개 멤버의 허점

반환 값이 객체나 배열이라면 값이 아닌 참조가 반환되기 때문에 외부에서 값을 변경할 수 있다.

```javascript
function Gadget() {
  // 비공개 멤버
  var specs = {
    screen_width: 320 ,
    screen_height: 480 ,
    color: "white"
  };
  // 공개 함수
  this.getSpecs = function () {
    return specs;
	};
}

var toy = new Gadget();
space = toy.getSpecs();
console.log(toy.getSpecs());

space.color = "black";
space.price = "free";
// 참조가 반환되므로 값이 변경된 것을 확인 할 수 있다.
console.log(toy.getSpecs());
```

### 모듈 패턴

### 샌드박스 패턴

### 스태틱 멤버

### 객체 상수

ES6 const를 사용하자...

### 체이닝 패턴

체이닝 패턴이란 객체에 연쇄적으로 메서드를 호출할 수 있도록 하는 패턴이다. 좀 더 간결해져 가독성이 올라간다. 하지만 에러가 발생할 경우 실패한 메서드가 어느 것인지 알아내기가 어렵다.

```javascript
var obj = {
  value: 1,
  increment: function () {
    this.value += 1;
    return this;
  },
  add: function (v) {
    this.value += v;
    return this;
  },
  shout: function () {
    alert(this.value);
  }
};

// 메서드 체이닝 호출
obj.increment().add(3).shout(); // 5

// 메서드를 하니씩 호출하려면 다음과 같이 해야 한다 .
obj.increment();
obj.add(3);
obj.shout(); // 5
```
