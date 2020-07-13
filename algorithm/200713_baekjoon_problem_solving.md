# 200713_baekjoon Problem Solving

### 목차
[1. BOJ 5397 키로거](#boj-5397-keylogger)
</br>
[2. BOJ 1158 요세푸스 문제](#boj-1158)
</br>
[3. BOJ 10828 스택](#boj-10828-stack)
</br>
[4. BOJ 10845_큐](#boj-10845-queue)
</br>
[5. BOJ 2339 석판 자르기](#boj-2339)
</br>

# BOJ 5397 KeyLogger

## 1. 개요

https://www.acmicpc.net/problem/5397

## 2. 코드

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()){
    var caseNum = readLine().toInt()

    while (caseNum-- > 0) {
        val symbol = LinkedList<Char>()
        val cursor = symbol.listIterator()

        readLine().forEach {
            when(it) {
                '-' -> delete(cursor)
                '<' -> moveLeft(cursor)
                '>' -> moveRight(cursor)
                else -> cursor.add(it)
            }
        }
        println(symbol.toString())
    }
}

// 이전 글자가 존재한다면 삭제
fun delete(cursor: MutableListIterator<Char>) {
    if (cursor.hasPrevious()) {
        cursor.previous()
        cursor.remove()
    }
}

// 왼쪽 이동
fun moveLeft(cursor: MutableListIterator<Char>) {
    if (cursor.hasPrevious())
        cursor.previous()
}

// 오른쪽 이동
fun moveRight(cursor: MutableListIterator<Char>) {
    if (cursor.hasNext())
        cursor.next()
}
```

## 3. 설명

- LinkedList의 listIterator() 함수를 이용하여 LinkedList를 탐색할 수 있는 MutableListIterator 사용
- listIterator() 함수를 사용하면 cursor의 위치는 **항상 문제에서의 커서 위치와 같다**.

## 4. 결과

![image](https://user-images.githubusercontent.com/24761073/87264801-f1c0c800-c4fb-11ea-9ed1-d7779dcf89bc.png)



# BOJ 1158

## 1. 개요

https://www.acmicpc.net/problem/1158

## 2. 코드

```kotlin
import java.util.*

/** 
	N : N명의 사람
  K : K번째 사람
	person : N명의 사람의 순서를 저장한 LinkedList
*/
fun main() = with(System.`in`.bufferedReader()) {
    var (N, K) = readLine().split(" ").map { it.toInt() }
    val person = LinkedList<Int>()
    for (i in 1..N) {
        person.add(i)
    }

    val sb = StringBuilder()

    var idx = 0
    for (i in 1..N) {
        idx += K - 1
        idx %= person.size

        if (i != N)
            sb.append("${person.removeAt(idx)}, ")
        else
            sb.append("${person.removeAt(idx)}")
    }

    println("<$sb>")
}
```

## 3. 설명

- N명의 사람은 이어져 있다. **(맨 끝에서 다음은 맨 처음 사람)**
- 즉, 6명이 있을 때 7번째 사람은 1번째 사람이 되는 것이다.
- 이것을 수식으로 표현했을 때 `실제 순서 = 계산한 순서 % N명` 이다.
- K번째 사람을 제거하는 것을 N번 반복하므로 N번 동안 (idx + K - 1) % N에 해당하는 사람을 제거하면 된다.
- 그 후, 인덱스를 갱신한다.

## 4. 결과

![image](https://user-images.githubusercontent.com/24761073/87264868-216fd000-c4fc-11ea-960d-087f1712baf2.png)



# BOJ 10828 Stack

### 1. 개요

https://www.acmicpc.net/problem/10828

### 2. 코드

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()
    val stack = Stack<Int>()

    while (caseNum-- > 0) {
        val commands = readLine().split(" ")

        when (commands[0]) {
            "push" -> stack.push(commands[1].toInt())
            "pop" -> println(if (stack.isNotEmpty()) stack.pop() else -1)
            "size" -> println(stack.size)
            "empty" -> println(if (stack.isEmpty()) 1 else 0)
            "top" -> println(if (stack.isNotEmpty()) stack.peek() else -1)
        }
    }
}
```

### 3. 설명

- 각 로직에 따라 스택 메소드를 사용

### 4. 결과

![image](https://user-images.githubusercontent.com/24761073/87264955-5bd96d00-c4fc-11ea-9f08-8b93fbd8af95.png)



# BOJ 10845 Queue

### 1. 개요

https://www.acmicpc.net/problem/10845

### 2. 코드

```kotlin
import java.util.*

fun main() = with(System.`in`.bufferedReader()) {
    var caseNum = readLine().toInt()
    val queue = LinkedList<Int>()

    while (caseNum-- > 0) {
        val commands = readLine().split(" ")

        when (commands[0]) {
            "push" -> queue.add(commands[1].toInt())
            "pop" -> println(if (queue.isNotEmpty()) queue.poll() else -1)
            "size" -> println(queue.size)
            "empty" -> println(if (queue.isEmpty()) 1 else 0)
            "front" -> println(if (queue.isNotEmpty()) queue.first else -1)
            "back" -> println(if (queue.isNotEmpty()) queue.last else -1)
        }
    }
}
```

### 3. 설명

- 각 로직에 따라 LinkedList의 메소드를 사용

### 4. 결과

![image](https://user-images.githubusercontent.com/24761073/87265019-80354980-c4fc-11ea-8e40-804f1eaac849.png)



# BOJ 2339

### 1. 문제

- https://www.acmicpc.net/problem/2339
- 석판에 있는 불순물과 보석 결정체의 정보가 주어질 때, 이 석판에서 불순물들을 없애면서 석판을 나누되, 각 조각에 반드시 하나의 보석 결정체만이 들어 있도록 석판을 나누는 방법이 모두 몇 가지 존재하는지 계산하라.

### 2. 조건

시간 제한 : 2초

메모리 제한 : 128 MB

### 3. 입출력

입력

- 첫 번째 줄에는 석판의 크기 **N** **(1 ≤ N ≤ 20)**
- 다음 줄부터 **N** 줄에 걸쳐서 석판의 상태가 입력으로 들어온다. **(1 : 불순물, 2 : 보석 결정체, 0 : 빈 공간)**

출력

- **가능한 경우의 수**, 없으면 **-1**을 출력

### 4. 풀이과정

1. `분할 정복 알고리즘`으로 접근한다.
   - **재귀적**으로 석판을 **두 개**씩 잘라낸다. (가로 또는 세로 방향으로 잘라본다. 그러나 이전에 잘라본 방향으로는 자를 수 없다.)
2. 석판을 **재귀적으로 자를 때**, 잘라낸 석판이 **잘못 자른 경우**인지, **제대로 자른 경우**인지, **더 잘라봐야 하는지** 판단한다.
   - 잘못 자른 경우는 **석판에 보석 결정체가 없거나, 보석 결정체가 1개이고 불순물이 1개 이상 있거나, 보석 결정체가 두 개 이상인데 불순물이 없는 경우**이다.
   - 제대로 자른 경우는 **보석 결정체가 딱 1개 있고, 불순물이 없는 경우**이다.
   - 나머지 경우는 더 잘라봐야 한다.
3. 더 잘라보는 작업은 **이전 방향**을 검토하여 **가지고 있는 불순물**에 대해서 경우의 수를 구한다.
   - 경우의 수는 **각 불순물**에 대해서, `가로(또는 세로)로 자른 석판 1의 경우의 수 * 석판 2의 경우의 수`이다.

```kotlin
const val HORIZONTAL = 10
const val VERTICAL = 11

fun main() = with(System.`in`.bufferedReader()) {
    val size = readLine().toInt()
    val arr = Array<Array<Int>>(size) { Array<Int>(size) { 0 } }

    for (i in 1..size) {
        arr[i-1] = readLine().split(" ").map { it.toInt() }.toTypedArray()
    }

    val result = cut(arr, 0)
    println(if (result == 0) -1 else result)
}

fun cut(arr: Array<Array<Int>>, direction: Int): Int {
    val jewelNum = getJewelNum(arr)
    val trashes = getTrashes(arr)

    if (jewelNum == 0 || (jewelNum == 1 && trashes.size > 0) || (jewelNum >= 2 && trashes.isEmpty()))
        return 0

    if (jewelNum == 1 && trashes.isEmpty())
        return 1

    var ret = 0

    for (t in trashes) {
        if (direction != HORIZONTAL && canDivide(arr, t, HORIZONTAL)) {
            val slate1 = divideArray(arr, 0, 0, arr[0].size, t.y)
            val slate2 = divideArray(arr, 0, t.y+1, arr[0].size, arr.size)

            ret += cut(slate1, HORIZONTAL) * cut(slate2, HORIZONTAL)
        }
        if (direction != VERTICAL && canDivide(arr, t, VERTICAL)) {
            val slate1 = divideArray(arr, 0, 0, t.x, arr.size)
            val slate2 = divideArray(arr, t.x+1, 0, arr[0].size, arr.size)

            ret += cut(slate1, VERTICAL) * cut(slate2, VERTICAL)
        }
    }
    return ret
}

fun getJewelNum(arr: Array<Array<Int>>): Int {
    var num = 0
    for (i in arr.indices) {
        num += arr[i].filter { it == 2 }.count()
    }
    return num
}

fun getTrashes(arr: Array<Array<Int>>): MutableList<Trash> {
    val trashes = mutableListOf<Trash>()

    for (i in arr.indices) {
        arr[i].forEachIndexed { index, value ->
            if (value == 1)
                trashes.add(Trash(index, i))
        }
    }
    return trashes
}

fun divideArray(arr: Array<Array<Int>>, fromX: Int, fromY: Int, toX: Int, toY: Int): Array<Array<Int>> {
    val newArr = Array<Array<Int>> (toY - fromY) { Array<Int>(toX- fromX) {0} }

    for ((cY, i) in (fromY until toY).withIndex()) {
        for ((cX, j) in (fromX until toX).withIndex()) {
            newArr[cY][cX] = arr[i][j]
        }
    }
    return newArr
}

fun canDivide(arr: Array<Array<Int>>, t: Trash, direction: Int): Boolean {
    if (direction == HORIZONTAL) {
        for (i in arr[t.y]) {
            if (i == 2)
                return false
        }
    }
    else {
        for (i in arr.indices) {
            if (arr[i][t.x] == 2)
                return false
        }
    }
    return true
}

class Trash {
    var x: Int
    var y: Int

    constructor(x: Int, y: Int) {
        this.x = x
        this.y = y
    }
}
```