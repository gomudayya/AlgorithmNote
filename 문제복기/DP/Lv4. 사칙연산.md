# [Lv4. 사칙연산](https://school.programmers.co.kr/learn/courses/30/lessons/1843)

이 문제를 풀면서 재귀와 DP는 밀접한 관계가 있다는 것을 알게되었다.

재귀와 DP문제 모두 점화식으로 치환이 가능하다.

따라서 DP문제는

1. 재귀함수안에서 메모이제이션을 사용하거나

2. 메모이제이션 배열을 순회하며순차적으로 채워나가거나

두가지 방식이 있는 것 같다. 

이 문제는 사실 메모이제이션은 거들뿐이고, 재귀 정의를 어떻게하느냐가 핵심이였던 것 같다.

## 느낀점

사실 지금까지 내가 풀어본 DP문제는 그냥 구하고자 하는 값을 DP배열에 넣어놓고 

어떠한 '경향성' 을 분석하다보면 (예를들어 n+1항이 추가된다면 DP상태가 어떻게 바뀌는가?) 문제가 풀렸다.

하지만 이 문제는 보다 **재귀 본연의 개념**에 집중해야만 풀 수 있었다. 마치 하노이의탑과 같은 느낌이다.

## 문제 풀이(아이디어)

이 문제는 서로 다른 연산순서의 계산 결과 중 최댓값을 return 해야한다.

처음에 나는, 연산 결과의 경우의수를 어떻게든 모두 다 찾아서, 그 중에서 최댓값을 return 하려고 했다.

문제에서 중요한 힌트를 놓친셈이였다. 

그리고 '연산 순서' 라는 것에 너무 얽매였던것도 같다. 

**어떻게 괄호를 쳐야 최댓값을 뽑아낼 수 있을까?** 로 접근하면 어땟을까 아쉬움이 남는다.

이 문제는 +연산자의 경우에는 양 옆의 수식들이 최댓값이 나와야 한다.

반면 -연산자의 경우에는 양 옆의 수식이 왼쪽은 최댓값, 오른쪽은 최댓값이 나와야 한다.

즉 아래와 같이 재귀를 정의할 수 있다.

```
연산자가 +인경우 : max(start, end) = max(start, i-1) + max(i+1, end)

연산자가 -인경우 : max(start, end) = max(start, i-1) + min(i+1, end)

(i|i는 구간 안의 모든 연산자의 인덱스)
```

이런식으로 재귀를 이용해서 부문제를 풀다보면 그 과정에서 모든 연산순서를 다 확인하게 되고,

이 때 메모이제이션을 활용하면 중복되서 나타나는 구간의 값을 적은 비용으로 구할 수 있게 된다.

## 코드
```java
import java.util.*;

class Solution {
    
    int[][] max = new int[202][202];
    int[][] min = new int[202][202];
    
    public int solution(String arr[]) {
        for (int r=0; r<202; r++) {
            Arrays.fill(max[r], Integer.MIN_VALUE);
            Arrays.fill(min[r], Integer.MAX_VALUE);
        }
        
        return max(0, arr.length-1, arr);
    }
    
    int max(int start, int end, String[] arr) {
        if (max[start][end] != Integer.MIN_VALUE) {
            return max[start][end];
        }
        if (end - start == 0) {
            return Integer.parseInt(arr[start]);
        }
        
        int ret = Integer.MIN_VALUE;
        for (int i = start+1; i <= end-1; i+=2) {
            if (arr[i].equals("+")) {
                ret = Math.max(ret, max(start, i-1, arr) + max(i+1, end, arr));
            }
            else {
                ret = Math.max(ret, max(start, i-1, arr) - min(i+1, end, arr));
            }
        }
        
        return max[start][end] =ret;
    }
    
    int min(int start, int end, String[] arr) {
        if (min[start][end] != Integer.MAX_VALUE) {
            return min[start][end];
        }
        if (end - start == 0) {
            return Integer.parseInt(arr[start]);
        }

        int ret = Integer.MAX_VALUE;
        for (int i = start+1; i <= end-1; i+=2) {
            if (arr[i].equals("+")) {
                ret = Math.min(ret, min(start, i-1, arr) + min(i+1, end, arr));
            }
            else {
                ret = Math.min(ret, min(start, i-1, arr) - max(i+1, end, arr));
            }
        }
        
        return min[start][end] = ret;
    }
}
```
