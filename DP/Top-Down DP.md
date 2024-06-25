# Top-Down DP

지금까지 내가 정리해놓은 DP들, 그리고 만났던 문제들 대부분은 Bottom-Up 방식의 DP였다.

하지만 최근에 문제를 풀어보니, Top-Down Dp도 한 번 짚고 넘어가긴 해야할 것 같아서 정리해본다.

## Top-Down DP가 무엇?

이 부분은 백준게시판에 어떤분이 잘 써놓으신게 있어서 한 번 가져와봤다.

```
탑다운과 바텀업 방식의 차이점은

탑다운:큰 문제를 작은 문제로 쪼개서 푸는 것

바텀업:작은 문제를 큰문제로 합치는 것입니다.

따라서 두 방식은 구현 자체도 크게 갈리게됩니다. 탑다운은 보통 재귀함수로 구현하고, 바텀업은 반복문으로 구현하게되죠.

점화식으로 내가 알고있는 정보를 쓰면 바텀업이고, 모르는 정보를 쓰면 탑다운입니다.

(출처 : https://www.acmicpc.net/board/view/47749)
```

좀더 실전 관점에서 설명하면 **Bottom-Up 방식의 DP는 배열을 앞에서부터 하나씩 채워나가는 방식.**

탑다운 DP는 **반환형이 있는 재귀함수를 이용하여 푸는방식**인데, **메모이제이션을 활용해서 시간복잡도를 대폭 낮춘다.** 

## 예시문제 : [백준13302 리조트](https://www.acmicpc.net/problem/13302)

예시문제를 보면서 살펴보는게 더 도움이 될 것 같다.

문제를 보면 N이 생각보다 작은데, 완전탐색을 돌리기는 뭔가좀 꼬롬한면이 있는 값이다.

요런 때 한 번 DP적접근도 고려해보면 좋다. 완전탐색, 재귀, 점화식, DP 얘네들은 뭔가 조금조금씩 엮여있는 부분이 있다.

### 테이블 정의

DP로 접근한다면, 먼저 D[i]에 대한값을 정의해야 하는데 이부분은 이전에도 계속 정리했던 대로 **문제에서 구하고자 하는 값**

으로 치환해서 적으면 대부분 풀린다.

이 문제에서 구하고자 하는 값은 아래와 같다.

**1일부터 (N일까지), 스케줄이 없는 날은 모두 리조트에 간다고 할 때, 지불하는 가장 적은 요금이다.**

이 때 1일을 i로 바꾸면...

**i일부터 (N일까지), 스케줄이 없는 날은 모두 리조트에 간다고 할 때, 지불하는 가장 적은 요금이다.**

가 되고, 이것이 바로 D[i]에대한 정의가 된다.

### 점화식 구하기

수영이가 Di의 상황에서 고를 수 있는 선택지는 아래의 4가지가 있다. 

- 하루 이용권을 구매한다
- 연속 3일권을 구매한다
- 연속 5일권을 구매한다
- 보유하고 있는 쿠폰이 3장 이상이라면 쿠폰을 구매한다.

그리고 하루 이용권을 구매하면 당일만 사용하기 때문에, 다음날에 무엇을 할지 선택해야한다.

마찬가지로 3일권을 구매하면 3일만 이용하기 때문에 3일뒤에는 다시 무엇을 할지 선택해야 한다.

따라서... 

즉 Di = Min(하루이용권 구매비 + Di+1, 3일권 구매비 + Di+3, 5일권 구매비 + Di+5, 쿠폰을 사용할 수 있다면 쿠폰사용) 가 된다.

생각해보니 여기서 쿠폰이라는 하나의 변수가 더 생기게 된다.

**우리는 매 순간 선택을 해야하는 상황에서 쿠폰갯수에 대한 정보가 필요하다.**

아마 재귀로 구현하게된다면 매개변수로 그것을 저장하게 될 것인데, 단순히 점화식을 D[i] 로 가져가기에는 무언가 괴리가 있다.

**재귀와 DP 테이블은 개념적으로 항상 일치해야 한다. (중요)** 

그래서 테이블 정의를 다시해보면, 

**D[i][j] 는 i일에 j개의 쿠폰을 가지고 있는 상황에서, i일부터 N일까지 모두 리조트를 가기위해 지불하는 가장 적은 요금.**

이라고 쿠폰에 대한 정보를 추가로 포함해서 정의할 수 있다 !!

### 코드 구현

코드 구현할 때 괜찮은 것 같은 방법은

1. 먼저 메모이제이션을 활용하지 않고, 재귀만을 이용해 값을 구한다.

2. 테스트를 해봤을 때, 값이 올바르게 나온다면 메모이제이션을 이용해서 시간을 단축시킨다.

```java
import java.util.*;
import java.util.stream.*;
import java.io.*;

class Main {

    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static StringTokenizer st;

    static int N;
    static int M;
    static Set<Integer> except = new HashSet<>();;
    static int[][] D;
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());

        if (M != 0) {
            st = new StringTokenizer(br.readLine());
            for (int i = 0; i < M; i++) except.add(Integer.parseInt(st.nextToken()));
        }
        
        D = new int[N+1][555];
        for (int i = 0; i < D.length; i++) Arrays.fill(D[i], -1);

        int ans = f(1,0);
        System.out.println(ans);
    }

    static int f(int date, int coupon) {
        while (except.contains(date)) {
            date++;
        }
        if (date > N) return 0;
        if (D[date][coupon] != -1) return D[date][coupon];
        int useCoupon = coupon >= 3 ? f(date + 1, coupon - 3) : Integer.MAX_VALUE;
        int min = getMin(10000 + f(date + 1, coupon), 25000 + f(date + 3, coupon + 1), 37000 + f(date + 5, coupon + 2), useCoupon);

        
        return D[date][coupon] = min;
    }

    static int getMin(int... arr) {
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < arr.length; i++) {
            min = Math.min(min, arr[i]);
        }
        return min;
    }
}
```

여기서 이 방식이 왜 탑다운인가? 를 살펴보면 `main` 함수에서 `f(1, 0)`을 호출한다.

**`f(1, 0)`은 이 문제에서 가장 최종적으로 구해야하는 우리가 원하는 답이다.**

(1일, 쿠폰0인상황에서, 모두 리조트를 갈 때 지불할 수 있는 요금 중 최소 요금)

그리고 이 f(1,0) 에서 재귀가 계속 파생되면서 작은 문제들에 대한 값을 호출한다.

이렇게 큰 문제에서 파생되어 작은 문제들을 풀어나가기 때문에, 탑다운 방식이라고 볼 수 있다.

