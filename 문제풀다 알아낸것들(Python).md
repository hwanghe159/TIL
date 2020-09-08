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



### 스택, 큐

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
```

### arr.sort() 와 sorted(arr)의 차이

```python
arr = [3, 6, 2, 7, 8, 5, 3, 4]
result = sorted(arr) # arr는 변경되지 않음
arr.sort() # arr는 변경됨
```

