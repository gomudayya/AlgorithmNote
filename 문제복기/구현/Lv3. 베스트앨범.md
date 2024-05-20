# [Lv3. 베스트 앨범](https://school.programmers.co.kr/learn/courses/30/lessons/42579)

사실 시키는 대로 하면 되는 구현문제인데

자바로 할 때 문법적으로 조금 까다로운 부분이 있는 것 같다.

그 부분에서만 한 번 정리하고 가면 좋을 것 같다.

그리고 확실히 **코딩 시작하기 전에 어떤식으로 할지 순서를 한 번 다 짚어보는것이 좋은 것 같다.**
  
## Map에서 value값을 기준으로 정렬하기

이 문제에서는 장르별로 재생횟수가 어떻게 되는지, 기록을 해놓는것이 필요했는데 이럴때 가장 적합한 것은 Map자료형이다.

문제는 Map자료형은 애초에 순서를 배제한 자료형이기 때문에 그자체로 정렬이 불가능하다는 것이다.

문제의 요구대로 구현하기 위해선 많이 재생된 장르대로 출력을 해야하는데, 

그러기 위해서는 Map의 Value값을 기준으로 정렬을 해주어야 한다.

이 때 나는 entrySet을 List로 만들어서 정렬했는데, 일단은 이게 젤 좋은 방법같긴 하다.

예시코드

```Java
List<String> getSortedGenres(Map<String, Integer> gcMap) {
        List<Map.Entry<String, Integer>> entryList = new ArrayList<>(gcMap.entrySet()); 
        Collections.sort(entryList, (en1, en2) -> en2.getValue() - en1.getValue());
        
        List<String> ret = new ArrayList<>();
        for (var entry : entryList) {
            ret.add(entry.getKey());
        }
        
        return ret;
    }
```

## Map의 Value값이 컬렉션타입일 경우

이 문제에서는 장르별로 노래의 고유번호가 어떻게되는지, 즉 Map<장르, 고유번호들> 과 같은 구조의 맵이 필요했다.

요런 형태의 맵을 초기화하기 위해서 이전까지는 `getOrDefault()`를 사용해서 초기화했는데, 

`putIfAbsent()` 메서드가 좀 더 괜찮을 것 같다.

코드는 아래와 같다

**[ getOrDefault()를 이용한 초기화 ]**
```java
        for (int i = 0; i <genres.length; i++) {
            List<Integer> ids = giMap.getOrDefault(genres[i], new ArrayList<>());
            ids.add(i);
            giMap.put(genres[i], ids);
        }
```

**[ putIfAbsent()를 이용한 초기화 ]**

```java
        for (int i = 0; i <genres.length; i++) {
            giMap.putIfAbsent(genres[i], new ArrayList<>());
            giMap.get(genres[i]).add(i);
        }
```

사실 별차이 없는것 같기도.... 그래도 그나마 실수할 가능성이 덜한게 후자인것 같아서 한 번 써보려고 한다.

## 주의사항

`Map.Entry` 타입을 다룰 때 꼭 제네릭까지 명시를 해주는게 좋다. `Map.Entry<String, Integer>` 

안써주면 이게 Map.Entry<Object, Object> 로 판정되는데, 이러면 형변환이 필요해져서 컴파일에러가 무지 많이 뜬다. 

안그래도 프로그래머스 환경이 디버깅하기 빡세기 때문에 이런것도 꼭 염두해주자.

## 다른 코드

스터디하면서 클럽장님의 코드를 봤는데, 괜찮아보였다.

나는 정렬을 두단계에 걸쳐서 했는데, 정렬을 한번에 할 수 있는 코드였다.

정렬을 한번만 하니까 정렬 메소드를 한번만 정의하면 되서 깔끔했다.

**다만 주의해야할 것은 <장르, 재생횟수>의 Map을 참조하기 위해선 static 타입으로 map을 선언하고, 내부 클래스를 사용해야 한다.**

```java
import java.util.*;
import java.util.stream.*;

class Solution {
    
    static Map<String, Integer> gpMap = new HashMap<>(); // <장르, 재생횟수>
    List<Music> musics = new ArrayList<>();
    List<Integer> answer = new ArrayList<>();
    
    public int[] solution(String[] genres, int[] plays) {
        for (int i = 0; i <genres.length; i++) {
            gpMap.merge(genres[i], plays[i], (v1, v2) -> v1 + v2);
            musics.add(new Music(i, plays[i], genres[i]));
        }
        
        Collections.sort(musics);
        String nowGenre = musics.get(0).genre;
        int nowCount = 0;
        for (Music music : musics) {
            if (!nowGenre.equals(music.genre)) {
                nowGenre = music.genre;
                nowCount = 0;
            }
            if (nowCount < 2) {
                answer.add(music.id);
                nowCount++;
            } else continue;
        }
        return answer.stream().mapToInt(n -> n).toArray();
    }
    
    static class Music implements Comparable<Music> {
        int id;
        int play;
        String genre;

        public Music(int id, int play, String genre) {
            this.id = id;
            this.play = play;
            this.genre = genre;
        }

        @Override
        public int compareTo(Music other) {
            int comp1 = gpMap.get(other.genre) - gpMap.get(this.genre);
            if (comp1 != 0) return comp1;
            int comp2 = other.play - this.play;
            if (comp2 != 0 ) return comp2;
            return this.id - other.id;
        }
    }
}
```

기존 내 코드
```java
import java.util.*;
import java.util.stream.*;

class Solution {
    
    Map<String, Integer> gpMap = new HashMap<>(); // <장르, 재생횟수>      
    Map<String, List<Integer>> giMap = new HashMap<>(); // <장르, 고유번호>
    List<Integer> answer = new ArrayList<>();
        
    public int[] solution(String[] genres, int[] plays) {
        for (int i = 0; i <genres.length; i++) {
            gpMap.merge(genres[i], plays[i], (v1, v2) -> v1 + v2);
            giMap.putIfAbsent(genres[i], new ArrayList<>());
            giMap.get(genres[i]).add(i);
        }
        
        List<String> sortedGenres = getSortedGenres(gpMap);
        
        for (String genre : sortedGenres) {
            List<Integer> songs = giMap.get(genre);
            Collections.sort(songs, (s1, s2) -> {
                int comp = plays[s2] - plays[s1];
                if (comp != 0 ) return comp;
                else return s1-s2;
            });
            
            //노래를 두개씩만 모아야함.
            if (songs.size() > 2) {
                answer.add(songs.get(0));
                answer.add(songs.get(1));
            } else for (Integer song : songs) answer.add(song);                

        }
        return answer.stream().mapToInt(n -> n).toArray();
    }
    
    List<String> getSortedGenres(Map<String, Integer> gpMap) {
        List<Map.Entry<String, Integer>> entryList = new ArrayList<>(gpMap.entrySet()); 
        Collections.sort(entryList, (en1, en2) -> en2.getValue() - en1.getValue());
        return entryList.stream()
            .map(entry -> entry.getKey())
            .collect(Collectors.toList());
    }
}
```
