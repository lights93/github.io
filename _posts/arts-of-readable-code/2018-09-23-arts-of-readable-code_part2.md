---
title: "읽기 좋은 코드가 좋은 코드다 part2"
date: 2018-09-23
excerpt: "읽기 좋은 코드가 좋은 코드다 part2"
categories:
  - CodeReadability
tags:
  - artsOfReadableCode
---


# 루프와 논리를 단순화하기

## 7. 읽기 쉽게 흐름제어 만들기

* #### 비교 구문을 작성할 때는, 변화하는 값을 왼쪽에 놓고 안정적인 값을 오른쪽에 놓는 편이 좋다
	* bytes_received < bytes_expected

* #### 일반적으로 긍정적이고, 쉽고, 흥미로운 경우를 앞에 놓는 것이 좋다

* #### 삼항 연산자, do/while, goto는 사용하지 않는 것이 좋다

* #### 중첩된 코드 블록의 흐름을 따라가려면 더 집중해야 한다. 지나친 중첩을 피하기 위해 *선형적인* 코드를 추구

* #### 함수 중간에 반환하면 중첩을 피하고 코드를 더 깔끔하게 작성할 수 있다.

* #### 함수 앞 부분에 *보호 구문*으로 간단한 경우를 미리 처리하는 방식도 유용하다

## 8. 거대한 표현을 잘게 쪼개기

* #### 커다란 하위표현을 대체하는 설명 변수를 도입
	* 거대한 표현을 작은 조각으로 나눈다.
	* 하위 표현을 간결한 이름으로 대체하여 코드를 문서화한다.
	* 코드를 읽는 사람이 코드의 핵심 *개념*을 파악하는 것을 돕는다.

* #### 드모르간의 법칙 사용
	* if(!(a && b)) -> if(!a \|\| b)로 수정

* #### 복잡한 논리적 조건들을 작은 구문으로 나눈다.
	* 문제의 의미를 거꾸로 부정하거나 혹은 의도한 목적의 반대편을 생각해 볼 필요도 있다.

## 9. 변수와 가독성

* #### 변수를 덜 사용하고, 최대한 가볍게 만들어 코드의 가독성을 높일 수 있다.

* #### 방해되는 변수를 제거하라. 결과를 즉시 처리하는 방식으로 *중간 결과값*을 저장하는 변수를 제거할 수 있다.

* #### 각 변수의 범위를 최대한 작게 줄여라.

* #### 값이 한 번만 할당되는 변수를 선호하라.