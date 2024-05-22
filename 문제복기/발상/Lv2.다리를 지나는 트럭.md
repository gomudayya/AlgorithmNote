# [다리를 지나는 트럭](https://school.programmers.co.kr/learn/courses/30/lessons/42583)

## Intro

레벨2인데 개인적으로 조금 어려웠다. 문제 유형은 스택/큐로 되어있긴 한데, 

사실 발상을 떠올리냐 마느냐의 여부에 달린 것 같다.

## 시간복잡도 분석

제약 조건에서 bridgeLength는 최대 1만, 트럭의 갯수도 최대 1만

시간이 1초씩 흐름에 따라서, 트럭의 움직임을 구현하는 방식으로 했을 때

최악의 경우 1만 * 1만이 나오게 된다. (다리에 트럭 하나밖에 못올라오는 경우)

이 값은 1억으로, 살짝은 아슬아슬하게? 통과할 수 있는 정도이다.

## 구현

먼저 들어간 트럭이 먼저 나오게 되므로, 큐가 적절해보인다.

더군다나 마치 컨베이어벨트처럼 트럭이 줄줄이 나오는 구조이기 때문에 큐를 떠올리는것은 어렵지 않았다.

그다음 문제는 큐에다가 넣고 1초마다 자동차의 위치를 조정해주어야 하는데 이것을 어떻게 할지 여부이다.

자동차 별로 위치를 저장하는 변수를 두고, 1초마다 자동차의 위치를 갱신할 수도 있다.

하지만 이 방법은 다리에 올라선 자동차들이 많아질수록 컨트롤하기가 어렵고, 시간적으로도 효율적이지 못하다.

## 발상

이 때 떠올려야하는 발상은 큐 전체를 0으로 꽉꽉 채워놓고, 자동차를 이동시키는 것이다.

1초에 한번씩 큐에서 요소를 빼고, 자동차를 넣을지 안넣을지 판별하면 된다.

그리고 이 때 다리의 무게제한에 걸려서 자동차를 다리에 올려놓지 못할 경우 0을 추가하면 된다.

이렇게 하면 시간이 1흐름에 따라서, 위치가 1씩 이동하는것처럼 구현할 수 있다.

이런식으로 프로그래밍하면서 이 문제의 0과 같은 깡통 데이터를 사용하는 것은 생각보다 흔하므로 꼭 기억해두자.

(디자인 패턴에서 null객체패턴도 비슷하게 생각이 난다)

## 코드

import java.util.*;

class Solution {
    public int solution(int length, int weight, int[] trucks) {
        Queue<Integer> Q = new LinkedList<>();
        for (int i = 0; i < length; i++) Q.add(0);
        
        int time = 0;
        int truckCount = 0;
        int nowWeight = 0;
        for (int i = 0; i < trucks.length;) {
            time++;
            int poll = Q.poll();
            if(poll != 0) {
                truckCount--;
                nowWeight -= poll;
            }
            if (truckCount < length && nowWeight + trucks[i] <= weight) {
                Q.add(trucks[i]);
                truckCount++;
                nowWeight += trucks[i];
                i++;
            } else {
                Q.add(0);
            }
        }
        
        return time += length;
    }
}
