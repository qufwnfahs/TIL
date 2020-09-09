## 문제 구성

- [1. (BOJ) 경사로](#문제-1-경사로)
- [2. (BOJ) 톱니바퀴](#문제-2-톱니바퀴)
- [3. (BOJ) 감시](#문제-3-감시)
- [4. (BOJ) 사다리 조작](#문제-4-사다리-조작)
- [5. (BOJ) 드래곤 커브](#문제-5-드래곤-커브)
- [6. (Kakao 2018) 셔틀버스](#문제-6-셔틀버스)
- [7. (Kakao 2018) 추석 트래픽](#문제-7-추석-트래픽)


## 문제 1 경사로

### 문제

[https://www.acmicpc.net/problem/14890](https://www.acmicpc.net/problem/14890)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.abs

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (n, wayLength) = readInts()
    val arr = Array(n) { IntArray(n) }
    val dy = arrayOf(0, 1)
    val dx = arrayOf(1, 0)
    var answer = 0

    fun input() {
        for (y in 0 until n) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until n) {
                arr[y][x] = st.nextToken().toInt()
            }
        }
    }
    input()

    fun checkRoad1(y: Int) {
        val counter = IntArray(n)
        var index = 0

        while (index < n - 1) {
            if (arr[y][index] == arr[y][index + 1]) index++
            else {
                if (abs(arr[y][index] - arr[y][index + 1]) >= 2) return

                if (arr[y][index] < arr[y][index + 1]) {
                    // 경사로 놓기
                    if (index - wayLength + 1 < 0) return

                    for (i in 0 until wayLength) {
                        if (counter[index - i] != 0 || arr[y][index] != arr[y][index - i]) return
                        counter[index - i]++
                    }
                    index++
                }
                else {
                    // 경사로 놓기
                    if (index + wayLength >= n) return

                    for (i in 1..wayLength) {
                        if (counter[index + i] != 0 || arr[y][index + 1] != arr[y][index + i]) return
                        counter[index + i]++
                    }
                    index += wayLength
                }
            }
        }
        answer++
    }

    fun checkRoad2(x: Int) {
        val counter = IntArray(n)
        var index = 0

        while (index < n - 1) {
            if (arr[index][x] == arr[index + 1][x]) index++
            else {
                if (abs(arr[index][x] - arr[index + 1][x]) >= 2) return

                if (arr[index][x] < arr[index + 1][x]) {
                    // 경사로 놓기
                    if (index - wayLength + 1 < 0) return

                    for (i in 0 until wayLength) {
                        if (counter[index - i] != 0 || arr[index][x] != arr[index - i][x]) return
                        counter[index - i]++
                    }
                    index++
                }
                else {
                    // 경사로 놓기
                    if (index + wayLength >= n) return

                    for (i in 1..wayLength) {
                        if (counter[index + i] != 0 || arr[index + 1][x] != arr[index + i][x]) return
                        counter[index + i]++
                    }
                    index += wayLength
                }
            }
        }
        answer++
    }

    // 세로 방향 확인
    for (y in 0 until n) {
        checkRoad1(y)
    }

    // 가로 방향 확인
    for (x in 0 until n) {
        checkRoad2(x)
    }
    print(answer)
}
```

### 문제 해설

만들어질 수 있는 길에 대해 이어진 요소끼리의 차이가 **2 이상**이거나, **뒤에 요소가 더 클 때의 경사로 잇는 법, 뒤에 요소가 더 작을 때 경사로 잇는 법**, 이 세 가지 조건을 고려하면 문제를 해결할 수 있다.

</br>

## 문제 2 톱니바퀴

### 문제

[https://www.acmicpc.net/problem/14891](https://www.acmicpc.net/problem/14891)

### 코드

```kotlin
import java.lang.StringBuilder

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val gears = Array(5) { IntArray(16) }
    val gearsIndex = IntArray(5)
    var answer = 0

    fun input() {
        for (i in 1..4) {
            val str = readLine()
            for (j in str.indices) {
                gears[i][j] = str[j].toInt() - 48
                gears[i][j + 8] = gears[i][j]
            }
        }
    }
    input()

    fun checkWhoWillRotate(number: Int, direction: Int, visited: BooleanArray, rotateInfo: IntArray) {
        visited[number] = true
        rotateInfo[number] = direction

        if (number - 1 >= 1 && !visited[number - 1]) {
            val currLeft = gears[number][gearsIndex[number] + 6]
            val targetRight = gears[number - 1][gearsIndex[number - 1] + 2]

            if (currLeft != targetRight) {
                checkWhoWillRotate(number - 1, -direction, visited, rotateInfo)
            }
        }
        if (number + 1 <= 4 && !visited[number + 1]) {
            val currRight = gears[number][gearsIndex[number] + 2]
            val targetLeft = gears[number + 1][gearsIndex[number + 1] + 6]

            if (currRight != targetLeft) {
                checkWhoWillRotate(number + 1, -direction, visited, rotateInfo)
            }
        }
    }

    fun rotate(rotateInfo: IntArray) {
        for (i in 1..4) {
            when (rotateInfo[i]) {
                1 -> {
                    gearsIndex[i]--
                    if (gearsIndex[i] < 0) gearsIndex[i] += 8
                }
                -1 -> gearsIndex[i] = (gearsIndex[i] + 1) % 8
            }
        }
    }

    val cmdN = readInt()

    repeat(cmdN) {
        val (_number, direction) = readInts()
        val rotateInfo = IntArray(5)

        checkWhoWillRotate(_number, direction, BooleanArray(5), rotateInfo)
        rotate(rotateInfo)
    }

    fun calculate() {
        for (i in 1..4) {
            if (gears[i][gearsIndex[i]] == 1) answer += (1 shl (i-1))
        }
    }
    calculate()
    print(answer)
}
```

### 문제 해설

톱니바퀴를 돌리기 전, 먼저 누가 돌아갈 것인지 모두 계산한 뒤에 그 정보를 바탕으로 톱니바퀴를 회전

`원형인 톱니바퀴를 2배로 늘려 직선처럼 계산`하면 쉽게 문제를 해결할 수 있다.

</br>

## 문제 3 감시

### 문제

[https://www.acmicpc.net/problem/15683](https://www.acmicpc.net/problem/15683)

### 코드

```kotlin
import java.lang.StringBuilder
import java.util.*
import kotlin.math.min

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (row, col) = readInts()
    var board = Array(row) { IntArray(col) }
    val cctv = mutableListOf<Pair<Int, Int>>()
    var answer = Int.MAX_VALUE

    fun input() {
        for (y in 0 until row) {
            val st = StringTokenizer(readLine(), " ")
            for (x in 0 until col) {
                board[y][x] = st.nextToken().toInt()
                if (board[y][x] in 1..5) cctv.add(Pair(y, x))
            }
        }
    }
    input()

    fun left(y: Int, x: Int, copyBoard: Array<IntArray>): Int {
        var count = 0
        var index = 1

        while (true) {
            val xPos = x - index

            if (xPos < 0) break
            if (copyBoard[y][xPos] == 6) break

            if (copyBoard[y][xPos] == 0) {
                copyBoard[y][xPos] = -1
                count++
            }
            index++
        }
        return count
    }

    fun right(y: Int, x: Int, copyBoard: Array<IntArray>): Int {
        var count = 0
        var index = 1

        while (true) {
            val xPos = x + index

            if (xPos >= col) break
            if (copyBoard[y][xPos] == 6) break

            if (copyBoard[y][xPos] == 0) {
                copyBoard[y][xPos] = -1
                count++
            }
            index++
        }
        return count
    }

    fun top(y: Int, x: Int, copyBoard: Array<IntArray>): Int {
        var count = 0
        var index = 1

        while (true) {
            val yPos = y - index

            if (yPos < 0) break
            if (copyBoard[yPos][x] == 6) break

            if (copyBoard[yPos][x] == 0) {
                copyBoard[yPos][x] = -1
                count++
            }
            index++
        }
        return count
    }

    fun bottom(y: Int, x: Int, copyBoard: Array<IntArray>): Int {
        var count = 0
        var index = 1

        while (true) {
            val yPos = y + index

            if (yPos >= row) break
            if (copyBoard[yPos][x] == 6) break

            if (copyBoard[yPos][x] == 0) {
                copyBoard[yPos][x] = -1
                count++
            }
            index++
        }
        return count
    }

    fun progress(y: Int, x: Int, copyBoard: Array<IntArray>, cmd: Int): Int {
        return when (cmd) {
            0 -> top(y, x, copyBoard)
            1 -> bottom(y, x, copyBoard)
            2 -> left(y, x, copyBoard)
            3 -> right(y, x, copyBoard)
            else -> 0
        }
    }

    fun DFS(cnt: Int, visited: BooleanArray, target: Array<IntArray>) {
        if (cnt == cctv.size) {
            var sum = 0
            target.forEach { ints ->
                sum += ints.filter { it == 0 }.count()
            }

            answer = min(answer, sum)
            return
        }

        val (y, x) = cctv[cnt]
        when (board[y][x]) {
            1 -> {
                for (i in 0 until 4) {
                    var copyBoard = copyMatrix(target)
                    progress(y, x, copyBoard, i)

                    DFS(cnt + 1, visited, copyBoard)
                }
            }
            2 -> {
                var cmd = arrayOf(0, 1, 2, 3)

                for (i in 0 until 4 step 2) {
                    var copyBoard = copyMatrix(target)

                    progress(y, x, copyBoard, cmd[i])
                    progress(y, x, copyBoard, cmd[i + 1])

                    DFS(cnt + 1, visited, copyBoard)
                }
            }
            3 -> {
                var cmd = arrayOf(0, 2, 0, 3, 1, 2, 1, 3)

                for (i in 0 until 4) {
                    var copyBoard = copyMatrix(target)

                    for (j in 0 until 2) {
                        progress(y, x, copyBoard, cmd[i * 2 + j])
                    }
                    DFS(cnt + 1, visited, copyBoard)
                }
            }
            4 -> {
                var cmd = arrayOf(2, 0, 3, 0, 3, 1, 3, 1, 2, 1, 2, 0)

                for (i in 0 until 4) {
                    var copyBoard = copyMatrix(target)

                    for (j in 0 until 3) {
                        progress(y, x, copyBoard, cmd[i * 3 + j])
                    }
                    DFS(cnt + 1, visited, copyBoard)
                }
            }
            5 -> {
                var copyBoard = copyMatrix(target)

                for (i in 0 until 4) {
                    progress(y, x, copyBoard, i)
                }
                DFS(cnt + 1, visited, copyBoard)
            }
        }
    }

    DFS(0, BooleanArray(cctv.size), copyMatrix(board))

    print(answer)
}

private fun copyMatrix(board: Array<IntArray>): Array<IntArray> {
    val result = Array(board.size) { IntArray(board[0].size) }
    board.forEachIndexed { index, _ -> result[index] = board[index].copyOf() }

    return result
}
```

### 문제 해설

5가지 종류의 CCTV마다 다른 경우의 수를 가진다.

이 `각 CCTV가 놓이는 경우의 수`를 모두 **완전 탐색**하여 문제를 해결할 수 있다.

즉, 한 CCTV가 놓이는 각 방향마다 DFS를 수행한다.

</br>

## 문제 4 사다리 조작

### 문제

[https://www.acmicpc.net/problem/15684](https://www.acmicpc.net/problem/15684)

### 코드

```kotlin
import java.lang.StringBuilder
import kotlin.math.min

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val (col, ladderN, row) = readInts()
    var arr = Array(row + 1) { IntArray(col + 1) }
    var answer = 4

    fun input() {
        repeat(ladderN) {
            val (y, x) = readInts()
            arr[y][x] = 1
        }
    }
    input()

    fun doLadder(): Boolean {
        for (i in 1..col) {
            var yPos = 1
            var xPos = i

            while (true) {
                if (yPos > row) break

                if (arr[yPos][xPos] == 1) {
                    xPos++
                }
                else if (arr[yPos][xPos - 1] == 1) {
                    xPos--
                }
                yPos++
            }

            if (xPos != i) return false
        }
        return true
    }

    fun combination(volume: Int, cnt: Int, y: Int, x: Int) {
        if (cnt == volume) {
            if (doLadder()) {
                answer = min(answer, cnt)
            }
            return
        }

        for ( _y in y..row) {
            for (_x in 1 until col) {
                if (_y == y && _x < x) continue
                if (arr[_y][_x] == 1 || arr[_y][_x - 1] == 1 || (_x + 1 <= col && arr[_y][_x + 1] == 1) ) continue

                arr[_y][_x] = 1
                combination(volume, cnt + 1, _y, _x)
                arr[_y][_x] = 0
            }
        }
    }

    if (doLadder()) {
        answer = 0
    }
    else {
        for (i in 1..3) {
            combination(i, 0, 1, 1)
        }
    }
    print(if (answer < 4) answer else - 1)
}
```

### 문제 해설

가로선이 놓일 수 있는 **모든 조합**을 구하여 해결하는 문제

최대 3개까지 놓을 수 있으므로 `1개부터 3개까지의 조합`을 모두 계산하여 각각 사다리 타기를 진행

사다리 타기가 성공한 최소 개수를 출력한다.

</br>

## 문제 5 드래곤 커브

### 문제

[https://www.acmicpc.net/problem/15685](https://www.acmicpc.net/problem/15685)

### 코드

```kotlin
import java.lang.StringBuilder

private val out = StringBuilder()
private val br = System.`in`.bufferedReader()

private fun readLine() = br.readLine()!!
private fun readInt() = br.readLine().toInt()
private fun readInts() = br.readLine().split(" ").map { it.toInt() }

fun main() {
    val arr = Array(101) { IntArray(101) }
    val curveN = readInt()
    val curves = Array(curveN) { mutableListOf<Int>() } // curve의 정보
    val dy = arrayOf(0, -1, 0, 1)
    val dx = arrayOf(1, 0, -1, 0)
    var answer = 0

    fun makeCurve(d: Int, g: Int, list: MutableList<Int>) {
        for (i in 0..g) {
            if (i == 0) {
                list.add(d)
            }
            else {
                for (j in list.size - 1 downTo 0) {
                    list.add((list[j] + 1) % 4)
                }
            }
        }
    }

    fun drawCurve(y: Int, x: Int, list: MutableList<Int>) {
        var yPos = y
        var xPos = x
        arr[yPos][xPos] = 1

        for (d in list) {
            yPos += dy[d]
            xPos += dx[d]

            arr[yPos][xPos] = 1
        }
    }

    fun input() {
        for (i in 0 until curveN) {
            val (col, row, d, g) = readInts()
            makeCurve(d, g, curves[i])
            drawCurve(row, col, curves[i])
        }
    }
    input()

    fun calculate() {
        for (y in 0 until arr.size - 1) {
            for (x in 0 until arr[0].size - 1) {
                if (arr[y][x] == 1 && arr[y][x + 1] == 1 && arr[y + 1][x] == 1 && arr[y + 1][x + 1] == 1)
                    answer++
            }
        }
    }
    calculate()
    print(answer)
}
```

### 문제 해설

세대가 진행되는 방식엔 규칙이 있다.

이전 세대의 `각 방향 + 1`을 뒤에다 이어 붙이면 다음 세대의 구성 요소가 이루어진다.

이와 같이 커브를 만든 뒤에 그 커브의 정보대로 2차원 배열 상에 점을 찍어준다.

</br>

## 문제 6 셔틀버스

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/17678](https://programmers.co.kr/learn/courses/30/lessons/17678)

### 코드

```kotlin
package programmers.kakao2018;

import java.util.Arrays;

public class 셔틀버스 {
    private int time = 540; // 09 : 00 | 09 * 60 + 00
    private int index = 0;

    public String solution(int n, int t, int m, String[] timetable) {
        Arrays.sort(timetable);
        String answer = progress(n - 1, t, m, timetable);
        return answer;
    }

    private String progress(int n, int t, int m, String[] timetable) {
        int count = 0;

        while (true) {
            if (index >= timetable.length) break;
            if (n <= 0 && count == m - 1) break;
            if (n > 0 && count == m) break;

            int convertToTime = convertToTime(timetable[index]);
            if (convertToTime <= time) {
                count++;
                index++;
            }
            else break;
        }

        if (n > 0) {
            time += t;
            return progress(n - 1, t, m, timetable);
        }
        else {
            if (index < timetable.length && convertToTime(timetable[index]) <= time) {
                return convertToString(convertToTime(timetable[index]) - 1);
            }
            else {
                return convertToString(time);
            }
        }
    }

    private int convertToTime(String str) {
        String[] tokens = str.split(":");
        return Integer.parseInt(tokens[0]) * 60 + Integer.parseInt(tokens[1]);
    }

    private String convertToString(int time) {
        int hour = time / 60;
        int minute = time % 60;

        StringBuilder sb = new StringBuilder();
        if (hour < 10) sb.append("0");
        sb.append(hour);
        sb.append(":");
        if (minute < 10) sb.append("0");
        sb.append(minute);

        return sb.toString();
    }
}
```

### 문제 해설

시간을 `분 형식`으로 치환하여 문제를 해결

`n이 0보다 작을 땐` 크루의 출근 시간을 구해야하므로 고려해야 하는 경우는 다음과 같다.

1. 구해야 하는 시점에 index가 아직 끝나지 않았고 뒷 사람이 막차 시간에 걸치는 경우
2. 구해야 하는 시점에 index가 끝났거나, 뒷 사람이 상관없는 경우

</br>

## 문제 7 추석 트래픽

### 문제

[https://programmers.co.kr/learn/courses/30/lessons/17676](https://programmers.co.kr/learn/courses/30/lessons/17676)

### 코드

```kotlin
package programmers.kakao2018;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class 추석트래픽 {
    private int answer = 0;

    public int solution(String[] lines) {
        List<Work> task = new ArrayList<Work>();
        convert(task, lines);

        Collections.sort(task);
        calculate(task);
        return answer;
    }

    private void calculate(List<Work> task) {
        int count = 0;

        for (Work w : task) {
            if (w.state == 0) {
                count++;
            }
            else count--;

            answer = Math.max(answer, count);
        }
    }

    private void convert(List<Work> task, String[] lines) {
        for (int i = 0; i < lines.length; i++) {
            String[] tokens = lines[i].split(" ");

            int end = convertToTime(tokens[1]);
            int gap = (int) (Double.parseDouble(tokens[2].substring(0, tokens[2].length() - 1)) * 1000);
            int start = end - gap + 1;

            task.add(new Work(start, 0));
            task.add(new Work(end + 999, 1));
        }
    }

    private int convertToTime(String str) {
        int time = 0;
        String[] timeTokens = str.split(":");

        time += Integer.parseInt(timeTokens[0]) * 3600;
        time += Integer.parseInt(timeTokens[1]) * 60;
        time *= 1000;

        time += (int) (Double.parseDouble(timeTokens[2]) * 1000);

        return time;
    }
    class Work implements Comparable<Work> {
        int time;
        int state;

        public Work(int time, int state) {
            this.time = time;
            this.state = state;
        }

        @Override
        public int compareTo(Work w1) {
            if (time != w1.time) {
               return time - w1.time;
            }
            return state - w1.state;
        }
    }
}
```

### 문제 해설

시간을 ms 단위로 치환하여 문제를 해결

한 작업물이 끝난 시각으로부터 **999ms** 안에 다른 작업물이 있으면 그 둘은 동시에 진행되는 작업물이다. 일일이 999ms씩 붙여서 비교하기 보단 `처음부터 작업 끝 시각에 999ms`를 붙여준다.

작업의 개수가 변하는 시간은 시작 시간 또는 끝 시간 뿐이다.

각 시간을 정렬한 뒤 정렬한 스케줄대로 작업을 수행하면서 `동시 작업 개수가 최대인 수를 기록`한다.
