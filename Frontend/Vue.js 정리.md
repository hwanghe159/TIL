# Vue.js 정리

## 프로젝트 생성

1. Vue.js 설치

   `npm install vue`

2. vue-cli 설치

   `npm install -g @vue/cli`

   설치를 마치고 `vue --version`으로 뷰 버전 확인

3. 프로젝트 생성

   `vue create 프로젝트이름`

4. 프로젝트 실행

   `npm run serve`

## eslint, prettier 설정

1. eslint 설정

   `cd <project-folder>`

   `npm init -y` 

   `npm install eslint`

2. 사용자 환경변수 설정

   eslint 위치 ( C:\Users\junho\todolist-vue-practice\node_modules\.bin ) Path에 추가

3. 초기화

   `eslint --init`

4. 설정 진행 후 설치

5. 루트 경로에 `.eslintrc.js` 생성 확인

6. 인텔리제이 eslint 설정

   Preferences... > Languages & Frameworks > Javascript > Code Quality Tools > ESLint에서

   Manual ESLint configuration 체크 후 설정

7. Prettier 설치

   `npm i -D prettier`

   `npm i -D eslint-config-prettier eslint-plugin-prettier`

8. Prettier 설정

   .eslintrc.js의 extends 마지막에 아래 추가

   ```
   {
     "extends": ["plugin:prettier/recommended"]
   }
   ```

   

## 디렉티브

디렉티브 : HTML의 요소에 대해서 Vue가 어떤 일을 하는지 지정하는 명령어

### v-text

머스태시를 사용하지 않고 data를 표시할때

```html
<div id="app">
    <p v-text="myText"></p>
</div>

<script>
new Vue({
    el: '#app',
    data: {
        myText: 'Hello'
    }
})
</script>
```

### v-html

html을 표현하고자 할때

```html
<div id="app">
    <p v-html="myHtml"></p>
</div>

<script>
new Vue({
    el: '#app',
    data: {
        myHtml: '<h1>Hello</h1>'
    }
})
</script>
```

### {{ $data }}

오브젝트의 전체 데이터 확인할 수 있다.

```html
<div id="app">
    {{ $data }}
    <hr>
    <li v-for="(item, key) in $data">{{key}} : {{item}}</li>
</div>

<script>
new Vue({
    el: '#app',
    data: {
        myText: 'Hello',
        myHtml: '<h1>Hello</h1>',
        myArray: [1,2,3,4,5]
    }
})
</script>
```

### v-bind

태그의 속성을 데이터로 지정하고 싶을때 사용한다. `v-bind`는 생략하고 `:`만 써도 된다.

```html
<div id="app">
    <img v-bind:src="fileName"></img>
	<img :src="fileName"></img>
</div>

<script>
new Vue({
    el: '#app',
    data: {
        fileName: 'face1.png'
    }
})
</script>
```

### v-model

입력과 데이터를 연결할 때 사용한다.

`input`, `select`, `textarea` 태그 등을 사용한다.

```html
<div id="app">
    <input v-model="myName" placeholder="이름">
    <p>나는 {{myName}}입니다.</p>
</div>

<script>
new Vue({
    el: '#app',
    data: {
        myName: ''
    }
})
</script>
```

- `v-model.lazy` : 다 입력하고 나서 vue인스턴스 데이터에 입력하고 싶을때
- `v-model.number` : 입력 내용을 자동으로 수식으로 변경하고 싶을때
- `v-model.trim` : 앞뒤 공백을 자동으로 제거하고 싶을때

### v-on

이벤트와 메서드를 연결할 때 사용한다.

`v-on:`을 `@`로 대체할 수 있다.

```html
<div id="app">
    <p>{{count}}</p>
    <button v-on:click="countUpOne">1씩증가</button>
    <button @click="countUp(3)">3씩증가</button>
    
    <input @keyup.enter="showAlert">
</div>

<script>
new Vue({
    el: '#app',
    data: {
        count: 0
    },
    methods: {
        countUpOne: function() {
            this.count++;
        },
        countUp: function(value) {
            this.count += value;
        },
        showAlert: function() {
            alert("엔터 입력!")
        }
    }
})
</script>
```

### v-if

조건에 따라 HTML을 표시하거나 표시하지 않을때 사용한다.

```html
<div id="app">
    <label><input type="checkbox" v-model="isShow">동의하시겠습니까?</label>
    <p v-if="isshow">동의하셨습니다.</p>
    <p v-else>동의하지않았습니다.</p>
</div>

<script>
new Vue({
    el: '#app',
    data: {
        isShow: false
    }
})
</script>
```

### v-for

HTML태그를 반복해서 표시하고 싶을때 사용한다.

```html
<ul>
    <li v-for="item in objArray">{{item.name}}은 {{item.price}}원 입니다.</li>
</ul>
```

```html
<ul>
    <li v-for="n in 5">{{n}} X 5 = {{n*5}}</li>
</ul>
```

```html
<ul>
    <li v-for="(item, index) in myArray">{{index}}번째에 {{item}}가 있다.</li>
</ul>
```

버튼으로 리스트에 추가/삭제하기

```html
<div id="app">
    <ul>
        <li v-for="item in myArray">{{item}}</li>
    </ul>
    <button v-on:click="addLast">맨뒤에추가</button>
    <button v-on:click="addObj(3)">네번째에추가</button>
    <button v-on:click="changeObj(0)">첫번째를변경</button>
    <button v-on:click="deleteObj(1)">두번째를삭제</button>
</div>

<script>
  new Vue({
    el: '#app',
    data: {
      myArray: ['첫번째', '두번째', '세번째', '네번째', '다섯번째']
    },
    methods: {
      addLast: function () {
        this.myArray.push("[맨뒤에추가]");
      },
      addObj: function (index) {
        this.myArray.splice(index, 0, '['+ (index+1) +'번째에추가]');
      },
      changeObj: function (index) {
        this.myArray.splice(index, 1, '[변경]');
      },
      deleteObj: function (index) {
        this.myArray.splice(index, 1);
      }
    }
  })
</script>
```

### computed

`<p> {{myPrice * 1.08 }} </p>`보단 `<p> {{taxIncluded}} </p>`가 가독성이 좋다.

이처럼 데이터의 값을 계산하여 쓸 때는 computed옵션(산출 프로퍼티)를 사용한다.

데이터가 변하면 다시 계산한다

```html
<div id="app">
    <input v-model.number="price">원 x
    <input v-model.number="count">개
    <p>합계 {{sum}} 원</p>
    <p>세금포함 {{taxIncluded}} 원</p>
</div>

<script>
  new Vue({
    el: '#app',
    data: {
      price: 100,
      count: 1
    },
    computed: {
      sum: function() {
        return this.price * this.count;
      },
      taxIncluded: function () {
        return this.sum * 1.08;
      }
    }
  })
</script>
```

검색결과 즉시반영

```html
<div id="app">
    <input v-model="findWord">
    <ul>
        <li v-for="item in findItems">{{item}}</li>
    </ul>
</div>

<script>
  new Vue({
    el: '#app',
    data: {
      findWord: '',
      items: ['설악산', '한라산', '북한산', '백두산', '지리산']
    },
    computed: {
      findItems: function () {
        if (this.findWord) {
          return this.items.filter((value) => (value.indexOf(this.findWord) > -1), this);
        } else {
          return this.items;
        }
      }
    }
  })
</script>


```

### watch

데이터나 수식의 값이 변할때 자동으로 감지할 수 있다.

프로퍼티가 변하면 메서드를 다시 실행한다.

```html
<div id="app">
    <p>금지문자는 {{forbiddenText}}</p>
    <textarea v-model="inputText"></textarea>
</div>

<script>
  new Vue({
    el: '#app',
    data: {
      forbiddenText: '욕설',
      inputText: ''
    },
    watch: {
      inputText: function () {
        var pos = this.inputText.indexOf(this.forbiddenText);
        if (pos >= 0) {
          alert(this.forbiddenText + "는 입력할 수 없습니다.");
          this.inputText = this.inputText.substr(0, pos);
        }
      }
    }
  })
</script>

```

### transition

표시/비표시때에 애니메이션 효과를 낼 때 사용한다.

```html
<div id="app">
    <label><input type="checkbox" v-model="isOK">변경</label>
    <transition>
        <p v-if="isOK">표시/비표시의 애니메이션</p>
    </transition>
</div>

<script>
  new Vue({
    el: "#app",
    data: {
      isOK: false
    }
  })
</script>
```

```css
.v-enter-active, .v-leave-active { //나타나고 있을때와 사라질때는 0.5초
    transition: 0.5s;
}

.v-enter, .v-leave-to { //나타나기전, 사라진상태엔 투명도가 0, 밑으로 20 이동
    opacity: 0;
    transform: translateY(20px);
}
```

### transition-group

리스트가 증감하거나 위치가 이동할때 애니메이션 효과를 낸다.

```html
<div id="app">
    <transition-group>
        <li v-for="item in dataArray" :key="item">{{item}}</li>
    </transition-group>
    <label><input v-model="inputItem" placeholder="추가할 리스트"></label>
    <button @click="addList">추가</button><p>
    <button @click="removeLast">맨뒤1개삭제</button>
</div>

<script>
  new Vue({
    el: "#app",
    data: {
      dataArray: ['벚꽃', '산수유', '진달래', '철쭉'],
      inputItem: ''
    },
    methods: {
      addList: function () {
        this.dataArray.push(this.inputItem);
        this.inputItem = '';
      },
      removeLast: function () {
        var lastIdx = this.dataArray.length - 1;
        this.dataArray.splice(lastIdx, 1);
      }
    }
  })
</script>
```

```css
.v-enter-active, .v-leave-active { //나타나고 있을때와 사라질때는 0.5초
    transition: 0.5s;
}

.v-enter, .v-leave-to { //나타나기전, 사라진상태엔 투명도가 0, 삭제될땐 오른쪽으로 삭제됨, 추가될땐 왼쪽으로 추가됨
    opacity: 0;
    transform: translateX(50px);
}
```



## vuetify

```vue
<--! sm범위에선 2:10  -->
<v-container>
	<v-row>
        <v-col sm="2">
        <v-col sm="10">
    </v-row>
</v-container>
```

