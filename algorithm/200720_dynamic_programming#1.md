# 동적 계획법

### 1. 중복 되는 부분 문제

- 큰 의미에서 분할 정복과 같은 접근 방식을 의미한다.
- 동적 계획법과 분할 정복의 차이가 발생하는 부분은 `문제를 나누는 방식`이다.

    → 문제를 해결하기 위한 `점화식`을 찾아낸 후 점화식의 항을 밑에서 부터 차례로 구해 나가 답을 알아내는 형태
    
</br>

- 함수의 결과를 저장하는 장소를 마련해 두고, 한 번 계산한 값을 저장해 뒀다 재활용하는 최적화 기법을 `메모이제이션` 이라고 부른다.

- 두 번 이상 반복 계산되는 `부분 문제들의 답을 미리 저장`함으로써 속도의 향상을 꾀하는 알고리즘 설계 기법

</br>

### 2. 메모이제이션을 적용할 수 있는 경우

- 함수의 반환 값이 그 입력 값만으로 결정되는 지의 여부를 `'참조적 투명성'` 이라고 한다.

- 즉, 입력이 고정되어 있을 때 그 결과가 항상 같은 함수들을 `참조적 투명 함수`라고 부른다.

- 당연하게도 메모이제이션은 참조적 투명 함수의 경우에만 적용할 수 있다.

</br>

### 3. 메모이제이션 구현 패턴

~~이 패턴을 사용하기는 아직 이해가 충분치 않다고 생각한다. (200720 PM 09:24)~~

> 한 가지 패턴을 정해두고 항상 같은 형태로 구현하기로 하면 작성하기도, 버그를 찾기도 쉬워진다.

- 다음과 같은 재귀 함수가 있다고 하자.

```cpp
// a와 b는 각각 [0, 2500) 구간 안의 정수
// 반환 값은 항상 int형 안에 들어가는 음이 아닌 정수
int someObscureFunction(int a, int b);
```

- `someObscureFunction()`은 한 번 계산하는데 굉장히 시간이 오래 걸리는 참조적 투명 함수라고 가정하자.

- `메모이제이션 구현 과정`

    1. **항상 기저 사례를 제일 먼저 처리한다.**
    
        - 입력이 범위를 벗어난 경우 등을 기저 사례로 처리하면 매우 유용한데, 기저 사례를 먼저 확인하지 않고 `cache[]`에 접근하면 범위를 벗어나는 등의 오류가 있을 수 있기 때문이다.
        
    2. 함수의 반환 값이 항상 **0**이라는 점을 이용해 `cache[]` 를 모두 **-1**로 초기화했다.
    
        - `cache[]` 의 해당 위치에 적혀 있는 값이 **-1** 이라면 이 값은 계산된 반환 값일리 없다.

```cpp
// 전부 -1로 초기화해둔다.
int cache[2500][2500];

int someObscureFunction(int a, int b) {
    // 기저 사례를 처음에 처리
    if (...) return ...;
		
    // (a, b)에 대한 답을 구한 적이 있으면 곧장 반환
    int& ret = cache[a][b];
    if (ret != -1) return ret;
		
    // 답 계산
    ...
    return ret;
}
```

</br>

### 4. 예제 : 외발 뛰기

1. **재귀 호출에서 시작하기**

    - `동적 계획법 알고리즘`을 만드는 첫 단계는 해당 문제를 재귀적으로 해결하는 `완전 탐색 알고리즘`을 만드는 것이다.
    
    - `jump(y, x)` = `(y, x)`에서부터 맨 마지막까지 도달할 수 있는지 여부 반환

    ```cpp
    // 외발 뛰기 재귀 호출 알고리즘
    int n, board[100][100];
    bool jump(int y, int x) {
        // 기저 사례 : 게임판을 벗어난 경우
        if (y >= n || x >= n) return false;
        // 기저 사례 : 마지막 칸에 도착한 경우
        if (y == n-1 && x == n-1) return true;

        int jumpSize = board[y][x];
        return jump(y + jumpSize, x) || jump(y, x + jumpSize);
    }
    ```

2. **메모이제이션 적용하기**

    ```cpp
    // 외발 뛰기 동적 계획법 알고리즘
    int n, board[100][100];
    int cache[100][100];
    int jump2(int y, int x) {
        // 기저 사례 처리
        if (y >= n || x >= n) return 0;
        if (y == n-1 && x == n-1) return 1;
    		
        // 메모이제이션
        int& ret = cache[y][x];
        if (ret != -1) return ret;

        int jumpSize = board[y][x];
        return ret = (jump2(y + jumpSize, x) || jump2(y, x + jumpSize));
    }
    ```

    - **중복되는 부분 문제를 제거**
    
3. **동적 계획법 레시피**

    - 대개 동적 계획법 알고리즘의 구현은 다음과 같은 두 단계로 이루어진다.
    
        1. 주어진 문제를 완전 탐색을 이용해 해결
        
        2. 중복된 부분 문제를 한 번만 계산하도록 메모이제이션 적용
        
4. **다른 구현 방법에 관하여**

    - 물론 재귀 호출을 이용하지 않고도 동적 계획법 알고리즘을 구현할 수 있다.
    
    - 이런 방법을 `반복적 동적 계획법`이라고 부른다.
    
</br>

### 5. 문제 : 계단 오르기

[https://www.acmicpc.net/problem/2579](https://www.acmicpc.net/problem/2579)

### 1) 완전 탐색으로 풀기

1. **기저 사례**

    - 마지막 계단을 넘어가는 경우
    
    - 마지막 계단을 밟은 경우

```kotlin
import kotlin.math.max

fun main() = with(System.`in`.bufferedReader()) {
    val stairs = IntArray(readLine().toInt())

    for (i in stairs.indices)
        stairs[i] = readLine().toInt()

    println(climb(0, 0, 0, stairs))
}

fun climb(idx: Int, cnt: Int, sum: Int, stairs: IntArray): Int {
    if (idx >= stairs.size) // 계단을 벗어난 경우
        return -1

    if (idx == stairs.size - 1) // 마지막 계단인 경우
        return sum + stairs[idx]

    var score = 0

    // 1. 한 계단 오르기
    if (cnt != 2) //
        score = max(score, climb(idx + 1, cnt + 1, sum + stairs[idx], stairs))

    // 2. 두 계단 오르기
    score = max(score, climb(idx + 1, 0, sum, stairs))

    return score
}
```

### 2) 메모이제이션 적용하기

- `cache[i]` = i번째 계단에 올랐을 때 최대 점수

- 그러나, `메모이제이션 구현 패턴`을 이용하여 `재귀 호출 알고리즘`을  바꾸는 것에 실패

### 3) 해설 참조

[https://blog.encrypted.gg/737?category=773649](https://blog.encrypted.gg/737?category=773649)

- `D[i]` = **i** 번쨰 계단까지 올라섰을 때의 점수 합의 최댓값

- `D[1]`은 자명하게 `S[1]`이다.

- 그럼 `K = 2 to N` 에 대해 `D[K]`를 어떻게 계산할 것인가?

- `D[2]`는 1번째, 2번째 계단을 다 밟으면 되니 30일 것이다.

- `D[3]`은 연속한 세 계단을 모두 밟아서는 안된다는 제약 조건으로 인해 `20 + 15 = 35`가 된다. 그런데 지금 `D[i]`의 값을 가지고는 점화식을 세우고 싶어도 연속한 세 계단을 모두 밟아서는 안된다는 `제약 조건을 점화식에 넣을 수 없다.`


1. **DP 테이블 설계 변경**

- `D[i][j]` = 현재까지 **j** 개의 계단을 연속해서 밟고 **i** 번째 계단까지 올라섰을 때의 점수 합의 최댓값, 단 **i** 번째 계단은 반드시 밟아야 함.

- `D[1][1]`은 자명하게 `S[1]`이고 `D[1][2]`는 **0**이다. 그리고 **j** 는 **1 혹은 2**이다.

- (10, 20, 15, 25, 10, 20)을 예시로 DP 테이블을 그려보면

    `D[i][1]` = `max(D[i-2][1], D[i-2][2])` + `S[i]`

    `D[i][2]` = `D[i-1][1]` + `S[i]`

    점화식을 얻을 수 있다.

```kotlin
import kotlin.math.max

fun main() = with(System.`in`.bufferedReader()) {
    val size = readLine().toInt()
    val stairs = IntArray(size)
    val cache = Array(size) { IntArray(2) }

    for (i in stairs.indices)
        stairs[i] = readLine().toInt()

    cache[0][0] = stairs[0]

    for (i in 1 until size) {
        cache[i][0] = (if (i - 2 < 0) 0 else max(cache[i-2][0], cache[i-2][1])) + stairs[i]

        cache[i][1] = cache[i-1][0] + stairs[i]
    }

    println(max(cache[size-1][0], cache[size-1][1]))
}
```
