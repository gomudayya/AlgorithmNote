# DFS 코드짤 때 주의점 및 팁

## 백트래킹시 상태 복구

아래 코드는 DFS를 이용해서 조합을 구해서, map에다가 누적시키는 코드이다.

```java
    public void comb(String order, int idx, int size, String comb) {
        if (comb.length() == size) {
            map.merge(comb, 1, (n1, n2) -> n1 + n2);
            return;
        }
        
        for (int i = idx; i < order.length(); i++) {
            comb += order.charAt(i);
            i++;
            comb(order, i, size, comb);
            i--;
            comb = comb.substring(0, comb.length()-1);
        }
    }
```

보통 백트래킹을 구현할 때, 재귀함수의 앞전에 재귀타기전 로직을 적고 (보통 i까지 저렇게 하진 않지만.. 예시로 ㅋㅋ)

재귀타고 나온이후에 다시 복구로직을 적어준다. **이걸 안적어주면 for문의 다음 반복회차에 영향을 끼치게 된다.**

그런데 이렇게 하면 번거로우니


```java
    public void comb(String order, int idx, int size, String comb) {
        if (comb.length() == size) {
            map.merge(comb, 1, (n1, n2) -> n1 + n2);
            return;
        }
        
        for (int i = idx; i < order.length(); i++) {
            comb(order, i+1, size, comb + order.charAt(i));
        }
    }
```

이렇게 작성하면 해당 함수스택에서 사용되는 변수값을 변화시키는 것이 아니다

따라서 for문의 다음 반복회차에 영향이 가지 않는다.

이런식으로 함수 안에서 +1 해주는 방식으로 구현하는것이 더 편리하고, 실수할 가능성도 더 적다.


### 종료조건을 적을 때 return을 먼저 써놓자.

일반적인 dfs를 작성하면 코드 구성이 다음과 같다.

```java
void dfs() {
---- 종료조건 ---- {
   --- 종료하기전 로직---
   return;
}

---- 메인로직 (함수 하나가 해주는 일. 재귀호출 발생) ---- 
}
```

대부분의 경우 이 틀을 벗어나지 않는다. 

여기서 가장 많이 실수하는 것은 종료전 로직을 작성하고 **`return`** 을 **빼먹는 것**이다.

**그래서 권장되는 것은 .. 종료조건을 작성하고, 괄호 안에 그냥 바로 return을 적고 나중에 종료전 로직을 적는것이 좋다.**
