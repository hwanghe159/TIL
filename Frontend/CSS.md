# CSS

 ## 선택자(selectors)

- `*` : 전체

- `div`, `span`등 태그 : 해당 태그

- `#` : id

- `.` : class

- `:` : state

  - ```
    button:hover {
    	background: red;
    } //버튼에 마우스 올리면 빨간색 됨.
    ```

- `[]` : attribute

  - ```
    a[href&=".com"] {
    	color: purple;
    } //a태크의 href속성의 끝이 .com인 애들만 보라색
    ```



## Display

- 블럭 : 상자인데 한 줄당 하나
- 인라인 : 물건
- 인라인블럭 : 상자인데 한 줄에 여러개가 진열될 수 있는 특별한 상자

- div는 블럭레벨 (`display: inline`하면 인라인으로 바뀜)
- span은 인라인레벨(`display: block`하면 블럭으로 바뀜)



## Position

- `position: static;` 는 기본값이다. HTML에 정의된 순서대로 브라우저 상에 자연스럽게 보여짐
- `position: relative;` 는 원래 있어야 하는 자리에서 옮겨감
- `position: absolute;` 는 내 아이템이 담겨있는 아이템과 가장 가까이에 있는 박스 안에서 위치 변경됨
- `position: fixed;` 는 윈도우 기준으로 움직임
- `position: sticky;` 는 원래 있어야 하는 자리에 있으면서, 스크롤되어도 고정됨



## Flexbox

- 컨테이너에 적용할 수 있는 속성값과 컨테이너에 속하는 아이템들에 적용할 수 있는 속성값으로 나뉜다.
  - 컨테이너에 적용할 수 있는 속성들
    - display
      - `display: flex;` 하는 순간 아이템들이 자동으로 정렬됨
    - flex-direction
      - 기본값은 `flex-direction: row` (왼쪽에서 오른쪽으로)
      - `flex-direction: row-reverse;` (오른쪽에서 왼쪽으로)
      - `flex-direction: column`은 위에서 아래, reverse붙일수도 있음
    - flex-wrap
      - 기본값은 `flex-wrap: nowrap ` 화면이 작아져도 아이템들이 줄바꿈 되지않고 찌그러짐
      - `flex-wrap: wrap `화면이 작아지면 자동으로 다음 라인으로 이동
      - `flex-wrap: wrap-reverse` 위에서부터 반대로 래핑됨
    - flex-flow
      - flex-direction과 flex-wrap를 합친것.
      - `flex-flow: column nowrap`과 같이 쓸 수 있다.
    - justify-content
      - 기본값은 `justify-content: flex-start` 
      - `justify-content: flex-end`는 아이템 순서는 유지하되
        - flex-direction: row일땐 오른쪽에 붙음
        - flex-direction: column일땐 바닥에 붙음 
      - `justify-content: center` 아이템들을 가운데로 
      - `justify-content: space-around` 박스를 둘러싼 간격 일정
      - `justify-content: space-evenly` 맨 끝 포함 똑같은 간격
      - `justify-content: space-between` 맨 끝은 화면에 맞게,  중간에만 간격 일정
    - align-items
      - `align-items: center` 반대축 기준으로 가운데로
      - `align-items: baseline` 문자 기준선 기준
    - align-content
      - `align-content: space-between`
  - 아이템에 적용할 수 있는 속성들
    - order
    - flex-grow
      - 컨테이너가 커짐에 따라 다른 아이템에 비해 어떤 속도로 커질거냐?
      - 기본값은 `flex-grow: 0`
    - flex-shrink
      - 컨테이너가 작아짐에 따라 다른 아이템에 비해 어떤 속도로 작아질거냐?
    - flex-basis
      - 기본값은 `flex-basis: auto`
      - flex-grow와 flex-shrink가 있으면 이대로 실행되고,
      - 없으면 `flex-basis: 60%`와 같이 지정가능. 
      - 커질때도 작아질때도 60%비율을 유지한다.
    - align-self
      - 아이템별로 정렬가능