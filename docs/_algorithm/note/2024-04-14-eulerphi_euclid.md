---
layout: default
title: 오일러피, 유클리드 호제법
parent: Do it! 알고리즘 코딩테스트
last_modified_date: 2024-04-14
---

# Do it! 알고리즘 코딩테스트 - 오일러피, 유클리드 호제법
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## 오일러피

1부터 N까지의 숫자 중 N과 서로소인 수의 개수를 구하는 문제

> 원리를 모르면 풀기가 어려우니 원리를 이해하자

1. 1부터 N까지의 배열을 만든다.
2. 2부터 시작한다. ➡️ 이 숫자가 K가 된다.
  - 인덱스와 값이 같으면 다음 연산을 수행한다.
  - 값 - (값 / K)의 결과를 그 칸에 다시 대입한다.
  - K의 배수인 칸으로 이동한다.
  - 배열의 끝까지 2를 반복한다.
3. 배열의 끝인 N까지 왔다면 N번째 인덱스의 값이 N과 서로소인 수의 개수이다.

![](/attachment/2024/04/14/euler_phi.jpeg)

N과 나누어질 수 있는 수를 하나씩 제거하면서 찾아내는 방법이다.

## 유클리드 호제법

최대공약수를 쉽게 구하는 방법, MOD 연산(%)을 활용한다.

![](/attachment/2024/04/14/euclid.png)

재귀함수로 쉽게 구현할 수 있다.

```cpp
int gcd(int a, int b) {
  if(b == 0) return a;
  return gcd(b, a % b);
}
```