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

- https://rinae.dev/posts/why-every-beginner-front-end-developer-should-know-publish-subscribe-pattern-kr

```javascript
//Publisher

let data;
let listeners = [];

export function subscribe(callback) {
  listeners.push(callback);
}

function publish(data) {
  listeners.forEach(listener => {
    listener(data);
  });
}

export function setData(data) {
  data = data;
  publish(data);
}

export function getData() {
  return data;
}
```

```javascript
//Subscriber

function renderManagementPage(data) {
  // 정보의 변화가 생길 시 취할 행동 정의
}

subscribe(renderManagementPage);
```

