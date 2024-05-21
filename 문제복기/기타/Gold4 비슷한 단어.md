# [G4. 비슷한 단어](https://www.acmicpc.net/problem/2179)

## Intro

열심히 풀었는데 정답코드를 보고 좀 허무하고, 어이가 없었다..

나는 이 문제보고 N제한이 20000이라서 그냥 단순히 이중포문 돌리는것으로는 시간초과가 날것이라고 생각했는데,

왜 시간초과가 안나는지 모르겠다. n(n+1)/2 에다가 인덱스 알파벳별 접근까지 하면 1억을 훨씬 상회할 텐데....

엄청 어렵게 최적화해서 풀었는뎁.... 좀 허무했다.

## 문제 접근

n^2으로 푸는 방법은 처음부터 배제했기 때문에 최적화를 위해 고민을 많이했다.

보통 이럴때 알아낸 팁은, N이외의 **또 다른 입력의 최대 제한으로 눈을 돌리는 것이다.**

이 문제에서는 2<=N<=20,000 이라는 조건 이외에 **알파벳 소문자의 길이가 100이하**라는 조건이 있다.

어떻게 20,000 * 100 으로 도는 알고리즘을 작성한다면, 얼추 수행횟수 200만으로 풀어낼 수 있다.

## 해시를 사용하기

알파벳의 소문자길이가 100이하라는 조건을 보고,

100번에 걸쳐서 N을 순회하면서 알파벳을 한글자씩 Map에다가 추가하려는 생각을 했다. (100 * 20000)

이렇게 붙여진 알파벳을 Key로 두고, 붙여진 횟수마다 value값을 +1해준다.

그렇다면 value가 1이라면 해당 Key를 접두어로 가지는 비슷한 단어가 없는것이고,

value가 2이상이라면 해당 Key를 접두어로 가지는 비슷한 단어가 있는것이다.

그렇게 **Value가 2이상으로 갱신 될때마다, 접두어 (maxHeader)를 update**해주는 식으로 풀면 된다.

그리고 추후에 앞에서부터 N전체를 한번 더 돌면서 접두어로 시작하는 단어 두개를 골르면 된다.

## 출력 조건

이런식으로 하면 출력조건에서 조금 까다로운부분이 있다.

'먼저 같은단어는 비슷한단어로 치지 않는다.' 라는 말이 있다.

따라서 전처리로직으로 중복된단어는 제거해버렸다. **이런식으로 없앨거는 사전에 없애고가는게 편할때가 종종 있다.**

그리고 '길이가 비슷한 접두어가 여러개면 앞에있는 것 두개를 출력해야한다' 는 조건이 있다.

즉 동일 길이면 접두어가 가장 앞에있는 것을 maxHeader로 갱신해야 하는데, 지금 방법처럼 갱신하면은 동일 길이일 때 뒤쪽에있는 접두어가 업데이트 된다.

**따라서 거꾸로 생각해서, 즉 for문을 거꾸로 돌면서 value가 2이상으로 갱신될 때 접두어를 update 해주었다.**

## 코드

```java
import java.util.*;
import java.util.stream.*;
import java.io.*;

public class Main {
	
	static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	static Map<String, Integer> map = new HashMap<>();
	
	public static void main(String[] args) throws IOException {
		int N = Integer.parseInt(br.readLine());
		String[] words = new String[N];
		for (int i = 0; i < N; i++) 
			words[i] = br.readLine();
		
		words = removeDuplicate(words);
		int maxLength = getMaxLength(words);
		String maxHeader = "";
		
		for (int i = 0; i < maxLength; i++) {
			for (int j = words.length-1; j >= 0; j--) {
				if (i > words[j].length()) continue;
				
				String slice = words[j].substring(0, i);				
				map.merge(slice, 1, (v1, v2) -> v1 + v2);
				
				if(map.get(slice) >= 2) {
					maxHeader = slice;
				}
			}
		}
		
		List<String> answer = new ArrayList<>();
		for (String word : words) {
			if(word.startsWith(maxHeader)) {
				answer.add(word);
				if(answer.size() == 2) break;
			}
		}
		
		answer.forEach(s -> System.out.println(s));
	}
	
	static int getMaxLength(String[] words) {
		int maxLength = 0;
		for (String word : words) {
			maxLength = Math.max(word.length(), maxLength);
		}
		return maxLength;
	}
	
	static String[] removeDuplicate(String[] words) {
		Set<String> set = new LinkedHashSet<>();
		for (String word : words) set.add(word);
		
		String[] ret = new String[set.size()];
		int idx = 0;
		for (String word : set) {
			ret[idx++] = word;
		}
		
		return ret;
	}
}
```

### [참고] N^2으로 도는 코드 

```java
import java.io.*;

public class Main {
	
	static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	public static void main(String[] args) throws IOException {
		int N = Integer.parseInt(br.readLine());
		String[] words = new String[N];
		for (int i = 0; i < N; i++) words[i] = br.readLine();
		
		String S = "", T = "";
		int maxCount = 0;
		for (int i = 0; i < N; i++) {
			for (int j = i+1; j < N; j++) {
				if (words[i].equals(words[j])) continue;
				
				int len = Math.min(words[i].length(), words[j].length());
				int count = 0;
				for (int k = 0; k < len; k++) {
					if (words[i].charAt(k) == words[j].charAt(k)) {
						count++;
					} else break;
				}
				if (count > maxCount) {
					maxCount = count;
					S = words[i];
					T = words[j];
				}
			}
		}
		System.out.println(S);
		System.out.println(T);
	}
}
```

시간차이는 꽤 난다. 아마 N이 10만정도만 됐어도 N^2으로는 안풀렸을거다. ㅂㄷㅂㄷ
![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/1a6d5274-8a6d-4d99-b89a-a4bca3dcf67b)


