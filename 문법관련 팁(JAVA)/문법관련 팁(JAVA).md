## Stream 

프로그래머스에서 `import java.util.*;` 만 써서는 `...collect(Collectors.toList());` 와 같은 코드가 작동을 안함.

**`import java.util.stream.*;` 를 임포트 해주어야 함.**

## 리스트를 배열로 변환하기

문제 풀 때는 리스트가 더 쓰기 유용한데, solution의 반환값을 배열로 바꾸어야 하는 경우가 많음.

타입별로 변환하는 방법이 다르다.

1. List<String>

  - **`list.toArray(new String[0]);`**

  - 사이즈를 0으로 하는 이유는, 사이즈가 부족하면 알아서 리스트 사이즈에 맞춰서 만듬.

2. List<Integer>

  - `toArray()`를 사용하면 안됨. 랩핑타입(Integer)와 원시타입(int) 간의 괴리 때문에 컨버팅이 안됨

  - 그래서 보통 다음과 같이 사용함 **`list.stream().mapToInt(n -> n).toArray();`**

## 배열을 리스트로 변환하기

**`Arrays.asList(arr);` 를 사용한다.**

`List.of()`는 인자로 배열을 받지 못하지만 `Arrays.asList()`는 가변인자를 받기 때문에 배열을 받을 수 있다. 

하지만 `List.of()` 둘 다 `Arrays.asList()` 내부 요소를 조작하는것이 잘 안된다.

`List.of()` 는 애초에 불변리스트를 반환하고,

`Arrays.asList()`는 Arrays 클래스의 내부클래스로 있는 ArrayList를 반환하는데, 이 리스트에는 add함수가 없다.

(참고로 자바는 어레이리스트 클래스가 두개이다. java.util.ArrayList 와 java.util.Arrays의 내부클래스 ArrayList)

그래서 **내부 요소를 조작해야 한다면 `new ArrayList<>(Collection)` 을 사용해서 아에 새로운 ArrayList를 생성하자.**

[Arrays.asList() 와 List.of() 차이 한방 정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-ArraysasList-%EC%99%80-Listof-%EC%B0%A8%EC%9D%B4-%ED%95%9C%EB%B0%A9-%EC%A0%95%EB%A6%AC)

## StringBuilder

성능을 끌어올려야 하는 문자열 문제들을 풀 때 사용한다. 

**자바의 String은 굉장히 비효율적이여서, 길이가 긴 문자열을 다루어야 하는 문제가 나온다면 시간초과 나기가 쉽다.**

그리고 String타입이면 substring으로 자르고 붙이고 참 귀찮지만, 스트링 빌더를 사용하면 내부 문자열를 지우기가 더 편리하다. 

**`sb.delete(시작 인덱스, 마지막 인덱스+1)`** 를 사용해서 **내부 문자열을 없앨 수 있다**.

**내부 문자열을 지우거나 붙이는 연산이 자주 발생하면 StringBuilder를 사용하자.**

## Map

### Map을 정렬해보자.

간혹가다 Map의 key를 value값을 이용해서 정렬해야하는 경우가 있다. ex) <판매목록, 판매량> 을 판매량이 높은순으로 정렬하기

이 때는 Map.Entry 타입으로 정렬하면 된다. 

```java
      public List<String> sortMap(Map<String, Integer> map) {
          List<Map.Entry<String, Integer>> list = new ArrayList<>(play.entrySet());
      
          //판매량이 높은순으로 정렬. 낮은순이면 entry1.getValue() - entry2.getValue()
          Collections.sort(list, (entry1, entry2) -> entry2.getValue() - entry1.getValue()); 
          return list.stream()
              .map(entry -> entry.getKey())
              .collect(Collectors.toList());
      }
```

그리고 순서가 보장되는 Map을 사용하고 싶으면 `LinkedHashMap`을 사용하면 된다. 

인덱스로 접근은 안되지만 iterator로 순회할 때 순서대로 접근한다. (향상된 for문)

### merge를 사용하여 코드를 간단하게 작성해보자

자바로 알고리즘을 풀면서 Map을 사용해보면 null체크 때문에 작성하기가 참 지저분하다.

보통 처음 사용하면 containsKey를 이용하고, 그다음은 getOrDefault()를 이용하곤 한다.

사용해보면 얘네 둘다 썩 만족스럽지 못하다. 가능하면 merge()를 이용해서 해보자.

아래의 입력을 merge()를 이용해서 Map에다가 정리해보자. 장르에 따른 재생횟수가 나오는 입력이다.

![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/eeac0258-1b58-4cde-884f-9138857714b2)

```java
      Map<String, Integer> play = new HashMap<>();    // <장르, 재생횟수>

      for (int i = 0; i < genres.length; i++) {
          play.merge(genres[i], plays[i], (v1, v2) -> v1+v2);
      }
```

### Map의 Value타입이 리스트일 경우

`putIfAbsent(new ArrayList<>())`를 이용하자.

이게 getOrDefault()보다 더 편리한 것 같다.

예시 코드
```java
  for (String key : keys) {
      map.putIfAbsent(key, new ArrayList<>());
      map.get(key).add(value에 해당되는 값);
  }
```

## 문자열

### split

- 보통 공백으로 나누는경우가 많긴 한데, 경우에 따라 '.'과 같은 특수문자로 나누어야 할 때도 있다.

- 이 때 str.split(".")과 같이 작성하면 안된다. 이스케이프를 써서 str.split("//.")과 같이 작성해주어야 한다. (\가 2개임)
