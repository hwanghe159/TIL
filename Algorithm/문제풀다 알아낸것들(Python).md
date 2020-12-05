### 입력

``` python
a, b = map(int, input()) # 12 입력하면 a=1, b=2
a, b = map(int, input().split()) # 1 2 입력하면 a=1, b=2
arr = list(map(int, input().split())) # 1 2 3 입력하면 arr=[1, 2, 3]
```



### N * M 크기의 배열 입력받기 (띄어쓰기 안할때)

```python
n, m = map(int, input().split())

graph = []
for _ in range(n):
    graph.append(list(map(int, input())))
```

```python
# 입력
# 2 3
# 101
# 010

# 결과
# graph = [[1, 0, 1], [0, 1, 0]]
```



### N * M 크기의 2차원 배열 초기화

```python
graph = [[0] * m for _ in range(n)]
```



### 2차원 배열 시계방향 90도 회전

```python
def rotate_90_degree(matrix):
    n = len(matrix)
    m = len(matrix[0])
    result = [[0] * n for _ in range(m)]
    for i in range(n):
        for j in range(m):
            result[j][n - i - 1] = matrix[i][j]
    return result
```



### DFS로 덩어리 개수 구하기

```python
n, m = map(int, input().split())

graph = []
for _ in range(n):
    graph.append(list(map(int, input())))

result = 0


def dfs(x, y):
    if x < 0 or x >= n or y < 0 or y >= m:
        return False
    if graph[x][y] == 0:
        graph[x][y] = 1
        dfs(x - 1, y)
        dfs(x, y - 1)
        dfs(x + 1, y)
        dfs(x, y + 1)
        return True
    return False


for i in range(n):
    for j in range(m):
        if dfs(i, j):
            result += 1

print(result)

```



### 스택, 큐, 덱

```python
# 스택
stack = []

stack.append(5) # push
stack[-1] # top
stack.pop() # pop

# 큐 (deque 이용) ㄷ모양이라고 상상하면 됨
from collections import deque

queue = deque()

queue.append(5)
queue.popleft()

# 큐 (리스트 이용)
queue = [1]

queue.append(5)
del queue[0]

# 덱
from collections import deque

deque = deque([1, 2, 3])

deque.append(4) #오른쪽에 요소 삽입
deque.appendleft(5) #왼쪽에 요소 삽입
deque.pop() #오른쪽 요소 삭제
deque.popleft() #왼쪽 요소 삭제
deque.insert(1, 100) #인덱스 1번째에 100 삽입
```

### arr.sort() 와 sorted(arr)의 차이

```python
arr = [3, 6, 2, 7, 8, 5, 3, 4]
result = sorted(arr) # arr는 변경되지 않음
arr.sort() # arr는 변경됨
```

### 진수 변환

```python
# 10진수 -> 2진수
bin(3) #0b11
"{0:b}".format(7).zfill(8) #00000111

# 10진수 -> 8진수
oct(10) #0o12

# 10진수 -> 16진수
hex(17) #0x11
'%X' % 17 #11
'%x' % 17 #11

# 10진수 -> (base)진수
def convert(n, base):
    T = "0123456789ABCDEF"
    q, r = divmod(n, base)
    if q == 0:
        return T[r]
    else:
        return convert(q, base) + T[r]
    
#(base)진수 -> 10진수
int(num, base=10) # num은 문자열
```

### 배열 다루기

```python
arr = ["a", "b", "c", "d", "e", "f", "g", "h"]

arr[5:0:-1] # ['f', 'e', 'd', 'c', 'b']
arr[5::-1] # ['f', 'e', 'd', 'c', 'b', 'a']
arr[::-1] # ['h', 'g', 'f', 'e', 'd', 'c', 'b', 'a']
arr[::] # ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
```

### 딕셔너리 정렬

```python
dict = {'A': 2, 'D': 3, 'C': 4, 'B': 1}

# key기준 오름차순(내림차순은 reverse=True를 두번째 인자에 추가하기)
sorted(dict) # ['A', 'B', 'C', 'D']
sorted(dict.keys()) # ['A', 'B', 'C', 'D']
sorted(dict.items()) # [('A', 2), ('B', 1), ('C', 4), ('D', 3)]
sorted(dict.values()) # [1, 2, 3, 4]
sorted(dict.items(), key=lambda item: item[0]) # [('A', 2), ('B', 1), ('C', 4), ('D', 3)]

# value기준 오름차순(내림차순은 reverse=True를 두번째 인자에 추가하기)
sorted(dict.items(), key=lambda item: item[1]) # [('B', 1), ('A', 2), ('D', 3), ('C', 4)]
```

### 정규표현식에 해당하는 문자열 추출

```python
import re

results = re.findall("(\d+)([SDT])([*#]?)", "1D2S#10S")
print(results) # [('1', 'D', ''), ('2', 'S', '#'), ('10', 'S', '')]

results = re.findall("\d+[SDT][*#]?", "1D2S#10S")
print(results) # ['1D', '2S#', '10S']
```

### deque 다루기

```python
from collections import deque

q = deque([(1, 1, 1), (1, 1, 2), (2, 1, 1)])
q = sorted(q)  # 오름차순정렬 [(1, 1, 1), (1, 1, 2), (2, 1, 1)] 내림차순하려면 reverse=True추가
print(max(q))  # (2, 1, 1)
```

