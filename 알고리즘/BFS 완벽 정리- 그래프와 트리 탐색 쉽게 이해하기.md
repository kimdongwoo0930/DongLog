[Velog로 가기](https://velog.io/@kimdongwoo0930/BFS-완벽-정리-그래프와-트리-탐색-쉽게-이해하기)

released at 2026-01-28 13:45:47 KST

updated at 2026-11-12 15:47:31 KST

|[BFS](https://velog.io/tags/BFS)|[Breadth_first_search](https://velog.io/tags/Breadth_first_search)|[python](https://velog.io/tags/python)|[그래프](https://velog.io/tags/그래프)|[알고리즘](https://velog.io/tags/알고리즘)|[코딩테스트](https://velog.io/tags/코딩테스트)|[탐색](https://velog.io/tags/탐색)|[트리](https://velog.io/tags/트리)|
|----|----|----|----|----|----|----|----|





그래프나 트리를 탐색할 때 가장 먼저 배우는 알고리즘 중 하나가 **BFS(Breadth-First Search, 너비 우선 탐색)**입니다.

이번 글에서는 **BFS의 개념**과 **Python 코드**로 구현해보고 **BFS**가 어디에 활용 가능한지 알아 보겠습니다.

> 다음 편에서는 DFS(Depth-First Search, 깊이 우선 탐색)와 비교해보겠습니다.

---

## 1️⃣ BFS란
**BFS**는 시작 노드에서 가까운 노드로 차례대로 탐색하는 알고리즘입니다.
- **단계별 탐색**: 루트에서 가까운 순서대로 방문합니다.
- **큐(Queue)** 구조를 이용한 탐색 순서를 가집니다.

---
## 2️⃣ BFS 동작 원리

**1. 시작 노드를 큐에 넣고 방문 처리**
**2. 큐에서 하나씩 꺼내어 연결된 노드를 탐색**
**3. 방문하지 않은 노드는 모두 큐에 넣음**
**4. 큐가 빌 때까지 반복 **


![](https://velog.velcdn.com/images/kimdongwoo0930/post/c3300360-2d0d-4d71-9511-ca2f2f480326/image.png)

---
## 💻 백준 1926번 그림 문제로 배우는 BFS (Python)

### 1️⃣ 문제 개요


- 2차원 배열 `n × m`
- 1 = 그림, 0 = 배경
- 연결된 1들은 상하좌우로 연결된 하나의 그림
- 목표:
	1. 전체 그림 개수
	2. 가장 큰 그림의 크기
---

### 2️⃣ 코드 구현

```python
#=====================================================================
#   1926번:    그림                   
#   @link:   https://www.acmicpc.net/problem/1926  
#=====================================================================


import sys
from collections import deque

input = sys.stdin.readline

n, m = map(int, input().split())
map = [list(map(int, input().split())) for _ in range(n)]
chk = [[False] * m for _ in range(n)]


dx = [0, 1, 0, -1]
dy = [1, 0, -1, 0]


def BFS(y, x):
    rs = 1
    q = deque([(y, x)])
    while q:
        ey, ex = q.popleft()
        for k in range(4):
            ny = ey + dy[k]
            nx = ex + dx[k]
            if 0 <= ny < n and 0 <= nx < m:
                if map[ny][nx] == 1 and chk[ny][nx] == False:
                    chk[ny][nx] = True
                    rs += 1
                    q.append((ny, nx))

    return rs


cnt = 0
maxV = 0

for j in range(n):
    for i in range(m):
        if map[j][i] == 1 and chk[j][i] == False:
            chk[j][i] = True
            # 전체 그림 갯수 + 1
            cnt += 1
            # BFS 그림 최대 크기 구하기
            maxV = max(maxV, BFS(j, i))
            # 최대값 갱신
print(cnt)
print(maxV)
```

---
### 3️⃣ 코드 설명
**1. 방문 체크 배열 `visited`**
- 이미 방문한 노드를 다시 탐색하지 않기 위해 사용
- 코테에서는 `bool[][]` 사용이 안전하고 빠름

**2. 큐(Queue) 사용**
  - BFS는 큐 구조가 핵심
  - `popleft()`로 큐 구조(FIFO) 방식으로 탐색
 
**3. 상하좌우 이동**
- `dx`, `dy` 배열로 반복 처리
- 반복문 하나로 간단하게 구현 가능

**4. 그림 크기 계산**
- BFS가 끝날 때마다 `size`를 반환
- 최대 그림 크기를 갱신

---
## 4️⃣ BFS 특징

| 특징    | 설명                           |
| ----- | ---------------------------- |
| 자료구조  | 큐(Queue)                     |
| 탐색 순서 | 가까운 노드부터 레벨별 탐색              |
| 최단 경로 | 간선 비용 동일 시 최단 경로 보장          |
| 메모리   | 한 레벨 모든 노드 저장, DFS보다 상대적으로 큼 |

---
## 🔹 BFS를 쓰는 문제 유형
| 번호 | 유형                  | 설명                             |
| -- | ------------------- | ------------------------------ |
| 1  | 최단 경로 찾기            | 간선 비용이 동일할 때 목적지까지 최단 거리 탐색    |
| 2  | 연결 요소 / 영역 탐색       | 2차원 맵에서 연결된 영역 탐색              |
| 3  | 레벨별 탐색 / 단계 계산      | 몇 단계만에 도착하는 문제                 |
| 4  | 최소 이동 횟수 / 경로 길이 계산 | 첫 방문 시 최단 거리 보장                |
| 5  | 멀티소스 BFS            | 시작점이 여러 개인 BFS, 동시에 확산되는 상황 처리 |


**정리:**
> BFS는 최단 거리, 단계 계산, 연결 영역 탐색 문제에서 필수적이며, 다양한 그래프/맵 기반 문제에 활용된다

---

## 📌 정리

- 이번 글에서는 BFS 개념과 동작 원리, Python 구현, 1926번 그림 문제를 통한 실전 예제를 다뤘습니다.
- 다음에는 DFS와 BFS 차이, 백트래킹 활용, 깊이 우선 탐색 전략까지 이어서 배웁니다.