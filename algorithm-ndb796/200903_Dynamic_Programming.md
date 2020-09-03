# 200903_Dynamic_Programming

## 1. 중복되는 연산을 줄이자

어떤 문제는 메모리 공간을 약간 더 사용하면 연산 속도를 비약적으로 증가시킬 수 있는 방법이 있다. 

대표적인 방법이 바로 **다이나믹 프로그래밍(Dynamic Programming)** 기법으로 **동적 계획법**이라고 표현하기도 한다. 

</br>

항상 **다이나믹 프로그래밍**을 사용할 수는 없으며, 다음 조건을 만족할 때 사용할 수 있다.

1. 큰 문제를 `작은 문제`로 나눌 수 있다.

2. 작은 문제에서 구한 정답은 그것을 `포함하는 큰 문제에서도 동일`하다.

</br>

정리하자면 **다이나믹 프로그래밍**이란 큰 문제를 작게 나누고, 같은 문제라면 **한 번씩만** 풀어 문제를 효율적으로 해결하는 알고리즘 기법이다.

사실 큰 문제를 작게 나누는 방법은 **분할 정복 알고리즘**으로 분류되는데 **다이나믹 프로그래밍**과 **분할 정복**의 `차이점`은 **다이나믹 프로그래밍**은 `문제들이 서로 영향을 미치고 있다는 점`이다.

</br>

**퀵 정렬**을 예로 들면, 한번 **기준 원소(Pivot)** 가 자리를 변경해서 자리를 잡게 되면 그 기준 원소의 위치는 더 이상 바뀌지 않고 그 피벗값을 다시 처리하는 부분 문제는 존재하지 않는다.

반면에 **다이나믹 프로그래밍**은 `한 번 해결했던 문제를 다시금 해결한다는 점`이 **특징**이다. 그렇기 때문에 `이미 해결된 부분 문제에 대한 답을 저장해 놓고, 이 문제는 이미 해결이 됐던 것이니까 다시 해결할 필요가 없다고 반환하는 것`이다.

</br>

### 피보나치 수열 (재귀적)

```kotlin
fun pibo(x: Int) {
		if (x == 1 || x == 2) return 1
		if (dp[x] != 0) return dp[x]
		
		dp[x] = pibo(x - 1) + pibo(x - 2)
		return dp[x]
}
```

이처럼 **재귀 함수**를 이용하여 **다이나믹 프로그래밍** 소스코드를 작성하는 방법을, `큰 문제를 해결하기 위해 작은 문제를 호출`한다고 하여 **탑다운(Top-Down) 방식**이라고 말한다.

반면에 단순히 **반복문**을 이용하여 소스코드를 작성하는 경우 `작은 문제부터 차근차근 답을 도출`한다고 하여 **바텀업(Bottom-Up) 방식**이라고 말한다.

</br>

### 피보나치 수열 (반복적)

```kotlin
dp[1] = 1
dp[2] = 1

for (i in 3 until n) {
		dp[i] = dp[i - 1] + dp[i - 2]
}

print(dp[n])
```

**다이나믹 프로그래밍**의 전형적인 형태는 **바텀업 방식**이다.

**바텀업 방식**에서 사용되는 결과 저장용 리스트는 '`DP 테이블`'이라 부르며, **메모이제이션**은 **탑다운 방식**에 국한되어 사용되는 표현이다.

</br>

또한 메모이제이션은 때에 따라서 다른 자료형, 예를 들어 `사전(dict) 자료형`을 이용할 수도 있다.

`사전 자료형`은 수열처럼 연속적이지 않은 경우에 유용한데, 예를 들어 모두가 아닌 `일부의 작은 문제에 대한 해답만 필요한 경우`가 존재할 수 있다. 이럴 때에는 `사전 자료형`을 사용하는 게 더 효과적이다.

</br>

### 접근 방법

문제를 푸는 **첫 번째 단계**는 주어진 문제가 `다이나믹 프로그래밍 유형임을 파악`하는 것이다.

특정한 문제를 **`완전 탐색 알고리즘`**으로 접근했을 때 시간이 매우 오래 걸리면 **`다이나믹 프로그래밍`**을 적용할 수 있는지 해결하고자 하는 `부분 문제들의 중복 여부를 확인`해보자.

</br>

## 실전문제 5 효율적인 화폐 구성

### 문제

[https://www.acmicpc.net/problem/2294](https://www.acmicpc.net/problem/2294)

### 코드

```kotlin
fun main() {
    val (n, m) = readInts()
    val coins = IntArray(n) { readInt() }
    val dp = IntArray(m + 1) { 10001 }

    dp[0] = 0

    for (c in coins) {
        for (i in c..m) {
            dp[i] = min(dp[i], dp[i - c] + 1)
        }
    }

    print(if (dp[m] == 10001) -1 else dp[m])
}
```

### 문제 해설

이 문제는 그리디에서 다루었던 거스름돈 문제와 거의 동일하다. 단지 화폐 단위에서 **큰 단위가 작은 단위의 배수가 아니라는 점**만 다르다.

그렇기 때문에 **그리디 알고리즘**을 사용했던 예시처럼 매번 가장 큰 화폐 단위부터 처리하는 방법으로는 해결할 수 없고 **다이나믹 프로그래밍**을 이용해야 한다.

</br>

이번 문제는 `적은 금액부터 큰 금액까지 확인하며 차례대로 만들 수 있는 최소한의 화폐 개수`를 찾으면 된다.

각 인덱스를 '**금액**'으로 고려하여 **메모이제이션**을 진행한다.

</br>

## 문제 구성

- [1. 금광](#기출문제-1-금광)
- [2. 정수 삼각형](#기출문제-2-정수-삼각형)
- [3. 퇴사](#기출문제-3-퇴사)
- [4. 병사 배치하기](#기출문제-4-병사-배치하기)
- [5. 못생긴 수](#기출문제-5-못생긴-수)
- [6. 편집 거리](#기출문제-6-편집-거리)

</br>

## 기출 문제 1 금광

### 문제

n * m 크기의 금광이 있습니다. 각 칸에는 특정한 크기의 금이 들어 있습니다.

채굴자는 첫 번째 열부터 출발하여 금을 캐기 시작합니다. 맨 처음에는 첫 번째 열의 어느 행에서든 출발할 수 있습니다.

이후에 m번에 걸쳐서 매번 오른쪽 위, 오른쪽, 오른쪽 아래 3가지 중 하나의 위치로 이동해야 합니다.

결과적으로 채굴자가 얻을 수 있는 금의 최대 크기를 출력하는 프로그램을 작성하시오.

### 코드

```kotlin
fun main() {
    repeat(readInt()) {
        val (row, col) = readInts()
        val dp = Array(row) { IntArray(col) }
        var answer = 0

        fun input() {
            val st = StringTokenizer(readLine(), " ")
            for (y in 0 until row) {
                for (x in 0 until col) {
                    dp[y][x] = st.nextToken().toInt()
                }
            }
        }

        input()

        for (x in 1 until col) {
            for (y in 0 until row) {
                val top = if (y - 1 < 0) 0 else dp[y - 1][x - 1]
                val middle = dp[y][x - 1]
                val bottom = if (y + 1 >= row) 0 else dp[y + 1][x - 1]

                dp[y][x] = maxOf(top, middle, bottom) + dp[y][x]
            }
        }

        IntRange(0, row - 1).forEach { answer = max(answer, dp[it][col - 1]) }
        out.appendln(answer)
    }
    print(out)
}
```

### 문제 해설

금광의 모든 위치에 대하여, **(1) 왼쪽 위에서 오는 경우, (2) 왼쪽 아래에서 오는 경우, (3) 왼쪽에서 오는 경우**의 3가지 경우만 존재한다.

즉, `dp[i][j] = max(dp[i - 1][j - 1], dp[i + 1][j - 1], dp[i][j - 1]) + array[i][j]` 이다.

</br>

## 기출 문제 2 정수 삼각형

### 문제

[https://www.acmicpc.net/problem/1932](https://www.acmicpc.net/problem/1932)

### 코드

```kotlin
fun main() {
    val n = readInt()
    val dp = Array(n) { IntArray(n) { -1 } }

    fun input() {
        for (y in 0 until n) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until st.countTokens()) {
                dp[y][x] = st.nextToken().toInt()
            }
        }
    }

    fun solution() {
        for (y in 1 until n) {
            for (x in 0 until n) {
                if (dp[y][x] == -1) break

                val left = if (x - 1 < 0) 0 else dp[y-1][x-1]
                val right = dp[y-1][x]

                dp[y][x] = max(left, right) + dp[y][x]
            }
        }
    }

    input()
    solution()
    print(dp[n-1].max()!!)
}
```

### 문제 해설

금광과 매우 유사한 문제이다.

이 문제에서도 특정한 위치로 도달하기 위해서는 **(1) '왼쪽 위' 혹은 (2) '바로 위'** 2가지 위치에서만 내려올 수 있다.

따라서, `dp[i][j] = max(dp[i - 1][j - 1], dp[i - 1][j]) + array[i][j]` 이다.

</br>

## 기출문제 3 퇴사

### 문제

[https://www.acmicpc.net/problem/14501](https://www.acmicpc.net/problem/14501)

### 코드

```kotlin
fun main() {
    val n = readInt()
    val arr = Array<Pair<Int, Int>>(n) {
        val (day, money) = readInts()
        Pair(day, money)
    }
    val dp = IntArray(n + 1)
    var max_value = 0

    for (i in n - 1 downTo 0) {
        val time = arr[i].first + i

        if (time <= n) {
            dp[i] = max(arr[i].second + dp[time], max_value)
            max_value = dp[i]
        }
        else
            dp[i] = max_value
    }

    print(max_value)
}
```

### 문제 해설

**1일 차**에 상담을 진행하는 경우, **최대 이익**은 '`1일 차의 상담 금액 + 4일부터의 최대 상담 금액`'이 된다.

따라서 이러한 원리를 이용하여 뒤쪽 날짜부터 거꾸로 계산하며 문제를 해결할 수 있다.

즉, 뒤쪽부터 매 상담에 대하여 '`현재 상담 일자의 이윤(p[i]) + 현재 상담을 마친 일자부터의 최대 이윤(dp[t[i] + i]])`'을 계산하면 된다.

</br>

'`dp[i] = i번쨰 날부터 마지막 날까지 낼 수 있는 최대 이익`'이라고 하면 점화식은

`dp[i] = max(p[i] + dp[t[i] + i], max_value)`가 된다. 

`max_value`는 뒤에서부터 계산할 때, 현재까지의 최대 상담 금액에 해당하는 변수이다.

</br>

## 기출문제 4 병사 배치하기

### 문제

[https://www.acmicpc.net/problem/18353](https://www.acmicpc.net/problem/18353)

### 코드

```kotlin
fun main() {
    val n = readInt()
    val arr = IntArray(n)
    val dp = IntArray(n) { 1 }

    fun input() {
        val st = StringTokenizer(readLine(), " ")
        for (i in arr.indices)
            arr[i] = st.nextToken().toInt()
    }

    fun solution() {
        for (i in 1 until n) {
            for (j in 0 until i) {
                if (arr[j] > arr[i])
                    dp[i] = max(dp[i], dp[j] + 1)
            }
        }
    }

    input()
    solution()
    print(n - dp.max()!!)
}
```

### 문제 해설

이 문제의 기본 아이디어는 '`가장 긴 증가하는 부분 수열(LIS, Longest Increasing Subsequence)`'로 알려진 전형적인 **다이나믹 프로그래밍** 문제의 아이디어와 같다.

'`dp[i] = array[i]를 마지막 원소로 가지는 부분 수열의 최대 길이`'라고 정의하면, 가장 긴 증가하는 부분 수열을 계산하는 **점화식**은 다음과 같다.

이때 초기의 **DP 테이블**의 값은 **모두 1로 초기화**한다.

→ 모든 `0 ≤ j < i` 에 대하여, `dp[i] = max(dp[i], dp[j] + 1) if array[j] < array[i]`

</br>

## 기출문제 5 못생긴 수

### 문제

**못생긴 수**란 오직 **2, 3, 5**만을 **소인수(Prime Factor)**로 가지는 수를 의미한다. 다시 말해 오직 2, 3, 5를 약수로 가지는 합성수를 의미한다.

**1**은 **못생긴 수**라고 가정한다. 이때 n번째 못생긴 수를 찾는 프로그램을 작성하시오.

### 코드

```kotlin
fun main() {
    val n = readInt()
    val ugly = IntArray(n)

    ugly[0] = 1

    var (i2, i3, i5) = intArrayOf(0, 0, 0)
    var (next2, next3, next5) = intArrayOf(2, 3, 5)

    for (i in 1 until n) {
        ugly[i] = minOf(next2, next3, next5)

        if (ugly[i] == next2) {
            i2++
            next2 = ugly[i2] * 2
        }
        if (ugly[i] == next3) {
            i3++
            next3 = ugly[i3] * 3
        }
        if (ugly[i] == next5) {
            i5++
            next5 = ugly[i5] * 5
        }
    }

    print(ugly[n - 1])
}
```

### 문제 해설

이 문제는 가능한 **못생긴 수**를 앞에서부터 하나씩 찾는 방법으로 해결할 수 있다.

이때 `못생긴 수에 2, 3, 혹은 5를 곱한 수 또한 '못생긴 수'에 해당`한다는 점이 포인트이다.

**2의 배수 변수, 3의 배수 변수, 5의 배수 변수**에 대하여 각각 '`가장 작은 못생긴 수`'부터 오름차순으로 하나씩 확인하면서, 각 배수를 곱한 값도 '**못생긴 수**'가 될 수 있도록 처리하면 정답 판정을 받을 수 있다.

</br>

## 기출문제 6 편집 거리

### 문제

두 개의 문자열 A와 B가 주어졌을 때, 문자열 A를 편집하여 문자열 B로 만들고자 합니다.

문자열 A를 편집할 때는 다음의 세 연산 중에서 한 번에 하나씩 선택하여 이용할 수 있습니다.

1. **삽입 (Insert)** : 특정한 위치에 하나의 문자를 삽입

2. **삭제 (Remove)** : 특정한 위치에 하나의 문자를 삭제

3. **교체 (Replace)** : 특정한 위치에 있는 하나의 문자를 다른 문자로 교체

이때 **편집 거리**란 문자열 A를 편집하여 문자열 B로 만들기 위해 사용한 연산의 수를 의미한다.

문자열 A를 문자열 B로 만드는 **최소 편집 거리**를 계산하는 프로그램을 작성하세요.

### 코드

```kotlin
fun main() {
    val strFrom = readLine()
    val strTo = readLine()

    val dp = Array(strFrom.length) { IntArray(strTo.length) }

    IntRange(0, strFrom.length - 1).forEachIndexed { index, _ -> dp[index][0] = index }
    IntRange(0, strTo.length - 1).forEachIndexed { index, _ -> dp[0][index] = index }

    for (y in 1 until strFrom.length) {
        for (x in 1 until strTo.length) {
            dp[y][x] = if (strFrom[y] == strTo[x]) dp[y - 1][x - 1]
                       else minOf(dp[y][x - 1], dp[y - 1][x], dp[y - 1][x - 1]) + 1
        }
    }

    print(dp[strFrom.length - 1][strTo.length- 1])
}
```

### 문제 해설

이 문제는 **최소 편집 거리**를 담을 **2차원 테이블**을 초기화한 뒤에, 최소 편집 거리를 계산해 테이블에 저장하는 과정으로 문제를 해결할 수 있다.

각 왼쪽 가장자리와 위쪽 가장자리는 각 인덱스 값으로 초기화.

</br>

**다이나믹 프로그래밍**의 **점화식**은 다음과 같다.

1. **두 문자가 같은 경우** : `dp[i][j] = dp[i - 1][j - 1]`

2. **두 문자가 다른 경우** : `dp[i][j] = min(dp[i][j - 1], dp[i - 1][j], dp[i - 1][j - 1]) + 1`

이를 말로 풀어서 쓰면 다음과 같다.

1. 행과 열에 해당하는 문자가 **서로 같다면**, 이전 수를 그대로 대입

2. 행과 열에 해당하는 문자가 **서로 다르다면**, `삽입, 삭제, 교체`에 해당하는 수 중에서 **가장 작은 수에 1을 더함**
