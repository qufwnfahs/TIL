# 200814_Greedy Practice_#1

## BOJ 2457번 공주님의 정원

### 1. 개요

[https://www.acmicpc.net/problem/2457](https://www.acmicpc.net/problem/2457)

### 2. 코드

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
    val flowers = mutableListOf<Pair<Int, Int>>()

    for(f in 0 until n) {
        val st = StringTokenizer(readLine(), " ")
        val sMonth = st.nextToken().toInt()
        val sDay = st.nextToken().toInt()
        var eMonth = st.nextToken().toInt()
        var eDay = st.nextToken().toInt()

        flowers.add(Pair(sMonth * 100 + sDay, eMonth * 100 + eDay))
    }
    flowers.sortWith(Comparator<Pair<Int, Int>> { p0, p1 -> p0.first - p1.first })

    var idx = 0
    var end = 301
    var tmpEnd = 0
    var ans = 0

    while (end < 1131 && idx < flowers.size) {
        var isFind = false

        for (i in idx until flowers.size) {
            if (flowers[i].first > end) {
                idx++
                break
            }

            if (tmpEnd < flowers[i].second) {
                tmpEnd = flowers[i].second
                isFind = true
                idx = i
            }
        }

        if (isFind) {
            end = tmpEnd
            ans++
        }
        else break
    }

    print(if (end > 1130) ans else 0)
}
```

### 3. 풀이 과정

- 꽃이 피는 날짜로 **오름차순 정렬**한 뒤, `'현재 피어 있는 꽃이 지는 날보다 먼저 피는 꽃 중 가장 지는 날이 늦은 날을 고른다'`

    → `curr.endTime > flower.startTime` 중 `flower.endTime`이 가장 큰 값

### 4. 회고

- 처음에는 3월 1일부터 11월 30일 사이에 가장 넓은 범위를 가지는 Element를 고르고, 나머지 범위를 채우는 방식을 생각했었다.

- 그러나, 중간에 이어지지 않는 Element를 고를 시 여러 측면에서 비교해야 됬고, 작성해야 할 코드의 양이 계속 길어졌다.

- 일단, 날짜 계산에서 코드의 양이 커졌다. 위와 같이 Int 형으로 month * 100 + day 값으로 계산하니 매우 편했다.

</br>

## BOJ 2012번 등수매기기

### 1. 개요

[https://www.acmicpc.net/problem/2012](https://www.acmicpc.net/problem/2012)

### 2. 코드

```kotlin
import java.lang.StringBuilder

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val n = readInt()
    val arr = IntArray(n)
    var ans = 0L

    for (i in arr.indices) {
        arr[i] = readInt()
    }

    arr.sort()

    for (i in arr.indices) {
        if (arr[i] == i + 1) continue

        ans += Math.abs(arr[i] - (i + 1))
    }

    print(ans)
}
```

### 3. 풀이 과정

- 1부터 N까지 연속된 수열의 꼴이 되므로 오름차순 정렬한 뒤, 오름차순 순열과 비교한 절대값을 더한다.

### 4. 회고

- 500,000 크기의 순열이 되므로 각 비교한 값이 Int 형을 넘게 된다. Long 형으로 선언해주어야 한다.

</br>

## BOJ 1744번 수 묶기

### 1. 개요

[https://www.acmicpc.net/problem/1744](https://www.acmicpc.net/problem/1744)

### 2. 코드

```kotlin
import java.lang.StringBuilder

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    // 내림차순 정렬 후
    // 두 숫자씩 봐서 2보다 작으면 break
    val n = readInt()
    val arr = IntArray(n)
    var ans = 0L

    for (i in arr.indices)
        arr[i] = readInt()

    arr.sort()

    var front = 0
    var end = arr.size - 1

    while (front < arr.size - 1) {
        if (front + 1 >= arr.size) break
        if (arr[front] > 0 || arr[front + 1] > 0) break

        ans += arr[front] * arr[front + 1]
        front += 2
    }

    while (end > 0) {
        if (end - 1 < 0) break
        if (arr[end] < 2 || arr[end - 1] < 2) break

        ans += arr[end] * arr[end - 1]
        end -= 2
    }

    for (i in front..end)
        ans += arr[i]

    print(ans)
}
```

### 3. 풀이 과정

- 1보다 큰 양수를 곱하면 최대가 보장되고, 1보다 작은 수를 곱해도 최대가 보장된다.

- 짝을 지어주고 남은 수들은 그냥 더 해준다.

### 4. 회고

- 1보다 작은 수를 곱해도 최대가 보장되는 점을 간과했다.

- 그리고 0의 수를 따로 세어줬었는데, 애초에 0도 곱해버리면 되므로 무의미했다.
