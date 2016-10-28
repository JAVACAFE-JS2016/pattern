6장 코드 재사용 패턴
--------------------

##### 클래스 방식의 상속 패턴1 - 기본패턴

```javascript
  // 부모생성자
  function Parent(name) {
    this.name = name || 'Adam';
  }
  // 생성자의 프로토타입에 기능을 추가한다.
  Parent.prototype.say = function() {
    return this.name;
  };
  // 아무 내용이 없는 자식 생성자
  function Child(name) {}

  function inherit(C, P) {
    C.prototype = new P();
  }
  // 여기서 상속의 마법이 일어난다.
  inherit(Child, Parent);

  var kid = new Child();
  //kid.name = "Patrick";
  kid.say();
```

-	단점
	1.	부모객체의 this에 추가된 객체 자신의 프로퍼티와 프로토타입 프로퍼티를 모두 물려받는다.
	2.	자식생성자에 인자를 넘겨도 부모 생성자에게 전달하지 못한다.

#### 클래스 방식의 상속 패턴2 - 생성자 빌려쓰기

```javascript
// 부모생성자
function Article() {
  this.tags = ['js', 'css'];
}
var article = new Article();

// 클래스 방식의 패턴#1
function BlogPost() {}
BlogPost.prototype = article;
var blog = new BlogPost();

// 생성자 빌려쓰기 패턴
function StaticPage() {
  Article.call(this);
}
var page = new StaticPage();

console.log(article.hasOwnProperty('tags'));
console.log(blog.hasOwnProperty('tags'));
console.log(page.hasOwnProperty('tags'));
```

```javascript
// 부모 생성자
function Parent(name) {
  this.name = name || 'Adam';
}
// 생성자의 프로토타입에 기능을 추가한다.
Parent.prototype.say = function() {
  return this.name;
};
// 자식 생성자
function Child(name) {
  Parent.apply(this, arguments);
}
var kid = new Child("Patrick");
kid.name;
kid.say;

```

#### 클래스 방식의 상속 패턴3 - 생성자 빌려쓰고 프로토 타입 지정 해주기

```javascript
function Parent(name) {
  this.name = name || 'Adam';
}
// 생성자의 프로토타입에 기능을 추가한다.
Parent.prototype.say = function() {
  return this.name;
};
// 자식 생성자
function Child(name) {
  Parent.apply(this, arguments);
}
Child.prototype = new Parent();

var kid = new Child("Patrick");
kid.name;
kid.say();
delete kid.name;
kid.say();

```

#### 클래스 방식의 상속 패턴4 - 프로토타입 공유

```javascript
function Parent(name) {
  this.name = name || 'Adam';
}
// 생성자의 프로토타입에 기능을 추가한다.
Parent.prototype.say = function() {
  return this.name;
};
// 자식 생성자
function Child(name) {}

//부모의 프로토타입을 공유
function inherit(C, P) {
  C.prototype = P.prototype;
}
inherit(Child, Parent);

var parent = new Parent();
var kid = new Child();
```

#### 클래스 방식의 상속 패턴5 - 임시 생성자

```javascript
function Parent(name) {
  this.name = name || 'Adam';
}
// 생성자의 프로토타입에 기능을 추가한다.
Parent.prototype.say = function() {
  return this.name;
};
// 자식 생성자
function Child(name) {}

// 임시 생성자를 활용한 클래스 방식의 상속

function inherit(C, P) {
  var F = function() {};
  F.prototype = P.prototype;
  C.prototype = new F();
  C.uber = P.prototype;
  C.prototype.constructor = C;
}
inherit(Child, Parent);

var parent = new Parent();
var kid = new Child();
```

#### Klass

```javascript
//Klass
var klass = (function () {
  var F = function () {};
  return function (Parent, props) {
    var Child, i;

    //1. 새로운 생성자
    Child = function() {
      if (Child.uber && Child.uber.hasOwnProperty('__construct')) {
        console.log('Child.uber.__construct 적용');
        Child.uber.__construct.apply(this, arguments);
      }
      if(Child.prototype.hasOwnProperty('__construct')) {
        console.log('Child.prototype.__construct 적용');
        Child.prototype.__construct.apply(this, arguments);
      }
    };

    //2. 상속
    Parent = Parent || Object;

    F.prototype = Parent.prototype;
    Child.prototype = new F();
    Child.uber = Parent.prototype;
    Child.prototype.constructor = Child;

    //3. 구현 메서드를 추가한다.
    for (i in props) {
      if (props.hasOwnProperty(i)) {
        Child.prototype[i] = props[i];
      }
    }

    //'클래스'를 반환한다.
    return Child;
  };
}());

var Man = klass(null, {
  __construct: function (what) {
    console.log("Man's Contructor");
    this.name = what;
  },
  getName: function () {
    return this.name;
  }
});

var SuperMan = klass(Man, {
  __construct: function (what) {
    console.log("SuperMan's Contructor");
  },
  getName: function () {
    var name = SuperMan.uber.getName.call(this);
    return "I am " + name;
  }
});
```

#### 프로토타입을 활용한 상속

```javascript
// 상속해줄 객체
var parent = {
  name: "Papa";
};
// 새로운 객체
var child = object(parent);

// 프로토타입 상속
function object(o) {
  function F() {}
  F.prototype = o;
  return new F();
}
```

#### 프로퍼티 복사를 통한 상속

```javascript
function extend(parent, child) {
  var i;
  child = child || {};
  for (i in parent) {
    if(parent.hasOwnProperty(i)) {
      child[i] = parent[i];
    }
  }
  return child;
}

var dad = {
  counts: [1, 2, 3],
  reads: {paper: true}
};
var kid = extend(dad);
kid.counts.push(4);
dad.counts.toString();   // "1,2,3,4"
dad.reads === kid.reads; // true


//깊은 복사 
function extendDeep (parent, child) {
  var i, 
    toStr = Object.prototype.toString,
    astr = "[object Array]"

    child = child || {};

    for(i in parent) {
      if(parent.hasOwnProperty(i)) {
        if(typeof parent[i] === "object") {
          child[i] = (toStr.call(parent[i]) === astr) ? [] : {};
          extendDeep(parent[i], child[i]);
        } else {
          child[i] = parent[i]
        }
      }
    }
    return child;
}

var dad = {
  counts: [1, 2, 3],
  reads: {paper: true}
};
var kid = extendDeep(dad);
kid.counts.push(4);
kid.counts.toString();   // "1,2,3,4"
dad.counts.toString();   // "1,2,3"

dad.reads === kid.reads; // false
kid.reads.papaer = false;
kid.reads.web = true;
dad.reads.paper; // true
```

#### 믹스-인
```javascript
function mix() {
  var arg, prop, child = {};
  for (arg = 0; arg < arguments.length; arg+=1) {
    for (prop in arguments[arg]) {
      if (arguments[arg].hasOwnProperty(prop)) {
        child[prop] = arguments[arg][prop];
      }
    }
  }
  return child;
}

var cake = mix(
  {eggs: 2, large: true}
  ,{butter: 1, salted: true}
  ,{flour: "3 cups"}
  ,{sugar: "sure!"}
);
```


#### 메서드 빌려쓰기
```javascript
var one = {
  name: "object"
  ,say: function (greet) {
    return greet + ", " + this.name;
  }
};
one.say("hi");

var two = {
  name: "another object"
};
one.say.apply(two, ['hello']);
one.say.call(two, 'hello');

//bind
var twosay2 = one.say.bind(two);
twosay2('Bonjour');
var twosay3 = one.say.bind(two, '안녕');
twosay3();
```

