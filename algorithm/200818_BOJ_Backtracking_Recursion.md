# 백준 문제풀이 (BackTracking, Recursion)

## BOJ 1799번 비숍

## 1. 개요

[https://www.acmicpc.net/problem/1799](https://www.acmicpc.net/problem/1799)

## 2. 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.max

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

private var ans1 = 0
private var ans2 = 0

fun main() {
    val n = readInt()
    val arr = Array(n) { IntArray(n) }
    val color = Array(n) { IntArray(n) }
    val cLeft = BooleanArray(2 * arr.size - 1)
    val cRight = BooleanArray(2 * arr.size - 1)

    // arr 정보 저장
    for (y in 0 until n) {
        val st = StringTokenizer(readLine(), " ")
        for (x in 0 until n) {
            arr[y][x] = st.nextToken().toInt()
            color[y][x] = if (y % 2 != x % 2) 0 else 1
        }
    }
    solve(arr, color, cLeft, cRight, 0, -1, 0, 0)
    solve(arr, color, cLeft, cRight, 0, -1, 0, 1)

    print(ans1 + ans2)
}

private fun solve(arr: Array<IntArray>, color: Array<IntArray>, cLeft: BooleanArray, cRight: BooleanArray, startY: Int, startX: Int, cnt: Int, colorValue: Int) {
    if (colorValue == 0) ans1 = max(ans1, cnt)
    else ans2 = max(ans2, cnt)

    for (y in startY until arr.size) {
        for (x in arr[0].indices) {
            if (y == startY && x < startX) continue
            if (arr[y][x] == 0 || colorValue == color[y][x] || cLeft[y + x] || cRight[y - x + arr.size - 1]) continue

            cLeft[y + x] = true
            cRight[y -  x + arr.size - 1] = true
            solve(arr, color, cLeft, cRight, y, x, cnt + 1, colorValue)
            cLeft[y + x] = false
            cRight[y -  x + arr.size - 1] = false
        }
    }
}
```

## 3. 풀이 과정

- 단순하게 생각하면 체스판의 각 칸의 모든 경우를 DFS 수행하면 된다.

- 그 과정 속에서 놓여진 비숍들에 의한 대각선을 확인하며 백트래킹한다.

</br>

- 그러나, 이 문제에서 단순한 풀이는 시간 초과에 도달한다. (`2 * N * N`의 시간 복잡도)

- **체스판의 영역을 흑과 백으로 나누어서 두 영역을 따로 탐색하는 방법**이 **핵심**이다.

    → 흑과 백 칸은 서로의 문제에 영향을 주지 않는다. (대각선에만 영향을 주기 때문에)

### 3-1 해결 과정

1. 먼저, 배열의 정보를 입력받고 흑과 백의 영역을 나누어 준다.

```kotlin
// arr 정보 저장
for (y in 0 until n) {
    val st = StringTokenizer(readLine(), " ")
    for (x in 0 until n) {
        arr[y][x] = st.nextToken().toInt()
        color[y][x] = if (y % 2 != x % 2) 0 else 1
    }
}
```

 2. 흑 영역 DFS, 백 영역 DFS 수행

```kotlin
solve(arr, color, cLeft, cRight, 0, -1, 0, 0)
solve(arr, color, cLeft, cRight, 0, -1, 0, 1)
```

3. 비숍의 위치를 추가할 때마다 왼쪽 대각선, 오른쪽 대각선 정보를 갱신하며 탐색을 수행

```kotlin
private fun solve(arr: Array<IntArray>, color: Array<IntArray>, cLeft: BooleanArray, cRight: BooleanArray, startY: Int, startX: Int, cnt: Int, colorValue: Int) {
    if (colorValue == 0) ans1 = max(ans1, cnt)
    else ans2 = max(ans2, cnt)

    for (y in startY until arr.size) {
        for (x in arr[0].indices) {
            if (y == startY && x < startX) continue
            if (arr[y][x] == 0 || colorValue == color[y][x] || cLeft[y + x] || cRight[y - x + arr.size - 1]) continue

            cLeft[y + x] = true
            cRight[y -  x + arr.size - 1] = true
            solve(arr, color, cLeft, cRight, y, x, cnt + 1, colorValue)
            cLeft[y + x] = false
            cRight[y -  x + arr.size - 1] = false
        }
    }
}
```

## 4. 결과

</br>

## BOJ 1182번 부분수열의 합

## 1. 개요

[https://www.acmicpc.net/problem/1182](https://www.acmicpc.net/problem/1182)

## 2. 코드

```kotlin
import java.lang.StringBuilder

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (n, sum) = readInts()
    val arr = readInts().toIntArray()
    val visited = BooleanArray(n)
    var ans = 0

    fun solve(idx: Int, depth: Int, end: Int, total: Int) {
        if (depth == end) {
            if (total == sum) ans++
            return
        }

        for (i in idx until arr.size) {
            if (!visited[i]) {
                visited[i] = true
                solve(i + 1, depth + 1, end, total + arr[i])
                visited[i] = false
            }
        }
    }

    for (i in 1..n)
        solve(0, 0, i, 0)

    print(ans)
}
```

## 3. 풀이 과정

- 모든 부분수열을 구한 뒤 합을 비교한다.

### 3-1 해결 과정

1. 1 부터 n 길이의 부분 수열의 합을 비교

```kotlin
for (i in 1..n)
    solve(0, 0, i, 0)
```

 2. end 길이의 부분 수열을 구한다.

- 부분 수열이 완성됬을 때 `(depth == end)` 합을 비교

```kotlin
fun solve(idx: Int, depth: Int, end: Int, total: Int) {
        if (depth == end) {
            if (total == sum) ans++
            return
        }

        for (i in idx until arr.size) {
            if (!visited[i]) {
                visited[i] = true
                solve(i + 1, depth + 1, end, total + arr[i])
                visited[i] = false
            }
        }
    }
```

## 4. 결과

</br>

## BOJ 2447번 별 찍기 - 10

## 1. 개요

[https://www.acmicpc.net/problem/2447](https://www.acmicpc.net/problem/2447)

## 2. 코드

```kotlin
import java.lang.StringBuilder

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val n = readInt()
    val star = Array (n / 3) { CharArray (n / 3) { ' ' } }
    val painting = Array(n) { CharArray(n) { ' ' } }

    getStar(0, 0, star, n / 3)
    drawStar(star, painting)

    painting.forEach { println(it.joinToString("")) }
}

private fun getStar(y: Int, x: Int, star: Array<CharArray>, n: Int) {
    if (n == 1) {
        star[y][x] = '*'
        return
    }

    var nextN = n / 3

    getStar(y, x, star, nextN)
    getStar(y, x + nextN, star, nextN)
    getStar(y, x + nextN * 2, star, nextN)

    getStar(y + nextN, x, star, nextN)
    getStar(y + nextN, x + nextN * 2, star, nextN)

    getStar(y + nextN * 2, x, star, nextN)
    getStar(y + nextN * 2, x + nextN, star, nextN)
    getStar(y + nextN * 2, x + nextN * 2, star, nextN)
}

private fun drawStar(star: Array<CharArray>, painting: Array<CharArray>) {
    val nextN = painting.size / 3

    drawStar(0, 0, star, painting)
    drawStar(0, nextN, star, painting)
    drawStar(0, nextN * 2, star, painting)

    drawStar(nextN, 0, star, painting)
    drawStar(nextN, nextN * 2, star, painting)

    drawStar(nextN * 2, 0, star, painting)
    drawStar(nextN * 2, nextN, star, painting)
    drawStar(nextN * 2, nextN * 2, star, painting)
}

private fun drawStar(y: Int, x: Int, star: Array<CharArray>, painting: Array<CharArray>) {
    for (yPos in star.indices) {
        for (xPos in star.indices) {
            painting[y + yPos][x + xPos] = star[yPos][xPos]
        }
    }
}
```

## 3. 풀이 과정

- 반복되는 가장 큰 사각형을 구한 뒤, 가장 큰 사각형을 각 위치에 그려준다.

### 3-1 해결 과정

1. 먼저, 반복되는 가장 큰 사각형을 `배열 star` 에 저장한다.

```kotlin
val star = Array (n / 3) { CharArray (n / 3) { ' ' } }
getStar(0, 0, star, n / 3)

private fun getStar(y: Int, x: Int, star: Array<CharArray>, n: Int) {
    if (n == 1) {
        star[y][x] = '*'
        return
    }

    var nextN = n / 3

    getStar(y, x, star, nextN)
    getStar(y, x + nextN, star, nextN)
    getStar(y, x + nextN * 2, star, nextN)

    getStar(y + nextN, x, star, nextN)
    getStar(y + nextN, x + nextN * 2, star, nextN)

    getStar(y + nextN * 2, x, star, nextN)
    getStar(y + nextN * 2, x + nextN, star, nextN)
    getStar(y + nextN * 2, x + nextN * 2, star, nextN)
}
```

 2. 가장 큰 사각형을 각 위치에 그려준다.

```kotlin
private fun drawStar(star: Array<CharArray>, painting: Array<CharArray>) {
    val nextN = painting.size / 3

    drawStar(0, 0, star, painting)
    drawStar(0, nextN, star, painting)
    drawStar(0, nextN * 2, star, painting)

    drawStar(nextN, 0, star, painting)
    drawStar(nextN, nextN * 2, star, painting)

    drawStar(nextN * 2, 0, star, painting)
    drawStar(nextN * 2, nextN, star, painting)
    drawStar(nextN * 2, nextN * 2, star, painting)
}

private fun drawStar(y: Int, x: Int, star: Array<CharArray>, painting: Array<CharArray>) {
    for (yPos in star.indices) {
        for (xPos in star.indices) {
            painting[y + yPos][x + xPos] = star[yPos][xPos]
        }
    }
}
```

## 4. 결과

</br>

## BOJ 1629번 곱셈

## 1. 개요

[https://www.acmicpc.net/problem/1629](https://www.acmicpc.net/problem/1629)

## 2. 코드

```kotlin
import java.lang.StringBuilder

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }
private fun readLongs() = br.readLine().split(" ").map { it.toLong() }

fun main() {
    val (A, B, C) = readLongs()

    print(multiple(A, B, C))
}

fun multiple(A: Long, B: Long, C: Long): Long {
    if (B == 1L) return A % C
    var result = multiple(A, B/2, C)
    result = result * result % C

    return if (B % 2 == 0L) result else result * A % C
}
```

## 3. 풀이 과정

- 시간 초과를 회피하기 위해 10^10 = 10^5 * 10^5인 성질을 이용

### 3-1 해결 과정

1. 각 곱셈을 계속 이등분하여 계산

```kotlin
var result = multiple(A, B/2, C)
```

 2. 곱해야 하는 수가 짝수인 경우는 result, 홀수인 경우는 result에 A를 곱한 수 반환

```kotlin
return if (B % 2 == 0L) result else result * A % C
```

## 4. 결과
