# 분할정복

주어진 문제를 둘 이상의 부분 문제로 나눈 뒤 각 문제에 대한 답을 재귀 호출을 이용해 계산하고, 각 부분 문제의 답으로부터 전체 문제의 답을 계산한다.

**일반 재귀 호출과 다른 점은 문제를 거의 같은 크기의 부분 문제로 나눈다는 점이다.**

</br>

### 1. 알고리즘 수행 과정

- 문제를 더 작은 문제로 분할하는 과정 (divide)

- 각 문제에 대한 답을 원래 문제에 대한 답으로 병합 (merge)

- 더 이상 답을 분할하지 않고 곧장 풀 수 있는 매우 작은 문제 (base case)

  </br>

### 2. 분할 정복의 장점

1. 예제 : 수열의 빠른 합과 행렬의 빠른 제곱

- 1부터 n 까지의 합을 n 개의 조각으로 나눈 뒤, 이들을 반으로 잘라 <img src="https://render.githubusercontent.com/render/math?math=n/2">개의 조각들로 이만들어진 부분 문제 2개를 만든다.
  
  <img src="https://render.githubusercontent.com/render/math?math=fastSum() = 1 %2B 2 %2B ... %2B n = (1 %2B 2 %2B ... %2B n/2)((n/2 %2B 1) %2B ... %2B n)">
  
  </br>

  첫 번째 부분 문제는 <img src="https://render.githubusercontent.com/render/math?math=fastSum(n/2)">으로 나타낼 수 있지만, 두 번째 부분 문제는 그렇지 않다.

  → 문제를 재귀적으로 풀기 위해서는 각 부분 문제를 **1부터 n까지의 합** 꼴로 표현할 수 있어야  한다.

  </br>

  따라서, 두 번째 무문 문제를 <img src="https://render.githubusercontent.com/render/math?math=fastSum(x)">를 포함하는 형태
  
  </br>
  
  <img src="https://render.githubusercontent.com/render/math?math=(n/2 %2B 1) %2B ... %2B n = (n/2 %2B 1) %2B (n/2 %2B 2) %2B ... %2B (n/2 %2B n/2)">

  </br>
  
  <img src="https://render.githubusercontent.com/render/math?math== n/2 * n/2 %2B (1 %2B 2 %2B ... %2B n/2) = fastSum(n/2) %2B n^2/4">
 
  </br>
  
  즉,  
  
  <img src="https://render.githubusercontent.com/render/math?math=fastSum(n) = 2 * fastSum(n/2) %2B n^2/4">
 
  이다.

  </br>

  ```kotlin
  fun fastSum(n: Int) {
  	if (n == 1) return 1
  	if (n % 2 == 1) return fastSum(n-1) + n
  	return 2 * fastSum(n/2) + (n/2) * (n/2)
  }
  ```

  </br>

  **호출될 때마다 최소한 두 번에 한 번 꼴로 n이 절반으로 줄어든다.**

  </br>

1. 행렬의 거듭제곱

- n * n 크기의 행렬 A가 주어질 때, A의 거듭제곱 A^m은 A를 연속해서 m번 곱한 것이다.

- 행렬의 곱셈에는 O(n^3)의 시간이 들기 때문에 A^m을 구하려면 모두 O(n^3 * m)번의 연산이 필요해 너무 느리다.

- A^m을 구하는데 필요한 m개의 조각을 절반으로 나눠보자.
  
  <img src="https://render.githubusercontent.com/render/math?math=A^m = A^{m/2} * A^{m/2}"/> </br> </br>
 
  m이 홀수일 때,
  
  <img src="https://render.githubusercontent.com/render/math?math=A^m = A * A^{m-1}"/>로 나누지 않고, <img src="https://render.githubusercontent.com/render/math?math=A^7 = A^3 * A^4"/>로 나누는 것이 더 좋지 않을까라는 생각을 할 수 있다.

  </br>

  실제로 문제의 크기가 매번 절반에 가깝게 줄어들면 기저 사례에 도달하기까지 걸리는 분할의 횟수가 줄어들기 때문에 대두분의 `분할 정복 알고리즘`은 **가능한 한 절반에 가깝게 문제를 나누고자 한다.**

  → 그러나 이 문제에선, 그 방식의 분할은 알고리즘을 더 느리게 만든다.

  </br>

### 3. 병합 정렬과 퀵 정렬

</br>

### 4. 문제 : 쿼드 트리 뒤집기

### 문제

[https://algospot.com/judge/problem/read/QUADTREE](https://algospot.com/judge/problem/read/QUADTREE)

### 풀이과정

**x** 가 나오면 다음 문자 4개가 한 그룹 (ex : x **(bwwb)** )

그러나, x **(bwx(bwwb)b)** 처럼 그룹 안에 그룹이 존재할 수 있다.

문자열을 순회하면서 **x** 가 나오면 재귀 호출을 하도록 설계

Queue를 이용하여 쉽게 앞에서부터 순회하도록 하였다.

```kotlin
fun main() = with(System.`in`.bufferedReader()){
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        val queue = LinkedList<Char>()
        readLine().toCharArray().forEach { queue.add(it) }

        println(reverseQT(queue))
    }
}

fun reverseQT(queue: LinkedList<Char>): String {
    var sb = StringBuilder()
    var cnt: Int = 0

    for (i in 0..3) {
        if (queue.isEmpty())
            break

        var c = queue.poll()

        when (c) {
            'x' -> {
                sb.append(c)
                sb.append(reverseQT(queue))
            }
            else -> sb.append(c)
        }
        cnt++
    }

    return swap(sb.toString(), cnt)
}

fun swap(str: String, cnt: Int): String {
    if (cnt < 3)
        return str

    var idx = 0
    var cursor = 0
    for (i in str.indices) {
        if (idx == 2) {
            cursor = i
            break
        }

        idx++
        if (str[i] == 'x')
            idx -= 4
    }

    return str.substring(cursor) + str.substring(0, cursor)
}
```

### 더 나은 풀이

위 문제는 그림 영역을 4개로 나누면서 압축하는 동작을 한다.

즉, 한 번의 연산에 **4번의 영역 나누기**를 한다는 전제가 깔려 있다.

</br>

분할 정복에 대한 높은 이해와 코드의 명확성을 갖춘 코드라고 볼 수 있다.

```kotlin
var itr = 0

fun main() = with(System.`in`.bufferedReader()){
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        println(reverse(readLine()))
    }
}

fun reverse(painting: String): String {
    var c = painting[itr]
    ++itr

    if (c == 'b' || c == 'w')
        return c.toString()
    var upperLeft = reverse(painting)
    var upperRight = reverse(painting)
    var lowerLeft = reverse(painting)
    var lowerRight = reverse(painting)

    return "x$lowerLeft$lowerRight$upperLeft$upperRight"
}
```
</br>

### 5. 문제 : 울타리 잘라내기

### 문제

[https://algospot.com/judge/problem/read/FENCE](https://algospot.com/judge/problem/read/FENCE)

### 풀이과정

최대 판자는 20,000개, 각 판자의 최대 높이는 10,000이다.

판자에 대해서 모든 경우의 수를 탐색하기에는 20,000 * 10,000 = 200,000,000이라 시간 제한에 걸려 어려워보인다.

</br>

여기서 `분할 정복`을 이용하여 해결한다.

</br>

> 분할 정복 알고리즘의 설계

분할 정복 알고리즘을 설계하기 위해서는 문제를 **어떻게 분할할지를 가장 먼저 결정**해야 한다.

</br>

**n** 개의 판자를 절반으로 나눠 두 개의 부분 문제를 만들자.

그러면 우리가 찾는 직사각형은 다음 **세 가지 중의 하나**일 것이다.

- 가장 큰 직사각형을 **왼쪽 부분 문제**에서만 잘라낼 수 있다.
- 가장 큰 직사각형을 **오른쪽 부분 문제**에서만 잘라낼 수 있다.
- 가장 큰 직사각형은 **왼쪽 부분 문제와 오른쪽 부분 문제**에 걸쳐 있다.

    → 힌트는 이 직사각형은 **반드시 부분 경계에 있는 두 판자를 포함**한다는 점이다.

    → 이 직사각형에서 시작해 **왼쪽과 오른쪽 중 더 높은 판자를 포함**하도록 **확장**해 나가면 최대 직사각형을 구할 수 있다.

```kotlin
import java.lang.Integer.max
import java.lang.Integer.min

fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        readLine().toInt()
        val arr = readLine().split(" ").map { it.toInt() }.toTypedArray()
        println(solve(arr, 0, arr.size-1))
    }
}

fun solve(arr: Array<Int>, left: Int, right: Int): Int {
    // 기저 사례 : 판자 하나
    if (left == right) return arr[left]

    var mid = (left + right) / 2
    var ret = max(solve(arr, left, mid), solve(arr, mid + 1, right))

    var lb = mid
    var rb = mid + 1
    var height = min(arr[lb], arr[rb])

    while (left < lb || rb < right) {
        // 항상 높이가 더 높은 쪽으로 확장
        height = if (rb < right && (lb == left || arr[lb - 1] < arr[rb + 1])) {
            rb++
            min(height, arr[rb])
        } else {
            lb--
            min(height, arr[lb])
        }

        ret = max(ret, height * (rb - lb + 1))
    }
    return ret
}
```
</br>

### 6. 문제 : 팬미팅

### 문제

[https://algospot.com/judge/problem/read/FANMEETING](https://algospot.com/judge/problem/read/FANMEETING)

### 풀이과정

</br>

> 곱셉으로의 변형

이 문제를 빠르게 푸는 방법은 두 큰 수의 곱셈으로 바꾸는 것이다.

길이 **3**인 정수 **A**와 길이 **5**인 정수 **B**의 곱 **C**는 아래 그림과 같다.

![Untitled](https://user-images.githubusercontent.com/24761073/87224737-a5be3800-c3c2-11ea-8b8d-ccbce55bb7a2.png)


위 규칙에서 A를 뒤집으면 이 문제의 규칙과 동일한 결과를 얻을 수 있다.

</br>

아래 그림은 **C4** 에 대한 값이 구해지는 과정을 나타낸 것이다.

![Untitled](https://user-images.githubusercontent.com/24761073/87224766-d2724f80-c3c2-11ea-8517-4b537e4faa95.png)

</br>

이후엔 `카라츠바 알고리즘`을 통해 문제를 해결한다.

</br>

> 카라츠바 알고리즘 (karatsuba)

각각 a 와 b 가 256자리 수라면 <img src="https://render.githubusercontent.com/render/math?math=a_1">과 <img src="https://render.githubusercontent.com/render/math?math=b_1">은 첫 128자리, <img src="https://render.githubusercontent.com/render/math?math=a_0">과 <img src="https://render.githubusercontent.com/render/math?math=b_0">은 그 다음 128자리를 저장

<img src="https://render.githubusercontent.com/render/math?math=a = a_1 * 10^{128} %2B a_0">

<img src="https://render.githubusercontent.com/render/math?math=b = b_1 * 10^{128} %2B b_0">

</br>

<img src="https://render.githubusercontent.com/render/math?math=a * b"> 를 네 개의 조각을 이용해 표현

<img src="https://render.githubusercontent.com/render/math?math=a * b = a_1 * b_1 * 10^{256} %2B (a_1 * b_0 %2B a_0 * b_1) * 10^{128} %2B a_0 * b_0">

</br>

카라츠바가 발견한 것은 네 번의 곱을 **단 세 번의 곱** 으로 계산이 가능하다는 것이다.

</br>

<img src="https://render.githubusercontent.com/render/math?math=z_2 = a_1 * b_1">

<img src="https://render.githubusercontent.com/render/math?math=z_1 = a_1 * b_0 %2B a_0 * b_1">

<img src="https://render.githubusercontent.com/render/math?math=z_0 = a_0 * b_0">

로 표현했을 때, 

<img src="https://render.githubusercontent.com/render/math?math=z_1">**은 이렇게도 표현 가능하다.**

</br>

<img src="https://render.githubusercontent.com/render/math?math=z_1 = (a_0 %2B a_1) * (b_0 %2B b_1) - z_0 - z_2">

```kotlin
import java.lang.Integer.max
import java.lang.Integer.min
import kotlin.math.abs

// a += b * (10^k)
fun addTo(a: MutableList<Int>, b: MutableList<Int>, k: Int) {
     ensureSize(a, max(a.size, b.size + k))

    for (i in b.indices) {
        a[i + k] += b[i]
    }
    normalize(a)
}

// a -= b
fun subFrom(a: MutableList<Int>, b: MutableList<Int>) {
    ensureSize(a, max(a.size, b.size + 1))

    for (i in b.indices) {
        a[i] -= b[i]
    }
    normalize(a)
}

fun multiply(a: MutableList<Int>, b: MutableList<Int>): MutableList<Int> {
    val c = mutableListOf<Int>()
    for (i in 0 until a.size+ b.size + 1)
        c.add(0)

    for (i in a.indices) {
        for (j in b.indices) {
            c[i+j] += a[i] * b[j]
        }
    }
    normalize(c)
    return c
}

fun normalize(num: MutableList<Int>) {
    num.add(0)
    // 자릿수 올림 처리
    for (i in 0 until num.size-1) {
        if (num[i] < 0) {
            val borrow = (abs(num[i]) + 9) / 10
            num[i + 1] -= borrow
            num[i] += borrow * 10
        }
        else {
            num[i + 1] += num[i] / 10
            num[i] %= 10
        }
    }
    while (num.size > 1 && num[num.size-1] == 0) num.removeAt(num.size - 1)
}

fun ensureSize(a: MutableList<Int>, size: Int) {
    while (a.size < size) {
        a.add(0)
    }
}

fun fromString(s: String): MutableList<Int> {
    val ret = mutableListOf<Int>()
    for (c in s) {
        ret.add(c - '0')
    }
    ret.reverse()

    return ret
}

// 두 긴 정수의 곱 반환
fun karatsuba(a: MutableList<Int>, b: MutableList<Int>): MutableList<Int> {
    val an = a.size
    val bn = b.size

    // a가 b보다 짧을 경우 둘을 바꾼다.
    if (an < bn) return karatsuba(b, a)

    // 기저 사례 : a나 b가 비어 있는 경우
    if (an == 0 || bn == 0) return mutableListOf<Int>()

    // 기저 사례 : a가 비교적 짧은 경우 일반 곱셈으로 변경
    if (an <= 50) return multiply(a, b)

    val half = an / 2
    // a와 b를 밑에서 half 자리와 나머지로 분리
    val a0 = a.subList(0, half)
    val a1 = a.subList(half, a.size)
    val b0 = b.subList(0, min(b.size, half))
    val b1 = b.subList(min(b.size, half), b.size)

    val z2 = karatsuba(a1, b1)
    val z0 = karatsuba(a0, b0)
    addTo(a1, a0, 0)
    addTo(b0, b1, 0)

    val z1 = karatsuba(a0, b0)
    subFrom(z1, z0)
    subFrom(z1, z2)

    val ret = ArrayList<Int>()
    addTo(ret, z0, 0)
    addTo(ret, z1, half)
    addTo(ret, z2, half + half)

    return ret
}

fun main() = with(System.`in`.bufferedReader()) {
    val a = fromString(readLine())
    val b = fromString(readLine())

    val c = karatsuba(a, b)
    for (i in c.size-1 downTo 0) {
        print(c[i])
    }
}
```

</br>

> 카라츠바 알고리즘을 이용한 구현

일렬로 선 사람들의 성별을 긴 정수로 표시한다.

각 자릿수는 한 사람의 성별을 나타내며, **M(남자)면 1, F(여자)면 0**으로 표시한다.

</br>

남성과 남성이 만났을 때 두 자릿수의 곱은 1, 다른 경우엔 0이 되므로

`nomarlize` 하지 않은 `각 자릿수의 곱이 0`이라면 모든 멤버가 `포옹을 한다`는 경우로 판단 가능하다.

```kotlin
fun hugs(members: String, fans: String): Int {
    val A = mutableListOf<Int>()
    ensureSize(A, members.length)
    val B = mutableListOf<Int>()
    ensureSize(B, fans.length)

    for (i in members.indices) A[i] = if (members[i] == 'M') 1 else 0
    for (i in fans.indices) B[B.size-i-1] = if (fans[i] == 'M') 1 else 0

    val C = karatsuba(A, B)
    var allHugs = 0
    for (i in A.size-1 until B.size) {
        if (C[i] == 0)
            allHugs++
    }
    return allHugs
}

fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()
    
    while (caseNum-- > 0)
        println(hugs(readLine(), readLine()))
}
```
