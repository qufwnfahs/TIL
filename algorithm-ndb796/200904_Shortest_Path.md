# 200904_Shortest_Path

## 1. 가장 빠르게 도달하는 방법

**최단 경로(Shortest Path) 알고리즘**은 말 그대로 가장 짧은 경로를 찾는 알고리즘이다. 그래서 '`길 찿기`' 문제라고도 불린다.

최단 경로 알고리즘 유형에는 다양한 종류가 있는데, 상황에 맞는 효율적인 알고리즘이 이미 정립되어 있다.

</br>

## 2. 다익스트라 최단 경로 알고리즘

**다익스트라(Dijkstra) 최단 경로 알고리즘**은 그래프에서 여러 개의 노드가 있을 때, `특정한 노드에서 출발하여 다른 노드로 가는 각각의 최단 경로`를 구해주는 알고리즘이다.

**다익스트라 최단 경로 알고리즘**은 '음의 간선'이 없을 때 정상적으로 동작한다.

</br>

**다익스트라 최단 경로 알고리즘**은 기본적으로 **그리디 알고리즘**으로 분류된다. 매번 '`가장 비용이 적은 노드`'를 선택해서 임의의 과정을 반복하기 때문이다.

1. 출발 노드를 설정한다.

2. 최단 거리 테이블을 초기화한다.

3. 방문하지 않은 노드 중에서 최단 거리가 짧은 노드를 선택한다.

4. 해당 노드를 거쳐 다른 노드로 가는 비용을 계산하여 최단 거리 테이블을 갱신한다.

5. 위 과정에서 3과 4번을 반복한다.

</br>

**다익스트라 최단 경로 알고리즘**에서는 '`방문하지 않은 노드 중에서 가장 최단 거리가 짧은 노드를 선택`'하는 과정을 반복하는데, 이렇게 선택된 노드는 '**최단 거리**'가 완전히 선택된 노드이므로, 더 이상 알고리즘을 반복해도 최단 거리가 줄어들지 않는다.

다시 말해 **다익스트라 알고리즘**이 진행되면서 `한 단계당 하나의 노드에 대한 최단 거리를 확실히 찾는 것으로 이해`할 수 있다.

```kotlin
fun main() {
    val (nodeN, edgeN) = readInts()
    val start = readInt()
    val adj = Array(nodeN + 1) { mutableListOf<Pair<Int, Int>>() }
    val distance = IntArray(nodeN + 1) { 1_000_000_000 }

    fun input() {
        repeat(edgeN) {
            val (from, to, cost) = readInts()
            adj[from].add(Pair(to, cost))
        }
    }

    fun dijkstra() {
        val pQueue = PriorityQueue<Pair<Int, Int>>(compareBy { it.second })
        pQueue.add(Pair(start, 0))  // first : cost, second : Node
        distance[start] = 0

        while (pQueue.isNotEmpty()) {
            val (now, dist) = pQueue.poll()
            // 처리된 적이 있다면 무시
            if (distance[now] < dist) continue
            // 현재 노드와 인접한 다른 노드 확인
            for ((to, cost) in adj[now]) {
                val newDist = dist + cost
                if (newDist < distance[to]) {
                    distance[to] = newDist
                    pQueue.add(Pair(to, newDist))
                }
            }
        }
    }

    input()
    dijkstra()
    println(distance.joinToString(" "))
}
```

### 시간 복잡도

노드의 개수를 V, 간선의 개수를 E라 할 때 위 다익스트라 알고리즘의 시간 복잡도는 **O(ElogV)** 이다.

</br>

## 3. 플로이드 워셜 알고리즘

**다익스트라 알고리즘**은 '`한 지점에서 다른 특정 지점까지의 최단 경로를 구해야 하는 경우`'에 사용할 수 있는 **최단 경로 알고리즘**이다.

**플로이드 워셜 알고리즘(Floyd-Warshall)** 은 '`모든 지점에서 다른 모든 지점까지의 최단 경로를 모두 구해야 하는 경우`'에 사용할 수 있는 알고리즘이다.

노드의 개수가 N개일 때 알고리즘 상으로 N번의 단계를 수행하며, 단계마다 **O(N^2)** 의 연산을 통해 '`현재 노드를 거쳐 가는`' 모든 경로를 고려한다. 따라서 **플로이드 워셜 알고리즘**의 총 시간 복잡도는 **O(N^3)** 이다.

또한 **다익스트라 알고리즘**은 **그리디 알고리즘**인데 **플로이드 워셜 알고리즘**은 **다이나믹 프로그래밍**이라는 특징이 있다.

```kotlin
fun main() {
    val nodeN = readInt()
    val edgeN = readInt()
    val graph = Array(nodeN + 1) { IntArray(nodeN + 1) { 1_000_000_000 } }

    // 자기 자신에서 자기 자신으로 가는 비용은 0으로 초기화
    fun init() {
        for (y in 1..nodeN) {
            graph[y][y] = 0
        }
    }

    fun input() {
        repeat(edgeN) {
            val (from, to, cost) = readInts()
            graph[from][to] = cost
        }
    }

    fun floyd() {
        for (k in 1..nodeN) {   // k를 거쳐가는 경우
            for (from in 1..nodeN) {
                for (to in 1..nodeN) {
                    graph[from][to] = min(graph[from][to], graph[from][k] + graph[k][to])
                }
            }
        }
    }

    init()
    input()
    floyd()
    graph.forEach { println(it.joinToString(" ")) }
}
```

</br>

## 문제 구성

- [1. 플로이드](#기출문제-1-플로이드)
- [2. 정확한 순위](#기출문제-2-정확한-순위)
- [3. 화성 탐사](#기출문제-3-화성-탐사)
- [4. 숨바꼭질](#기출문제-4-숨바꼭질)

</br>

## 기출 문제 1 플로이드

### 문제

[https://www.acmicpc.net/problem/11404](https://www.acmicpc.net/problem/11404)

### 코드

```kotlin
fun main() {
    val nodeN = readInt()
    val edgeN = readInt()
    val graph = Array(nodeN + 1) { IntArray(nodeN + 1) { 1_000_000_000 } }

    fun init() {
        for (y in 1..nodeN) {
            for (x in 1..nodeN) {
                if (y == x) graph[y][x] = 0
            }
        }
    }

    fun input() {
        repeat(edgeN) {
            val (from, to, cost) = readInts()
            graph[from][to] = min(graph[from][to], cost)
        }
    }

    fun floyd() {
        for (k in 1..nodeN) {   // k를 거쳐가는 경우
            for (from in 1..nodeN) {
                for (to in 1..nodeN) {
                    graph[from][to] = min(graph[from][to], graph[from][k] + graph[k][to])
                }
            }
        }
    }

    fun print() {
        for (y in 1..nodeN) {
            for (x in 1..nodeN) {
                if (graph[y][x] == 1_000_000_000) graph[y][x] = 0
            }
        }

        for (y in 1..nodeN) {
            out.appendln(graph[y].sliceArray(1..nodeN).joinToString(" "))
        }

        print(out)
    }

    init()
    input()
    floyd()
    print()
}
```

### 문제 해설

전형적인 최단 경로 문제이다. 중복되는 간선은 비용이 짧은 것만 고려하면 된다.

또한 도시의 개수 n이 **100 이하의 정수**이므로, **플로이드 워셜 알고리즘**을 이용하는 것이 효과적이다.

그러므로 초기에 간선 정보를 입력받을 때 '`가장 짧은 간선`' 정보만 저장한 뒤에, **플로이드 워셜 알고리즘**을 수행하여 결과를 출력한다.

</br>

## 기출 문제 2 정확한 순위

### 문제

선생님은 시험을 본 학생 N명의 성적을 분실하고, 성적을 비교한 결과의 일부만 가지고 있습니다.

학생들의 성적을 비교한 결과가 주어질 때, 성적 순위를 정확히 알 수 있는 학생은 모두 몇 명인지 계산하는 프로그램을 작성하세요.

### 코드

```kotlin
fun main() {
    val (nodeN, edgeN) = readInts()
    val graph = Array(nodeN + 1) { IntArray(nodeN + 1) { 1_000_000_000 } }
    var answer = 0

    fun init() {
        for (y in 1..nodeN) {
            for (x in 1..nodeN) {
                if (y == x) graph[y][x] = 0
            }
        }
    }

    fun input() {
        repeat(edgeN) {
            val (from, to) = readInts()
            graph[from][to] = 1
        }
    }

    fun floyd() {
        for (k in 1..nodeN) {
            for (from in 1..nodeN) {
                for (to in 1..nodeN) {
                    graph[from][to] = min(graph[from][to], graph[from][k] + graph[k][to])
                }
            }
        }
    }

    fun solution() {
        for (y in 1..nodeN) {
            var count = 0
            for (x in 1..nodeN) {
                if (graph[y][x] != 1_000_000_000 || graph[x][y] != 1_000_000_000)
                    count++
            }
            if (count == nodeN) answer++
        }
    }

    init()
    input()
    floyd()
    solution()
    print(answer)
}
```

### 문제 해설

A번 학생과 B번 학생의 성적을 비교할 때, '`경로`'를 이용하여 성적 비교 결과를 알 수 있다.

A에서 B로 도달이 가능하다는 것은, A가 B보다 성적이 낮다는 의미가 된다. 따라서 `A에서 B로 도달이 가능하거나, B에서 A로 도달이 가능하면 '성적 비교'가 가능`한 것이다.

</br>

이 문제에서는 학생의 수 N이 **500 이하의 정수**이므로 **O(N^3)** 의 시간 복잡도로 동작하는 **플로이드 워셜 알고리즘**을 이용해 문제를 해결할 수 있다.

**플로이드 워셜 알고리즘**을 수행한 뒤에 모든 노드에 대하여 다른 노드와 서로 도달이 가능한지를 체크하여 문제를 해결할 수 있다.

</br>

## 기출 문제 3 화성 탐사

### 문제

화성 탐사 기계가 존재하는 공간은 N * N 크기의 2차원 공간이며, 각각의 칸을 지나기 위한 비용(에너지 소모량)이 존재합니다.

가장 왼쪽 위 칸인 [0][0] 위치에서 가장 오른쪽 아래 칸인 [N - 1][N - 1] 위치로 이동하는 최소 비용을 출력하는 프로그램을 작성하세요.

화성 탐사 기계는 특정한 위치에서 상하좌우 인접한 곳으로 1칸씩 이동할 수 있습니다.

### 코드

```kotlin
fun main() {
    repeat(readInt()) {
        val (nodeN) = readInts()
        val arr = Array(nodeN) { IntArray(nodeN) }
        val distance = Array(nodeN) { IntArray(nodeN) { 1_000_000_000 } }
        val dy = arrayOf(-1, 1, 0, 0)
        val dx = arrayOf(0, 0, -1, 1)

        fun input() {
            for (y in 0 until nodeN) {
                val st = StringTokenizer(readLine(), " ")
                for (x in 0 until st.countTokens()) {
                    arr[y][x] = st.nextToken().toInt()
                }
            }
        }

        fun dijkstra() {
            val pQueue = PriorityQueue<Triple<Int, Int, Int>>(compareBy { it.third })
            pQueue.add(Triple(0, 0, arr[0][0]))  // first : cost, second : Node
            distance[0][0] = arr[0][0]

            while (pQueue.isNotEmpty()) {
                val (y, x, dist) = pQueue.poll()
                // 처리된 적이 있다면 무시
                if (distance[y][x] < dist) continue
                // 현재 노드와 인접한 다른 노드 확인
                for (i in 0 until 4) {
                    val yPos = y + dy[i]
                    val xPos = x + dx[i]

                    if (yPos < 0 || yPos >= nodeN || xPos < 0 || xPos >= nodeN) continue

                    val newDist = dist + arr[yPos][xPos]

                    if (newDist < distance[yPos][xPos]) {
                        distance[yPos][xPos] = newDist
                        pQueue.add(Triple(yPos, xPos, newDist))
                    }
                }
            }
        }

        input()
        dijkstra()
        out.appendln(distance[nodeN-1][nodeN-1])
    }
    print(out)
}
```

### 문제 해설

N의 범위 크기가 **최대 125**로 작다고 느낄 수 있지만, **2차원 공간**이기 때문에 전체 노드의 개수는 **N^2으로 10,000**을 넘을 수 있다.

따라서 **플로이드 워셜 알고리즘**으로는 이 문제를 해결하기에 적합하지 않으며 **다익스트라 최단 경로 알고리즘**을 이용하면 효과적으로 답을 도출할 수 있다.

</br>

## 기출문제 4 숨바꼭질

### 문제

동빈이는 1 ~ N번까지의 헛간 중에서 하나를 골라 숨을 수 있으며, 술래는 항상 1번 헛간에서 출발합니다. 전체 맵에는 총 M개의 양방향 통로가 존재합니다.

동빈이는 1번 헛간으로부터 최단 거리가 가장 먼 헛간이 가장 안전하다고 판단하고 있습니다.

이때 최단 거리의 의미는 지나야 하는 길의 최소 개수를 의미합니다. 동빈이가 숨을 헛간의 번호, 헛간까지의 거리, 동일한 거리의 헛간의 개수를 출력하세요.

### 코드

```kotlin
fun main() {
    val (nodeN, edgeN) = readInts()
    val adj = Array(nodeN + 1) { mutableListOf<Int>() }
    val distance = IntArray(nodeN + 1) { 1_000_000_000 }

    fun input() {
        repeat(edgeN) {
            val (from, to) = readInts()
            adj[from].add(to)
            adj[to].add(from)
        }
    }

    fun dijkstra() {
        val pQueue = PriorityQueue<Pair<Int, Int>>(compareBy { it.second })
        pQueue.add(Pair(1, 0))
        distance[1] = 0

        while (pQueue.isNotEmpty()) {
            val (now, dist) = pQueue.poll()

            if (distance[now] < dist) continue

            for (to in adj[now]) {
                val newDist = dist + 1
                if (newDist < distance[to]) {
                    distance[to] = newDist
                    pQueue.add(Pair(to, newDist))
                }
            }
        }
    }

    fun solution() {
        var _number = 0
        var _dist = 0
        var _count = 0

        for (i in 1..nodeN) {
            if (_dist < distance[i]) {
                _dist = distance[i]
                _count = 1
                _number = i
            }
            else if (_dist == distance[i]) _count++
        }

        print("$_number $_dist $_count")
    }

    input()
    dijkstra()
    solution()
}
```

### 문제 해설

1번 노드(헛간)로부터 다른 모든 노드로의 최단 거리를 계산한 뒤에, 가장 최단 거리가 긴 노드를 찾는 문제이다.

거쳐간 노드의 개수가 거리이기 때문에 각 노드를 이동할 때마다 **비용을 1**로 잡는다.

</br>

거리가 1이기 때문에 **BFS**를 이용하여서도 구할 수 있다. **다익스트라 최단 경로 알고리즘**을 이용하여 해결하였다.
