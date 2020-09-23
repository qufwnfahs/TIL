## 문제 구성

- [1. (KAKAO 2020 인턴십) 키패드 누르기](#문제-1-키패드-누르기)
- [2. (KAKAO 2020 인턴십) 수식 최대화](#문제-2-수식-최대화)
- [3. (KAKAO 2020 인턴십) 보석 쇼핑](#문제-3-보석-쇼핑)
- [4. (KAKAO 2020 인턴십) 경주로 건설](#문제-4-경주로-건설)
- [5. (KAKAO 2020 인턴십) 동굴 탐험](#문제-5-동굴-탐험)
- [6. (BOJ) 미세먼지 안녕!](#문제-6-미세먼지-안녕)
- [7. (BOJ) 낚시왕](#문제-7-낚시왕)

</br>

## 문제 1 키패드 누르기

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/67256](https://programmers.co.kr/learn/courses/30/lessons/67256)

### 코드

```kotlin
import java.lang.StringBuilder
import kotlin.math.abs

class Solution {

    fun solution(numbers: IntArray, hand: String): String {
        var leftThumb = arrayOf(3, 0) // 왼손 엄지손가락 *
        var rightThumb = arrayOf(3, 2) // 오른손 엄지손가락 #
        val sb = StringBuilder()

        fun push(number: Int) {
            val numberPos = if (number == 0) arrayOf(3, 1) else arrayOf((number - 1) / 3, (number - 1) % 3)
            val leftPos = abs(numberPos[0] - leftThumb[0]) + abs(numberPos[1] - leftThumb[1])
            val rightPos = abs(numberPos[0] - rightThumb[0]) + abs(numberPos[1] - rightThumb[1])

            if (leftPos < rightPos) {
                leftThumb = numberPos
                sb.append('L')
            }
            else if (leftPos > rightPos) {
                rightThumb = numberPos
                sb.append('R')
            }
            else {
                if (hand == "left") {
                    leftThumb = numberPos
                    sb.append('L')
                }
                else {
                    rightThumb = numberPos
                    sb.append('R')
                }
            }
        }

        for (number in numbers) {
            when (number) {
                1, 4, 7 -> {
                    leftThumb[0] = (number - 1) / 3
                    leftThumb[1] = 0
                    sb.append('L')
                }
                3, 6, 9 -> {
                    rightThumb[0] = (number - 1) / 3
                    rightThumb[1] = 2
                    sb.append('R')
                }
                else -> {
                    push(number)
                }
            }
        }

        return sb.toString()
    }
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

**1, 4, 7** 키패드는 항상 왼쪽 엄지 손가락이 누르며 **3, 6, 9** 키패드는 항상 오른쪽 엄지 손가락이 누른다.

나머지는 키패드의 위치와 가까운 손의 손가락이 누르는데, `거리가 같을 경우` 어떤 손잡이냐에 따라 다르다.

</br>

## 문제 2 수식 최대화

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/67257](https://programmers.co.kr/learn/courses/30/lessons/67257)

### 코드

```kotlin
import java.lang.StringBuilder
import kotlin.math.abs
import kotlin.math.max

class Solution {

    fun solution(expression: String): Long {
        val oper = arrayOf('*', '+', '-')
        var answer = 0L

        fun doOperation(operation: Char, _expr: String): String {
            val result = StringBuilder()
            var index = 0

            fun getNumber(_expr: String): Long {
                val sb = StringBuilder()

                for (i in index until _expr.length) {
                    if (!oper.contains(_expr[i])) sb.append(_expr[i])  // digit
                    else {
                        if (sb.isNotEmpty()) {
                            index = i
                            return sb.toString().toLong()
                        }
                        else sb.append(_expr[i])
                    }
                }
                index = _expr.length
                return sb.toString().toLong()
            }

            var curr = getNumber(_expr)

            while (index < _expr.length) {
                val c = _expr[index++]
                val next = getNumber(_expr)

                if (c == operation) {
                    var value = when (c) {
                        '*' -> curr * next
                        '+' -> curr + next
                        '-' -> curr - next
                        else -> 0L
                    }
                    curr = value
                }
                else {
                    result.append("$curr$c")
                    curr = next
                }
            }
            result.append(curr)

            return result.toString()
        }

        fun calculate(stored: MutableList<Char>) {
            var _expr = expression

            for (c in stored) {
                _expr = doOperation(c, _expr)
            }
            answer = max(answer, abs(_expr.toLong()))
        }

        fun combination(cnt: Int, volume: Int, visited: BooleanArray, stored: MutableList<Char>) {
            if (cnt == volume) {
                calculate(stored)
                return
            }

            for (i in oper.indices) {
                if (!visited[i]) {
                    visited[i] = true
                    stored.add(oper[i])
                    combination(cnt + 1, oper.size, visited, stored)
                    visited[i] = false
                    stored.remove(oper[i])
                }
            }
        }
        combination(0, oper.size, BooleanArray(oper.size), mutableListOf())

        return answer
    }
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

`*, +, -` 세 연산자에 대한 모든 우선순위 **조합**을 구한 뒤, 매 **조합**마다 우선순위 기준으로 계산하여 가장 큰 값을 기록하였다.

계산 과정 도중에 **음수인 수**가 존재하여 음수인 수도 올바르게 **토큰화**할 수 있도록 `getNumber()` 함수를 만들었다.

</br>

## 문제 3 보석 쇼핑

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/67258](https://programmers.co.kr/learn/courses/30/lessons/67258)

### 코드

```kotlin
class Solution {

    fun solution(gems: Array<String>): IntArray {
        val gemsN = gems.toSet().size // 보석의 종류 개수
        val gemsCounter = mutableMapOf<String, Int>() // 각 보석의 카운터
        var totalCount = 0  // 포함하고 있는 전체 보석 카운터

        val answer = IntArray(2)
        var min_length = gems.size
        var end = 0

        for (start in gems.indices) {
            while (totalCount < gemsN && end < gems.size) {
                gemsCounter[gems[end]] = (gemsCounter[gems[end]] ?: 0) + 1
                if (gemsCounter[gems[end]] == 1) totalCount++

                end++
            }

            if (totalCount == gemsN && gemsCounter[gems[start]] == 1) {    // 1인 경우 : 보석을 모두 포함하는 짧은 구간
                val length = (end - 1) - start
                if (min_length > length) {
                    answer[0] = start + 1
                    answer[1] = end
                    min_length = length
                }

                totalCount--
            }
            gemsCounter[gems[start]] = gemsCounter[gems[start]]!! - 1
        }
        return answer
    }
}
```

### 문제 해설

**투 포인터**를 사용하여 문제를 해결하였다.

</br>

**시작 지점**과 **끝 지점**을 각각 가리키는 변수를 정의한 뒤 

1. 끝 지점을 모든 보석을 포함하는 지점까지 **확장**시킨다.
2. 시작 지점을 모든 보석이 포함되는 한계 지점까지 **축소**시킨다.
3. 현재 거리를 계산한다.

</br>

## 문제 4 경주로 건설

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/67259](https://programmers.co.kr/learn/courses/30/lessons/67259)

### 코드

```kotlin
import java.util.*

class Solution {
    
    fun solution(board: Array<IntArray>): Int {
        val dy = arrayOf(-1, 1, 0, 0)
        val dx = arrayOf(0, 0, -1, 1)
        val distance = Array(board.size) { IntArray(board.size) }

        fun calculateCost(direct: Int, to: Int): Int {
            return if (direct in 0..1 && to in 2..3) 600
            else if (direct in 2..3 && to in 0..1) 600
            else 100
        }

        fun BFS() {
            val queue = LinkedList<Road>()

            distance[0][0] = 1
            queue.add(Road(0, 0, 0, -1))

            while (queue.isNotEmpty()) {
                val road = queue.poll()

                for (i in 0 until 4) {
                    val yPos = road.y + dy[i]
                    val xPos = road.x + dx[i]

                    if (yPos < 0 || yPos >= board.size || xPos < 0 || xPos >= board.size) continue
                    if (board[yPos][xPos] == 1) continue
                    val cost = road.cost + calculateCost(road.direct, i)
                    if (distance[yPos][xPos] != 0 && distance[yPos][xPos] < cost) continue

                    distance[yPos][xPos] = cost
                    queue.add(Road(yPos, xPos, cost, i))
                }
            }
        }
        BFS()

        return distance[board.size - 1][board.size - 1]
    }

    class Road {
        var x: Int
        var y: Int
        var cost: Int
        var direct: Int

        constructor(y: Int, x: Int, cost: Int, direct: Int) {
            this.y = y
            this.x = x
            this.cost = cost
            this.direct = direct
        }
    }
}
```

### 문제 해설

**BFS**를 통해 경주로의 최소 비용을 구한다.

</br>

**현재 방향**과 **탐색할 방향**을 비교하여 코너의 여부를 파악하여 비용을 계산해나간다.

`distance` 배열을 두어 지나갔지만 비용이 더 작은 경우 **다시 탐색을 수행**하게 했다.

</br>

## 문제 5 동굴 탐험

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/67260](https://programmers.co.kr/learn/courses/30/lessons/67260)

### 코드

```kotlin
class Solution {

    fun solution(n: Int, path: Array<IntArray>, order: Array<IntArray>): Boolean {
        val adj = Array(n) { mutableListOf<Int>() }
        val need = Array(n) { mutableListOf<Int>() }
        val visited = BooleanArray(n)
        val recur = BooleanArray(n)

        for ((from, to) in path) {
            adj[from].add(to)
            adj[to].add(from)
        }

        fun setParent(node: Int, parent: Int) {
            for (next in adj[node]) {
                if (next == parent) continue

                need[next].add(node)
                setParent(next, node)
            }
        }
        setParent(0, -1)

        for ((from, to) in order) {
            need[to].add(from)
        }

        fun findCycleWithDFS(x: Int): Boolean {
            visited[x] = true
            recur[x] = true

            for (next in need[x]) {
                if (!visited[next] && findCycleWithDFS(next)) {
                    return true
                }
                else if (recur[next]) {
                    return true
                }
            }
            recur[x] = false

            return false
        }

        for (i in 0 until n) {
            if (!visited[i]) {
                if (findCycleWithDFS(i)) {
                    return false
                }
            }
        }
        return true
    }
}
```

### 문제 해설

동굴이 0번, 3번, 6번으로 구성되어 있고 6번을 방문해야지만 3번을 방문할 수 있다고 가정해보자.

```kotlin
path = [[0, 3], [3, 6]]
order = [6, 3]
```

6번을 방문하기 위해서는 필연적으로 3번을 먼저 방문해야 함으로 이 동굴을 탐험할 수 없다는 것을 알 수 있다.

이 상황을 방문의 상관 순서에 따라 단방향 그래프로 표현해보자.

![image](https://user-images.githubusercontent.com/24761073/93466142-c22ca280-f926-11ea-927e-fae530f8aa9c.png)

종합해보면 이 문제는 유향 그래프 내에 Cycle의 유무 여부를 확인하는 문제로 환원될 수 있다.

Cycle이 있다면 불가능한 경우이므로 `false`, 아니라면 모든 방을 방문할 수 있음으로 `true` 를 반환한다.

</br>

먼저 **path** 정보를 통해 **양방향 그래프**를 만든 뒤, `0번부터 방문한다`는 조건이 존재하므로 0번부터 탐색하여 **단방향 그래프**로 바꾸어준다.

그 후, **DFS**를 통해 **Cycle**의 존재 여부를 판단한다. `(단방향 그래프의 Cycle 판단은 DFS 알고리즘을 사용)`

### 참고

[https://medium.com/@haeseok/프로그래머스-동굴-탐험-a669d62f304d](https://medium.com/@haeseok/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4-%EB%8F%99%EA%B5%B4-%ED%83%90%ED%97%98-a669d62f304d)

</br>

## 문제 6 미세먼지 안녕

### 문제

[https://www.acmicpc.net/problem/17144](https://www.acmicpc.net/problem/17144)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (row, col, time) = readInts()
    var room = Array(row) { IntArray(col) }
    var airFresher = 0 // 공기 청정기 Top 위치
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)

    fun initRoom() {
        for (y in 0 until row) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until st.countTokens()) {
                room[y][x] = st.nextToken().toInt()
                if (airFresher == 0 && room[y][x] == -1) {
                    airFresher = y
                }
            }
        }
    }
    initRoom()

    fun fill(fromY: Int, fromX: Int, toY: Int, toX: Int, arr: Array<IntArray>) {
        for (y in fromY..toY) {
            for (x in fromX..toX) {
                arr[y][x] = room[y][x]
            }
        }
    }

    fun spread() {
        val spreaded = Array(row) { IntArray(col) }
        spreaded[airFresher][0] = -1
        spreaded[airFresher + 1][0] = -1

        for (y in room.indices) {
            for (x in room[0].indices) {
                if (room[y][x] > 0) {
                    val amount = room[y][x] / 5
                    var count = 0

                    for (i in 0 until 4) {
                        val yPos = y + dy[i]
                        val xPos = x + dx[i]

                        if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) continue
                        if (room[yPos][xPos] == -1) continue

                        spreaded[yPos][xPos] += amount
                        count++
                    }
                    spreaded[y][x] += room[y][x] - (amount * count)
                }
            }
        }
        room = spreaded
    }

    fun cycle() {
        val cycled = Array(row) { IntArray(col) }
        val top = airFresher
        val bottom = airFresher + 1

        for (x in 2 until col) {
            cycled[top][x] = room[top][x - 1]
            cycled[bottom][x] = room[bottom][x - 1]
        }
        for (x in col - 2 downTo 0) {
            cycled[0][x] = room[0][x + 1]
            cycled[row - 1][x] = room[row - 1][x + 1]
        }
        // Top
        for (y in top - 1 downTo 0) {
            cycled[y][col - 1] = room[y + 1][col - 1]
        }
        for (y in 1 until top) {
            cycled[y][0] = room[y - 1][0]
        }
        // Bottom
        for (y in bottom + 1 until row) {
            cycled[y][col - 1] = room[y - 1][col - 1]
        }
        for (y in row - 2 downTo bottom + 1) {
            cycled[y][0] = room[y + 1][0]
        }
        cycled[top][0] = -1
        cycled[bottom][0] = -1

        fill(1, 1, top - 1, col - 2, cycled)
        fill(bottom + 1, 1, row - 2, col - 2, cycled)

        room = cycled
    }

    repeat(time) { // T초만큼 반복
        spread()
        cycle()
    }

    fun sum(): Int {
        var answer = 0

        for (y in 0 until row) {
            for (x in 0 until col) {
                if (room[y][x] > 0) answer += room[y][x]
            }
        }
        return answer
    }
    println(sum())
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

주어진 **T**초만큼 **확산**과 **순환**을 반복한다.

</br>

## 문제 7 낚시왕

### 문제

[https://www.acmicpc.net/problem/17143](https://www.acmicpc.net/problem/17143)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (row, col, sharkN) = readInts()
    val arr = Array(row) { Array(col) { mutableListOf<Shark>()} }
    var pos = -1
    var answer = 0

    fun initShark() {
        repeat(sharkN) {
            val st = StringTokenizer(readLine(), " ")
            val y = st.nextToken().toInt() - 1
            val x = st.nextToken().toInt() - 1
            arr[y][x].add(Shark(st.nextToken().toInt(), st.nextToken().toInt(), st.nextToken().toInt()))
        }
    }
    initShark()

    fun getSharkNextPos(y: Int, x: Int, shark: Shark): Pair<Int, Int> {
        var cY = y
        var cX = x
        var speed = shark.speed
        var direct = shark.direct

        while (speed-- > 0) {
            when (direct) {
                1 -> {
                    if (cY == 0) {
                        direct = 2
                        speed++
                    }
                    else cY--
                }
                2 -> {
                    if (cY == row - 1) {
                        direct = 1
                        speed++
                    }
                    else cY++
                }
                3 -> {
                    if (cX == col - 1) {
                        direct = 4
                        speed++
                    }
                    else cX++
                }
                4 -> {
                    if (cX == 0) {
                        direct = 3
                        speed++
                    }
                    else cX--
                }
            }
        }
        shark.direct = direct

        return Pair(cY, cX)
    }

    fun catchShark() {
        for (y in 0 until row) {
            if (arr[y][pos].isNotEmpty()) {
                val shark = arr[y][pos][0]

                answer += shark.size
                arr[y][pos].remove(shark)
                break
            }
        }
    }

    fun moveShark() {
        val queue = LinkedList<Triple<Int, Int, Shark>>()

        for (y in 0 until row) {
            for (x in 0 until col) {
                if (arr[y][x].isNotEmpty()) {
                    val shark = arr[y][x][0]
                    arr[y][x].remove(shark)

                    val nextPos = getSharkNextPos(y, x, shark)
                    queue.add(Triple(nextPos.first, nextPos.second, shark))
                }
            }
        }

        while (queue.isNotEmpty()) {
            val curr = queue.poll()
            arr[curr.first][curr.second].add(curr.third)

            if (arr[curr.first][curr.second].size > 1) {
                val maxShark = arr[curr.first][curr.second].maxBy { it.size }!!
                arr[curr.first][curr.second].clear()
                arr[curr.first][curr.second].add(maxShark)
            }
        }
    }

    while (pos++ < col - 1) {
        catchShark()
        moveShark()
    }
    println(answer)
}

private class Shark {
    val speed: Int
    var direct: Int
    val size: Int

    constructor(speed: Int, direct: Int, size: Int) {
        this.speed = speed
        this.direct = direct
        this.size = size
    }
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

격자판의 크기만큼 **List**를 생성하여 각각 상어를 저장할 수 있게 하였다.

모든 격자판을 탐색하여 상어가 **List**에 있을 시 이동시켰다.

상어들의 속도만큼의 이동과 방향 전환을 위해 `getSharkNextPos()`를 구현하였다.
