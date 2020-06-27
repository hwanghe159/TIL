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

