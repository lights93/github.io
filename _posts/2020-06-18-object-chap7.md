---
layout: post
title: "객체 분해"
date: 2020-06-18
excerpt: "오브젝트"
tags: [object]
comments: true
---

# 7. 객체 분해

## 7.1 프로시저 추상화와 데이터 추상화

현대적인 프로그래밍 언어를 특정 짓는 중요한 두 가지 추상화 매커니즘은 **프로시저 추상화**와 **데이터 추상화**

프로시저 추상화는 소프트웨어가 무엇을 해야하는지를 추상화

데이터 추상화는 소프트웨어가 무엇을 알아야 하는지를 추상화

소프트웨어는 데이터를 이용해 정보를 표현하고 프로시저를 이용해 데이터를 조작



프로그래밍 패러다임이란 적절한 추상화의 윤곽을 따라 시스템을 어떤 식으로 나눌 것인지를 결정하는 원칙과 방법의 집합

시스템을 분해하는 방법을 결정하려면 먼저 프로시저 추상화를 중심으로 할 것인지, 데이터 추상화를 중심으로 할 것인지를 결정해야 한다.

프로시저 추상화를 중심으로 시스템을 분해하기로 결정했다면 **기능 분해(functional decompostion)** == **알고리즘 분해(algorithm decomposition)**

데이터 추상화를 중심으로 시스템을 분해하기로 결정했다면 데이터를 중심으로 **타입 추상화(type abstraction)** 또는 데이터를 중심으로 **프로시저를 추상화(procedure abstraction)**

전자를 추상 **데이터 타입(abstract data type)**, 후자를 **객체 지향(Object-Oriented)**

## 7.2 프로시저 추상화와 기능 분해

### 메인 함수로서의 시스템

기능 분해의 관점에서 추상화의 단위는 프로시저이며 시스템은 프로시저를 단위로 분해

프로시저는 반복적으로 실행되거나 거의 유사하게 실행되는 작업들을 하나의 장소에 모아놓음으로써 로직을 재사용하고 중복을 방지할 수 있는 추상화

프로시저 중심의 기능 분해 관점에서 시스템은 수학의 함수와 동일



전통적인 기능 분해 방법은 하향식 접근법을 따른다.

하향식 접근법이란 시스템을 구성학는 가장 최상위 기능을 정의하고, 이 최상위 기능을 좀 더 작은 단계의 하위 기능으로 분해해 나가는 방법

각 세분화 단계는 바로 위 단계보다 더 구체적이어야 함



### 급여 관리 시스템

예제로 급여 관리 시스템



기능 분해 방법에서는 기능을 줌심으로 필요한 데이터를 결정

이것은 유지보수에 다양한 문제를 야기

### 급여 관리 시스템 구현

### 하향식 기능 분해의 문제점

- 시스템은 하나의 메인 함수로 구성돼 있찌 않다
- 기능 추가나 요구사항 변경으로 인해 메인 함수를 빈번하게 수정해야 한다
- 비즈니스 로직이 사용자 인터페이스와 강하게 결합된다
- 하향식 분해는 너무 이른 시기에 함수들의 실행 순서를 고정시키기 때문에 유연성과 재사용성이 저하된다
- 데이터 형식이 변경될 경우 파급효과를 예측할 수 없다



설계는 코드 배치 방법이며 설계가 필요한 이유는 변경에 대비하기 위함

변경은 성공적인 소프트웨어가 맞이해야 하는 피할 수 없는 운명

하향식 접근법과 기능 분해가 가지는 근본적인 문제점은 변경에 취약한 설계를 낳는다는 것

#### 하나의 메인 함수라는 비현실적인 아이디어

대부분의 시스템에서 하나의 메인 기능이란 개념은 존재하지 않음

하향식 접근법은 하나의 알고리즘을 구현하거 배치 처리를 구현하기에는 적합하지만 현대적인 상호작용 시스템을 개발하는 데는 적합하지 않다

현대적인 시스템은 동등한 수준의 다양한 기능으로 구성됨

#### 메인 함수의 빈번한 재설계

새로운 기능을 추가할 때마다 매번 메인 함수를 수정해야 함

기존 코드를 수정하는 것은 항상 새로운 버그를 만들어낼 확률을 높인다

#### 비즈니스 로직과 사용자 인터페이스의 결합

하향식 접근법은 비즈니스 로직을 설계하는 초기 단계부터 입력 방법과 출력 양식을 함께 고민하도록 강요함



문제는 비즈니스 로직과 사용자 인터페이스가 변경되는 빈도가 다름

사용자 인터페이스는 시스템 내에서 가장 자주 변경되는 부분

사용자 인터페이스를 변경하는 경우 비즈니스 로직까지 영향을 받음

따라서 하향식 접근법은 근본적으로 변경에 불안정한 아키텍처를 낳음

#### 성급하게 결정된 실행순서

설계를 시작하는 시점부터 시스템이 무엇을 해야 하는지가 아니라 어떻게 해야 하는데 집중하도록 만든다

실행 순서나 조건, 반복과 같은 제어 구조를 미리 결정하지 않고는 분해를 진행할 수 없기 때문에 기능 분해 방식은 중앙집중 제어 스타일의 형태를 띨 수밖에 없다.

결과적으로 모든 중요한 제어 흐름의 결정이 상위 함수에서 이루어지고 하위 함수는 상위 함수의 흐름에 따라 적절한 시점에 호출됨

문제는 중요한 설계 결정사항인 함수의 제어 구조가 빈번한 변경의 대상이라는 점

이를 해결할 수 있는 한 가지 방버은 자주 변경되는 시간적인 제약에 대한 미련을 버리고 좀 더 안정적인 논리적 제약을 설계의 기준으로 삼는 것



하향식 접근법을 통해 분해한 함수들은 재사용하기도 어려움

함수가 재사용하려면 상위 함수보다 더 일반적이어야 함

하지만 하향식 접근법을 따를 경우 분해된 하위 함수는 항상 상위 함수보다 문맥에 종속적



하향식 설계와 관련된 모든 문제의 원인은 **결합도**

함수는 상위 함수가 강요하는 문맥에 강하게 결합됨

전체 시스템의 핵심적인 구조를 결정하는 함수들이 데이터와 강하게 결합됨

#### 데이터 변경으로 인한 파급효과

하향식 기능 분해의 가장 큰 문제점은 어떤 데이터를 어떤 함수가 사용하고 있는지를 추적하기 어렵다는 것

데이터 변경으로 인해 어떤 함수가 영향을 받을지 예상하기 어렵다



의존성과 결합성의 문제

구현된 모든 함수를 분석해셔 영향도를 파악해야 한다



데이터 변경으로 인해 발생하는 함수에 대한 영향도으를 파악하는 것이 생각보다 어렵다

데이터 변경으로 인한 영향을 최소화하려면 데이터와 함께 변경되는 부분과 그렇지 않은 부분을 명확하게 분리해야 함

데이터와 함께 변경되는 부분을 하나의 구현 단위로 묶고 외부에서는 제공되는 함수만 이용해 데이터에 접근해야 한다

즉, 잘 정의된 퍼블릭 인터페이스를 통해 데이터에 대한 접근을 통제해야 함



변경에 대한 영향을 최소화하기 위해 영향을 받는 부분과 받지 않는 부분을 명확하게 분리하고 잘 정의된 인터페이스를 통해 변경되는 부분에 대한 접근을 통제

### 언제 하향식 분해가 유용한가?

설계가 어느 정도 안정화 된 후에는 설계의 다양한 측면을 논리적으로 설명하고 문서화하기에 용이함



하향식은 완전히 이해된 사실을 서술하기에 적합한 방법

하향식은 새로운 것을 개발하고, 설계하고, 발견하는 데는 적합한 방법이 아니다.



하향식 분해 방식으로 설계된 소프트웨어는 하나의 함수에 제어가 집주오디기 때문에 확장이 어렵다.

하향식 분해는 프로젝트 초기에 설계의 본질적인 측면을 무시하고 사용자 인터페이스 같은 비본질적인 측면에 집중하게 만듦

## 7.3 모듈

### 정보 은닉과 모듈

기능을 기반으로 시스템을 분해하는 것이 아니라 변경의 방향에 맞춰 시스템을 분해하는 것



**정보 은닉(information hiding)**은 시스템의 모듈 단위로 분해하기 위한 기본 원리로 시스템에서 자주 변경되는 부분을 상대적으로 덜 변경되는 안정적인 인터페이스 뒤로 감춰야 함

정보 은닉은 외부에 감춰야 하는 비밀에 따라 시스템을 분할하는 모듈 분할 원리

모듈은 변경될 가능성이 있는 비밀을 내부로 감추고, 잘 정의되고 쉽게 변경되지 않을 퍼블릭 인터페이스를 외부에 제공해서 내부의 비밀에 함부로 접근하지 못하게 함



모듈과 기능 분해는 상호 배타적인 관계가 아니다

시스템을 모듈로 분해한 후에는 각 모듈 내부를 구현하기 위해 기능 분해를 적용할 수 있다



시스템을 모듈 단위로 어떻게 분해?? -> 시스템이 감춰야 하는 비밀을 찾아라

모듈은 다음과 같은 두 가지 비밀을 감춰야 함

- 복잡성: 모듈이 너무 복잡한 경우 이해하고 사용하기가 어렵다. 외부에 모듈을 추상화할 수 있는 간단한 인터페이스를 제공해서 모듈의 복잡도를 낮춘다.
- 변경 가능성: 변경 가능한 설계 결정이 외부에 노출될 경우 실제로 변경이 발생했을 때 파급효과가 커진다.

### 모듈의 장점과 한계

모듈의 장점

#### 모듈 내부의 변수가 변경되더라도 모듈 내부에만 영향을 미친다

모듈을 사용하면 모듈 내부에 정의된 변수를 직접 참조하는 코드의 위치를 모듈 내부로 제한할 수 있다.

모듈은 데이터 변경으로 인한 파급효과를 제어할 수 있기 때문에 코드를 수정하고 디버깅하기가 더 용이하다.

#### 비즈니스 로직과 사요자 인터페이스에 대한 관심사를 분리한다

#### 전역 변수와 전역 함수를 제거함으로써 네임스페이스 오염(namesplace pollution)을 방지한다

모듈은 전역 네임스페이스의 오염을 방지하는 동시에 이름 충돌의 위험을 완화



모듈은 기능이 아니라 변경의 정도에 따라 시스템을 분해하게 한다.

각 모듈은 외부에 감춰야 하는 비밀과 관련성 높은 데이터와 함수의 집합

모듈 내부는 높은 응집도를 유지

모듈과 모듈 사이에는 퍼블릭 인터페이스를 통해서만 통신해야 한다 -> 낮은 결합도



모듈이 정보 은닉이라는 개념을 통해 데이터라는 존재를 설계의 중심 요소로 부각시킴

모듈에 있어서 핵심은 데이터

기능이 아니라 데이터를 중심으로 시스템을 분해

모듈은 데이터와 함수가 통합된 한 차원 높은 추상화를 제공하는 설계 단위



모듈이 프로시저 추상화보다는 높은 추상화 개념을 제공하지만 태생적으로 변경을 관리하기 위한 구현 기법이기 때문에 추상화 관점에서의 한계점이 명확

모듈의 가장 큰 단점은 인스턴스의 개념을 제공하지 않는다는 점

좀 더 높은 수준의 추상화를 위해서는 독립적인 단위로 다룰 수 있어야 함

## 7.4 데이터 추상화와 추상 데이터 타입

### 추상 데이터 타입

**타입(type)**: 변수에 저장할 수 있는 내용물의 종류와 변수에 적용될 수 있는 연산의 가짓수



추상 데이터 타입은 프로시저 추상화 대신 데이터 추상화를 기반으로 소프트웨어를 개발하게 한 최초의 발걸음



추상 데이터 타입을 구현하기 위한 프로그래밍 언어의 특성

- 타입 정의를 선언할 수 있어야 함
- 타입의 인스턴스를 다루기 위해 사용할 수 있는 오퍼레이션의 집합을 정의할 수 있어야 함
- 제공된 오퍼레이션을 통해서만 조작할 수 있도록 데이터를 외부로부터 보호할 수 있어야 함
- 타입에 대해 여러 개의 인스턴스를 생성할 수 있어야 함



추상 데이터 타입은 사람들이 세상을 바라보는 방식에 좀 더 근접해지도록 추상화 수준을 향상시킴

추상 데이터 타입 정의를 기반으로 객체를 생성하는 것은 가능하지만 여전히 데이터와 기능을 분리해서 바라봄

## 7.5 클래스

### 클래스는 추상 데이터 타입인가?

명확한 의미에서 추상 데이터 타입과 클래스는 동일하지 않다

가장 핵심적인 차이는 클래스는 상속과 다형성을 지원하는 데 비해 추상 데이터 타입은 지원하지 못함

추상 데이터 타입은 타입을 추상화한 것이고 클래스는 절차를 추상화한 것



추상 데이터 타입이 오퍼레이션을 기준으로 타입을 묶는 방법이라면 객체지향은 타입을 기준으로 오퍼레이션을 묶는다

클라이언트의 관점에서 두 클래스의 인스턴스는 동일하게 보임

실제로 내부에서 수행되는 절차는 다르지만 클래스를 이용한 다형성은 절차에 대한 차이점을 감춘다 -> 절차 추상화

### 추상 데이터 타입에서 클래스로 변경하기

### 변경을 기준으로 선택하라

타입을 깆누으로 절차를 추상화하지 않았다면 그것은 객체지향 분해가 아니다

클래스가 추상 데이터 타입의 개념을 따르는지를 확인할 수 있는 가장 간단한 방법은 클래스 내부에 인스턴스의 타입을 표현하는 변수가 있는지 살펴보는 것



객체 지향에서는 타입 변수를 이용한 조건문을 다형성으로 대체

클라이언트가 객체의 타입을 확인한 후 적절한 메서드를 호출하는 것이 아니라 객체가 메시지를 처리할 적절한 메서드를 선택



조건문을 사용하는 방식을 기피하는 이유 역시 변경 때문

객체지향은 구현하는 클래스를 추가하고 오버라이딩 하면 됨



**개방-폐쇄 원칙(Open-Closed Principle, OCP)**: 기존 코드에 아무련 영향도 미치지 않고 새로운 객체 유형과 행위를 추가할 수 있는 객체지향의 특성



새로운 타입을 빈번하게 추가해야 한다면 객체지향의 클래스 구조가 더 유용함

새로운 오퍼레이션을 빈번하게 추가해야 한다면 추상 데이터 타입을 선택하는 것이 현명한 판단

### 협력이 중요하다

## 참조

1. 오브젝트
