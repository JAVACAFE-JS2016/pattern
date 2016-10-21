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
}
inherit(Child, Parent);

var parent = new Parent();
var kid = new Child();
```
