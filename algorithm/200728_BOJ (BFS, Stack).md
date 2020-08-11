# 1. BFS (Breadth First Search)

- 다차원 배열에서 각 칸을 방문할 때 너비를 우선으로 방문하는 알고리즘

- 우선, BFS 알고리즘에서는 좌표를 담을 큐가 필요

## 1.1 Flood Fill

## BOJ 1926번 그림

### 1. 개요

[https://www.acmicpc.net/problem/1926](https://www.acmicpc.net/problem/1926)

### 2. 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.max

fun main() = with(System.`in`.bufferedReader()) {
    val out = StringBuilder()
    val (row, col) = readLine().split(" ").map { it.toInt() }
    val arr = Array(row) { IntArray(col) }
    val visited = Array(row) { BooleanArray(col) }
    val queue = LinkedList<Pair<Int, Int>>()
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)
    var cnt = 0
    var answer = 0

    for (i in 0 until row)
        arr[i] = readLine().split(" ").map { it.toInt() }.toIntArray()

    for (i in 0 until row) {
        for (j in 0 until col) {
            if (arr[i][j] == 1 && !visited[i][j]) {
                cnt++
                var width = 1

                queue.offer(Pair(i, j))
                visited[i][j] = true

                while (queue.isNotEmpty()) {
                    val curr = queue.poll()

                    for (k in 0 until 4) {
                        val yPos = curr.first + dy[k]
                        val xPos = curr.second + dx[k]

                        if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
                        if (arr[yPos][xPos] == 0 || visited[yPos][xPos]) continue

                        width++
                        queue.offer(Pair(yPos, xPos))
                        visited[yPos][xPos] = true
                    }
                }
                answer = max(answer, width)
            }
        }
    }
    out.appendln(cnt)
    out.appendln(answer)

    print(out)
}
```

### 3. 풀이 과정

- `이중 for문`을 돌면서 `visited`을 통해 방문하지 않은 칸에 대해 BFS를 진행

- while문이 진행될 때마다 그림의 전파가 이루어짐

</br>

## 1.2 다차원 배열에서의 거리 측정

### BOJ 2178번 미로 탐색

### 1. 개요

[https://www.acmicpc.net/problem/2178](https://www.acmicpc.net/problem/2178)

### 2. 코드

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()) {
    val (row, col) = readLine().split(" ").map { it.toInt() }
    val arr = Array(row) { IntArray(col) }
    val distance = Array(row) { IntArray(col) {-1} }    // -1 로 초기화
    val queue = LinkedList<Pair<Int, Int>>()
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)

    for (i in 0 until row)
        arr[i] = readLine().toCharArray().map { it.toInt() - 48 }.toIntArray()

    distance[0][0] = 1
    queue.offer(Pair(0, 0))

    while (queue.isNotEmpty()) {
        val curr = queue.poll()

        for (i in 0 until 4) {
            val yPos = curr.first + dy[i]
            val xPos = curr.second + dx[i]

            if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
            if (arr[yPos][xPos] == 0 || distance[yPos][xPos] >= 0) continue

            distance[yPos][xPos] = distance[curr.first][curr.second] + 1
            queue.offer(Pair(yPos, xPos))
        }
    }
    print(distance[row-1][col-1])
}
```

### 3. 풀이 과정

- `distance 배열`을 통해 `(0,0)`에서 각 칸에 도달하기 까지의 거리를 저장함과 동시에 방문한 적이 있는지를 원소 값의 상태를 통해 알아낼 수 있다. **(-1 이 아니라면 방문한 적이 있는 칸이다.)**

- `(0,0)`부터 상하좌우로 BFS를 진행하여 끝난 후 도착 지점에 `distance 배열` 값을 출력

### 4. 결과

![image](https://user-images.githubusercontent.com/24761073/88662205-981fe680-d114-11ea-90f7-144de2588400.png)

</br>

## 1.3 시작점이 여러 개일 때

### BOJ 7576번 토마토

### 1. 개요

https://www.acmicpc.net/problem/7576

### 2. 코드

```kotlin
import java.util.*
import kotlin.math.max

fun main() = with(System.`in`.bufferedReader()) {
    val (col, row) = readLine().split(" ").map { it.toInt() }
    val arr = Array(row) { IntArray(col) }
    val distance = Array(row) { IntArray(col) }
    val queue = LinkedList<Pair<Int, Int>>()
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)
    var answer = 0

    // 1. 익은 토마토가 있는 좌표 queue 에 모두 등록
    for (i in 0 until row)
        readLine()
            .split(" ")
            .map { it.toInt() }
            .forEachIndexed { index, value ->
                arr[i][index] = value
                if (value == 1) queue.offer(Pair(i, index))
            }

    while (queue.isNotEmpty()) {
        val curr = queue.poll()

        for (j in 0 until 4) {
            val yPos = curr.first + dy[j]
            val xPos = curr.second + dx[j]

            if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
            if (arr[yPos][xPos] != 0) continue

            arr[yPos][xPos] = 1
            queue.offer(Pair(yPos, xPos))
            distance[yPos][xPos] = distance[curr.first][curr.second] + 1

            answer = max(answer, distance[yPos][xPos])
        }
    }

    // 0이 존재하면 -1, 아니면 dayCount 출력
    print(if (hasUnripedTomato(arr)) -1 else answer)
}

fun hasUnripedTomato(arr: Array<IntArray>): Boolean {
    for (i in arr.indices)
        for (j in arr[i].indices)
            if (arr[i][j] == 0) return true

    return false
}
```

### 3. 풀이 과정

- 토마토가 모두 익었을 때, `distance 배열`의 `max` 값이 이 문제의 **최소 일수**이다.

- 모든 익은 토마토로 부터 **상하좌우로 전파**되므로, 첫 BFS의 **시작점이 여러 개**가 된다. 그것을 모두 `Queue`에 쌓아놓고 시작해야 한다.

### 4. 결과

![image](https://user-images.githubusercontent.com/24761073/88662626-2dbb7600-d115-11ea-962a-1fb09614f4d1.png)

### BOJ 7569번 토마토 (3차원)

### 1. 개요

[https://www.acmicpc.net/problem/7569](https://www.acmicpc.net/problem/7569)

### 2. 코드

```kotlin
import java.util.*
import kotlin.math.max

fun main() = with(System.`in`.bufferedReader()) {
    val (col, row, height) = readLine().split(" ").map { it.toInt() }
    val arr = Array(height) { Array(row) { IntArray(col) } }    // 3차원 배열
    val distance = Array(height) { Array(row) { IntArray(col) } }
    val queue = LinkedList<Triple<Int, Int, Int>>()
    val dh = arrayOf(0, 0, 0, 0, -1, 1)
    val dy = arrayOf(-1, 1, 0, 0, 0, 0) // 상, 하, 좌, 우, 앞, 뒤
    val dx = arrayOf(0, 0, -1, 1, 0, 0) // 상, 하, 좌, 우, 앞, 뒤
    var answer = 0

    // 익은 토마토를 모두 시작점으로 등록
    for (h in 0 until height) {
        for (r in 0 until row) {
            readLine().split(" ").map { it.toInt() }
                .forEachIndexed { index, value ->
                    arr[h][r][index] = value
                    if (value == 1) queue.offer(Triple(h, r, index))
                }
        }
    }

    while (queue.isNotEmpty()) {
        val curr = queue.poll()

        for (i in 0 until 6) {
            val hPos = curr.first + dh[i]
            val yPos = curr.second + dy[i]
            val xPos = curr.third + dx[i]

            if (hPos < 0 || hPos >= height || yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
            if (arr[hPos][yPos][xPos] != 0) continue

            arr[hPos][yPos][xPos] = 1
            queue.offer(Triple(hPos, yPos, xPos))

            distance[hPos][yPos][xPos] = distance[curr.first][curr.second][curr.third] + 1
            answer = max(answer, distance[hPos][yPos][xPos])
        }
    }

    // 안 익은 토마토가 있으면 -1, 아니면 answer
    print(if (hasUnripedTomato3d(arr)) -1 else answer)
}

fun hasUnripedTomato3d(arr: Array<Array<IntArray>>): Boolean {
    for (h in arr.indices)
        for (y in arr[h].indices)
            for (x in arr[h][y].indices)
                if (arr[h][y][x] == 0) return true

    return false
}
```

### 3. 풀이 과정

- 마찬가지로 토마토가 모두 익었을 때, `distance 배열`의 `max` 값이 이 문제의 **최소 일수**이다.

- 3차원 개념으로 윗 상자와 아랫 상자의 토마토도 전파시켜야 하므로 6가지 방향으로 전파

- **6가지 방향**에 맞는 좌표값 설정을 위해 `dh, dy, dx 배열` 사용

### 4. 결과

![image](https://user-images.githubusercontent.com/24761073/88663536-bdadef80-d116-11ea-9285-fff4a41f5d2b.png)

</br>

## 1.4 시작점이 두 종류일 때

### BOJ 4179번 불!

### 1. 개요

[https://www.acmicpc.net/problem/4179](https://www.acmicpc.net/problem/4179)

### 2. 코드

1. **두 종류의 BFS 동시 진행**

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()) {
    val (row, col) = readLine().split(" ").map { it.toInt() }
    val arr = Array(row) { CharArray(col) }
    val distance = Array(row) { IntArray(col) { 1 } }
    val survivor = LinkedList<Pair<Int, Int>>()
    val fire = LinkedList<Pair<Int, Int>>()
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)
    var answer = -1

    // 시작 지점 등록
    for (i in 0 until row) {
        readLine().toCharArray()
            .forEachIndexed { index, c ->
                arr[i][index] = c
                if (c == 'J') survivor.offer(Pair(i, index))
                if (c == 'F') fire.offer(Pair(i, index))
            }
    }

    loop@while (true) {
        // 기저 사례 : 생존자가 이동할 공간이 없다면
        if (survivor.isEmpty()) break

        // 1. 생존자 이동
        if (survivor.isNotEmpty()) {
            val survivorPerMinute = survivor.size

            for (i in 0 until survivorPerMinute) {
                val svCurr = survivor.poll()

                if (arr[svCurr.first][svCurr.second] == 'F') continue   // 불에 잠겼다면 skip
                if ((svCurr.first == 0 || svCurr.first == row - 1 || svCurr.second == 0 || svCurr.second == col - 1) && arr[svCurr.first][svCurr.second] != 'F') {
                    answer = distance[svCurr.first][svCurr.second]
                    break@loop
                }

                for (j in 0 until 4) {
                    val yPos = svCurr.first + dy[j]
                    val xPos = svCurr.second + dx[j]

                    if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
                    if (arr[yPos][xPos] != '.' || distance[yPos][xPos] != 1) continue  // 이동 가능한 공간이 아니거나, 이미 이동한 공간이라면 skip

                    survivor.offer(Pair(yPos, xPos))
                    distance[yPos][xPos] = distance[svCurr.first][svCurr.second] + 1
                }
            }
        }

        // 2. 불 이동
        if (fire.isNotEmpty()) {
            val firePerMinute = fire.size

            for (i in 0 until firePerMinute) {
                val fireCurr = fire.poll()

                for (j in 0 until 4) {
                    val yPos = fireCurr.first + dy[j]
                    val xPos = fireCurr.second + dx[j]

                    if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
                    if (arr[yPos][xPos] == 'F' || arr[yPos][xPos] == '#') continue  // 벽 또는 이미 불

                    arr[yPos][xPos] = 'F'
                    fire.offer(Pair(yPos, xPos))
                }
            }
        }
    }

    print(if (answer == -1) "IMPOSSIBLE" else answer)
}
```

  2. **두 종류의 BFS 따로 진행**

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()) {
    val (row, col) = readLine().split(" ").map { it.toInt() }
    val arr = Array(row) { CharArray(col) }
    val distFire = Array(row) { IntArray(col) { 1 } }
    val distSurvivor = Array(row) { IntArray(col) { 1 } }
    val survivor = LinkedList<Pair<Int, Int>>()
    val fire = LinkedList<Pair<Int, Int>>()
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)
    var answer = -1

    // 시작 지점 등록
    for (i in 0 until row) {
        readLine().toCharArray()
            .forEachIndexed { index, c ->
                arr[i][index] = c
                if (c == 'J') survivor.offer(Pair(i, index))
                if (c == 'F') fire.offer(Pair(i, index))
            }
    }

    // 불 BFS
    while (fire.isNotEmpty()) {
        val fCurr = fire.poll()

        for (i in 0 until 4) {
            val yPos = fCurr.first + dy[i]
            val xPos = fCurr.second + dx[i]

            if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
            if (arr[yPos][xPos] == '#' || distFire[yPos][xPos] != 1) continue

            fire.offer(Pair(yPos, xPos))
            distFire[yPos][xPos] = distFire[fCurr.first][fCurr.second] + 1
        }
    }

    loop@while (survivor.isNotEmpty()) {
        val svCurr = survivor.poll()

        for (i in 0 until 4) {
            val yPos = svCurr.first + dy[i]
            val xPos = svCurr.second + dx[i]

            // 범위 밖을 벗어났다는 건 탈출에 성공했다는 것을 의미
            if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) {
                answer = distSurvivor[svCurr.first][svCurr.second]
                break@loop
            }

            if (arr[yPos][xPos] == '#' || distSurvivor[yPos][xPos] != 1) continue
            if (distFire[yPos][xPos] != 1 && distFire[yPos][xPos] <= distSurvivor[svCurr.first][svCurr.second] + 1) continue

            survivor.offer(Pair(yPos, xPos))
            distSurvivor[yPos][xPos] = distSurvivor[svCurr.first][svCurr.second] + 1
        }
    }

    print(if (answer == -1) "IMPOSSIBLE" else answer)
}
```

### 3. 풀이 과정

1. **두 종류의 BFS 동시 진행**

   - 시간 순에 따라 생존자와 불의 BFS를 **각각 진행**시켰다.

   - 기저 사례로 `survivor` 이 비었다면 더 이상 진행할 수가 없으므로 `break`

   - 생존자의 BFS를 먼저 진행

     1. 생존자의 현재 위치가 F라면 불에 잠겼다는 의미이므로 `continue`

     2. 생존자의 현재 위치가 `가장자리`라면 탈출한 것이므로 프로그램 종료

     3. 상하좌우에 대해서, `이동가능한 공간(.)`이며 `방문하지 않은 (distance ≠ -1)` 칸이라면 `Queue`에 추가

   - 그 후, 불의 BFS를 진행

     1. 상하좌우에 대해서, `벽(#)` 또는 이미 `불(F)`이 아닌 칸이라면 `Queue`에 추가

2. **두 종류의 BFS 따로 진행**

   - 각 BFS 마다 `distance 배열`을 두어 시간 순에 따르지 않고 각자 진행

   - 불의 BFS를 먼저 진행하고 나서 생존자의 BFS를 진행한다.

     1. 상하좌우에 대해서, 불의 거리 값이 `초기값 (1)` 이 아닐 때 불의 거리 값이 **더 작다면** 불이 더 빨리 번진 것이므로 `생존자 Queue`에 추가하지 않는다.

     2. 상하좌우 중, **범위를 벗어난 것**이 있으면 **가장자리에 도달**한 것이므로 프로그램 종료

### 4. 결과

1. 생존자가 처음부터 가장자리에 있는 경우를 처리하지 못함

2. BFS 따로 진행으로 바꾸는 과정에서 BFS 조건 설정을 잘못하였다.

![image](https://user-images.githubusercontent.com/24761073/88663144-0fa24580-d116-11ea-9d5c-5826fb28485a.png)

</br>

## 1.5 1차원에서의 BFS

### BOJ 1697번 숨바꼭질

### 1. 개요

[https://www.acmicpc.net/problem/1697](https://www.acmicpc.net/problem/1697)

### 2. 코드

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()) {
    val (N, X) = readLine().split(" ").map { it.toInt() }
    val dist = IntArray(100001) { -1 }
    val queue = LinkedList<Int>()

    queue.add(N)
    dist[N] = 0

    while (queue.isNotEmpty()) {
        val curr = queue.poll()

        if (curr == X)
            break

        for (nxt in arrayOf(curr - 1, curr + 1, curr * 2)) {
            if (nxt < 0 || nxt > 100_000) continue
            if (dist[nxt] != -1) continue

            dist[nxt] = dist[curr] + 1
            queue.offer(nxt)
        }
    }

    print(dist[X])
}
```

### 3. 풀이 과정

- 중복되는 값은 `dist 배열`로 처리하여 `Queue`에 추가하지 않는다.

</br>

## 2. Stack

### BOJ 6198번 옥상 정원 꾸미기

### 1. 개요

[https://www.acmicpc.net/problem/6198](https://www.acmicpc.net/problem/6198)

### 2. 코드

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()) {
    val buildingNum = readLine().toInt()
    val buildings = LongArray(buildingNum)
    val stack = Stack<Long>()
    var answer = 0L

    for (i in 0 until buildingNum)
        buildings[i] = readLine().toLong()

    for (i in 0 until buildingNum) {
        while (stack.isNotEmpty() && stack.peek() <= buildings[i])
            stack.pop()

        answer += stack.size.toLong()
        stack.push(buildings[i])
    }

    print(answer)
}
```

### 3. 풀이 과정

- 빌딩을 앞에서부터 순차 탐색을 하면서

  1. `Stack`이 비어있지 않을 때 현재 탐색하는 빌딩이 `Stack`의 `Top`보다 크다면 `pop()`

  2. 아니라면, 현재 `Stack`의 크기만큼을 `answer`에 더하고 빌딩을 `push()`

- 그렇다면 `Stack`에는 현재 탐색하는 빌딩보다 큰 빌딩만이 남아있게 되고 즉, 현재 탐색하는 빌딩이 몇 개의 빌딩한테 보여지는 지를 알 수 있다.

### 4. 결과

1. answer의 변수 타입을 Long으로 해주지 않아서 OverFlow 발생

![image](https://user-images.githubusercontent.com/24761073/88663363-74f63680-d116-11ea-9f4c-b67cc357e132.png)
