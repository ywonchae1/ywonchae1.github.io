---
layout: default
title: Do it! 알고리즘 코딩테스트 - 소수
parent: 알고리즘 코딩테스트 - JAVA
last_modified_date: 2024-04-11
---

# Do it! 알고리즘 코딩테스트 - 소수

## [📚 개념 정리](/algorithm/note/2024-04-11-prime_number){:target="_blank"}

## 대표 문제 - 백준 1929

### [소수구하기](https://www.acmicpc.net/problem/1929){:target="_blank"}

*문제*

M이상 N이하의 소수를 모두 출력하는 프로그램을 작성하시오.

*입력*

첫째 줄에 자연수 M과 N이 빈 칸을 사이에 두고 주어진다. (1 ≤ M ≤ N ≤ 1,000,000) M이상 N이하의 소수가 하나 이상 있는 입력만 주어진다.

*출력*

한 줄에 하나씩, 증가하는 순서대로 소수를 출력한다.

*예제*

```
3 16
```

```
3
5
7
11
13
```

*풀이*

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int M = sc.nextInt();
        int N = sc.nextInt();

        int[] numbers = new int[N+1];

        // 배열 초기화, 1번 인덱스에 1, 2번 인덱스에 2..
        for(int i = 1; i < N+1; i++) {
            numbers[i] = i;
        }

        // N의 제곱근까지만 탐색해도 된다.
        for(int i = 2; i <= Math.sqrt(N); i++) {
            if(numbers[i] == 0) continue;
            for(int j = i + i; j <= N; j += i) {
                numbers[j] = 0;
            }
        }

        // print
        for(int i = M; i <= N; i++) {
            if(numbers[i] != 0 && i != 1) System.out.println(i);
        }
    }
}
```

{: .note }
> **N의 제곱근까지만 탐색하는 이유**
>
> `a*b <= N` 형태일 때 a와 b는 절대 N보다 커질 수 없다.
>
> 그러므로 N의 제곱근까지만 탐색하면 전체를 탐색했다고 볼 수 있다.