# Intro

보통의 이진탐색은 **중복이 없는 컬렉션**안에서 특정한 target의 위치를 반환한다. 그리고 해당값이 없으면 -1을 반환해주곤 한다.

그러나 이런 단순한 이진탐색 말고 lowerBound와 upperBound의 개념이 필요할 때가 있다.

**target 값을 컬렉션안에 끼워넣고싶은데, 끼워넣을 자리의 인덱스는 몇 이어야 되는가?**

lowerBound와 upperBound는 위와같은 상황에서 활용할 수 있다.

인덱스 | 0 | 1 | 2 | 3 | 4 | 5 |  6 |  7 |  8 |  9 
|--|--|--|--|--|--|--|--|--|--|--|
값 | 1 | 3 | 5 | 6 | 6 | 6 | 13 | 16 | 18 | 30

위의경우 6을 끼워넣을 수 있는 위치는 굉장히 많은데 (6이 여러개기 때문) , 이중에서 가장 왼쪽자리의 위치를 반환하는것이 lowerBound 이다.

이 경우 lowerBound는 3이된다.

반면에 가장 오른쪽자리의 위치를 반환하는것은 upperBound 이다.

이 경우 upperBound는 6이 된다.

그리고 lowerBound와 upperBound는 target이 컬렉션 안에 없을때에도 동작할 수 있어야한다. (끼어넣을 위치를 찾는것이기 때문에)

target이 컬렉션안에 없다면 lowerBound값과 upperBound값은 일치하게 된다.

# 코드

## LowerBound

인덱스 | 0 | 1 | 2 | 3 | 4 | 5 |  6 |  7 |  8 |  9 
|--|--|--|--|--|--|--|--|--|--|--|
값 | 1 | 3 | 5 | 6 | 6 | 6 | 13 | 16 | 18 | 30

### 1. lt, rt 셋팅

위와같은 경우에서 만약 target값이 35라고 생각을해보자. 이렇게 되면 인덱스가 10인자리에 target을 끼워 넣어야 한다.

즉 lowerBound의 값은 10이다.

이렇게 기본 이진탐색과 다르게 lowerBound와 upperBound는 rt의 범위가 +1 크게된다.

따라서 `lt = 0, rt = length or size` 가 된다.

### 2. 탐색범위 분석

target이 6이라고 가정할 때, lowerBound의 값은 3이된다. **즉 target이 존재한다면, target중에 가장 왼쪽의 값이 lowerBound가 된다**.

인덱스 | 0 | 1 | 2 | 3 | 4 | 5 |  6 |
|--|--|--|--|--|--|--|--|
값 | 1 | 3 | 5 | 13 | 16 | 18 | 30

**만약 target이 존재하지 않는다면?** **target보다 큰수가 처음나오는 자리가 lowerBound가 된다.**

위의 특징을 이용해서 mid값을 구했을 때 다음 탐색 범위를 어떻게 잡을지 코드를 작성할 수 있다.


1. mid값이 target보다 큰 경우.

   - 다음 탐색범위에 mid도 포함시켜야 한다. 위의 예시로 보면 13이있는 자리가 lowerBound가 될 수 있기 때문이다.
   - mid를 포함한 왼쪽범위를 다음번에 탐색한다. (rt = mid)

2. mid값과 target이 같은 경우

   - 만약에 target값이 하나라면 mid도 lowerBound가 될 수 있다. 즉 mid도 탐색범위에 포함시켜야한다.
   - 하지만 여러개라면 가장 왼쪽에 있는값이 lowerBound가 되므로 mid를 포함한 왼쪽범위를 다음번에 탐색해야한다. (rt = mid)

3. mid값이 target보다 작은 경우.

   - target과 같거나 큰 수가 처음 나오는자리가 lowerBound이다. 따라서 mid가 target보다 작은경우에는 mid를 다음 탐색범위에 포함시키면 안된다.
   - mid를 제외한 오른쪽 범위를 탐색한다. (lt = mid+1)

    
```java
    static int lowerBound(List<Integer> nums, int target) {
        int lt = 0, rt = nums.size();

        while (lt < rt) {
            int mid = (lt + rt) / 2;
            int find = nums.get(mid);

            if (find >= target) {
                rt = mid;
            } else if (find < target) {
                lt = mid + 1;
            }
        }

        return rt;
    }


```






## 활용하는 법

- 만약에 숫자 컬렉션에서, target보다 작은값이 몇개인지 알고싶다면? lowerBound의 반환값을 이용하면 된다.

  - [관련문제](https://school.programmers.co.kr/learn/courses/30/lessons/258709)
  
    - 이 문제는 주사위로 얻을 수 있는 Score를 다 구한 이후 승리횟수를 Count할 때 이중 for문을 이용하면 시간초과가 발생한다. O(n^2)
    - 반면 lowerBound를 이용하면 O(n log n)으로 구할 수 있다.
    - 만약에 승리횟수와 무승부도 계산할일이 있다면? upperBound값으로 계산하면 된다.
