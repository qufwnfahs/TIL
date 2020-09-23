## 문제 구성

- [1. (BOJ) 이차원 배열과 연산](#문제-1-이차원-배열과-연산)
- [2. (BOJ) 연구소 3](#문제-2-연구소-3)
- [3. (BOJ) 게리멘더링 2](#문제-3-게리멘더링-2)
- [4. (BOJ) 새로운 게임](#문제-4-새로운-게임)
- [5. (BOJ) 원판 돌리기](#문제-5-원판-돌리기)
- [6. (BOJ) 주사위 윷놀이](#문제-6-주사위-윷놀이)

</br>

## 문제 1 이차원 배열과 연산

### 문제

[https://www.acmicpc.net/problem/17140](https://www.acmicpc.net/problem/17140)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.max

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (r, c, k) = readInts()
    var arr = Array(100) { IntArray(100) }

    var max_row = 3
    var max_col = 3
    var answer = 0

    fun init() {
        for (y in 0 until 3) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until 3) {
                arr[y][x] = st.nextToken().toInt()
            }
        }
    }
    init()

    fun doR() {
        val newArr = Array(100) { IntArray(100) }

        for (y in 0 until max_row) {
            val pQueue = PriorityQueue<Pair<Int, Int>>(compareBy( { it.second }, { it.first } ))
            val maps = mutableMapOf<Int, Int>()
            var idx = 0
            // Map 정보 입력
            for (x in 0 until max_col) {
                if (arr[y][x] > 0) maps[arr[y][x]] = (maps[arr[y][x]] ?: 0) + 1
            }
            maps.forEach { (key, value) ->
                pQueue.add(Pair(key, value))
            }

            while (pQueue.isNotEmpty()) {
                val (ints, cnt) = pQueue.poll()
                newArr[y][idx++] = ints
                newArr[y][idx++] = cnt
            }

            max_col = max(max_col, idx)
            if (max_col > 100) max_col = 100
        }

        arr = newArr
    }

    fun doC() {
        val newArr = Array(100) { IntArray(100) }

        for (x in 0 until max_col) {
            val pQueue = PriorityQueue<Pair<Int, Int>>(compareBy( { it.second }, { it.first } ))
            val maps = mutableMapOf<Int, Int>()
            var idx = 0
            // Map 정보 입력
            for (y in 0 until max_row) {
                if (arr[y][x] > 0) maps[arr[y][x]] = (maps[arr[y][x]] ?: 0) + 1
            }
            maps.forEach { (key, value) ->
                pQueue.add(Pair(key, value))
            }

            while (pQueue.isNotEmpty()) {
                val (ints, cnt) = pQueue.poll()
                newArr[idx++][x] = ints
                newArr[idx++][x] = cnt
            }

            max_row = max(max_row, idx)
            if (max_row > 100) max_row = 100
        }

        arr = newArr
    }

    while (true) {
        if (answer > 100) break
        if (arr[r - 1][c - 1] == k) break  // 정답
        if (max_row >= max_col) {
            doR()
            answer++
        }

        if (arr[r - 1][c - 1] == k) break  // 정답
        if (max_row < max_col) {
            doC()
            answer++
        }
    }
    println(if (answer > 100) -1 else answer)
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

배열의 크기는 **100**을 넘을 수 없으므로 `계산의 편의성`을 위해 **100**으로 초기화시켰다.

**Map** 자료구조를 통해 개수를 센 뒤, 정렬을 위해 **PriortyQueue**를 사용하였다.

R연산과 C연산을 정확히 구현하기 위해 각각 `doR(), doC()` 함수 생성

</br>

## 문제 2 연구소 3

### 문제

[https://www.acmicpc.net/problem/17142](https://www.acmicpc.net/problem/17142)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.max
import kotlin.math.min

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (n, m) = readInts()
    val board = Array(n) { IntArray(n) }
    val viruses = mutableListOf<Pair<Int, Int>>()
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)
    var answer = Int.MAX_VALUE

    fun initBoard() {
        for (y in 0 until n) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until n) {
                board[y][x] = st.nextToken().toInt()
                if (board[y][x] == 2) viruses.add(Pair(y, x))   // 바이러스 위치 정보 저장
            }
        }
    }
    initBoard()

    fun spreadVirus(stored: MutableList<Pair<Int, Int>>) {
        val copyBoard = copyMatrix(board)
        val queue = LinkedList<Pair<Int, Int>>()
        var time = 1
        var max_time = 0

        stored.forEach {
            queue.add(it)
            copyBoard[it.first][it.second] = 100
        }

        while (queue.isNotEmpty()) {
            for (cnt in 0 until queue.size) {
                val (y, x) = queue.poll()

                for (i in 0 until 4) {
                    val yPos = y + dy[i]
                    val xPos = x + dx[i]

                    if (yPos < 0 || yPos >= n || xPos < 0 || xPos >= n) continue
                    if (copyBoard[yPos][xPos] == 1 || copyBoard[yPos][xPos] > 2) continue

                    if (copyBoard[yPos][xPos] == 0 || copyBoard[yPos][xPos] == 2) queue.add(Pair(yPos, xPos))

                    if (copyBoard[yPos][xPos] == 0) {
                        copyBoard[yPos][xPos] = time + 3
                        max_time = max(max_time, time)
                    }
                    else if (copyBoard[yPos][xPos] == 2) copyBoard[yPos][xPos] = 100
                }
            }
            time++
        }

        fun isAllSpread(): Boolean {
            for (y in 0 until n) {
                for (x in 0 until n) {
                    if (copyBoard[y][x] == 0) {
                        return false
                    }
                }
            }
            return true
        }
        if (isAllSpread()) {
            answer = min(answer, max_time)
        }
    }

    fun combination(idx: Int, cnt: Int, stored: MutableList<Pair<Int, Int>>) {
        if (cnt == m) {
            spreadVirus(stored)
            return
        }

        for (i in idx until viruses.size) {
            stored.add(viruses[i])
            combination(i + 1, cnt + 1, stored)
            stored.remove(viruses[i])
        }
    }
    combination(0, 0, mutableListOf())
    println(if (answer == Int.MAX_VALUE) -1 else answer)
}

private fun copyMatrix(board: Array<IntArray>): Array<IntArray> {
    val result = Array(board.size) { IntArray(board[0].size) }
    board.forEachIndexed { index, _ -> result[index] = board[index].copyOf() }

    return result
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

`조합 알고리즘`을 통해 활성 시킬 바이러스를 선택한 후 바이러스 확산을 진행하였다.

활성 바이러스는 **100**으로 표현, 비활성 바이러스는 배열에서 **2**로 남아있다가 확산 시에 **100**으로 치환된다.

빈 공간에 바이러스가 확산될 때 시간을 검사하여 모두 퍼지는 시간을 구하였다.

`Queue`를 모두 돌아도 모든 공간으로 확산이 됬다는 보장이 없으므로 `isAllSpread()` 함수를 이용하여 바이러스가 모두 퍼졌는지 확인한 뒤 **answer** 값을 갱신하였다.

</br>

## 문제 3 게리멘더링 2

### 문제

[https://www.acmicpc.net/problem/17779](https://www.acmicpc.net/problem/17779)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.min

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val n = readInt()
    val arr = Array(n) { IntArray(n) }
    var totalPerson = 0
    var answer = Int.MAX_VALUE

    fun initArray() {
        for (y in 0 until n) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until n) {
                arr[y][x] = st.nextToken().toInt()
                totalPerson += arr[y][x]
            }
        }
    }
    initArray()

    fun isPossibleD1(d: Int, y: Int, x: Int): Boolean = !(y + d >= n || x - d < 0)

    fun isPossibleD1D2(d1: Int, d2: Int, y: Int, x: Int): Boolean {
        if (y + d2 >= n || x + d2 >= n) return false
        if (y + d1 >= n || x - d1 < 0 || y + d1 + d2 >= n || x - d1 + d2 < 0 || x - d1 + d2 >= n) return false
        if (y + d2 >= n || x + d2 >= n || y + d2 + d1 >= n || x + d2 - d1 < 0 || x + d2 - d1 >= n) return false

        return true
    }

    fun getSection1(d1: Int, y: Int, x: Int): Int {
        var person = 0
        var idx = 0

        for (yPos in 0 until y + d1) {
            if (yPos < y) {
                for (xPos in 0..x) {
                    person += arr[yPos][xPos]
                }
            }
            else {
                for (xPos in 0 until x - idx++) {
                    person += arr[yPos][xPos]
                }
            }
        }
        return person
    }

    fun getSection2(d1: Int, d2: Int, y: Int, x: Int): Int {
        var person = 0
        var idx = 1

        for (yPos in 0..y + d2) {
            if (yPos < y) {
                for (xPos in x + 1 until n) {
                    person += arr[yPos][xPos]
                }
            }
            else {
                for (xPos in x + idx++ until n) {
                    person += arr[yPos][xPos]
                }
            }
        }
        return person
    }

    fun getSection3(d1: Int, d2: Int, y: Int, x: Int): Int {
        var person = 0
        var idx = 0

        for (yPos in y + d1 until n) {
            if (yPos < y + d1 + d2) {
                for (xPos in 0 until x - d1 + idx++) {
                    person += arr[yPos][xPos]
                }
            }
            else {
                for (xPos in 0 until x - d1 + d2) {
                    person += arr[yPos][xPos]
                }
            }
        }
        return person
    }

    fun getSection4(d1: Int, d2: Int, y: Int, x: Int): Int {
        var person = 0
        var idx = 0

        for (yPos in y + d2 + 1 until n) {
            if (yPos <= y + d2 + d1) {
                for (xPos in x + d2 - idx++ until n) {
                    person += arr[yPos][xPos]
                }
            }
            else {
                for (xPos in x + d2 - d1 until n) {
                    person += arr[yPos][xPos]
                }
            }
        }
        return person
    }

    fun simulation(y: Int, x: Int) {
        var d1 = 1

        while (isPossibleD1(d1, y, x)) {
            var d2 = 1
            while (isPossibleD1D2(d1, d2, y, x)) {
                val s1 = getSection1(d1, y, x)
                val s2 = getSection2(d1, d2, y, x)
                val s3 = getSection3(d1, d2, y, x)
                val s4 = getSection4(d1, d2, y, x)
                val s5 = totalPerson - s4 - s3 - s2 - s1

                val tempArr = arrayOf(s1, s2, s3, s4, s5)
                answer = min(answer, tempArr.max()!! - tempArr.min()!!)
                d2++
            }
            d1++
        }
    }

    for (y in 0 until n) {
        for (x in 0 until n) {
            simulation(y, x)
        }
    }
    println(answer)
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

## 문제 4 새로운 게임 2

### 문제

[https://www.acmicpc.net/problem/17837](https://www.acmicpc.net/problem/17837)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.max

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

private val WHITE = 0
private val RED = 1
private val BLUE = 2

fun main() {
    val (n, k) = readInts()
    val board = Array(n) { Array<Board> (n) { Board(0) } }
    val knightInfos = mutableListOf<Knight>()
    val dy = arrayOf(0, 0, 0, -1, 1)
    val dx = arrayOf(0, 1, -1, 0, 0)
    var max_knight = 1
    var answer = 0

    fun initBoard() {
        for (y in 0 until n) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until n) {
                board[y][x] = Board(st.nextToken().toInt())
            }
        }
    }
    initBoard()

    fun initKnights() {
        for (key in 1..k) {
            val (y, x, direct) = readInts()
            val knight: Knight = Knight(key, y - 1, x - 1, direct)
            knightInfos.add(knight)
            board[y - 1][x - 1].knights.add(knight)
        }
    }
    initKnights()

    fun reverse(direct: Int): Int {
        return when (direct) {
            1 -> 2
            2 -> 1
            3 -> 4
            4 -> 3
            else -> -1
        }
    }

    fun move(currK: Knight, yPos: Int, xPos: Int, color: Int) {
        val movable = board[currK.y][currK.x].getMovable(currK.key)
        board[yPos][xPos].knights.addAll(if (color == RED) movable.reversed() else movable)

        for (kn in movable) {
            knightInfos[kn.key - 1].y = yPos
            knightInfos[kn.key - 1].x = xPos
        }

        max_knight = max(max_knight, board[yPos][xPos].knights.size)
    }

    while (true) {
        if (max_knight >= 4 || answer > 1_000) {
            break
        }

        for (i in knightInfos.indices) {
            val currK = knightInfos[i]

            val yPos = currK.y + dy[currK.direct]
            val xPos = currK.x + dx[currK.direct]

            if (yPos < 0 || yPos >= n || xPos < 0 || xPos >= n || board[yPos][xPos].color == BLUE) {   // 범위를 벗어나거나 파란색이면
                currK.direct = reverse(currK.direct)    // 반전

                val yPos2 = currK.y + dy[currK.direct]   // 1칸 이동
                val xPos2 = currK.x + dx[currK.direct]

                if (yPos2 < 0 || yPos2 >= n || xPos2 < 0 || xPos2 >= n || board[yPos2][xPos2].color == BLUE) {    // 이동할 방향이 또 파란색이면
                    continue
                }
                // 아니면 이동
                move(currK, yPos2, xPos2, board[yPos2][xPos2].color)
            }
            else move(currK, yPos, xPos, board[yPos][xPos].color)
        }
        answer++
    }

    println(if (answer > 1_000) -1 else answer)
}

private class Board {
    val color: Int
    var knights = mutableListOf<Knight>()

    constructor(color: Int) {
        this.color = color
    }

    fun getMovable(key: Int): MutableList<Knight> {
        val movable = mutableListOf<Knight>()
        val target = knights.find { it.key == key }
        val index = knights.indexOf(target)

        while (knights.size > index) {
            movable.add(knights.removeAt(index))
        }
        return movable
    }
}

private class Knight {
    var key: Int
    var y: Int
    var x: Int
    var direct: Int

    constructor(key: Int, y: Int, x: Int, direct: Int) {
        this.key = key
        this.y = y
        this.x = x
        this.direct = direct
    }
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

## 문제 5 원판 돌리기

### 문제

[https://www.acmicpc.net/problem/17822](https://www.acmicpc.net/problem/17822)

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
    val (n, m, t) = readInts()
    var arr = Array(n + 1) { IntArray(m) }
    var answer = 0

    fun initArray() {
        for (y in 1 until n + 1) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until m) {
                arr[y][x] = st.nextToken().toInt()
            }
        }
    }
    initArray()

    fun rotateClockwise(target: Int, direct: Int, size: Int) {
        val rotated = IntArray(m)

        for (x in 0 until m) {
            var idx = (x - size) % m
            if (idx < 0) idx += m

            rotated[x] = arr[target][idx]
        }
        arr[target] = rotated
    }

    fun rotateCounterClockwise(target: Int, direct: Int, size: Int) {
        val rotated = IntArray(m)

        for (x in 0 until m) {
            var idx = (x + size) % m

            rotated[x] = arr[target][idx]
        }
        arr[target] = rotated
    }

    fun hasNumber(target: Int): Boolean {
        for (i in arr[target]) {
            if (i > 0) {
                return true
            }
        }
        return false
    }

    fun erase(target: Int, copyArr: Array<IntArray>): Boolean {
        var canErase = false

        for (x in arr[target].indices) {
            var curr = arr[target][x]

            if (curr == -1) continue

            val left = if (x - 1 < 0) arr[target][m - 1] else arr[target][x - 1]

            if (left == curr) {
                copyArr[target][x] = -1
                if (x - 1 < 0) copyArr[target][m - 1] = -1 else copyArr[target][x - 1] = -1
                canErase = true
            }

            val right = if (x + 1 >= m) arr[target][0] else arr[target][x + 1]

            if (right == curr) {
                copyArr[target][x] = -1
                if (x + 1 >= m) copyArr[target][0] = -1 else copyArr[target][x + 1] = -1
                canErase = true
            }

            if (target - 1 > 0) {
                val down = arr[target - 1][x]

                if (down == curr) {
                    copyArr[target][x] = -1
                    copyArr[target - 1][x] = -1
                    canErase = true
                }
            }
            if (target + 1 < arr.size) {
                val up = arr[target + 1][x]

                if (up == curr) {
                    copyArr[target][x] = -1
                    copyArr[target + 1][x] = -1
                    canErase = true
                }
            }
        }
        return canErase
    }

    fun getTotalAndCount(): IntArray {
        var total = 0
        var cnt = 0

        for (y in 1 until arr.size) {
            for (x in arr[y].indices) {
                if (arr[y][x] == -1) continue

                total += arr[y][x]
                cnt++
            }
        }
        return intArrayOf(total, cnt)
    }

    fun increaseOrDecrease(target: Int, average: Double) {
        for (x in arr[target].indices) {
            if (arr[target][x] == -1) continue

            if (arr[target][x] > average) arr[target][x] -= 1
            else if (arr[target][x] < average) arr[target][x] += 1
        }
    }

    repeat(t) {
        val (target, direct, size) = readInts()
        var canErase = false

        for (i in target until arr.size step target) {
            when (direct) {
                0 -> rotateClockwise(i, direct, size)
                1 -> rotateCounterClockwise(i, direct, size)
            }
        }
        val copyArr = copyMatrix(arr)

        for (i in 1 until arr.size) {   // 삭제
            if (erase(i, copyArr)) canErase = true
        }
        arr = copyArr

        if (!canErase) {
            val (total, cnt) = getTotalAndCount()
            val average = total.toDouble() / cnt

            for (i in 1 until arr.size) {
                increaseOrDecrease(i, average)
            }
        }
    }
    answer = getTotalAndCount()[0]
    print(answer)
}

private fun copyMatrix(board: Array<IntArray>): Array<IntArray> {
    val result = Array(board.size) { IntArray(board[0].size) }
    board.forEachIndexed { index, _ -> result[index] = board[index].copyOf() }

    return result
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

1번 원판부터 순차탐색하며 바로 지워나간다면, 값의 변조로 지워져야 할 번호가 지워지지 않기 때문에 **copy**하여 모든 원판을 모두 돌면서 **copy 배열**을 지운 뒤, 끝나고 나서 원판을 교체한다.

</br>

## 문제 6 주사위 윷놀이

### 문제

[https://www.acmicpc.net/problem/17825](https://www.acmicpc.net/problem/17825)

### 코드

```kotlin
import java.lang.StringBuilder
import kotlin.math.max

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

private val END = 21

fun main() {
    val board = arrayOf(
        intArrayOf(0, 1, 2, 3, 4, 5), // 0
        intArrayOf(2, 2, 3, 4, 5, 6), // 1
        intArrayOf(4, 3, 4, 5, 6, 7), // 2
        intArrayOf(6, 4, 5, 6, 7, 8), // 3
        intArrayOf(8, 5, 6, 7, 8, 9), // 4
        intArrayOf(10, 22, 23, 24, 30, 31), // 5
        intArrayOf(12, 7, 8, 9, 10, 11),
        intArrayOf(14, 8, 9, 10, 11, 12),
        intArrayOf(16, 9, 10, 11, 12, 13),
        intArrayOf(18, 10, 11, 12, 13, 14),
        intArrayOf(20, 28, 29, 30, 31, 32),
        intArrayOf(22, 12, 13, 14, 15, 16),
        intArrayOf(24, 13, 14, 15, 16, 17),
        intArrayOf(26, 14, 15, 16, 17, 18),
        intArrayOf(28, 15, 16, 17, 18, 19),
        intArrayOf(30, 25, 26, 27, 30, 31),
        intArrayOf(32, 17, 18, 19, 20, 21),
        intArrayOf(34, 18, 19, 20, 21, 21),
        intArrayOf(36, 19, 20, 21, 21, 21),
        intArrayOf(38, 20, 21, 21, 21, 21),
        intArrayOf(40, 21, 21, 21, 21, 21),
        intArrayOf(0, 21, 21, 21, 21, 21),
        intArrayOf(13, 23, 24, 30, 31, 32),
        intArrayOf(16, 24, 30, 31, 32, 20),
        intArrayOf(19, 30, 31, 32, 20, 21),
        intArrayOf(28, 26, 27, 30, 31, 32),
        intArrayOf(27, 27, 30, 31, 32, 20),
        intArrayOf(26, 30, 31, 32, 20, 21),
        intArrayOf(22, 29, 30, 31, 32, 20),
        intArrayOf(24, 30, 31, 32, 20, 21),
        intArrayOf(25, 31, 32, 20, 21, 21),
        intArrayOf(30, 32, 20, 21, 21, 21),
        intArrayOf(35, 20, 21, 21, 21, 21)
    )
    val dice = readInts()
    val person = IntArray(4)
    val participants = IntArray(board.size)
    var answer = 0

    participants[0] = 4

    fun DFS(cnt: Int, sum: Int) {
        if (cnt == 10) {
            answer = max(answer, sum)
            return
        }

        val move = dice[cnt]
        for (i in person.indices) {
            val currPos = person[i]
            val nextPos = board[currPos][move]

            if (nextPos != END && participants[nextPos] > 0) continue

            participants[currPos]--
            participants[nextPos]++
            person[i] = nextPos
            DFS(cnt + 1, sum + board[nextPos][0])
            participants[currPos]++
            participants[nextPos]--
            person[i] = currPos
        }
    }
    DFS(0, 0)

    println(answer)

    println(1.shl(20))
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

윷놀이판은 고정되있으므로 LookUp 테이블을 구성한 뒤, 모든 경우를 탐색하기 위해 **DFS** 탐색을 수행하였다.
