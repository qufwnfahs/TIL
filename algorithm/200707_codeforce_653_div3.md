## 1. 문제 구성

- [A : Required Remainder](#A. Required Remainder (Difficulity : 800))

- [B : Multiply by 2, divide by 6](#B. Multiply by 2, divide by 6 (Difficulity : 900))

- [C : Move Brackets](#C : Move Brackets (Difficulity : 1000))

- [D : Zero Remainder Array](#D : Zero Remainder Array (Difficulity : 1400))

- E1 : Reading Books (easy version)

- E2 : Reading Books (hard version)

- F : Cyclic Shifts Sorting

  </br>

## 2. 문제

</br>

## A. Required Remainder (Difficulity : 800)

### 문제

- **x, y, n**이 주어질 때, `0 ≤ k ≤ n` 그리고 `k % x = y` 를 만족하는 가장 큰 정수 **k**를 구하라.

  </br>

### 조건

시간 제한 : 1초

메모리 제한 : 256 MB

</br>

### 입출력

<u>입력</u>

- 첫째 줄에 테스트 케이스 수 **t (1 ≤ t ≤ 50,000)**이 주어진다.
- 다음 **t** 줄에 **x, y, n (2≤ x ≤ 1,000,000,000, 0 ≤ y ≤ x, y ≤ n ≤ 1,000,000,000)**이 주어진다.

<u>출력</u>

- 가장 큰 정수 **k**

  </br>

### 풀이과정

일단, 규칙을 찾으려 했다.

**7 5 12345**가 주어졌다면, **12345**를 **7**로 나눈 나머지를 구한다.

**12345 % 7 = 4**가 구해지는데, 우리는 나머지가 **5**가 나오는 수를 구해야한다.

그럼 가장 큰 정수는 **12345 + 1**인 **12346**이지만 **k**는 **n** 보다 클 수 없으므로

**12346 - 7 = 12339**가 **k**인 것을 알 수 있다.

</br>

즉, **n % x** 를 수행해서 나온 나머지 **n1**과 나와야 할 나머지 **y**를 비교하여

**k = n + (y - n1)**

만약, **k > n** 이면, **k = k - x** 라는 규칙을 알 수 있다.

</br>

이 알고리즘을 바탕으로 x, y, n이 제일 큰 값이 주어졌을 때 int 의 범위를 벗어나는지 분석해보았다.

n이 **1,000,000,000**이더라도 최대 **1,000,000,000**보다 적은 수를 더하니까 **int 의 범위 2,147,483,647**을 넘지 못한다.

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        var numbers = readLine().split(" ").map(String::toInt).toTypedArray()

        var x = numbers[0]
        var y = numbers[1]
        var n = numbers[2]

        var k = n + (y - (n % x))
        if (k > n)
            k -= x

        println(k)
    }
}
```

</br>

### 더 나은 풀이

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
		for (c in 1..readLine().toInt) {
				var (x, y, n) = readLine().split(" ").map(String::toInt)
				
				var k = n + (y - (n % x))
				
				if (k > n) k -= x

				println(k)
		}
}
```

</br>

## B. Multiply by 2, divide by 6 (Difficulity : 900)

### 문제

- 정수 **n** 이 주어진다.  한 행동에 **n** 에 **2를 곱하거나** **n**을 **6으로 나눌 수 있다.** **(6으로 나누어 떨어지는 조건에서만)**

- **n으로부터 1**을 얻을 수 있는 **최소 행동 횟수**를 구하라.

  </br>

### 조건

시간 제한 : 1초

메모리 제한 : 256 MB

</br>

### 입출력

<u>입력</u>

- 첫째 줄에 테스트 케이스 수 **t (1 ≤ t ≤ 20,000)**이 주어진다.
- 다음 **t** 줄에 각 테스트 케이스의 정수 **n(1 ≤ n ≤ 1,000,000,000)** 이 주어진다

<u>출력</u>

- **최소 행동 횟수**, 불가능할 경우 **-1**를 출력

  </br>

### 풀이과정

일단, 규칙을 찾으려 했다.

2를 아무리 곱해도 6으로 나누어 떨어지지 않는 수를 판단할 수 있는 조건은

**그 수가 이미 3의 배수가 아니면**이다.

</br>

즉, **n이 3의 배수가 아닌 시점부터**는 2를 아무리 곱해봤자 **6으로 나누어 떨어질 수 없다는 점**이다.

최소 한 번 곱함으로 6으로 나누어 떨어질 수 없다는 것이 판단되므로 int 의 범위를 넘는 경우는 없다.

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        var n = readLine().toInt()
        var cnt = 0

        // 3의 배수일 동안은
        while (n % 3 == 0) {
            if (n % 6 == 0)
                n /= 6
            else
                n *= 2
            cnt++
        }

        if (n != 1) println(-1) else println(cnt)
    }
}
```

</br>

### 더 나은 풀이

정수 **n**의 2와 3의 소인수 개수를 센다. (div2, div3)

만약, **div2 > div3** 라면, 2를 제거할 수 없으니 -1 을 출력하고

아니라면 **div3`6으로 나누는 횟수`** **+ (div3 - div2) `2를 곱하는 횟수`** 가 답이다.

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        var n = readLine().toInt()
        var div2 = 0
        var div3 = 0

        while (n % 2 == 0) {
            n /= 2
            div2++
        }

        while (n % 3 == 0) {
            n /= 3
            div3++
        }

        if (n == 1 && div2 <= div3) {
            println(div3 - div2 + div3)
        }
        else
            println(-1)
    }
}
```

</br>

## C : Move Brackets (Difficulity : 1000)

### 문제

- **n** 길이의 문자열 **s**가 주어지고, 문자열 **s** 구성요소의 절반은 **'('**로 나머지 절반은 **')'**로 구성되어 있다.

- 한 행동에 정확히 문자 **하나를 선택**하여, **문자열의 맨 앞 또는 맨 뒤로 이동**시킬 수 있다.

- **올바른 순서의 괄호쌍**을 만들 **최소 행동 횟수**를 구하라.

  </br>

### 조건

시간 제한 : 1초

메모리 제한 : 256 MB

</br>

### 입출력

입력

- 첫째 줄에는 테스트 케이스 수 **t(1 ≤ t ≤ 2,000)**이 주어진다.
- 그 다음 줄에는 문자열의 길이 **n (짝수)**과 그 다음 줄에는 문자열 **s** 가 주어진다.

출력

- **최소 행동 횟수**

  </br>

### 풀이과정

괄호쌍 문제는 스택을 이용하면 쉽게 해결할 수 있다.

**(** 가 오면 **+1**, **)** 가 오면 **-1** 을 해주면서 음수가 되면 잘못된 순서인 것이므로, 뒤로 밀어주면 된다.

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        var n = readLine().toInt()
        var queue = LinkedList<Char>()
        readLine().toCharArray().forEach { queue.add(it) }

        var ctr = 0
        var move = 0
        while (queue.isNotEmpty()) {
            var curr = queue.poll()
            when (curr) {
                '(' -> ctr++
                ')' -> ctr--
            }

            if (ctr < 0) {
                ctr = 0
                queue.add(curr)
                move++
            }
        }
        println(move)
    }
}
```

</br>

### 더 나은 풀이

스택에 넣어서 올바른 문자열을 만들 필요도 없이 틀린 개수를 세어 출력하면 해결된다.

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
		var caseNum = readLine().toInt()
		
		while (caseNum-- > 0) {
				var d = 0
				var answer = 0
				readLine().toInt()
				for (s in readLine()) {
						when(s) {
								'(' -> d++
								')' -> d--
						}
						answer = max(answer, -d)  // 음수가 최대일 때(즉, 틀린 개수)를 기억해놓는다.
				}
				println(answer)
		}
}
```

</br>

## D : Zero Remainder Array (Difficulity : 1400)

### 문제

- **n** 개의 양의 정수로 이루어진 배열 **a**가 주어진다.

- 초기에, x = 0이며 한 행동 중에, 두 연산 중 한 가지를 할 수 있다.

  1. 배열 **a** 의 원소 중 딱 하나를 골라 **x** 만큼 더하고 **x 값을 1 증가**시킨다.
  2. 그냥 **x 값을 1 증가**시킨다.

- 첫 번째 연산은 각 원소에 **한 번 이상으로는 행할 수 없다.**

- 배열 **a** 의 원소가 모두 **k** 로 나누어 떨어지는 **최소 연산 횟수**를 구하라.

  </br>

### 조건

시간 제한 : 2초

메모리 제한 : 256 MB

</br>

### 입출력

<u>입력</u>

- 첫째 줄에는 테스트 케이스 수 **t(1 ≤ t ≤ 20000)**가 주어진다.
- 각 테스트 케이스의 첫째 줄에는 **n** 과 **k (1 ≤ n ≤ 200,000, 1 ≤ k ≤ 1,000,000,000)**가 주어진다.
- 다음 줄에는 배열 **a** 의 원소가 주어진다. **(1 ≤ a[i] ≤ 1,000,000,000)**

<u>출력</u>

- **최소 연산 횟수**

  </br>

### 풀이과정

0부터 **k - 1**까지의 한 싸이클로 각 나머지 하나씩을 해결할 수 있다는 것을 알았다.

배열 **a** 의 모든 원소들을 **a[i]** 에 대해 **% k** 연산을 수행, 그 값들의 빈도를 기록해야 했다.

</br>

이때, **이미 a[i] % k == 0인 원소들은 계산하지 않아도 되는 점을 유의하자.**

</br>

빈도를 기록하는 과정에서 a[i] % k가 가질 수 있는 값은 **1부터 k-1**까지였다.

그러나, **k**는 최대 **1,000,000,000**이 올 수 있기 때문에 배열로 저장해버리면 메모리를 엄청나게 차지한다.

그래서 Map을 사용하여 구현했다. **(이 점을 간과하여 꽤나 고생했다.)**

</br>

Map 중 Value가 가장 높고 Key는 제일 작은 수를 구해야 했다.

a[i] % k로 빈도를 기록하고 연산을 수행하면 Key가 가장 작은 값이 마지막에 연산되므로 그 중 Key는 제일 작아야

**answer = value \* k - key + 1** 이 성립한다.

```kotlin
fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        var (num, k) = readLine().split(" ").map(String::toInt)
        var numbers = readLine().split(" ").map(String::toInt)

        var maps = HashMap<Int, Int>()
        for (i in numbers) {
            if (i % k == 0) continue
            maps[i % k] = (maps[i % k] ?: 0) + 1
        }

        var maxValue = -1
        var key = Int.MAX_VALUE

        for ( (n, f) in maps) {
            if (maxValue < f) {
                maxValue = f
                key = n
            }
            else if (maxValue == f && key > n) {
                key = n
            }
        }

        if (maxValue == -1)
            println(0)
        else
            println(maxValue.toLong() * k - key + 1)
    }
}
```

</br>

### 더 나은 풀이

**Kotlin**의 함수들을 잘 이용한 풀이가 있었다.

특히 **map**의 **maxWith**을 **compareBy**를 통해 **value** 값으로 먼저 비교하고, 그 중 **-it.key** 를 통해 작은 것을 골라내는 것이 인상 깊었다.

또한, 이 문제에서 자주 사용하는 `readLine().split(" ").map { it.toInt() }`를 `readInts()`로 함수화한 것도 인상 깊었다.

```kotlin
private fun readInts() = readLine()!!.split(' ').map { it.toInt() }

fun main() = repeat(readLine()!!.toInt()) {
    val k = readInts()[1]
    readInts()
        .map { it % k }
        .filter { it != 0 }
        .groupingBy { it }
        .eachCount()
        .maxWith(compareBy({ it.value }, { -it.key }))
        .let { println(if (it == null) 0 else it.value.toLong() * k - it.key + 1) }
}
```

> groupingBy()

```kotlin
@SinceKotlin("1.1")
public inline fun <T, K> Iterable<T>.groupingBy(crossinline keySelector: (T) -> K): Grouping<T, K> {
    return object : Grouping<T, K> {
        override fun sourceIterator(): Iterator<T> = this@groupingBy.iterator()
        override fun keyOf(element: T): K = keySelector(element)
    }
}

println(listOf("a", "bc", "de").groupingBy{it.length}.eachCount())
// {1=1, 2=2}
println(listOf("a", "bc", "de").groupBy {it.length})
// {1=[a], 2=[bc, de]}
```

</br>

## E1 : Reading Books (easy version)

## E2 : Reading Books (hard version)

## F : Cyclic Shifts Sorting