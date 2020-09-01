# 200901_BinarySearch

## 1. 순차 탐색

**순차 탐색(Sequential Search)** 이란 `리스트 안에 있는 특정한 데이터를 찾기 위해 앞에서부터 데이터를 하나씩 차례대로 확인하는 방법`이다.

보통 정렬되지 않은 리스트에서 데이터를 찾아야 할 때 사용한다.

데이터의 개수가 N개일 때 최대 N번의 비교 연산이 필요하므로 순차 탐색의 최악의 경우 시간 복잡도는 **O(N)** 이다.

</br>

## 2. 이진 탐색 : 반으로 쪼개면서 탐색하기

**이진 탐색(Binary Search)** 은 배열 내부의 데이터가 `정렬`되어 있어야만 사용할 수 있는 알고리즘이다.

**이진 탐색**은 위치를 나타내는 변수 3개를 사용하는데 탐색하고자 하는 범위의 **시작점**, **끝점**, 그리고 **중간점**이다.

`찾으려는 데이터의 중간점(Middle) 위치에 있는 데이터를 반복적으로 비교`해서 원하는 데이터를 찾는 게 이진 탐색 과정이다.

**이진 탐색**은 한 번 확인할 때마다 확인하는 원소의 개수가 절반씩 줄어든다는 점에서 시간 복잡도가 **O(logN)** 이다. 

```kotlin
/** 이진 탐색 **/
private fun binarySearch(arr: IntArray, target: Int, start: Int, end: Int): Int {
    if (start > end) return -1

    val mid = (start + end) / 2
    return when {
        target < arr[mid] -> binarySearch(arr, target, start, mid - 1)
        arr[mid] < target -> binarySearch(arr, target, mid + 1, end)
        else -> mid
    }
}
```

</br>

다음은 데이터가 존재한다면 **가장 첫 번째 위치**와 **마지막 위치**를 찾는 **이진 탐색 메서드**이다.

이러한 방법은 이진 탐색을 요구하는 고난이도 문제에서 자주 사용할 수 있는 테크닉이다.

```kotlin
private fun searchFirst(start: Int, end: Int, target: Int, arr: IntArray): Int {
    if (start > end) return -1

    val mid = (start + end) / 2

    if ( (mid == 0 || arr[mid - 1] < target) && arr[mid] == target) return mid

    return if (arr[mid] >= target) searchFirst(start, mid - 1, target, arr)
    else searchFirst(mid + 1, end, target, arr)
}

private fun searchLast(start: Int, end: Int, target: Int, arr: IntArray): Int {
    if (start > end) return -1

    val mid = (start + end) / 2

    if ( (mid == arr.size - 1 || arr[mid + 1] > target) && arr[mid] == target) return mid

    return if (arr[mid] <= target) searchLast(mid + 1, end, target, arr)
    else searchLast(start, mid - 1, target, arr)
}
```

</br>

## 3. 이진 탐색 트리

**왼쪽 자식 노드 < 부모 노드 < 오른쪽 자식 노드**가 성립하는 트리 구조이다.

</br>

## 4. 파라메트릭 서치

**파라메트릭 서치(Parametric Search)** 는 `최적화 문제(문제의 상황을 만족하는 특정 변수의 최솟값, 최댓값을 구하는 문제)`를 `결정 문제`로 바꾸어 푸는 것이다.

</br>

예를 들어 보자.

```kotlin
val person = arrayOf(A, B, C, D, E, F, G)
```

다음과 같이 나이순으로 정렬된 사람들이 있다. 그리고 25살 이상이면 소주를 좋아한다는 것이 증명되어 있다고 한다.

그럼 이 중에서 소주를 좋아하는 나이가 가장 어린 사람은 누구일까?

물론 가장 간단한 방법은 앞에서부터 확인하면서 25살 이상인 사람을 찾는 것이다.

하지만, **파라메트릭 서치**를 이용해 `최적화 문제`를 `결정 문제`로 바꾼다면 시간 복잡도 **O(logN)**으로 문제를 풀어낼 수 있다.

- 최적화 문제 : 소주를 좋아하는 나이가 가장 어린 사람 (배열의 원소 중 25살 이상인 사람이며 그 중의 최솟값)
- 결정 문제 : `if (나이 ≥ 25) true else fase`

</br>

'`원하는 조건을 만족하는 가장 알맞은 값을 찾는 문제`'에 주로 **파라메트릭 서치**를 사용한다.

예를 들어 범위 내에서 조건을 만족하는 가장 큰 값을 찾으라는 최적화 문제라면 **이진 탐색**으로 `결정 문제`를 해결하면서 범위를 좁혀갈 수 있다.

</br>

### 문제 - 떡볶이 떡 만들기

19cm, 15cm, 10cm, 17cm인 떡이 나란히 있고 적어도 M만큼의 떡을 얻기 위해 절단기에 설정할 수 있는 높이의 최댓값을 구하시오.

절단기의 높이가 **15cm**라면, 잘린 떡의 길이는 차례대로 **4, 0, 0, 2cm**이다. 손님은 **6cm**만큼의 길이를 가져간다.

### 문제 해설

이 문제의 풀이 아이디어는 적절한 높이를 찾을 때까지 절단기의 높이 H를 반복해서 조정하는 것이다.

그래서 '**현재 이 높이로 자르면 조건을 만족할 수 있는가?**'를 확인한 뒤에 조건의 **만족 여부('예' 혹은 '아니오')**에 따라서 탐색 범위를 좁혀서 해결할 수 있다.

범위를 좁힐 때는 **이진 탐색**의 원리를 이용하여 **절반씩** 탐색 범위를 좁혀 나간다.

절단기의 높이 H는 **0부터 가장 긴 떡의 길이 안**에 있어야만 떡을 자를 수 있다. `(0부터 최댓값까지의 범위를 이진 탐색)`

```kotlin
fun main() {
    val (N, M) = intArrayOf(4, 6)
    val riceCakes = intArrayOf(19, 15, 10, 17)

    riceCakes.sort()

    fun solution (start: Int, end: Int): Int {
        if (start > end) return -1

        val mid = (start + end) / 2

        var sum = 0
        val filtered = riceCakes.filter { it > mid }

        filtered.forEach { sum += it - mid }
        return when {
            sum > M -> solution(mid + 1, end)
            M > sum -> solution(start, mid - 1)
            else -> mid
        }
    }

    println(solution(0, riceCakes.max()!!))
}
```
</br>

## 문제 구성

- [1. 정렬된 배열에서 특정 수의 개수 구하기](#기출문제-1-정렬된-배열에서-특정-수의-개수-구하기)
- [2. 고정점 찾기](#기출문제-2-고정점-찾기)
- [3. 공유기 설치](#기출문제-3-공유기-설치)
- [4. 가사 검색](#기출문제-4-가사-검색)

</br>

## 기출문제 1 정렬된 배열에서 특정 수의 개수 구하기

### 문제

N개의 원소를 포함하고 있는 수열이 오름차순으로 정렬되어 있다. 이떄 이 수열에서 x가 등장하는 횟수를 계산하세요.

예를 들어 수열 {1, 1, 2, 2, 2, 2, 3}이 있을 때 x = 2라면, 현재 수열에서 값이 2인 원소가 4개이므로 4를 출력합니다.

단, 이 문제는 시간 복잡도 **O(logN)** 으로 알고리즘을 설계하지 않으면 '**시간 초과**' 판정을 받습니다.

### 코드

```kotlin
fun main() {
    val (n, target) = intArrayOf(7, 2)
    val arr = intArrayOf(1, 1, 2, 2, 2, 2, 3)
    var answer = 0

    fun searchFirst(start: Int, end: Int): Int {
        if (start > end) return -1

        val mid = (start + end) / 2

        if ( (mid == 0 || arr[mid - 1] < target) && arr[mid] == target) return mid

        return if (arr[mid] >= target) searchFirst(start, mid - 1)
        else searchFirst(mid + 1, end)
    }

    fun searchLast(start: Int, end: Int): Int {
        if (start > end) return -1

        val mid = (start + end) / 2

        if ( (mid == arr.size - 1 || arr[mid + 1] > target) && arr[mid] == target) return mid

        return if (arr[mid] <= target) searchLast(mid + 1, end)
        else searchLast(start, mid - 1)
    }

    val first = searchFirst(0, arr.size - 1)
    val last = searchLast(0, arr.size - 1)
    println(if (first == -1 || last == -1) -1 else last - first + 1)
}
```

### 문제 해설

찾는 데이터가 등장하는 **첫 번째 위치**를 구하는 이진 탐색과 **마지막 위치**를 구하는 이진 탐색을 수행하여 시간 복잡도 **O(logN)** 으로 해결

</br>

## 기출문제 2 고정점 찾기

### 문제

**고정점(Fixed Point)** 이란, 수열의 원소 중에서 `그 값이 인덱스와 동일한 원소`를 의미한다. 예를 들어 수열 a = { -15, -4, 2, 8, 13 }이 있을 때, a[2] = 2이므로 고정점은 2가 된다.

고정점을 출력하는 프로그램을 작성하세요. 만약 고정점이 없다면 -1을 출력합니다.

단, 이 문제는 시간 복잡도 **O(logN)** 으로 알고리즘을 설계하지 않으면 '**시간 초과**' 판정을 받습니다.

### 코드

```kotlin
fun main() {
//    val n = 5
//    val arr = intArrayOf(-15, -6, 1, 3, 7)
    val n = 7
    val arr = intArrayOf(-15, -4, 3, 8, 9, 13, 15)

    fun solution(start: Int, end: Int): Int {
        if (start > end) return -1

        val mid = (start + end) / 2

        return when {
            mid > arr[mid] -> solution(mid + 1, end)
            mid < arr[mid] -> solution(start, mid - 1)

            else -> mid
        }
    }

    println(solution(0, n - 1))
}
```

### 문제 해설

중간점이 가리키는 위치의 값보다 중간점이 작은 경우에는 왼쪽 부분을 탐색하고, 중간점이 가리키는 위치의 값보다 중간점이 큰 경우에는 오른쪽 부분을 탐색하는 것을 반복한다.

</br>

## 기출문제 3 공유기 설치

### 문제

[https://www.acmicpc.net/problem/2110](https://www.acmicpc.net/problem/2110)

### 코드

```kotlin
fun main() {
    val (n, c) = readInts()
    val arr = IntArray(n) { readLine().toInt() }
    var answer = 0

    arr.sort()

    fun solution(start: Int, end: Int) {
        if (start > end) return

        val mid = (start + end) / 2
        var curr = arr[0]
        var cnt = 1

        for (i in 1 until n) {
            if (arr[i] >= curr + mid) {
                curr = arr[i]
                cnt++
            }
        }
        if (cnt >= c) {
            answer = mid
            solution(mid + 1, end)
        }
        else solution(start, mid - 1)
    }

    solution(arr[1] - arr[0], arr[n-1] - arr[0])
    print(answer)
}
```

### 문제 해설

**이진 탐색**으로 '`가장 인접한 두 공유기 사이의 거리`'를 조절해가며, 매 순간 실제로 공유기를 설치하여 C보다 많은 개수로 공유기를 설치할 수 있는지 체크하여 문제를 해결할 수 있다.

다만, '`가장 인접한 두 공유기 사이의 거리`'의 **최댓값**을 찾아야 하므로, C보다 많은 개수로 공유기를 설치할 수 있다면 '`가장 인접한 두 공유기 사이의 거리`'의 **값을 증가**시켜서, 더 큰 값에 대해서도 성립하는지 체크하기 위해 다시 탐색을 수행한다.

</br>

즉, 가능한 **최소 거리**와 **최대 거리**의 범위 안에서 **이진 탐색**을 수행하여 해결하는 **파라메트릭 서치** 유형의 문제로 이해할 수 있다.

</br>

## 기출문제 4 가사 검색

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/60060](https://programmers.co.kr/learn/courses/30/lessons/60060)

### 코드

```kotlin
fun main() {
    val words = arrayOf("frodo", "front", "frost", "frozen", "frame", "kakao")
    val queries = arrayOf("fro??", "????o", "fr???", "fro???", "pro?")

    fun solution(words: Array<String>, queries: Array<String>): IntArray {
        val result = IntArray(queries.size)

        val wordList = Array(10001) { mutableListOf<String>() } // 1부터 10_000까지의 길이별 리스트
        val reversedList = Array(10001) { mutableListOf<String>() }

        words.forEach {
            wordList[it.length].add(it)
            reversedList[it.length].add(it.reversed())
        }

        wordList.filter { it.isNotEmpty() }
                .forEach { it.sort() }
        reversedList.filter { it.isNotEmpty() }
                    .forEach { it.sort() }

        for ((index, query) in queries.withIndex()) {
            val head = query[0] != '?'
            val length = query.length

            val start = if (head) searchFirst(0, wordList[length].size - 1, query, query.replace('?', 'a'), wordList[length])
                             else searchFirst(0, reversedList[length].size - 1, query.reversed(), query.reversed().replace('?', 'a'), reversedList[length])
            val end = if (head) searchLast(0, wordList[length].size - 1, query, query.replace('?', 'z'), wordList[length])
                           else searchLast(0, reversedList[length].size - 1, query.reversed(), query.reversed().replace('?', 'z'), reversedList[length])

            result[index] = if (start == -1 || end == -1) 0
                            else end - start + 1
        }

        return result
    }
    println(solution(words, queries).joinToString(" "))
}

private fun searchFirst(start: Int, end: Int, query: String, target: String, arr: MutableList<String>): Int {
    if (start > end) return -1

    val mid = (start + end) / 2

    if ( (mid == 0 || (arr[mid - 1] < target && !isMatch(arr[mid - 1], query)) ) && isMatch(arr[mid], query)) return mid

    return if (arr[mid] >= target) searchFirst(start, mid - 1, query, target, arr)
    else searchFirst(mid + 1, end, query, target, arr)
}

private fun searchLast(start: Int, end: Int, query: String, target: String, arr: MutableList<String>): Int {
    if (start > end) return -1

    val mid = (start + end) / 2

    if ( (mid == arr.size - 1 || (arr[mid + 1] > target && !isMatch(arr[mid + 1], query)) ) && isMatch(arr[mid], query)) return mid

    return if (arr[mid] <= target) searchLast(mid + 1, end, query, target, arr)
    else searchLast(start, mid - 1, query, target, arr)
}

private fun isMatch(word: String, query: String): Boolean {
    for (i in word.indices) {
        if (query[i] == '?') continue
        if (word[i] != query[i]) return false
    }
    return true
}
```

### 문제 해설

먼저 각 단어를 길이에 따라서 나눈다. 이후에 모든 리스트를 정렬한 뒤에, 각 쿼리에 대해서 **이진 탐색**을 수행하여 문제를 해결할 수 있다.

**"fro??"** 라는 쿼리가 들어왔다고 가정하면, **이진 탐색**을 이용해서 "**fro"** 로 시작되는 **첫 번째 단어의 위치**와 **마지막 단어의 위치**를 찾아 그 차이를 계산하면 된다.

</br>

이진 탐색을 수행할 때 **"fro??"** 쿼리를 **Raw**한 상태로 비교하면 **?**의 값 때문에 항상 다른 단어보다 큰 값으로 인식되기 때문에, **첫 번째 단어의 위치**를 찾을 때는 '**?**'를 '**a**'로 치환하고, **마지막 단어**를 찾을 때는 '**z**'로 치환하여 대소 비교를 올바르게 할 수 있도록 해줘야 한다.

또한, 일치 비교를 해주기 위해 `isMatch` 함수를 작성하였다.
