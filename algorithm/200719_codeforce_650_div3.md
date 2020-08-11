# 200719_Codeforce Round #650 div3

## 1. 문제 구성

- A : Short Substrings
- B : Even Array
- C : Social Distance
- D : Task On The Board
- E : Necklace Assembly
- ~~F1 : Flying Sort (Easy Version)~~
- ~~F2 : Flying Sort (Hard Version)~~

</br>

## 2. 문제

## A. Short Substrings [*800]

### 문제

- 각 자리에서 **두 글자씩 자른 문자열**들을 이어 붙인 문자열이 주어질 때 **원래 문자열**을 구하라.
- [https://codeforces.com/contest/1367/problem/A](https://codeforces.com/contest/1367/problem/A)

</br>

### 조건

시간 제한 : 2초

메모리 제한 : 256 MB

</br>

### 입출력

입력

- 첫째 줄에 테스트 케이스 수 **t (1 ≤ t ≤ 1,000)**이 주어진다.
- 각 테스트 케이스마다 문자열 **b** 가 주어진다. **(2 ≤ |b| ≤ 100)**

출력

- 원래 문자열 **t**

</br>

### 풀이 과정

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        val sb = StringBuilder()

        readLine().forEachIndexed { index, c ->
            if (index < 2)
                sb.append(c)
            else if (index % 2 == 1)
                sb.append(c)
        }
        println(sb)
    }
}
```

</br>

## B. Even Array [*800]

### 문제

- **홀수 인덱스에는 홀수, 짝수 인덱스에는 짝수**가 되도록 하는 배열의 **최소 이동 횟수**를 구하라.
- [https://codeforces.com/contest/1367/problem/B](https://codeforces.com/contest/1367/problem/B)

</br>

### 조건

시간 제한 : 2초

메모리 제한 : 256 MB

</br>

### 입출력

입력

- 첫째 줄에 테스트 케이스 수 **t (1 ≤ t ≤ 1,000)**이 주어진다.
- 각 테스트 케이스의 첫 번째 줄은 배열 **a** 의 길이 **n (1≤ n ≤ 40)** 이 주어진다.
- 두 번째 줄은 초기 배열이 주어진다. **(0 ≤ $a_i$ ≤ 1,000)**

</br>

### 풀이 과정

`틀린 짝수 개수와 홀수 개수`가 `같아야만` 올바른 배열로 수정할 수 있다는 점에 주목하자.

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        readLine().toInt()
        var notMatchInEvenIndex = 0
        var notMatchInOddIndex = 0
        readLine().split(" ").map { it.toInt() }.forEachIndexed { index, i ->
            if (index % 2 == 0) {
                if (i % 2 != 0)
                    notMatchInEvenIndex++
            }
            else {
                if (i % 2 != 1)
                    notMatchInOddIndex++
            }
        }

        println(if (notMatchInEvenIndex != notMatchInOddIndex) -1 else (notMatchInEvenIndex + notMatchInOddIndex) / 2)
    }
}
```

</br>

## C. Social Distance [*1300]

### 문제

- 문자열로 **레스토랑의 테이블 상태**가 주어질 때, **정해진 거리 규칙**을 준수하며 **더 앉을 수 있는 테이블 수**를 구하라.
- [https://codeforces.com/contest/1367/problem/C](https://codeforces.com/contest/1367/problem/C)

</br>

### 조건

시간 제한 : 2초

메모리 제한 : 256 MB

</br>

### 입출력

입력

- 첫째 줄에 테스트 케이스 수 **t (1 ≤ t ≤ 10,000)**이 주어진다.
- 각 테스트 케이스마다 첫 번째 줄에는 레스토랑의 테이블 수 **n** 과 준수해야 하는 거리 **k** 가 주어진다. **(1 ≤ k ≤ n ≤ 200,000)**
- 두 번째 줄에는 레스토랑 테이블 초기 상태를 나타내는 문자열 **s** 가 주어진다. **(1은 사용 중, 0은 빈 테이블)**

</br>

### 풀이 과정

처음 블록과 끝 블록, 중간 블록으로 나누어 분할 정복하였다.

```kotlin
Flying Sort (Easy Version)import java.util.*

var cnt = 0

fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        val (N, K) = readLine().split(" ").map { it.toInt() }
        val table = readLine()
        val used = LinkedList<Int>()
        table.map { it.toInt() }.forEachIndexed { index, i ->  if (i == 49) used.add(index)}

        cnt = 0
        if (used.isEmpty()) {
            getAnswer(0, table.length - 1, K)
        }
        else {
            var pLeft = used.poll()
            getAnswer(0, pLeft - K - 1, K)
            while (used.isNotEmpty()) {
                var pRight = used.poll()
                getAnswer(pLeft + K + 1, pRight - K - 1, K)
                pLeft = pRight
            }
            getAnswer(pLeft + K + 1, table.length - 1, K)
        }

        println(cnt)
    }
}

fun getAnswer(pLeft: Int, pRight: Int, K: Int) {
    if (pLeft > pRight)
        return

    if (pRight - pLeft >= 0) cnt++

    getAnswer(pLeft + K + 1, pRight, K)
}
```

</br>

## D. Task On The Board [*1800]

### 문제

- 문자열 **s** 가 주어지고, 주어진 문자열과 정보를 바탕으로 새 문자열 **t** 를 만든다.
- 새 문자열의 길이 **m** 과 배열 **b** 가 주어진다.
- <img src="https://render.githubusercontent.com/render/math?math=b_i"> 는 인덱스 <img src="https://render.githubusercontent.com/render/math?math=|i - j|"> 의 합이다. (인덱스 i 로 부터 **`알파벳 순서`** <img src="https://render.githubusercontent.com/render/math?math=t_j > t_i"> 를 만족하는 모든 j 에 대해)
- [https://codeforces.com/contest/1367/problem/D](https://codeforces.com/contest/1367/problem/D)

</br>

### 조건

시간 제한 : 2초

메모리 제한 : 256 MB 

</br>

### 입출력

입력

- 첫째 줄에 테스트 케이스 수 **q (1 ≤ q ≤ 100)**이 주어진다.
- 각 테스트 케이스의 첫 번째 줄은 문자열 **s (1 ≤ s ≤ 50)**이 주어진다.
- 두 번째 줄은 문자열 **s** 의 길이 **m (1 ≤ m ≤ |s|)**
- 세 번째 줄은 배열 **b** 가 주어진다. **(0 ≤ <img src="https://render.githubusercontent.com/render/math?math=b_i"> ≤ 1,225)**

출력 

- 새 문자열 **t**

</br>

### 풀이과정

배열 **b** 에서 **0** 에는 가능한 가장 큰 알파벳이 들어갈 수 있다.

주어진 문자열의 **알파벳 빈도 수**와 **0의 개수**를 비교하여 가능하다면,

**가능한 가장 큰 알파벳**을 넣어주면 된다.

1. **0을 탐색**
2. **가능한 가장 큰 알파벳**을 0인 자리에 넣는다.
3. 넣은 후 다시 배열 b의 값을 계산 <img src="https://render.githubusercontent.com/render/math?math=b_i = sum|i-j|">

```kotlin
import java.lang.StringBuilder
import kotlin.math.abs

fun main() = with(System.`in`.bufferedReader()) {
    val out = StringBuilder()

    repeat(readLine().toInt()) {
        val s = readLine()
        val m = readLine().toInt()
        val b = readLine().split(" ").map { it.toInt() }.toTypedArray()
        val freq = HashMap<Char, Int>()
        val answer = CharArray(m)
        val seen = BooleanArray(m)

        // 빈도 수 저장
        s.forEach { freq[it] = (freq[it] ?: 0) + 1 }

        // z 부터 a 까지
        for (letter in 'z' downTo 'a') {
            val ixs = mutableListOf<Int>()
            // 0인 인덱스 저장
            for (j in 0 until m) {
                if (b[j] == 0 && !seen[j]) {
                    ixs.add(j)
                }
            }
            // 0인 개수를 채울 수 있는 알파벳이면
            if (ixs.size <= freq[letter] ?: 0) {
                for (j in ixs) {
                    seen[j] = true
                    answer[j] = letter

                    for (k in 0 until m) {
                        b[k] -= abs(j - k)
                    }
                }
            }
        }
        out.appendln(answer)
    }
    print(out)
}
```

</br>

## E. Necklace Assembly (*1900)

### 문제

- **k** 번 돌리면 같은 형태가 되는 목걸이의 `가장 큰 길이`를 구하라.
- [https://codeforces.com/contest/1367/problem/E](https://codeforces.com/contest/1367/problem/E)

</br>

### 조건

시간 제한 : 2초

메모리 제한 : 256 MB 

</br>

### 입출력

입력

- 첫째 줄에 테스트 케이스 수 **t** **(1 ≤ t ≤ 100)**이 주어진다.
- 각 테스트 케이스의 첫 번째 줄은 **n (비즈의 수), k (k-번째)**가 주어진다. **(1 ≤ n, k ≤ 2,000)**
- 두 번째 줄은 문자열 **s** 가 주어진다. **(가게에 있는 비즈들)**

출력 

- 목걸이의 가장 큰 길이

</br>

### 풀이 과정

길이가 **n = 6** 인 목걸이를 만든다고 가정하자.

**첫 번째 비즈**에 **a** 를 박는다면, 이것을 **k = 3번** 시계 방향으로 돌렸을 때 똑같은 형태로 만들려면, 

**네 번째 비즈**에도 **a** 가 박혀야 한다.

</br>

즉,  `(idx + k) % n` 번째 비즈에는 **같은 알파벳**이 박혀야 한다.

```kotlin
import java.lang.StringBuilder

fun main() = with(System.`in`.bufferedReader()) {
    val out = StringBuilder()

    repeat(readLine().toInt()) {
        val (n, k) = readLine().split(" ").map { it.toInt() }
        val s = readLine()
        val freq = IntArray(26)

        s.forEach { freq[it - 'a']++ }  // 알파벳 개수 세기

        // 목걸이의 길이 n 부터 1 까지
        // (idx + k) % n 공식을 활용하여 동일한 알파벳이 들어가야 할 자리를 표시한다.
        // 자리 표시는 cnt 변수를 이용
        for (i in n downTo 1) {
            val answer = IntArray(i)
            var cnt = 1

            for (j in 0 until i) {
                var idx = j

                if (answer[idx] == 0) {

                    while (answer[idx] == 0) {
                        answer[idx] = cnt

                        idx = (idx + k) % i
                    }

                    // 다른 값이 이미 들어가 있다면
                    if (answer[idx] != cnt)
                        break

                    cnt++
                }
            }

            // 0이 아직 존재한다면, 중간에 종료한 것이므로 continue
            if (answer.contains(0))
                continue
            // 0이 없다면, 알파벳 개수와 비교하여 가능한 지를 검사
            else {
                var count = 0
                var key = 0

                for ((k, v) in answer.map { it }.groupingBy { it }.eachCount()) {
                    key = v
                    count++
                }

                for (f in freq) {
                    count -= f / key
                }

                if (count <= 0) {
                    out.appendln(i)
                    break
                }
            }
        }
    }
    print(out)
}
```

</br>

### 더 나은 풀이

길이가 **n** 인 목걸이에서 **k-Beautiful-Necklace**를 만들고자 할 때, 동일한 알파벳 쌍의 개수는 `n과 k의 최대공약수`와 같다.

또한, 한 쌍이 몇 개로 이루어져 있는지는 `n / gcd(n, k)`로 알 수 있다.

</br>

> 이유는 무엇일까?

길이가 **n** 인 목걸이를 채울 때 결국 우리는 **n** 개의 방을 채우게 된다.

**n = 6이고 k = 3**일 때, `(0, 3), (1, 4), (2, 5)`가 동일한 알파벳 쌍을 가지며, **2개씩 3쌍**을 이룬다.

`gcd(n, k) = 3, n / gcd(n, k) = 2`인 것을 알 수  있다.

```kotlin
import java.lang.StringBuilder

fun main() = with(System.`in`.bufferedReader()) {
    val out = StringBuilder()

    repeat(readLine().toInt()) {
        val (n, k) = readLine().split(" ").map { it.toInt() }
        val s = readLine()
        val freq = IntArray(26)

        s.forEach { freq[it - 'a']++ }  // 알파벳 개수 세기

        for (len in n downTo 1) {
            val pair = gcd(len, k)
            var answer = 0

            for (f in freq) {
                answer += f / (len / pair)
            }

            if (answer >= pair) {
                out.appendln(len)
                break
            }
        }
    }
    print(out)
}

fun gcd(x: Int, y: Int): Int = if (y == 0) x else gcd(y, x % y)
```
