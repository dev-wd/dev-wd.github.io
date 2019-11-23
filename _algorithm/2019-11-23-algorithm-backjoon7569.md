---
title: "[Python] BOJ 7569 토마토"
layout: collection
collection: algorithm
date: 2017-10-20 08:26:28 -0400
categories:
  - alorithm
  
---



- https://www.acmicpc.net/problem/7569


### 문제 이해 및 적용하기
- 일반적인 BFS에서는 dx, dy 방향으로 뻗어나간다면, 이문제에는 축 하나가 더 추가되어서 dx, dy, dz로 뻗어나가야 한다.

```python
# dx dy dz
# top bottom left right  zTop zBottom
dx = [ 0, 0, -1, 1, 0, 0]
dy = [ -1, 1, 0, 0, 0, 0]
dz = [ 0, 0, 0, 0, 1, -1]
```

- 익지 않은 토마토 : 0, 익은 토마토 : 1, 토마토가 없는 자리 : -1
 - BFS 함수에서 익게 된 토마토는 0 -> 1 로 변경해준다
 - 진행방향에서 값이 0 일때만 deque에 넣어준다.
- 토마토가 모두 익는데 걸리는 일수를 구하는 것이므로 BFS가 진행될 때에 최대 깊이를 리턴하여 구해주면 된다.


```python
# bfs
def bfs(listforone):
  ans = 0
  d = deque()
  for i in range(len(listforone)):
    d.append([listforone[i][0],listforone[i][1],listforone[i][2],0])
  
  while(d):
    z, y, x, c = d.popleft()
    ans = max([ans, c])
    for i in range(6):
      tx = x + dx[i]
      ty = y + dy[i]
      tz = z + dz[i]
      if tx < 0 or tx > M-1 or ty < 0 or ty > N-1 or tz < 0 or tz > H-1 : 
        continue
      if matrix[tz][ty][tx] != 0:
        continue
      d.append([tz,ty,tx,c+1])
      matrix[tz][ty][tx] = 1
      
    
  return ans
```



### 주의할 점
이 문제는 정답율이 37퍼센트 정도이며, BFS로 풀이 가능하다.

Python을 사용하면서 시간초과 이슈로 인해서 주의해야 할 부분이 있었는데, 간과하고 있던 부분이 있어서 틀렸던 문제이다.

BFS를 사용할 때에 list 를 사용하였는데, append()하는데 걸리는 시간이 꾀 느리다는 사실을 알게 되었고 deque를 이용하여 풀어야 Python에서는 정답처리가 된다.





### 전체 코드
```python
import sys
from collections import deque

# input
input = sys.stdin.readline
M, N, H = map(int,input().split())


# matrix
matrix = []
for i in range(H):
  buf2 = []
  for j in range(N):
    buf = list(map(int,input().split()))
    buf2.append(buf)
  matrix.append(buf2)


# dx dy dz
# top bottom left right  zTop zBottom
dx = [ 0, 0, -1, 1, 0, 0]
dy = [ -1, 1, 0, 0, 0, 0]
dz = [ 0, 0, 0, 0, 1, -1]

# bfs
def bfs(listforone):
  ans = 0
  d = deque()
  for i in range(len(listforone)):
    d.append([listforone[i][0],listforone[i][1],listforone[i][2],0])
  
  while(d):
    z, y, x, c = d.popleft()
    ans = max([ans, c])
    for i in range(6):
      tx = x + dx[i]
      ty = y + dy[i]
      tz = z + dz[i]
      if tx < 0 or tx > M-1 or ty < 0 or ty > N-1 or tz < 0 or tz > H-1 : 
        continue
      if matrix[tz][ty][tx] != 0:
        continue
      d.append([tz,ty,tx,c+1])
      matrix[tz][ty][tx] = 1
      
    
  return ans
  
# Buisness logic

List1 = []

# find 1
for h in range(H):
  for n in range(N):
    for m in range(M):
      if matrix[h][n][m] == 1:
        List1.append([h,n,m])
     
# find bfs depth 


realAns = bfs(List1)
  

# find 0

isZero = False
for h in range(H):
  for n in range(N):
    for m in range(M):
      if matrix[h][n][m] == 0:
        isZero = True
        break

if isZero == True:
  print(-1)
else:
  print(realAns)
  
```
