## 문제 구성

- [1. (BOJ) 나무 재테크](#문제-1-나무-재테크)
- [2. (BOJ) 아기 상어](#문제-2-아기-상어)

</br>

## 문제 1 나무 재테크

### 문제

[https://www.acmicpc.net/problem/16235](https://www.acmicpc.net/problem/16235)

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
    val (n, treeN, k) = readInts()
    val feedAmount = Array(n + 1) { IntArray(n + 1) }
    val board = Array (n + 1) { Array (n + 1) { Tree() } }
    val dy = arrayOf(-1, -1, -1, 0, 0, 1, 1, 1)
    val dx = arrayOf(-1, 0, 1, -1, 1, -1, 0, 1)

    fun initFeedAmount() {
       for (y in 1..n) {
           val st = StringTokenizer(readLine(), " ")
           for (x in 1..st.countTokens()) {
               feedAmount[y][x] = st.nextToken().toInt()
           }
       }
    }
    initFeedAmount()

    fun initTree() {
        repeat(treeN) {
            val st = StringTokenizer(readLine(), " ")
            board[st.nextToken().toInt()][st.nextToken().toInt()].children.add(st.nextToken().toInt())
        }
    }
    initTree()

    fun spring() {
        for (y in 1..n) {
            for (x in 1..n) {
                board[y][x].spring()
            }
        }
    }

    fun autumn() {
        for (y in 1..n) {
            for (x in 1..n) {
                val cnt = board[y][x].children.filter { it % 5 == 0 }.count()
                if (cnt > 0) {
                    for (i in 0 until 8) {
                        val yPos = y + dy[i]
                        val xPos = x + dx[i]

                        if (yPos < 1 || yPos > n || xPos < 1 || xPos > n) continue
                        repeat(cnt) {
                            board[yPos][xPos].children.add(1)
                        }
                    }
                }
            }
        }
    }

    fun winter() {
        for (y in 1..n) {
            for (x in 1..n) {
                board[y][x].food += feedAmount[y][x]
            }
        }
    }

    for (i in 0 until k) {
        spring()
        autumn()
        winter()
    }

    var answer = 0
    board.forEach { trees ->
        trees.forEach { answer += it.children.size }
    }
    println(answer)
}

private class Tree {
    var food = 5
    var children = mutableListOf<Int>()
    var deadTree = mutableListOf<Int>()

    fun spring() {
        var newChildren = mutableListOf<Int>()

        for (t in children.sorted()) {
            if (food < t) deadTree.add(t)
            else {
                food -= t
                newChildren.add(t + 1)
            }
        }
        children = newChildren
        summer()
    }

    fun summer() {
        for (i in deadTree.indices) {
            food += deadTree[i] / 2
        }
        deadTree.clear()
    }
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

</br>

## 문제 2 아기 상어

### 문제

[https://www.acmicpc.net/problem/16236](https://www.acmicpc.net/problem/16236)

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
    val n = readInt()
    val board = Array(n) { IntArray(n) }
    var current = IntArray(2)
    val dy = arrayOf(-1, 1, 0, 0)
    val dx = arrayOf(0, 0, -1, 1)

    // 아기 상어
    var sharkSize = 2
    var sharkEatCounter = 0
    var time = 0

    fun initBoard() {
        for (y in 0 until n) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until st.countTokens()) {
                board[y][x] = st.nextToken().toInt()
                if (board[y][x] == 9) {
                    current[0] = y
                    current[1] = x
                    board[y][x] = 0
                }
            }
        }
    }
    initBoard()

    fun BFS(): MutableList<Triple<Int, Int, Int>> {
        val result = mutableListOf<Triple<Int, Int, Int>>()
        val queue = LinkedList<Pair<Int, Int>>()
        val visited = Array(n) { BooleanArray(n) }
        var dist = 1

        queue.add(Pair(current[0], current[1]))
        visited[current[0]][current[1]] = true

        while (queue.isNotEmpty()) {
            if (result.isNotEmpty()) break

            repeat(queue.size) {
                val (y, x) = queue.poll()

                for (i in 0 until 4) {
                    val yPos = y + dy[i]
                    val xPos = x + dx[i]

                    if (yPos < 0 || yPos >= n || xPos < 0 || xPos >= n) continue
                    if (visited[yPos][xPos] || board[yPos][xPos] > sharkSize) continue

                    queue.add(Pair(yPos, xPos))
                    visited[yPos][xPos] = true
                    if (board[yPos][xPos] in 1 until sharkSize) result.add(Triple(yPos, xPos, dist))
                }
            }
            dist++
        }
        return result
    }

    while (true) {
        val fishesCanEat = BFS()

        if (fishesCanEat.isEmpty()) break

        fun eatFish() {
            if (fishesCanEat.size > 1) fishesCanEat.sortWith(compareBy( { it.first }, { it.second } ))

            time += fishesCanEat[0].third
            current[0] = fishesCanEat[0].first
            current[1] = fishesCanEat[0].second
            board[current[0]][current[1]] = 0
            sharkEatCounter++

            if (sharkSize == sharkEatCounter) {
                sharkSize++
                sharkEatCounter = 0
            }
        }
        eatFish()
    }
    println(time)
}
```

### 문제 해설

문제의 요구대로 정확히 구현하면 해결할 수 있는 문제이다.

매번 BFS를 수행하면 가장 가까운 물고기를 찾을 수 있다.
