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

  for(i = 0,; i< parts.length; i++) {
    if (typeof parent[parts[i]] === "undefined") {
      parent[parts[i]] = {};
      parent = parent[parts[i]];
    }
    parent = parent[parts[i]];    
  }
};
```

### 의존관계 선언
