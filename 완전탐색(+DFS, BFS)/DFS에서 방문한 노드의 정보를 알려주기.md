# DFS에서 방문한 노드의 정보를 알려주기

- 결론 : DFS 함수의 retrun값을 이용한다. 전역변수나 매개변수를 이용하려고 하면 꼬이려는 경우가 많다.

## Intro.

DFS 문제를 풀다 보면, 방문한 노드의 정보를 알려주고 싶을 때가 있다.

문제 예시는 https://www.acmicpc.net/problem/2533  이 문제 였는데, 

내가 하고싶었던 것은, 탐색을 마치고나서 이 탐색을 마친노드가 얼리어댑터인지 아닌지를 전달해주고 싶었다.

잘 생각해보니 이럴때는 반환값을 이용하는 것이 제일 깔끔하게 처리되었던 것 같다.

예전에 전역변수나 매개변수 값을 변경하는 것으로 시도를 했었는데, 잘 안되었던 것 같다.

**우선은 반환값 이용 + 백트래킹 로직(재귀호출 뒷부분) 을 사용하는 것이 제일 괜찮은 것 같다.**

(다른 방법을 알게 되면 추가예정)

## 코드예시 (반환값 + 백트래킹 로직)

```java
    static boolean dfs(int now, int[] visited) {
        visited[now] = 1;

        if (isLeafNode(now, visited)) {
            return false; // 현재 노드가 리프노드이면 얼리어댑터가 될 수 없으므로, false 를 반환한다.
        }
        
        boolean nowIsEalryAdapter = false;
        for (int next : graph.get(now)) {  
            if (visited[next] == 1) continue;

            if(!dfs(next, visited)) { // 반환값과 반환값을 이용한 백트래킹 로직
                set.add(now);
                nowIsEalryAdapter = true;
            } 
        }

        return nowIsEalryAdapter; // boolean 값을 반환. 객체타입을 반환하면 더 할 수 있는것이 많아질 듯 하다.
    }
```

