## 문제 요약

최장 공통 부분 수열의 길이를 구하는 문제.

예를들어

```
ACAYKP
CAPCAK
```

다음과 같은 경우 최장 공통 부분 수열은 `ACAK` 가 되고, 그 길이는 4이다.

## 풀이 방법 / 접근 방법

### 1단계 

일단 2차원 DP 테이블을 그려서 아래와 같이 양 사이드에 문자열s1과 문자열s2 를 둘 생각을 해야 한다. 

![image](https://github.com/gomudayya/AlgorithmNote/assets/129571789/0c941d06-8e81-4d85-b245-078fab568f06)

이 DP테이블의 정의는 DP[i][j] = s1의 i번째 문자열과 s2의 j번째 문자열까지의 최장 공통 부분 수열의 길이 이다. 

글로 적으려니까 조금 장황하게 표현되는데 곰곰히 조금만 생각해보면 별거 아니다.

그리고 테이블의 정의는 문제에서 구하고자 하는값 (최장 공통 부분 수열의 길이) 를 생각해보면, 충분히 정의내릴 수 있다.

최장 공통 부분 수열의 길이를 구해야하는데, 단지 그것을 step by step 으로 구하자는 소리이다.

### 2단계

사실 테이블 정의까지는 했는데, 그 다음에 점화식을 어떻게 구해야할지 막막하다.

여기서 점화식을 바로 떠올리는 것은 사실 불가능하다고 생각한다.

이럴때는 **첫번째 줄 부터라도 잘 채워나가볼 생각**을 하자.

첫번째 줄을 채우다보면

### 3단계




## 관련 문제

- [백준 9251](https://www.acmicpc.net/problem/9251)

## 참고 자료

- [그림으로 알아보는 LCS 알고리즘](https://velog.io/@emplam27/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-LCS-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-Longest-Common-Substring%EC%99%80-Longest-Common-Subsequence#%EC%B5%9C%EC%9E%A5-%EA%B3%B5%ED%86%B5-%EB%B6%80%EB%B6%84%EC%88%98%EC%97%B4longest-common-subsequence-%EA%B8%B8%EC%9D%B4-%EA%B5%AC%ED%95%98%EA%B8%B0)
- 
