## 문제 구성

- [1. (BOJ) 로봇 청소기](#문제-1-로봇-청소기)
- [2. (BOJ) 스타트와 링크](#문제-2-스타트와-링크)
- [3. (BOJ) 시험 감독](#문제-3-시험-감독)
- [4. (BOJ) 주사위 굴리기](#문제-4-주사위-굴리기)
- [5. (BOJ) 테트로미노](#문제-5-테트로미노)
- [6. (Programmers) 네트워크](#문제-6-네트워크)

## 문제 1 로봇 청소기

### 문제

[https://www.acmicpc.net/problem/14503](https://www.acmicpc.net/problem/14503)

### 코드

```kotlin
private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (row, col) = readInts()
    var (robotY, robotX, direction) = readInts()
    val board = Array(row) { IntArray(col) }
    val dy = arrayOf(-1, 0, 1, 0) // 북동남서
    val dx = arrayOf(0, 1, 0, -1)
    var answer = 0

    fun input() {
        board.forEachIndexed { y, ints ->
            val st = StringTokenizer(readLine(), " ")
            ints.forEachIndexed { x, _ -> board[y][x] = st.nextToken().toInt() }
        }
    }

    fun updateDirection() {
        direction--
        if (direction < 0) direction = 3
    }

    fun clean() {
        board[robotY][robotX] = 2
        answer++

        var count = 0
        while (true) {
            updateDirection()
            count++

            val yPos = robotY + dy[direction]
            val xPos = robotX + dx[direction]

            if (board[yPos][xPos] == 0) {
                robotY = yPos
                robotX = xPos
                board[yPos][xPos] = 2
                answer++
                count = 0
                continue
            }

            if (count == 4) {
                if (board[robotY + -dy[direction]][robotX + -dx[direction]] == 1) break

                robotY += -dy[direction]
                robotX += -dx[direction]
                count = 0
            }
        }
    }

    input()
    clean()
    print(answer)
}
```

### 문제 해설

제대로 구현한다면 해결할 수 있는 문제이다.

현재 방향을 나타내는 **direction**의 값을 `updateDirection()`를 통해 변환하고 다음을 수행한다.

</br>

## 문제 2 스타트와 링크

### 문제

[https://www.acmicpc.net/problem/14889](https://www.acmicpc.net/problem/14889)

### 코드

```kotlin
import java.lang.StringBuilder
import kotlin.math.abs
import kotlin.math.min

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val n = readInt()
    val arr = Array(n) { readInts().toIntArray() }
    var answer = Int.MAX_VALUE

    val comb = mutableListOf<MutableList<Int>>()
    combination(comb, n / 2, 0, mutableListOf())

    fun calculate(list: MutableList<Int>, other: MutableList<Int>) {
        var startTeam = 0
        var linkTeam = 0

        for (i in list.indices) {
            for (j in i + 1 until list.size) {
                startTeam += arr[list[i]][list[j]] + arr[list[j]][list[i]]
                linkTeam += arr[other[i]][other[j]] + arr[other[j]][other[i]]
            }
        }
        answer = min(answer, abs(startTeam - linkTeam))
    }

    for (i in comb.indices) {
        calculate(comb[i], IntRange(0, n).filter { !comb[i].contains(it) }.toMutableList())
    }
    print(answer)
}

private fun combination(result: MutableList<MutableList<Int>>, volume: Int, idx: Int, stored: MutableList<Int>) {
    if (stored.size == volume) {
        result.add(stored.toMutableList())  // toMuTableList()는 clone() 역할을 한다.
        return
    }

    for (i in idx until volume * 2) {
        stored.add(i)
        combination(result, volume, i + 1, stored)
        stored.remove(i)
    }
}
```

### 문제 해설

최악의 경우의 수는 20명 중 10명을 뽑는 조합의 수이므로 **완전 탐색**으로 해결이 가능하다.

모든 조합을 구한 뒤, 구한 조합과 포함되지 않는 조합에 대해 점수를 계산한다.

</br>

## 문제 3 시험 감독

### 문제

[https://www.acmicpc.net/problem/13458](https://www.acmicpc.net/problem/13458)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.ceil

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val n = readInt()
    val arr = IntArray(n)

    fun input() {
        val st = StringTokenizer(readLine(), " ")
        arr.forEachIndexed { index, _ -> arr[index] = st.nextToken().toInt() }
    }
    input()

    val (B, C) = readInts()
    var answer = n.toLong()
    var cnt = 0L

    arr.map { it - B }.filter { it > 0 }.forEach {
        if (it <= C) cnt++
        else cnt += ceil(it.toDouble() / C.toDouble()).toLong()
    }

    println(answer + cnt)
}
```

### 문제 해설

수학적 계산으로 풀이 가능한 문제였다.

필요한 총감독관의 수는 항상 **n**이며, 필요한 부감독관의 수는 각 방의 남은 인원에 대해

C보다 작거나 같으면 **+1**, 아니라면 나눈 뒤 올림한 값을 더 해준다.

</br>

## 문제 4 주사위 굴리기

### 문제

[https://www.acmicpc.net/problem/14499](https://www.acmicpc.net/problem/14499)

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
    var (row, col, y, x, cmdN) = readInts()
    val board = Array(row) { readInts().toIntArray() }
    val dy = arrayOf(0, 0, -1, 1) // 동 서 북 남
    val dx = arrayOf(1, -1, 0, 0)
    val _number = IntArray(7)
    val diceInfo = arrayOf(2, 1, 5, 6) // behind, top, front, bottom
    val diceInfo2 = arrayOf(4, 3) // left, right

    fun roll(cmd: Int) {
        when (cmd) {
            1 -> {
                var tempTop = diceInfo[1]
                var tempBottom = diceInfo[3]

                diceInfo[1] = diceInfo2[0]
                diceInfo[3] = diceInfo2[1]
                diceInfo2[0] = tempBottom
                diceInfo2[1] = tempTop
            }
            2 -> {
                var tempTop = diceInfo[1]
                var tempBottom = diceInfo[3]

                diceInfo[1] = diceInfo2[1]
                diceInfo[3] = diceInfo2[0]
                diceInfo2[0] = tempTop
                diceInfo2[1] = tempBottom
            }
            3 -> {
                var temp = diceInfo[0]
                for (i in 0..2) {
                    diceInfo[i] = diceInfo[i + 1]
                }
                diceInfo[3] = temp
            }
            4 -> {
                var temp = diceInfo[3]
                for (i in 3 downTo 1) {
                    diceInfo[i] = diceInfo[i - 1]
                }
                diceInfo[0] = temp
            }
        }
    }

    fun dice(cmd: Int) {
        val yPos = y + dy[cmd - 1]
        val xPos = x + dx[cmd - 1]

        if (yPos < 0 || yPos >= row || xPos < 0 || xPos >= col) return  // 범위를 벗어나면 무시

        roll(cmd)

        if (board[yPos][xPos] == 0) {
            board[yPos][xPos] = _number[diceInfo[3]]
        }
        else {
            _number[diceInfo[3]] = board[yPos][xPos]
            board[yPos][xPos] = 0
        }

        y = yPos
        x = xPos
        out.appendln(_number[diceInfo[1]])
    }

    val st = StringTokenizer(readLine(), " ")
    for (i in 0 until st.countTokens()) {
        val cmd = st.nextToken().toInt()
        dice(cmd)
    }

    print(out)
}
```

### 문제 해설

문제 설명대로 정확히 구현하면 해결되는 문제였다.

주어진 명령을 토대로 시작하는 `dice()`와 주사위를 굴리는 `roll()` 함수를 구현하였다.

**북, 남** 방향은 항상 4면을 기준으로 이동되며, **동, 서** 방향은 항상 **왼쪽, 오른쪽과 위, 아래**에 대해서 연산이 이루어진다.

</br>

## 문제 5 테트로미노

### 문제

[https://www.acmicpc.net/problem/14500](https://www.acmicpc.net/problem/14500)

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
    val (row, col) = readInts()
    val board = Array(row) { IntArray(col) }
    var answer = 0
    
    board.forEachIndexed { y, ints ->
        val st = StringTokenizer(readLine(), " ")
        ints.forEachIndexed { x, i ->
            board[y][x] = st.nextToken().toInt()
        }
    }

    fun one(y: Int, x: Int) {
        if (x + 2 < col) {
            val body = board[y][x] + board[y][x + 1] + board[y][x + 2]
            if (y - 1 >= 0) {
                answer = max(answer, body + board[y - 1][x])
                answer = max(answer, body + board[y - 1][x + 2])
            }
            if (y + 1 < row) {
                answer = max(answer, body + board[y + 1][x])
                answer = max(answer, body + board[y + 1][x + 2])
            }
        }
        if (y + 2 < row) {
            val body = board[y][x] + board[y + 1][x] + board[y + 2][x]
            if (x - 1 >= 0) {
                answer = max(answer, body + board[y][x - 1])
                answer = max(answer, body + board[y + 2][x - 1])
            }
            if (x + 1 < col) {
                answer = max(answer, body + board[y][x + 1])
                answer = max(answer, body + board[y + 2][x + 1])
            }
        }
    }

    fun two(y: Int, x: Int) {
        if (y + 1 < row && x + 1 < col) {
            answer = max(answer, board[y][x] + board[y][x + 1] + board[y + 1][x] + board[y + 1][x + 1])
        }
    }

    fun three(y: Int, x: Int) {
        if (x + 3 < col) {
            answer = max(answer, board[y][x] + board[y][x + 1] + board[y][x + 2] + board[y][x + 3])
        }
        if (y + 3 < row) {
            answer = max(answer, board[y][x] + board[y + 1][x] + board[y + 2][x] + board[y + 3][x])
        }
    }

    fun four(y: Int, x: Int) {
        if (x + 2 < col) {
            if (y + 1 < row) {
                answer = max(answer, board[y][x] + board[y][x + 1] + board[y + 1][x + 1] + board[y + 1][x + 2])
            }
            if (y - 1 >= 0) {
                answer = max(answer, board[y][x] + board[y][x + 1] + board[y - 1][x + 1] + board[y - 1][x + 2])
            }
        }
        if (x - 2 >= 0) {
            if (y + 1 < row) {
                answer = max(answer, board[y][x] + board[y][x - 1] + board[y + 1][x - 1] + board[y + 1][x - 2])
            }
            if (y - 1 >= 0) {
                answer = max(answer, board[y][x] + board[y][x - 1] + board[y - 1][x - 1] + board[y - 1][x - 2])
            }
        }
        if (y + 2 < row) {
            if (x + 1 < col) {
                answer = max(answer, board[y][x] + board[y + 1][x] + board[y + 1][x + 1] + board[y + 2][x + 1])
            }
            if (x - 1 >= 0) {
                answer = max(answer, board[y][x] + board[y + 1][x] + board[y + 1][x - 1] + board[y + 2][x - 1])
            }
        }
        if (y - 2 >= 0) {
            if (x + 1 < col) {
                answer = max(answer, board[y][x] + board[y - 1][x] + board[y - 1][x + 1] + board[y - 2][x + 1])
            }
            if (x - 1 >= 0) {
                answer = max(answer, board[y][x] + board[y - 1][x] + board[y - 1][x - 1] + board[y - 2][x - 1])
            }
        }
    }

    fun five(y: Int, x: Int) {
        if (x + 2 < col) {
            val body = board[y][x] + board[y][x + 1] + board[y][x + 2]
            if (y + 1 < row) {
                answer = max(answer, body + board[y + 1][x + 1])
            }
            if (y - 1 >= 0) {
                answer = max(answer, body + board[y - 1][x + 1])
            }
        }
        if (y + 2 < row) {
            val body = board[y][x] + board[y + 1][x] + board[y + 2][x]
            if (x + 1 < col) {
                answer = max(answer, body + board[y + 1][x + 1])
            }
            if (x - 1 >= 0) {
                answer = max(answer, body + board[y + 1][x - 1])
            }
        }
    }

    fun tetromino(y: Int, x: Int) {
        one(y, x)
        two(y, x)
        three(y, x)
        four(y, x)
        five(y, x)
    }

    for (y in 0 until row) {
        for (x in 0 until col) {
            tetromino(y, x)
        }
    }
    print(answer)
}
```

### 문제 해설

모든 좌표에 대해서 5가지 도형을 **완전 탐색**하여 문제를 해결하였다.

도면의 최대 크기는 500 * 500이며, 5가지 도형을 모두 보는 데에 19번의 연산이 필요하므로

최악의 경우라고 해도 충분히 시간 안에 해결할 수 있다.

</br>

## 문제 6 네트워크

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/43162](https://programmers.co.kr/learn/courses/30/lessons/43162)

### 코드

```kotlin
class Solution {
    fun solution(n: Int, computers: Array<IntArray>): Int {
        val adj = Array(n) { mutableListOf<Int>() }
        val visited = BooleanArray(n)
        var answer = 0
        
       fun input() {
            computers.forEachIndexed { y, ints -> 
                ints.forEachIndexed { x, i ->
                    if (y != x && i == 1) {
                        adj[y].add(x)
                        adj[x].add(y)
                    }
                }
            }
        }
        
        fun dfs(x: Int) {
            visited[x] = true
            
            for (node in adj[x]) {
                if (!visited[node]) {
                    dfs(node)
                }
            }
        }
        
        input()
        
        for (i in 0 until n) {
            if (!visited[i]) {
                dfs(i)
                answer++
            }
        }
        return answer
    }
}
```

### 문제 해설

N은 최대 20이기 때문에 인접 리스트를 이용한 DFS로 문제를 해결하였다.

`dfs()`를 처음 시작하는 횟수가 연결되있지 않은 네트워크 그룹의 수이다.
