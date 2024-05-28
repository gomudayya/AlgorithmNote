# DFS 상태값을 외부에서 변경시키기

보통 재귀 (DFS) 같은 경우 매개변수에 상태를 담아서 관리한다.

그런데 꼭 매개변수에만 상태를 담을 수 있는것은 아니다.

자바같은 경우에는 **클래스변수에 상태를 설정하고, 클래스 변수의 상태값을 변경하는식으로 구현할 수도 있다.**

##  [관련문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/84512)

이 문제를 처음 풀 때 매개변수에 `(int count)` 추가해서, 사전에서 몇번째 단어인지 관리하려고 했다.

하지만 이렇게해서 작성하기가 거의 불가능하다. (가능은 하려나..?,, 모르겠다 해보니까 힘든 것 같긴 하다.)

왜냐하면 이 문제의 경우 DFS 함수 호출이 한 번 일어날 때 마다, count값을 +1 해주어야 한다. 

A는 1번째, AA는 2번째 AAA는 3번째.. 이런식이니까.. 

하지만 **매개변수의 경우 지역변수에 속하기 때문에 해당 함수가 return 된다면 그 데이터가 휘발되어버린다.**

**그렇기 때문에 매개변수 만으로는 count값을 계속 끌고갈수가 없는 것이다. 하지만 예외는 있는데, reference 자료형을 쓰면 된다.**

아래와 같은 구현은 가능하다

```java
import java.util.*;

class Solution {
    
    int answer = 0;
    char[] arr = {'A', 'E', 'I', 'O', 'U'};
    
    public int solution(String word) {
        dfs("", new Count(), word);
        return answer;
    }
    
    void dfs(String status, Count count, String word) {
        count.plus();
        if (status.equals(word)) {
            answer = count.num;
            return;
        }
        if (status.length() == 5) return;
        for (int i = 0; i < 5; i++) {
            dfs(status + arr[i], count, word);
        }
    }
}
class Count {
    int num = -1;
    void plus() {
        num++;
    }
}
```

reference를 통해 상태를 변화시키는 것이다. `Count count` 에 해당하는 reference는 

함수 콜스택 가장 밑에있는 solution()에서 만들어져 계속 이어지기 때문에 매개변수 임에도 휘발되지가 않는것이다. 

그런데 이런식으로 하기는 조금 어색하기도하고(짜치니까), List를 이용한 풀이가 많다. 

```java
import java.util.*;

class Solution {
    
    int answer = 0;
    char[] arr = {'A', 'E', 'I', 'O', 'U'};
    
    public int solution(String word) {
        dfs("", new ArrayList<>(), word);
        return answer;
    }
    
    void dfs(String status, List<String> list, String word) {
        list.add(status);
        if (status.equals(word)) {
            answer = list.size()-1;
            return;
        }
        if (status.length() == 5) return;
        for (int i = 0; i < 5; i++) {
            dfs(status + arr[i], list, word);
        }
    }
}
```

마찬가지로 리스트도 레퍼런스를 사용하는, 객체자료형이기 때문에 이와같은 풀이가 가능하다.

그러면 원사타입 같은 경우에는 이런방식의 구현이 불가능한것일까?

결론적으로 원시타입의 경우 클래스 변수를 사용하면 된다.

```java
class Solution {
    
    String[] ch = {"A", "E", "I", "O", "U"};
    int answer = -1;
    boolean exit = false;
    public int solution(String word) {
        dfs("", word);
        return answer;
    }
    
    void dfs(String str, String word) {
        if (exit) return;
        answer++;
        if (word.equals(str)) {
            exit = true;
            return;
        }
        if (str.length() == 5) return;
        for (int i = 0; i < 5; i++) {
            str += ch[i];
            dfs(str, word);
            str = str.substring(0,str.length()-1);
        }
    }
}
```

클래스 변수로 선언한 answer 값을 dfs함수가 호출될 때 마다 1씩 더해주는 방식이다. 

(결론 : 다양한 방법이 있고, 이런 구현도 가능하다.)
