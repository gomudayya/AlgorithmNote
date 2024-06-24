# [leetcode2944. Minimum Number of Coins for Fruits](https://leetcode.com/problems/minimum-number-of-coins-for-fruits/description/)

## Intro

이 문제를 풀면서 Top Down DP의 개념을 알게되었다.

여태까지 풀어본 DP문제는 Bottom Up 방식의 DP문제가 대부분이였다.

DP배열을 만들고, 순차적으로 채워나가는 방식의 방법이 바텀업 방식이다.

반면에 **탑다운방식은 반환형이 있는 재귀를 이용해서 구현**한다. 

우선 큰 문제를 해결보자! 라는 생각이 깔려있기 때문에 탑다운이라고 부른다고 한다.

재귀호출 과정에서 순간순간의 Optimal한 값을 memorization 해놓고 재사용한다.

## 문제분석

이 문제는 i번째 인덱스에 있는 과일을 구매하면 그 다음 i+1번째의 과일까지 꽁짜로 준다.

우리는 모든 과일을 다사야하는데, 이 때 드는 코인의 최솟값을 구하는 문제이다.

처음에 단순히 드는생각은, 그리디한 방법을 떠올릴 수 있을 것 같다.

i번째 과일을 구매하고 그다음 i+1번째 (즉 2i+1)번째까지는 무료니까, 그다음은 2i+2번쨰 과일을 구매하는 방식이다.

하지만 음.. 이방법은 구매한 과일이 매우 비싼과일이면 당연히 통과할 수 없다.

따라서 그리디는 배제해야한다.

그 다음은 완전탐색인데, 만약에 우리가 i번째 과일을 구매하면 

그다음으로 구매해야할과일의 경우의수는 [i+1, 2i+2] 사이의 과일을 구매하는 것이다.

이런식으로 0부터 시작해서 모든 구간을 완전탐색하는 방법이 있는데, 문제 입력값의 최대가 1000이기 때문에 이방법은 힘들다.

잠깐 생각해보면, 거의 ^2의 속도 이상으로 경우의수가 불어나는것을 알 수 있다. 

이쯤되면 DP를 떠올려볼만 한데, .. DP풀이를 한 번 생각해보자.

## DP풀이

### 점화식정의

그리고 DP 배열 D[i]와 D[i]값을 구하는 함수 f(i)를 정의해본다면, 

**i번째 과일로부터, i 이후의 과일을 모두 구매할 때, 가장 코인을 적게지불하는 값.** 이라고 정의할 수 있다.

이렇게 정의한다면 우리가 찾고자하는 정답값은 D[0] , f(0)이 된다.

**우리가 i번째 과일을 구매하면 그다음 구매해야할 과일의 경우의 수는 [i+1, 2i+2] 사이이다.**

그러면 다음 점화식을 떠올릴 수 있는데, D[i] = P[i] + Math.min(D[i+1], D[i+2], D[i+3] ... ,D[2i+2]) 가 된다.

논리적으로 생각해보면,

매 번 [i+1, 2i+1] 범위안의 다음 과일 구매 경우의수를 따져보는데, 이때 그 경우의수도 모두 Optimal한 값으로 구해져있다. 

Optimal한 경우만 계속 누적되기 때문에 또다시 Optimal한 경우가 도출되게 된다.

(이런 재귀적 발상은, 이미 값이 구해져 있을거라고 생각하는것이 중요하다.)

따라서 위와같은 점화식이 도출될 수 있다.

### 재귀의 종료조건

그러면 재귀의 종료조건을 생각해보자. 

N이 10이라고 가정할 때, D[9]나 D[8]와 같이 N에가까운 값들은 그냥 P[9], P[8]과 일치한다.

이미 인덱스가 9번째 과일을 산 시점부터, N까지 모든 과일을 무료로 받게되기 떄문이다.

D[5]의 경우도 마찬가지이다. 2*5+1까지의 과일을 이미 무료로받았기 때문에 D[5] = P[5]이다.

D[4]의 경우는 좀 다른데. 4*2+1까지의 과일까지만 무료로 받기때문에 10번째 과일은 직접 구매해야한다.

따라서 D[5] ~ D[10]까지는 그냥 P[5] ~ P[10]으로 초기화 해버리면 된다.

즉 n을 순회하면서 돌면서 (2i+1 >= n)을 만족하는 i에 대해서는 D[i] = P[i]라고 초기화를 하는것이다.

위와같이 하는 방법도있고, 좀 더 깔끔한 종료조건은 f(i)에서 n을 i가 넘어가는 경우 0을 반환하는 방법이 있다.

이 경우는 코드로 보는게 더 나을 것 같다.

## 코드

- 특이사항, `ArrayIndexOutOfException`을 회피하기 위에 두 코드 모두 for문에서 `Math.min()`을 사용하였음.

- 위의 풀이와 다르게, 0-based index배열이기 때문에 N -> N-1로 변환되는 등의 작업이 추가됨.

### f(i)에서 n을 넘어갈때 0반환

```java
class Solution {
    int dp[];
    public int minimumCoins(int[] prices) {
      
        dp = new int[prices.length];
        Arrays.fill(dp,-1);
        
      return f(prices,0,prices.length);
    }
    
    public int f(int arr[] , int idx, int n)
    {
       if(idx>=n) return 0;
        if(dp[idx]!=-1) return dp[idx];
       
       int minans =(int)1e9;
       int maxidx=2*idx+2;
        for(int i=idx+1;i<=Math.min(n,maxidx);i++)
        {
           minans=Math.min(minans,arr[idx]+f(arr,i,n));
        }
     return dp[idx]=minans;
   }
}
```


### 뒷부분을 미리 초기화하는 방법

```java
class Solution {

    int N;
    int[] D;
    int[] P;
    public int minimumCoins(int[] prices) {
        this.N = prices.length;
        this.D = new int[N];
        this.P = prices;

        Arrays.fill(D, -1);
        for (int i = 0; i < N; i++) {
            if (2 * i + 1 >= N-1) D[i] = P[i];
        }

        return f(0);
    }

    int f(int idx) {
        if (D[idx] != -1) {
            return D[idx];
        }

        int min = Integer.MAX_VALUE;
        for (int i = Math.min(N-1, idx+1); i <= Math.min(2*idx+2, N-1); i++) {
            min = Math.min(min, f(i));
        }

        return D[idx] = P[idx] + min;
    }
}
```
