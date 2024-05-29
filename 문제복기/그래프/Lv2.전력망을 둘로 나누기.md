# [Lv2.전력망을 둘로 나누기](https://school.programmers.co.kr/learn/courses/30/lessons/86971)

## Intro

그래프 문제이다.

예전부터 고민했던 것이 그래프문제를 풀 때 간선정보를 어떻게 저장할지가 고민이었다.

대표적으로 잘 알려진것은 인접행렬과 인접리스트가 있는데, 이 것 둘다 뭔가 사용하면서 불편한 부분이 있었다.

먼저 인접행렬과 같은 경우 한 노드에서 다른노드로 연결되어있는 간선정보를 확인할때 불가피하게 N만큼의 for문을 돌아야한다.

따라서, N은 엄청나게 큰데 (여기서 N은 노드의 갯수이다) 간선이 별로 없는경우라면, 인접행렬은 매우 비효율적이다.

때에 따라 인접행렬을 사용해서는 시간초과로 못푸는 경우가 많다.

이러한 단점때문에 이제 인접리스트를 사용하게 되는데, 인접리스트도 불편한요소가 있다.

예를들어 노드가 1~N까지 전부 있는것이 아니라, 1~N사이에 없는노드가 존재한다면? 

인접리스트같은 경우에는 이럴 때에도 억지로 초기화를 해줘야 한다. 이런 부분이 좀 불편했다.

그리고 이번 문제같은 경우에도 인접리스트를 쓰면 간선을 하나 삭제하는 코드를 작성하기가 불편하다.

왜냐하면 리스트같은 경우에는 특정한 값에 대해서 remove를 쓰려면 요소를 전부 살펴보면서 확인을 해주어야 하기 때문이다.

## 내 방법

이러한 불편한요소 때문에 나는 그래프정보를 저장할 때 Map<Set<Integer>> 자료형을 사용한다.

이런식으로 하면 내부자료구조가 Set이기 때문에 Remove하기도 편리하고, 

간선정보를 확인하기 위해 인접 행렬처럼 N만큼의 for문을 돌지 않아도 된다.

그리고 중간에 비어있는 노드가 있어도, Map의 key값에 맵핑되기 때문에 편리하다.

그래서 개인적으로 이렇게 쓰는것을 선호하는 편이다.

## 단점 

다만 인접 행렬에비해 단점이 하나 있는데, 간선이 양방향으로 되어있는것이 아니라 단방향이 되어있을경우 조금 까다로울 수 있다.

간선이 단방향으로 되어있을 경우 자주 사용하는 정보가 두개 있는데

1. 특정 노드 n에서 나갈 수 있는 노드들의 집합. (바깥쪽 화살표)

2. 특정 노드 n으로 들어오는 노드들의 집합 (n쪽으로 가르키는 화살표)

이 두 정보를 표시하기 위해서는 아래와 같이 그래프를 두개를 만들어야한다

```java
Map<Set<Integer>> in = new HashMap<>();

Map<Set<Integer>> out = new HashMap<>();
```

하지만 인접행렬 같은 경우에는 바깥쪽으로 나가는 정보같은 경우에는 이차원배열의 row를 기준으로 조회하고,

안쪽으로 나가는 정보같은 경우에는 이차원배열의 column을 기준으로 조회하면 되서 편리하다.

## 실수할만한 요소

양방향 그래프를 초기화 해줄때에는 항상, 반대노드의 입장에서도 초기화를 해주어야한다.

예를들어 [1,2] 와 같은 간선정보가 들어온다면 1의 입장에서도 간선정보에 2를 넣어주어야 하고

2의 입장에서도 간선정보에 1을 넣어주어야 한다.

## 코드
```java
import java.util.*;

class Solution {
    Map<Integer, Set<Integer>> edges = new HashMap<>();
    int answer = 10000;
    public int solution(int n, int[][] wires) {
        for (int i = 1; i <= n; i++) edges.put(i, new HashSet<>()); 
        for (int[] wire : wires) {
            edges.get(wire[0]).add(wire[1]);
            edges.get(wire[1]).add(wire[0]);
        }
        
        for (int[] wire : wires) {
            //엣지 하나를 제거해보기
            edges.get(wire[0]).remove(wire[1]);
            edges.get(wire[1]).remove(wire[0]);
            
            //DFS탐색으로 wire[0]과 wire[1]에서 연결된 노드가 몇개인지 각각 계산
            int diff = Math.abs(dfs(wire[0], new int[n+1]) - dfs(wire[1], new int[n+1]));
            answer = Math.min(answer, diff);

            //제거한 엣지를 다시 원상복구
            edges.get(wire[0]).add(wire[1]);
            edges.get(wire[1]).add(wire[0]);
        }

        return answer;
    }
    
    int dfs(int node, int[] visited) {
        int count = 1;
        visited[node] = 1;
        
        for (int linkedNode : edges.get(node)) {
            if (visited[linkedNode] == 1) continue;
            count += dfs(linkedNode, visited);
        }
        
        return count;
    }
}
```
