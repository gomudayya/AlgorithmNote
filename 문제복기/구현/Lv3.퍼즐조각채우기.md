# [Lv3.퍼즐조각채우기](https://school.programmers.co.kr/learn/courses/30/lessons/84021)

## Intro

문제를 잘못읽어서 하루종일 끙끙됐는데,, 마지막에 문제를 다시 읽고보니 허망했다.

문제에는 아래와 같은 제약이 있었는데, 이러한 제약은 오히려 풀이를 쉽게 해주는 좋은 제약인데, 이부분을 내가 잘못 해석했다.

```
게임 보드에 새로 채워 넣은 퍼즐 조각과 인접한 칸이 비어있으면 안 됩니다.
```

'나는 빈공간보다 작은 퍼즐조각을 여러개 붙여서 해당 공간을 꽉채우는것은 가능하다.' 라고 해석을했는데, 그게 아니었다.

친절히도 예제케이스를 붙여가며 설명까지 해줬는데 이 설명을 제대로 읽지 않았다..


![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/3c41e9a9-c9b2-47a5-9b90-4d726d61ec44)


나는 여기서 두 번째 설명인 `5번 조각의 양 옆으로 인접한 칸에 빈칸이 생깁니다.` 만 읽고, 

아하! 4번 3번처럼 같이 붙여서 space를 이루는것은 가능하겠구나! 라고 넘겨짚었다.

가장 중요한 설명인 `3번 조각을 놓고 4번 조각을 놓기 전에 위쪽으로 인접한 칸에 빈칸이 생깁니다.` 를 읽지 않은 것이다.

<br>

이렇게 문제를 잘못해석하고나니,, 문제 난이도가 극상이었다.. 안 그래도 쉽지 않은문제였는데 ㅋㅋㅋ

조각을 붙여넣는 경우의수까지 탐색을 하기 위해서 

1. 빈 공간에 조각을 채워넣을 수 있으면 채워넣기.

2. 채워넣을 수 있으면 조각의 인덱스를+1하고 다시 dfs로 경우의수 탐색

3. answer값 갱신

4. 백트래킹으로다시 조각을 채워넣기 이전상태로

이런식으로 조각을 채워넣는 경우의 수까지 전부 탐색하는 코드를 작성했다. 

얼추 돌아가는 코드가 작성이 되긴 했는데, 효율성에서 터져버렸다. ㅋㅋㅋㅋ 

## 올바른 접근

사실 아래 조건 때문에 문제 풀이 프로세스는 꽤나 간단해졌다.

```
게임 보드에 새로 채워 넣은 퍼즐 조각과 인접한 칸이 비어있으면 안 됩니다.
```

그냥 gameBoard에서는 빈공간을 추출하고, table에서는 퍼즐조각들을 추출해서 

2중 for문을 돌면서 빈공간의 모양과, 퍼즐공간의 모양이 일치하는지 확인해준다. 이 때 일치하면 answer에 퍼즐의 size만큼을 더하면 된다.

그리고 문제에서 언급되있듯이, 일치하는지 확인할 때 빈공간이나, 퍼즐조각 둘중 하나를 회전시켜가면서 일치하는지 비교 해야한다.

### 퍼즐모양 추출방식

문제조건에서 `퍼즐 조각은 1 x 1 크기 정사각형이 최소 1개에서 최대 6개까지 연결된 형태로만 주어집니다.`

라는조건이 있었는데, **이 덕분에 퍼즐조각의 형태를 6x6 배열 형태안에서 전부 특정지을 수 있다.**

나 같은 경우에는 dfs를 통해서 퍼즐조각을 탐색하면서, 해당 퍼즐조각이 있던곳을 shape배열에 복사했다.

그리고 이 복사된 shape배열에 있는 도형을 왼쪽 위로 밀어버면서 6x6배열로 축소시켰다.

### 퍼즐모양 이동방식

뽑아낸 퍼즐모양을 왼쪽 위로 밀어버리려면, 위쪽과의 거리와 왼쪽과의 거리를 알아야한다.

이 때 위쪽과의 거리를 계산하기 위해서는 일반적인 2차원배열 for문 작성하듯이 

for문을 row, col순으로 작성하고 가장 처음만나는 블럭의 row값이 위쪽과의 거리이다.

반대로 왼쪽과의 거리를 알아내기 위해선 

for문을 col, row순으로 작성하고 가장 처음만나는 블럭의 col값이 왼쪽과의 거리이다.

그리고 이렇게 계산한 거리만큼 밀어버리면된다.

```java
int[][] moveLeftUp(int[][] shape) {
        int leftDistance = findLeftDistance(shape);
        int upDistance = findUpDistance(shape);
        
        return moveLeftUp(shape, leftDistance, upDistance);
    }
    
    int[][] moveLeftUp(int[][] shape, int leftDistance, int upDistance) {
        int[][] ret = new int[6][6];
        
        for (int r = 0; r < shape.length; r++) {
            for (int c = 0; c < shape[0].length; c++) {
                if (shape[r][c] != 0) {
                    ret[r-upDistance][c-leftDistance] = 1;
                }
            }
        }
        
        return ret;
    }
    
    int findLeftDistance(int[][] shape) {
        for (int c = 0; c < shape[0].length; c++) {
            for (int r = 0; r < shape.length; r++) {
                if(shape[r][c] == 1) {
                    return c;
                }
            }
        }
        throw new IllegalArgumentException();
    }
    
    int findUpDistance(int[][] shape) {
        for (int r = 0; r < shape.length; r++) {
            for (int c = 0; c < shape[0].length; c++) {
                if (shape[r][c] == 1) {
                    return r;
                }
            }
        }    
        throw new IllegalArgumentException();
    }
```

### 퍼즐조각 회전방식

회전방식은 처음에 고민을 많이했는데, 이 부분은 사실 하나의 축을기준으로 사고해보면 간단하게 해결할 수 있다.

점 자체를 한번에 회전시키려고 생각하지 말고, Row축과 Col축을 각각 나눠서 생각해보자.

![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/c0a55c40-803c-4988-b7c7-5a14fccab0b0)

그러면 row축을 왼쪽으로 90도 회전시키면, row축이 col축으로 바뀌는 것을 알 수 있다.

그리고 col축을 왼쪽으로 90도 회전시키면, col축이 row축으로 바뀌는데 이 때, row위치가 N-1-col로 변환된다.

코드로 작성하면 아래와 같다.

```java
    int[][] rotate (int[][] arr) { // 왼쪽으로 90도 회전
        int N = arr.length;
        int[][] res = new int[N][N];
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) { 
                res[N-1-j][i] = arr[i][j];
            }
        }
        return res;
    }
```

## 교훈

**문제를 풀다가 이게 아닌것 같다 싶으면 문제를 다시 읽어보자.. 넘겨짚지 말고, 한 글자도 남김없이**

## 전체 코드

```java
import java.util.*;

class Solution {
    int[] dr = {-1, 0, 1, 0};
    int[] dc = {0, 1, 0, -1};
    int answer = 0;
    List<Integer> sizes = new ArrayList<>();
    public int solution(int[][] gameBoard, int[][] table) {
        reverseVaule(gameBoard);
        
        List<int[][]> pieces = extractShape(table);
        List<int[][]> spaces = extractShape(gameBoard);
        boolean[] used = new boolean[pieces.size()];
        
        for (var space : spaces) {
            for (int i = 0; i < pieces.size(); i++) {
                if (used[i]) continue;
                if (equal(space, pieces.get(i))) {
                    answer += sizes.get(i);
                    used[i] = true;
                    break;
                }
            }
        }
        return answer;
    }
    
    void reverseVaule(int[][] arr) {
        for (int r = 0; r < arr.length; r++) {
            for (int c = 0; c < arr[0].length; c++) {
                if (arr[r][c] == 0) arr[r][c] = 1;
                else arr[r][c] = 0;
            }
        }
    }
    
    List<int[][]> extractShape(int[][] arr) {
        List<int[][]> result = new ArrayList<>();
        for (int r = 0; r < arr.length; r++) {
            for (int c = 0; c < arr[0].length; c++) {
                if (arr[r][c] == 0) continue;
                
                int[][] shape = new int[arr.length][arr[0].length];
                int size = dfs(r,c, shape, arr);
                sizes.add(size);
                result.add(moveLeftUp(shape));
            }
        }
        return result;
    }
    
    int[][] moveLeftUp(int[][] shape) {
        int leftDistance = findLeftDistance(shape);
        int upDistance = findUpDistance(shape);
        
        return moveLeftUp(shape, leftDistance, upDistance);
    }
    
    int[][] moveLeftUp(int[][] shape, int leftDistance, int upDistance) {
        int[][] ret = new int[6][6];
        
        for (int r = 0; r < shape.length; r++) {
            for (int c = 0; c < shape[0].length; c++) {
                if (shape[r][c] != 0) {
                    ret[r-upDistance][c-leftDistance] = 1;
                }
            }
        }
        
        return ret;
    }
    
    int findLeftDistance(int[][] shape) {
        for (int c = 0; c < shape[0].length; c++) {
            for (int r = 0; r < shape.length; r++) {
                if(shape[r][c] == 1) {
                    return c;
                }
            }
        }
        throw new IllegalArgumentException();
    }
    
    int findUpDistance(int[][] shape) {
        for (int r = 0; r < shape.length; r++) {
            for (int c = 0; c < shape[0].length; c++) {
                if (shape[r][c] == 1) {
                    return r;
                }
            }
        }    
        throw new IllegalArgumentException();
    }
    
    int[][] rotate (int[][] arr) { // 왼쪽으로 90도 회전
        int N = arr.length;
        int[][] res = new int[N][N];
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) { 
                res[N-1-j][i] = arr[i][j];
            }
        }
        return moveLeftUp(res);
    }
    
    int dfs(int r, int c, int[][] shape, int[][] table) {
        shape[r][c] = 1;
        table[r][c] = 0;
        int size = 1;
        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i];
            int nc = c + dc[i];
            
            if (nr >= 0 && nc >= 0 && nr < table.length && nc < table[0].length && table[nr][nc] == 1) {
                size += dfs(nr, nc, shape, table);
            }
        }
        return size;
    }
    
    boolean equal(int[][] space, int[][] piece) {
        for (int i = 0; i < 4; i++) {
            if (Arrays.deepEquals(space, piece)) return true;
            piece = rotate(piece);
        }
        return false;
    }
}
```
