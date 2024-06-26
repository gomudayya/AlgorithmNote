# Knapsack

냅색문제의 기본아이디어는 

여러가지 Item이 있을 때, 

A Item만 존재한다고 생각할 때 최적값

B Item만 존재한다고 생각할 때 최적값

C Item만 존재한다고 생각할 때 최적값 ... ~ 

이런식으로 구해나가는 것이다.

## 테이블

따라서 냅색의 경우에 일반적으로 2차원테이블을 생성하고

Row 에 Item을 놓는다. 그리고 Column은 그때그때 바뀌는데.. 이건 문제읽고 잘 판단해야 한다. (음 전형적인 가방 문제의 경우에는 무게가 Column)

그리고 간혹 Column이 지나치게 값이 큰 것 같다면, 테이블축을 반대로 설정한다거나 하는 방법도 고려할수 있다.

배열안에 들어가야하는 값은 **다른 DP문제와 비슷하게 대부분 문제에서 구해야하는 값**이 되게된다. (전형적인 가방문제의 경우 Value)

## 점화식

DP에서 점화식을 얻는 아이디어를 생각해보자.

먼저 Intro에서 아래와 같은 아이디어를 제시했었다.

```
A Item만 존재한다고 생각할 때 최적값

B Item만 존재한다고 생각할 때 최적값

C Item만 존재한다고 생각할 때 최적값 ... ~ 
```

예를들어 C Item이 있는 row를 채워넣을 때, 어떤식으로 접근해야할까.

먼저 **C Item를 무조건 사용한다고 고려할 때 최적값을 구할 것**이다.

이 값은 다음과 같다. **DP[i-1][j-c의 무게] + C아이템의 가치.**. 

**여기에서 C 아이템을 반드시 사용해야 하므로 C의 무게만큼 여유 무게를 확보해야한다. 따라서 [j-c의 무게] 가 붙는것이다.**

DP[i-1]은 이전까지의 최적값을 의미한다. 


그리고 그 다음에는 A~B까지 Item을 사용해서 구한 최적값과 비교한다.

만약에 C Item을 사용했을 때, 기존 A~B까지의 최적값보다 더 최적의 값이 나온다면 그 값으로 테이블에 업데이트 해준다.

(즉 이 경우는 C Item을 사용하는 상황이다.)


그래서.... 전형적인 보석있고, 아이템있고, 하는 가방문제라고 가정하면 다음과같은 점화식이 나온다.

DP[i][j] = Math.max(DP[i-1][j-weight[j] + value[j], DP[i-1][j]);

## 아이템의 중복을 허용할 때

위의 점화식은 아이템의 중복을 허용하지 않을 때 점화식이다.

만약에 아이템의 중복을 허용한다면 점화식이 어떻게 바뀔까?

이전에 점화식을 계산할 때 DP[i-1] 값을 계속해서 고려했던 이유는 i아이템 이전까지의 최적값에 대한 정보를 구하기 위해서였다.

**중복이 허용된다면 i아이템을 여러개 썼을때의 최적값도 고려해야한다.**

즉 **i-1번째 row가 아니라 i번째 row의 값들을 토대로 식을 세우면 된다.**

DP[i][j] = Math.max(DP[i-1][j], DP[i][j-weight[j]] + value[j]);

먼저 DP[i-1][j]는 똑같다. i번째 아이템 이전까지의 최적값이다. 

**만약에 i아이템이 정말 가성비가 떨어지는 아이템이라면 DP[i-1][j]값이 사용될 것이다.** (이건 중복허용/비허용 상관없이 마찬가지)

**DP[i][j-weight[j]] + value[j] **

이전에는 i-1번 row에서 i번째 아이템을 사용하는거를 고려했다면

, 지금은 i번째 row의 값에서 i번째 아이템을 사용하는 것을 고려한다. (아이템 i가 중복사용될 수 있기 때문에)

이런식으로 하면 아이템의 중복이 허용될 때도 고려하면서 테이블을 채워나갈 수 있다.


