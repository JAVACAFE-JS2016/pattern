6장 코드 재사용 패턴
------------------

### 클래스 방식의 상속 패턴

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
// 여기서 상속의 마법이 일어난다.
inherit(Child, Parent);
```

##### 패턴1 - 기본패턴
```javascript
  funtion inherit(C, P) {
    C.prototype = new P();
  }
```
* 단점 : 부모객체의 this에 추가된 객체 자신의 프로퍼티와 프로토타입 프로퍼티를 모두 물려받는다.


#### 패턴2 - 생성자 빌려쓰기
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
var page = New StaticPage();

console.log(article.hasOwnProperty('tags'));
console.log(blog.hasOwnProperty('tags'));
console.log(page.hasOwnProperty('tags'));

```
