# [Lv3. 여행 경로 (같은 간선이 여러개일 때)](https://school.programmers.co.kr/learn/courses/30/lessons/43164?language=java)

## 풀이

같은 문제의 릿코드 문제를 보는것을 추천한다.

https://leetcode.com/problems/reconstruct-itinerary/description/

프로그래머스 문제를 보니, 테스트케이스도 달랑 4개에 효율성은 전혀 체크하지 않는 수준이다.

`주어진 공항 수는 3개 이상 10,000개 이하입니다.` 라고 적혀있는데 정작 테케의 공항갯수는 몇백개도 안되는 것 같다.

그리고 가장 중요한 항공권의 갯수, 즉 tickets 배열의 사이즈에 대한 언급이 없다. 공항갯수보다 중요한것이 tickets의 사이즈인데..

그래도 프로그래머스에서 더 나은 부분은 

`주어진 항공권은 모두 사용해야 합니다.`,

`모든 도시를 방문할 수 없는 경우는 주어지지 않습니다.` 라고 명시가 되어있다는 점이다.

릿코드는 해당 설명이 없고, 그저 valid한 itinerary를 찾아라.. 라고만 되어있다. 이 부분때문에 릿코드 discussion 에서도 말 들이 많다. 

나는 프로그래머스에서 먼저 풀어보고 릿코드에서 다시 풀어보았는데, 같은 코드가 시간초과로 릿코드에서는 통과되지 않았다.

그런데 릿코드의 입력MAX가 프로그래머스보다 적다. 릿코드는 1 <= tickets.length <= 300인데, 프로그래머스는 공항갯수가 최대 1만개이다. 

공항갯수가 1만개면 항공권은 최소 9999개이상 존재해야 하는데..

내 코드가 프로그래머스에서 통과한것을 보면 프로그래머스에선 티켓의 최대 갯수가 100개이하인 것 같다.

--------------------------------------

https://www.youtube.com/watch?v=7ReZyOniO74

처음엔 이 영상을 보고 풀어봤는데,, 2년전 영상이라 그런지 당시에는 통과되었는데, 지금은 시간초과가 났다. 

이 영상에 나오는 풀이도 지나간 간선 자체를 없애버리는거라 간선을 flag처리 하는 것 보다는 훨씬 빠른편이다. 

하지만 **문제는 유효하지 않은 route를 타게되었을 때 백트래킹을해서 되돌아간 뒤에 다시 탐색해야한다는 점**이다. 

이것을 극복한 풀이는 유효하지 않은 경로를 타게되었을 때, 유효하지 경로의 마지막 노드를 리스트의 끝에 저장한다.

이말은 즉 해당 경로는 제일 마지막에 들리겠다는 의미이다.

![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/08646a26-6f55-4766-9641-f0c9548e0d21)

위와같은 그래프가 있을 때, 사전순으로 계속 따라간다면 A -> B -> C -> D -> E, 그리고 E에서 갈곳이없다.

이렇게 **막다른길에 다다르는 경로는 다른곳을 다 돌고 제일 마지막에 방문해야 한다.** 따라서 이 경로를 리스트의 끝부분에 넣는다.

**그리고 이런 막다른길에 다다르는 경로는 주어진 Input에서 하나밖에 없다.** 

모든 간선을 다 사용하는 Valid한 Itinerary가 존재하기 위해선 이런 경로가 두개이상 있어서는 안 된다. 

풀이코드를 보면...

```java
import java.util.*;

class Solution {

    Map<String, Queue<String>> graph = new HashMap<>();
    public List<String> findItinerary(List<List<String>> tickets) {
        for (var ticket : tickets) {
            graph.putIfAbsent(ticket.get(0), new LinkedList<>());
            graph.putIfAbsent(ticket.get(1), new LinkedList<>());
            graph.get(ticket.get(0)).add(ticket.get(1));
        }
        List<String> route = new ArrayList<>();
        dfs("JFK", route);
        return route;
    }

    void dfs(String airport, List<String> route) {
        while(!graph.get(airport).isEmpty()) {
            dfs(graph.get(airport).poll(), route);
         }
        route.addFirst(airport);
    }
}
```

위의 코드도 마찬가지로 지나간 간선을 소모하는 방식인데, 이 때 priorityQueue를 통해서 소모한다.

코드를 보면 사전순을 따라서 DFS탐색을 들어간다. **이 때 경로를 저장하는것은, 백트래킹 페이즈를 돌면서 쭉 저장하게 된다.**

### 백트래킹으로 다시 돌아왔는데, PriorityQuueue 가 비어있지 않을수도 있지 않나요??

이런 경우는 많아봐야 한 번 밖에 존재하지 않는다.

아까 말했듯이 막다른길로 가는 경로가 두개이상 있을순 없다.

![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/73552f58-160c-4c2b-abc1-1523d113ae7f)

두 개 이상이라면 위와 같은 형태인데 이런 경우는 애초에 Valid한 Itinerary를 만들 수 없다.

![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/410254cc-7f18-4565-9ef2-f523c241a517)

valid한 Itinerary가 되려면 위와 같은 구조여야 한다.

그리고 위와같은 구조는 C와 D를 들렀다가, A에 도착해서야 백트레킹 페이즈에 들어가게 된다.

백트레킹 페이즈에서는 가장 나중에 방문한 곳부터 순서대로 다시 돌아오기 때문에 addFirst를 사용하면 된다.

즉 정리하면 백트래킹 페이즈에 진입하는 시점은 두가지가 있는데

**1. 막다른길에 도달했을 때 (최대 1번)**

**2. 간선을 전부다 소모했을 때**

이 경우 모두 addFirst로 커버가 된다.

이 풀이는 시간복잡도가 n log n 밖에 들지 않는다. (간선을 사전순으로 정렬하는비용)

특히 dfs 호출 부분은 그냥 O(n)으로 끝나버린다. O(n log n)도 PriorityQueue의 정렬비용 때문에 발생하는 것임.. (쩐다..)

## 기타 코드팁

### 단방향 그래프에서 간선정보 저장할 때

```java
        for (var ticket : tickets) {
            graph.putIfAbsent(ticket.get(0), new LinkedList<>());
            graph.putIfAbsent(ticket.get(1), new LinkedList<>());
            graph.get(ticket.get(0)).add(ticket.get(1));
        }
```

이 때 ticket.get(0) -> ticket.get(1) 가는 간선만 존재하지만 1도 초기화 해주는게 좋다. (추후에 NPE 방지 해줌)
