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

`Arrays.asList(arr);` 를 사용한다.

개발할 때는 보통 `List.of()`를 많이 쓰지만 여러모로 코딩테스트에서는 `List.of()` 보다 `Arrays.asList()`가 더 유용하다. 

이유 

- `List.of()`는 인자로 배열을 받지못한다.

- `List.of()`는 불변리스트를 반환하기 때문에 **내부 요소를 조작할 수 없다. 내부 요소를 조작해야하는 경우도 빈번한 알고리즘에서는 안 좋다.**

그래서 List.of()는 지양하고 **`new ArrayList<>(컬렉션타입)` 또는 `Arrays.asList(배열 or 가변인자)`** 를 사용하자.

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
