## 자바스크립트

### 프로토타입

자바스크립트는 클래스라는 개념이 없기 때문에 기존의 객체를 복사하여 새로운 객체를 생성한다.

자바스크립트는 객체원형(프로토타입)을 이용하여 새로운 객체를 만들어 낸다.

```javascript
function Person() {}

var junho = new Person();
var jisoo = new Person();

Person.prototype.getType = function() {
  return "인간";
};

console.log(junho.getType()); //인간
console.log(jisoo.getType()); //인간
```

자바스크립트에선 기본 데이터 타입을 제외한 모든 것이 객체이다. 객체를 만드려면 프로토타입 객체를 이용해서 만든다. 만들어진 객체 안의 `__proto__` 속성에 자신을 만들어낸 프로토타입 객체를 참조하는 링크가 있다.

<br/>

### 옵저버 패턴

```javascript
var Subject = (function() {
  function Subject() {
    this.subscribers = [];
  }
  Subject.prototype.publish = function() {
    var self = this;
    this.subscribers.forEach(function(subscriber) {
      subscriber.fire(self);
    });
  };
  Subject.prototype.register = function(target) {
    this.subscribers.push(target);
  };
  return Subject;
})();

var Observer = (function() {
  function Observer() {
    this.list = [];
  }
  Observer.prototype.subscribe = function(target) {
    this.list.push({
      target: target,
      point: 0,
    });
    target.register(this);
  };
  Observer.prototype.unsubscribe = function(target) {
    this.list = this.list.filter(function(person) {
      return person.target !== target;
    });
  };
  Observer.prototype.fire = function(target) {
    this.list.some(function(person) {
      console.log(person.target, target, person.target === target);
      if (person.target === target) {
        ++person.point;
        return true;
      }
    });
  };
  return Observer;
})();
```

```javascript
var subject = new Subject();
var observer = new Observer();
observer.subscribe(subject);
subject.publish();
observer.list; //[{ target: Subject, point: 1 }]
```

