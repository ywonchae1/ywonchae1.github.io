---
layout: default
title: 그래프 표현
parent: Do it! 알고리즘 코딩테스트
last_modified_date: 2024-04-14
---

# Do it! 알고리즘 코딩테스트 - 그래프 표현
{: .no_toc }

> 코드에서 그래프를 표현하는 방법을 공부합시다.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## 에지리스트

![](/attachment/2024/04/14/graph-1.png)

짝을 리스트에 저장한다.

## 가중치가 있는 에지리스트

![](/attachment/2024/04/14/graph-2.png)

짝과 함께 가중치를 저장한다.

## 인접행렬

![](/attachment/2024/04/14/graph-3.png)

인덱스를 노드 번호로 취급하며 연결되어 있다면 1을 저장한다.

## 가중치가 있는 인접행렬

![](/attachment/2024/04/14/graph-4.png)

1을 저장했던 부분에 가중치를 저장한다.

{: .note }
> 인접행렬은 공간복잡도도 크지만, 시간복잡도도 크다.
>
> 노드 3과 연결되어 있는 노드를 찾기 위해서는 A[3]에서 각 칸을 탐색해야 하기 위해 N번의 탐색 과정이 필요하기 때문이다.

## 인접리스트

![](/attachment/2024/04/14/graph-5.png)

## 가중치가 있는 인접리스트

![](/attachment/2024/04/14/graph-6.png)

Node 클래스를 구현해야 한다는 점이 중요하다.