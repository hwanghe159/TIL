### 입력

``` python
a, b = map(int, input()) # 12 입력하면 a=1, b=2
a, b = map(int, input().split()) # 1 2 입력하면 a=1, b=2
arr = list(map(int, input().split())) # 1 2 3 입력하면 arr=[1, 2, 3]

#한 줄 입력받기
import sys
input_data = sys.stdin.readline().rstrip()
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

### 배열 조작

```python
arr = ["a", "b", "c", "d", "e", "f", "g", "h"]

arr[5:0:-1] # ['f', 'e', 'd', 'c', 'b']
arr[5::-1] # ['f', 'e', 'd', 'c', 'b', 'a']
arr[::-1] # ['h', 'g', 'f', 'e', 'd', 'c', 'b', 'a']
arr[::] # ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
```

### 배열 filter

```python
arr = [1, 2, 3, 4, 5]
list(filter(lambda x: x < 3, arr)) # [1, 2]
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

### 다익스트라 알고리즘(특정노드 -> 모든노드 최단거리)

```python
import heapq

INF = int(1e9)

n, m = map(int, input().split())
start = int(input())
graph = [[] for i in range(n + 1)]
distance = [INF] * (n + 1)

for _ in range(m):
    a, b, c = map(int, input().split())
    graph[a].append((b, c))


def dijkstra(start):
    q = [] #현재 가장 가까운 노드를 저장하기 위한 목적으로만 우선순위큐 사용
    heapq.heappush(q, (0, start))  # 거리, 노드
    distance[start] = 0
    while q:
        dist, now = heapq.heappop(q)
        if distance[now] < dist: #꺼냈는데 이미 처리된 노드면(꺼낸게 더 크면) 무시
            continue
        for next_node, next_cost in graph[now]:
            cost = dist + next_cost
            if cost < distance[next_node]: #더 작으면 갱신하고 우선순위큐에 삽입
                distance[next_node] = cost
                heapq.heappush(q, (cost, next_node))


dijkstra(start)

for i in range(1, n + 1):
    if distance[i] == INF:
        print("INFINITY")
    else:
        print(distance[i])



6 11
1
1 2 2
1 3 5
1 4 1
2 3 3
2 4 2
3 2 3
3 6 5
4 3 3
4 5 1
5 3 1
5 6 2
```

### 우선순위 큐(heapq 이용)

```python
import heapq

q = []
heapq.heappush(q, (1, 2))
heapq.heappush(q, (2, 1))
heapq.heappush(q, (4, 1))
heapq.heappush(q, (1, 3))
heapq.heappop(q)
heapq.heappop(q)
heapq.heappop(q)
heapq.heappop(q) #(1, 2), (1, 3), (2, 1), (4, 1)순으로 조회
```

### 커스텀 정렬

```python
from functools import cmp_to_key

def compare(int1, int2):  # 길이가 긴 순, 길이가 같으면 숫자가 큰 순
    int1string = str(int1)
    int2string = str(int2)
    if len(int1string) < len(int2string):
        return 1
    elif len(int1string) > len(int2string):
        return -1
    else:
        if int1 < int2:
            return 1
        elif int1 == int2:
            return 0
        else:
            return -1

numbers = [5, 2, 6, 123, 321]
numbers = sorted(numbers, key=cmp_to_key(compare)) #[321, 123, 6, 5, 2]
```

### 배열 글자순 정렬

```python
arr = ["a", "abc", "ab", "abcd"]
arr.sort(key=len) #['a', 'ab', 'abc', 'abcd']
arr.sort(key=len, reverse=True) #['abcd', 'abc', 'ab', 'a']
```

### 순열과 조합

```python
from itertools import permutations
from itertools import combinations

arr = ['a', 'b', 'c']
list(permutations(arr)) #[('a', 'b', 'c'), ('a', 'c', 'b'), ('b', 'a', 'c'), ('b', 'c', 'a'), ('c', 'a', 'b'), ('c', 'b', 'a')]
list(map(''.join, permutations(arr))) #['abc', 'acb', 'bac', 'bca', 'cab', 'cba']
list(combinations(arr, 3)) #[('a', 'b', 'c')]
list(map(''.join, combinations(arr, 3))) #['abc']
```

