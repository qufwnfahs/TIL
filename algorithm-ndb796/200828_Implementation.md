
# 200828_Implementation_(구현)

**구현(Implementation)** 이란 `'머릿 속에 있는 알고리즘을 소스 코드로 바꾸는 과정'`이다.

이 책에서는 완전 탐색, 시뮬레이션 유형을 모두 '구현' 유형으로 묶어서 다루고 있다.

</br>

**완전 탐색**은 `모든 경우의 수를 주저 없이 다 계산하는 해결 방법`을 의미하고, 

**시뮬레이션**은 `문제에서 제시한 알고리즘을 한 단계씩 차례대로 직접 수행`해야 하는 문제 유형을 의미한다. 

</br>

## 1. 문제 구성

- [1. 럭키 스트레이트](#기출문제-1-럭키-스트레이트)
- [2. 문자열 재정렬](#기출문제-2-문자열-재정렬)
- [3. 문자열 압축](#기출문제-3-문자열-압축)
- [4. 자물쇠와 열쇠](#기출문제-4-자물쇠와-열쇠)
- [5. 뱀](#기출문제-5-뱀)
- [6. 기둥과 보 설치](#기출문제-6-기둥과-보-설치)
- [7. 치킨 배달](#기출문제-7-치킨-배달)
- [8. 외벽 점검](#기출문제-8-외벽-점검)

</br>

## 기출문제 1 럭키 스트레이트

### 문제

[https://www.acmicpc.net/problem/18406](https://www.acmicpc.net/problem/18406)

### 코드

```kotlin
fun main() {
    val n = readLine()
    val length = n.length / 2
    var left = 0
    var right = 0

    for (i in 0 until length) {
        left += n[i] - '0'
    }
    for (i in length until n.length) {
        right += n[i] - '0'
    }

    print(if (left == right) "LUCKY" else "READY")
}
```

### 문제 해설

요구하는 바를 그대로 구현하면 해결할 수 있다.

읽어들인 문자열을 절반으로 나누어 각 부분의 자릿수의 합을 비교한다.

</br>

## 기출문제 2 문자열 재정렬

### 문제

문자열을 읽고, 모든 알파벳을 오름차순으로 정렬하여 이어서 출력한 뒤에, 그 뒤에 모든 숫자를 더한 값을 이어서 출력한다.

예를 들어, K1KA5CB7이라는 값이 들어오면 ABCKK13을 출력한다.

### 코드

```kotlin
fun main() {
    val list = mutableListOf<Char>()
    var sum = 0

    readLine().forEach {
        if (it in 'A'..'Z') list.add(it)
        else sum += it - '0'
    }

    out.append(list.sorted().joinToString(""))
    out.append(sum)

    print(out)
}
```

### 문제 해설

요구하는 내용을 그대로 구현하면 되는 문제이다.

알파벳은 따로 MutableList에 저장하고 숫자는 sum 변수에 계속 더한다.

마지막에 MutableList를 정렬하여 출력한 뒤, sum을 그 뒤에 출력한다.

</br>

## 기출문제 3 문자열 압축

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/60057](https://programmers.co.kr/learn/courses/30/lessons/60057)

### 코드

```kotlin
fun solution(s: String): Int {
    var slice = 1
    var length = s.length

    while (slice < s.length) {
        var count = 1
        var compare = s.substring(0, slice)
        val sb = StringBuilder()

        for (i in slice until s.length step slice) {
            val curr = if (i + slice > s.length) s.substring(i, s.length) else s.substring(i, i + slice)

            // 이전과 같다면
            if (compare == curr) count++
            // 이전과 같지 않다면
            else {
                // 문자열 추가
                sb.append(if (count > 1) "$count$compare" else "$compare")
                // 카운트 초기화
                count = 1
                compare = curr
            }
        }
        sb.append(if (count > 1) "$count$compare" else "$compare")

        length = min(length, sb.length)
        slice++
    }

    return length
}
```

### 문제 해설

입력으로 주어지는 문자열의 길이가 1,000 이하이기 때문에 가능한 모든 경우의 수를 탐색하는 **완전 탐색**을 수행할 수 있다.

길이가 N인 문자열이 입력되었다면 `1부터 N / 2까지의 모든 수`를 단위로 하여 문자열을 압축하는 방법을 모두 확인하고, 가장 짧게 압축되는 길이를 출력하면 된다.

→ **1부터 N / 2까지 보는 이유**는 그 이상부터는 항상 길이가 달라 압축이 되지 않기 때문이다.

</br>

## 기출문제 4 자물쇠와 열쇠

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/60059](https://programmers.co.kr/learn/courses/30/lessons/60059)

### 코드

```kotlin
fun solution(key: Array<IntArray>, lock: Array<IntArray>): Boolean {
    val expandLock = Array(lock.size * 3) { IntArray(lock.size * 3) }

    fun rotate(arr: Array<IntArray>): Array<IntArray> {
        val result = Array(arr.size) { IntArray(arr[0].size) }

        for (y in arr.indices) {
            for (x in arr[0].indices) {
                result[x][arr.size - 1 - y] = arr[y][x]
            }
        }
        return result
    }

    fun unLock(): Boolean {
        var lockLength = expandLock.size / 3

        for (y in lock.indices) {
            for (x in lock[0].indices) {
                if (expandLock[y + lock.size][x + lock.size] == 0) return false
            }
        }
        return true
    }

    fun copy(key: Array<IntArray>): Array<IntArray> {
        val copyKey = Array(key.size) { IntArray(key[0].size) }
        for (i in copyKey.indices) {
            copyKey[i] = key[i].copyOf()
        }

        return copyKey
    }
	
    // 중앙으로 이동
    for (y in lock.indices) {
        for (x in lock[0].indices) {
            expandLock[y + lock.size][x + lock.size] = lock[y][x]
        }
    }

    var copyKey = copy(key)

    for (r in 0 until 4) {
        copyKey = rotate(copyKey)

        for (y in 0 until lock.size * 2) {
            for (x in 0 until lock.size * 2) {
                for (dy in copyKey.indices) {
                    for (dx in copyKey[0].indices) {
                        expandLock[y + dy][x + dx] += copyKey[dy][dx]
                    }
                }

                if (unLock()) return true

                for (dy in copyKey.indices) {
                    for (dx in copyKey[0].indices) {
                        expandLock[y + dy][x + dx] -= copyKey[dy][dx]
                    }
                }
            }
        }
    }

    return false
}
```

### 문제 해설

문제에서 제시한 자물쇠와 열쇠의  가장 큰 크기는 20 * 20 이다. 크기카 20 * 20인 2차원 리스트에 있는 모든 원소에 접근할 때는 400만큼의 연산이 필요할 것이다. 그렇기 때문에, **완전 탐색**을 이용해서 열쇠를 이동이나 회전시켜서 자물쇠에 끼워보는 작업을 전부 시도해보는 접근 방법을 이용할 수 있다.

다만, 완전 탐색을 수월하게 하기 위해서 자물쇠 리스트의 크기를 **3배 이상**으로 변경하면 계산이 수월해진다. 

열쇠의 크기는 항상 자물쇠 이하이기 때문에 IndexOutOfArrayException은 발생하지 않는다.

</br>

먼저, 자물쇠를 크기가 3배인 새로운 리스트로 만들어 중앙 부분으로 옮긴다.

이제 열쇠 배열을 왼쪽 위부터 시작해서 한 칸씩 이동하는 방식으로 차례대로 자물쇠의 모든 홈을 채울 수 있는지 확인하면 된다.

문제에서는 0은 홈 부분, 1은 돌기 부분을 나타낸다. 그러므로 Key 행렬을 **90도씩 4번 회전**하며 더한 뒤,

자물쇠 부분의 모든 값이 정확히 1이 되는 지를 확인하면 된다.

</br>

## 기출문제 5 뱀

### 문제

[https://www.acmicpc.net/problem/3190](https://www.acmicpc.net/problem/3190)

### 코드

```kotlin
fun main() {
    val n = readInt()
    val k = readInt()
    val board = Array(n + 2) { IntArray(n + 2) }
    val dy = arrayOf(0, 1, 0, -1)
    val dx = arrayOf(1, 0, -1, 0)

    // 사과 입력
    for (i in 0 until k) {
        val (row, col) = readInts()
        board[row][col] = 1
    }

    val moveCount = readInt()
    val moves = LinkedList<Pair<Int, Char>>()

    // 이동 입력
    for (i in 0 until moveCount) {
        val line = readLine().split(" ")
        moves.add(Pair(line[0].toInt(), line[1].single()))
    }

    var time = 0
    val snakeInfo = ArrayDeque<Pair<Int, Int>>()

    fun init() {
        snakeInfo.add(Pair(1, 1))
        board[1][1] = 2
    }

    fun solution(): Int {
        var direction = 0

        while (true) {
            val curr = snakeInfo.peekLast()
            if (curr.first <= 0 || curr.first > n || curr.second <= 0 || curr.second > n) return time

            if (moves.isNotEmpty() && time == moves.peek().first) {
                val move = moves.pop()

                when (move.second) {
                    'L' -> direction = if (direction - 1 < 0) 3 else direction - 1
                    'D' -> direction = (direction + 1) % 4
                }
            }

            val yPos = curr.first + dy[direction]
            val xPos = curr.second + dx[direction]

            if (board[yPos][xPos] == 2) return time + 1// 몸에 부딪힌 경우

            if (board[yPos][xPos] == 0) {
                val toRemove = snakeInfo.pollFirst()
                board[toRemove.first][toRemove.second] = 0
            }

            board[yPos][xPos] = 2
            snakeInfo.addLast(Pair(yPos, xPos))

            time++
        }
    }

    init()
    print(solution())
}
```

### 문제 해설

문제가 요구하는 대로 구현하면 해결할 수 있다.

게임은 뱀이 벽에 부딪히거나 자기 자신의 몸과 부딪히면 끝이 난다.

벽에 부딪히는 경우는 가장자리에 닿는 경우로 판단할 수 있고, 자기 자신의 몸과 부딪히는 경우는 **Queue**를 이용하여 뱀의 이동 경로를 저장하여 판단할 수 있다.

</br>

뱀의 현재 방향에 따라 왼쪽, 오른쪽 회전을 수행하는 방법을 dy, dx 배열과 direction 변수의 값을 1 빼거나 더하는 방식으로 구현하면 더 효율적이다.

</br>

## 기출문제 6 기둥과 보 설치

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/60061](https://programmers.co.kr/learn/courses/30/lessons/60061)

### 코드

```kotlin
fun solution(n: Int, build_frame: Array<IntArray>): Array<IntArray> {
    val answer = mutableListOf<IntArray>()

    fun checkPillar(y: Int, x: Int): Boolean {
        if (y == 0) return true

        for (i in answer.indices) {
            if ((answer[i][0] == x || answer[i][0] == x - 1) && answer[i][1] == y && answer[i][2] == 1) return true
            if (answer[i][0] == x && answer[i][1] == y - 1 && answer[i][2] == 0) return true
        }
        return false
    }

    fun checkFloor(y: Int, x: Int): Boolean {
        var left = false
        var right = false

        for (i in answer.indices) {
            if ( (answer[i][0] == x || answer[i][0] == x + 1) && answer[i][1] == y - 1 && answer[i][2] == 0) return true
            if (answer[i][0] == x - 1 && answer[i][1] == y && answer[i][2] == 1) left = true
            if (answer[i][0] == x + 1 && answer[i][1] == y && answer[i][2] == 1) right = true
        }

        if (left && right) return true
        return false
    }

    fun build(y: Int, x: Int, structure: Int) {
        when (structure) {
            0 -> {
                if (checkPillar(y, x)) {
                    answer.add(intArrayOf(x, y, structure))
                }
            }
            1 -> {
                if (checkFloor(y, x)) {
                    answer.add(intArrayOf(x, y, structure))
                }
            }
        }
    }

    fun remove(y: Int, x: Int, structure: Int) {
        var canRemove = true
        var idx = 0
        for (i in answer.indices) {
            if (answer[i][0] == x && answer[i][1] == y && answer[i][2] == structure) {
                idx = i
                break
            }
        }
        val erased = answer.removeAt(idx)

        loop@for (i in answer.indices) {
            when (answer[i][2]) {
                0 -> {
                    if (!checkPillar(answer[i][1], answer[i][0])) {
                        canRemove = false
                        break@loop
                    }
                }
                1 -> {
                    if (!checkFloor(answer[i][1], answer[i][0])) {
                        canRemove = false
                        break@loop
                    }
                }
            }
        }

        if (!canRemove) answer.add(erased)
    }

    fun doCommand(y: Int, x: Int, structure: Int, command: Int) {
        when (command) {
            0 -> remove(y, x, structure)
            1 -> build(y, x, structure)
        }
    }

    for (i in build_frame.indices) {
        val yPos = build_frame[i][1]
        val xPos = build_frame[i][0]
        val structure = build_frame[i][2]
        val command = build_frame[i][3]

        doCommand(yPos, xPos, structure, command)
    }

    return answer.sortedWith(compareBy({it[0]}, {it[1]}, {it[2]})).toTypedArray()
}
```

### 문제 해설

전체 명령의 개수는 총 1,000개 이하이므로 이 문제는 O(M^3)의 알고리즘을 이용해도 정답 판정을 받을 수 있다. `(이 점을 간과하였다.)`

</br>

따라서 이 문제를 O(M^3)의 시간 복잡도로 해결하는 가장 간단한 방법은, 

설치 및 삭제 연산을 요구할 때마다 일일이 '전체 구조물을 확인하며' 규칙을 확인하는 것이다. 

`(그러나, 나는 설치 시에는 전체 구조물 중 설치할 구조물을 가능하게 하는 것이 있는지 확인하고
삭제 시에는 삭제시켜본 뒤 전체 구조물이 가능한 구조물인지 확인한 뒤 아니라면 복구시켰다.)`

</br>

## 기출문제 7 치킨 배달

### 문제

[https://www.acmicpc.net/problem/15686](https://www.acmicpc.net/problem/15686)

### 코드

```kotlin
fun main() {
    val (n, m) = readInts()
    val chickens = mutableListOf<Pair<Int, Int>>()
    val homes = mutableListOf<Pair<Int, Int>>()

    for (y in 0 until n) {
        val st = StringTokenizer(readLine(), " ")
        for (x in 0 until st.countTokens()) {
            val value = st.nextToken().toInt()
            when (value) {
                1 -> homes.add(Pair(y, x))
                2 -> chickens.add(Pair(y, x))
            }
        }
    }

    val candidates = combination(chickens, m)

    fun solution() {
        var answer = Int.MAX_VALUE

        // 조합 목록들 마다
        for (candidate in candidates) {
            var sum = 0

            for (home in homes) { // 집의 거리 계산
                var dist = Int.MAX_VALUE

                for (chicken in candidate) {
                    dist = min(dist, abs(chicken.first - home.first) + abs(chicken.second - home.second))
                }
                sum += dist
            }
            answer = min(answer, sum)
        }
        print(answer)
    }

    solution()
}

private fun combination(chickens: MutableList<Pair<Int, Int>>, volume: Int): MutableList<MutableList<Pair<Int, Int>>> {
    val result = mutableListOf<MutableList<Pair<Int, Int>>>()

    combination(result, chickens, volume, 0, mutableListOf<Pair<Int, Int>>())
    return result
}

private fun combination(result: MutableList<MutableList<Pair<Int, Int>>>, chickens: MutableList<Pair<Int, Int>>, volume: Int, idx: Int, stored: MutableList<Pair<Int, Int>>) {
    if (stored.size == volume) {
        result.add(stored.toMutableList())
        return
    }

    for (i in idx until chickens.size) {
        stored.add(chickens[i])
        combination(result, chickens, volume, i + 1, stored)
        stored.remove(chickens[i])
    }
}
```

### 문제 해설

치킨집의 개수 범위는 **M ≤ 치킨집의 개수 ≤ 13** 이다. 고려할 수 있는 경우의 수는 최대 13개에서 M개를 선택하는 조합과 동일하다. 이 경우 M이 어떤 값이 되든지 간에 _{13}C_{M}의 값은 100,000을 넘지 않는다. 집의 개수 또한 최대 100개이기 때문에, 모든 경우의 수를 다 고려하더라도 시간 초과 없이 문제를 해결할 수 있다.

`(조합의 시간복잡도를 고려하지 못했다.)`

조합 알고리즘을 이용해 치킨집 중 M개를 고르는 경우를 모두 구한 뒤에 치킨 거리의 합을 계산하여 최솟값을 출력하면 된다.

이 때, BFS를 이용하여 거리 계산을 하는 것이 아닌 각 치킨집의 좌표와 집의 좌표를 이용하여 **유클리드 거리**를 구한다. `(M의 값이 작기 때문에 이 경우가 훨씬 빠르고, 모든 칸을 이동할 수 있으므로 그냥 점과 점 사이의 거리를 계산하면 된다.)`

</br>

## 기출문제 8 외벽 점검

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/60062](https://programmers.co.kr/learn/courses/30/lessons/60062)

### 코드

```kotlin
fun solution(n: Int, weak: IntArray, dist: IntArray): Int {
    var answer = dist.size + 1

    val expand = IntArray(weak.size * 2)
    weak.forEachIndexed { index, i ->
        expand[index] = i
        expand[index + weak.size] = i + n
    }

    val candidates = permutation(dist, dist.size)   // 완전 탐색을 위해 모든 순열을 구함

    for (i in weak.indices) {   // 1, 3, 4, 9, 10
        for (candidate in candidates) { // 매 순열마다
            var start = i
            var position = expand[i]

            for ((person, to) in candidate.withIndex()) {
                position += to

                while (start < expand.size && position >= expand[start]) {
                    start++
                }

                if (start - i >= weak.size) { // 정답
                    answer = min(answer, person + 1)
                }

                if (start < expand.size) position = expand[start]
            }
        }
    }
    if (answer > dist.size) return -1
    return answer
}

private fun permutation(dist: IntArray, volume: Int): MutableList<MutableList<Int>> {
    val result = mutableListOf<MutableList<Int>>()

    permutation(result, dist, volume, -1, mutableListOf(), BooleanArray(dist.size))
    return result
}

private fun permutation(result: MutableList<MutableList<Int>>, dist: IntArray, volume: Int, idx: Int, stored: MutableList<Int>, visited: BooleanArray) {
    if (stored.size == volume) {
        result.add(stored.toMutableList())
        return
    }

    for (i in dist.indices) {
        if (visited[i]) continue

        stored.add(dist[i])
        visited[i] = true
        permutation(result, dist, volume, i, stored, visited)
        stored.remove(dist[i])
        visited[i] = false
    }
}
```

### 문제 해설

'제한 조건'을 보았을 때, **weak** 리스트와 **dist** 리스트의 길이가 매우 작은 것을 알 수 있다. 주어지는 데이터의 개수가 적을 때는 모든 경우를 일일이 확인하는 완전 탐색으로 접근해볼 수 있다.

문제에서 찾고자 하는 값은 '투입해야 하는 친구 수의 최솟값'이다. 이때 전체 친구 수(dist의 길이)는 최대 8이다. 따라서 모든 친구를 무작위로 나열하는 모든 순열(Permutations)의 개수를 계산해보면 $_8P_8$ = 8! = 40,320으로 충분히 계산 가능한 경우의 수가 된다. 따라서 친구를 나열하는 모든 경우의 수를 각각 확인하여 친구를 최소 몇 명 배치하면 되는지 계산하면 문제를 해결할 수 있다.

`(데이터의 개수가 적어 완전 탐색으로 접근이 가능한 점과 각 경우의 수를 순열을 이용해서 모두 계산하는 것을 생각해내지 못했다.)`

</br>

다만, 문제에서는 취약한 지점들이 원형으로 구성되어 있다고 설명하고 있다. 이처럼 원형으로 나열된 데이터를 처리하는 경우에는, 문제 풀이를 간단히 하기 위하여 길이를 **2배**로 늘려서 **'원형'을 일자 형태로 만드는 작업**을 해주면 유리하다.

각 **취약한 지점(0부터 weak.size - 1까지)** 을 시작점으로 하여 **모든 순열을 계산**하여 최솟값을 계산하면 해결할 수 있다.
