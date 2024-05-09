## DFS 코드짤 때 주의점 및 팁

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

이런식으로 작성하면 해당 함수 스택에서 사용되는 변수값을 변화시키는 것이 아니다

따라서 for문의 다음 반복회차에 영향이 가지 않는다.

이런식으로 함수 안에서 +1 해주는 방식으로 구현하는것이 더 편리하고, 실수할 가능성도 더 적다.

