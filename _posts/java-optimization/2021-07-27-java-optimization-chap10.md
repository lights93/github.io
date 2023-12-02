---
title: "JIT 컴파일의 세계로"
date: 2021-07-27
excerpt: "자바 최적화"
categories:
  - java
tags:
  - optimization
  - java
---

# 10. JIT 컴파일의 세계로

## 10.1 JITWatch란?

개발/데브옵스 담당자가 JITWatch를 이용하면, 애플리케이션 실행 중에 핫스팟이 실제로 바이트코드에 무슨 일을 했는지 이해하는 데 도움이 됨

JITWatch는 객관적인 비교에 필요한 측정값을 제공

JITWatch가 하는 일은, 실행 중인 자바 애플리케이션이 생성한 핫스팟 컴파일 상세 로그를 파싱/분석하여 그 결과를 GUI 형태로 보여주는 것

```
-XX:+UnlockDiagnosticVMOptions -XX:+TraceClassLoading -XX:+LogCompilation
```

### 10.1.1 기본적인 JITWatch 뷰

JITWatch는 실행시킨 프로그램에서 로그를 적재하는 일뿐만 아니라, JIT 작동을 시험해볼 수 있는 **샌드박스(sandbox)**라는 환경을 제공

샌드박스를 이용하면 작은 프로그램을 신속히 프로토타이핑하여 JVM이 어떤 JIT 결정을 내렸는지 확인 가능

RUN 버튼 클릭시 다음 작업 수행

1. 프로그램을 바이트코드로 컴파일
2. JIT 로그를 켜 놓고 JVM에서 프로그램을 실행
3. 분석용 JIT 로그파일을 JITWatch에 로드


샌드박스가 일반 JVM 애플리케이션 실행과 다른 점은, 풀 사이즈 애플리케이션에서 JVM이 단지 샌드박스 한 조각이 아닌, 훨씬 광범위한 코드를 대상으로 최적화를 조합할 수 있다는 것

JITWatch는 3단뷰(triview)라는 다재다능한 뷰를 메인으로 제공

이 뷰를 보면 소스 코드가 바이트코드, 어셈블리 양쪽으로 어떻게 컴파일됐는지 알 수 있다

### 10.1.2 디버그 JVM과 hsdis

디버그 JVM은 운영 JVM보다 더 상세한 디버깅 정보를 추출하려고 제작한 가상 머신

JIT 컴파일러가 생성한, 역어셈블된 네이티브 코드를 살펴보로면 hsdis 같은 역어셈블리 바이너리가 있어야함

메서드 어셈블리를 출력하기 위한 스위치

```
-XX:+PrintAssembly
```

## 10.2 JIT 컴파일 개요

핫스팟은 프로파일 기반 최적화(PGO)를 이옹해 JIT 컴파일 여부를 판단

내부적으로는 핫스팟이 실행 프로그램 정보를 메서드 데이터 객체(method data object, MDO)라는 구조체에 저장

MDO의 쓰임새는 바이트코드 인터프리터와 C1 컴파일러에서 JIT 컴파일러가 언제, 무슨 최적화를 할지 결정하는데 필요한 정보를 기록하는 것

프로파일링된 프로퍼티의 '사용 빈도'를 카운터에 계속 기록하고 그렇게 기록한 값들을 프로파일링을 거치면서 차츰 사라짐

컴파일 큐 맨 앞에 이르렀을 때도 아직 핫한 메서드만 컴파일됨

컴파일러는 컴파일할 코드의 내부 표현형을 빌드

컴파일러는 이 내부 표현형을 토대로 코드 컴파일


핫스팟 JIT 컴파일러는 다양한 최신 컴파일러 최적화 기법을 총동원

- 인라이닝
- 루프 펼치기
- 탈출 분석
- 락 생략/확장
- 단일형 디스패치
- 인트린직
- 온-스택 치환

핫스팟의 C1/C2 컴파일러 역시 이들 기법을 상이하게 조합해서 사용하며, 기본적으로 컴파일에 접근하는 철학 자체가 다름

C1은 추측성 최적화를 안 함

C2는 런타임 실행을 주시한 결과를 토대로 추정을 하고 그에 따른 최적화를 수행

추측성 최적화를 하기 전에 항상 **가드**라는 타당성 검사를 함

가드는 앞에서 추정한 내용이 여전히 유효한지 최적화된 코드를 실행할 때마다 확인

가드마저 실패하면 더 이상 컴파일드 코드는 안전하지 않으므로 제거

## 10.3 인라이닝

호출된 메서드(피호출부)의 콘텐츠를 호출한 지점에 복사하는 것

메서드 호출 시 다음과 같은 오버헤드 제거 가능

- 전달할 매개변수 세팅
- 호출할 메서드를 정확하게 룩업
- 새 호출 프레임에 맞는 런타임 자료 구조(지역 변수 및 평가 스택 등) 생성
- 새 메서드로 제어권 이송
- 호출부에 결과 반환(결괏값이 있는 경우)

인라이닝은 JIT 컴파일러가 제일 먼저 적용하는 최적화라서, **관문 최적화(gateway optimization)**라고도 함

예시)

```java
int result = add(a, b);


private int add(int x, int y) {
	return x+y;
}
```



```
int result = a + b;
```

인라이닝은 다른 최적화의 범위를 확장시키는 역할을 함

- 탈출 분석
- DCE(dead code elimination, 죽은 코드 제거)
- 루프 펼치기
- 락 생략

### 10.3.1 인라이닝 제한

인라이닝 서브시스템에 제한을 걸어야 하는 경우

VM에서 다음 항목을 조정해야 할 때

- JIT 컴파일러가 메서드를 최적화하는 데 소비하는 시간
- 생성된 네이티브 코드 크기(즉, 코드 캐시 메모리 사용량)

제약 조건이 하나도 없으면 컴파일러는 아주 깊은 호출 체인/가지 파헤치며 인라이닝함


핫스팟은 다음 항목을 따져보며 어떤 메서드를 인라이닝할지 결정

- 인라이닝할 메서드의 바이트코드 크기
- 현재 호출 체인에서 인라이닝할 메서드의 깊이
- 메서드를 컴파일한 버전이 코드 캐시에서 차지하는 공간

### 10.3.2 인라이닝 서브시스템 튜닝

인라이닝 스위치

-XX:MaxInlineSize=<n>: 메서드를 이 크기 이하로 인라이닝함

-XX:FreqInlineSize=<n>: (자주 호출되는) '핫' 메서드를 이 크기 이하로 인라이닝함

-XX:InlineSmallCode=<n>: 코드 캐시에 이 수치보다 더 많은 공간을 차지한 최종 단계 컴파일이 이미 존제할 경우 메서드를 인라이닝 하지 않음

-XX:MaxInlineLevel=<n>: 이 수준보다 더 깊이 호출 프레임을 인라이닝하지 않음   


이렇게 매개변수를 바꿔가며 튜닝할 때에는 반드시 측정 데이터를 근거로 삼아야 함

## 10.4 루프 펼치기

루프 내부의 메서드 호출을 전부 인라이닝하면, 컴파일러는 루프를 한번 순회할 때마다 비용이 얼마나 드는지, 반복 실행되는 코드는 크기가 얼마나 되는지 더 분명해짐

**백 브랜치(Back branch, 한번 순회를 마치고 다시 루프문 처음으로 돌아가는 것)**가 일어나면 그때마다 CPU는 유입된 명령어 파이프라인을 덤프하기 때문에 성능상 바람직하지 않음

보통 루프 바디가 짧을수록 백 브랜치 비용은 상대적으로 높기 때문에 핫스팟은 다음 기준에 따라 루프 펼치기 여부를 결정

- 루프 카운터 변수 유형(대부분 객체 아닌 int나 long형 사용)
- 루프 보폭(한번 순회할 때마다 루프 카운터 값이 얼마나 바귀는가)
- 루프 내부의 탈출 지점 개수(return 또는 break)

> 루프 펼치기는 핫스팟 버전별로 로직이 상이하고 아키텍처마다 많이 다름

### 10.4.1 루프 펼치기 정리

핫스팟은 다양한 최적화 기법으로 루프 펼치기를 함

- 카운터가 int, short, char형일 경우 루프를 최적화
- 루프 바디를 펼치고 세이프포인트 폴을 제거
- 루프를 펼치면 백 브랜치 횟수가 줄고 그만큼 분기 예측 비용도 덜 든다
- 세이프포인트 폴을 제거하면 루프를 순회할 때마다 하는 일이 줄어든다

## 10.5 탈출 분석(escape analysis)

핫스팟은 어떤 메서드가 내부에서 수행한 작업을 그 메서드 경계 밖에서도 볼 수 있는지, 또는 부수 효과를 유발하지는 않는지 범위 기반 분석(scope-based analysis)을 통해 판별

메서드 내부에서 할당된 객체를 메서드 범위 밖에서 바라볼 수 있는지를 알아보는 용도로 쓰임

핫스팟은 탈출 분석 단계 도중, 잠재적으로 탈출한 객체를 세 가지 유형으로 분류

- NoEscape: 객체가 메서드/스레드를 탈출하지 않고, 호출 인수로 전달되지 않으며, 스칼라로 대체 가능
- ArgEscape: 객체가 메서드/스레드를 탈출하지 않지만 호출 인수로 전달되거나 레퍼런스로 참조되며, 호출 도중에는 탈출하지 않음
- GlobalEscape: 객체가 메서드/스레드를 탈출

### 10.5.1 힙 할당 제거

핫스팟의 탈출 분석 최적화는 개발자가 객체 할당률을 신경 쓰지 않고도 자바 코드를 자연스레 작성할 수 있도록 설계

할당된 객체가 메서드를 탈출하지 않는다는 사실을 밝히면 VM은 **스칼라 치환(scalar replacement)**이라는 최적화를 적용해 객체 필드를 마치 처음부터 객체 필드가 아닌 지역 변수였던 것처럼 스칼라 값으로 바꿈

그런 다음 **레지스터 할당기(register allocator)**라는 핫스팟 컴포넌트에 의해 CPU 레지스터 속으로 배치   



탈출 분석의 목표는 힙 할당을 막을 수 있는지 추론하는 것

### 10.5.2 락과 탈출 분석

핫스팟은 탈출 분석 및 관련 기법을 통해 락 성능도 최적화함

> 단, 이 최적화는 (synchronized를 사용한) 인트린직 락에만 해당되며, java.util.concurrent 패키지에 있는 락에는 적용되지 않음

락 최적화의 핵심

- 비탈출(nonescaping) 객체에 있는 락은 제거(**락 생략, lock ellision**)
- 같은 락을 공유한, 락이 걸린 연속된 영역은 병합(**락 확장, lock coarsening**)
- 락을 해제하지 않고 같은 락을 반복 획득한 블록을 찾아냄(**중첩 락, nested lock**)

   

락 확장 최적화는 기본 활성화되어 있지만, VM 스위치 ```-XX:-EleminateLocks```로 해제 가능

또 핫스팟은 같은 객체에 걸린 중첩 락을 감지해 해당 스레드가 이미 그 락을 획득한 상태라면 내부 락을 제거

중첩 락 최적화도 기본 활성화되어 있지만, VM 스위치 ```-XX:-EleminateNestedLocks```로 제거 가능

핫스팟은 언제 락을 확장/제거하는 게 안전할지 자동으로 계산

### 10.5.3 탈출 분석의 한계

힙이 아니라도 다른 어딘가에는 할당을 해야 하는데, CPU 레지스터나 스택 공간은 상대적으로 희소한 리소스

또 기본적으로 원소가 64개 이상인 배열은 핫스팟에서 탈출 분석의 혜택을 볼 수 없다

개수 제한 조정 가능

```
-XX:ElimiateAllocationArraySizeLimit=<n>
```

배열 길이가 64를 초과하면 무조건 힙에 저장되고 이 코드의 할당률은 빠르게 상승

   

또 한 가지 중요한 한계점은 핫스팟이 **부분 탈출 분석(partial escape analysis)**을 지원하지 않는다는 점

객체가 어느 분기점에서건 메서드 범위를 탈출하면 힙에 객체를 할당하지 않는 최적화는 적용되지 않음

## 10.6 단형성 디스패치

핫스팟 C2 컴파일러가 수행하는 추측성 최적화는 대부분 경험적 연구 결과를 토대로 함

**단형성 디스패치(monomorphic dispatch)** 기법도 그런 부류 중 하나

즉, 어떤 객체에 있는 메서드를 호출할 때, 그 메서드를 최초로 호출한 객체의 런타임 타입을 알아내면 그 이후의 모든 호출도 동일한 타입일 가능성이 큼

이 추측성 가정이 옳다면 해당 호출부의 메서드 호출을 최적화 가능

   

일반 애플리케이션에서는 절대 다수가 단형적 호출

핫스팟은 자주 쓰이지는 않지만 **이형성 디스패치(bimorphic dispatch)**라는 최적화도 지원

   

단형도, 이형도 아닌 호출부를 **다형성(megamorphic, 객체지향 프로그래밍의 다형성과는 다름)**이라고 함

극히 소수의 관측된 타입을 지닌 다형성 호출부를 찾아내면 성능 향상을 도모할 묘기를 부릴 수 있다

## 10.7 인트린직

JIT 서브시스템이 동적 생성하기 이전에 JVM이 이미 알고 있는, 고도로 튜닝된 네이티브 메서드 구현체를 가리키는 용어

주로 OS나 CPU 아키텍처의 특정 기능을 응용하는, 성능이 필수적인 코어 메서드에 쓰임

JVM은 기동 직후 런타임에 자신을 실행한 하드웨어 CPU를 꼼꼼히 살펴보고 사용 가능한 프로세서의 기능을 목록화함

즉, 어떤 식으로 최적화할지 코드 컴파일 타임에 결정할 필요 없이 런타임까지 미룰 수 있다

   

많이 쓰이는 인트린직

- java.lang.System.arraycopy(): CPU의 벡터 지원 기능으로 배열을 빠르게 복사
- java.lang.System.currentTimeMillis(): 대부분 OS가 제공하는 구현체가 빠르다
- java.lang.Math.min(): 일부 CPU에서 분기 없이 연산 가능
- 기타 java.lnag.Math 메서드: 일부 CPU에서 명령어 직접 지원
- 암호화 함수(예, AES): 하드웨어로 가속하면 성능이 매우 좋아짐

> 자바 9부터 메서드 앞에 @HotSpotIntrinsicCandidate 애너테이션을 붙여 인트린직을 사용할 수 있음을 나타냄

   

새 인트린직을 추가할 때에는 복잡도가 증가하는 것과 유용하게 잘 쓰는 것 가이에서 저울질을 해봐야 함

인트린직은 정말 자주 쓰이는 작업에 한해서만 성능에 큰 영향을 미칠 수 있다

## 10.8 온-스택 치환

컴파일을 일으킬 정도로 호출 빈도가 높지는 않지만 메서드 내부에 핫 루프가 포함된 경우

핫스팟은 이런 코드를 **온-스택 치환(OSR)**을 이용해 최적화

인터프리터가 루프 백 브랜치 횟수를 세어보고 특정 한계치를 초과하면 루프를 컴파일한 후 치환해서 실행

## 10.9 세이프포인트 복습

GC STW 이벤트뿐만 아니라, 다음 경우에도 전체 스레드가 세이프포인트에 걸림

- 메서드를 역최적화
- 힙 덤프를 생성
- 바이어스 락(biased lock)을 취소
- 클래스를 재정의(가령, 인스트루먼테이션 용도로)

컴파일드 코드에서 세이프포인트 체크 발급은 JIT 컴파일러가 담당하며, 핫스팟에서는 다음 지점에 체크 코드를 넣음

- 루프 백 브랜치 지점
- 메서드 반환 지점

   

컴파일러는 세이프포인트를 폴링하며 체크하는 비용을 감수하느냐, 아미 세이프포인트에 닿은 스레드가 다른 스레드도 세이프포인트에 모두 닿을 때까지 대기해야 하는 긴 세이프포인트 시간을 회피하느냐 사이에서 고민

## 10.10 코어 라이브러리 메서드

### 10.10.1 인라이닝하기 적합한 메서드 크기 상한

인라이닝을 할지 말지는 메서드의 바이트코드 크기로 결졍되므로 클래스 파일을 정적 분석하면 인라이닝을 하기에 지나치게 큰 메서드를 솎아낼 수 있음

JarScan이라는 오픈 소스 툴은 클래스 폴더 또는 JAR 파일 내부에서 바이트코드 크기가 주어진 한계치 이상인 메서드를 모두 찾아냄

예를 들면, java.lang.String 클래스의 toUpperCase(), toLowerCase()는 정상적인 인라이닝 범위를 벗어남

#### 도메인에 특정한 메서드로 성능 개선

이 정도로 다양한 캐릭터셋을 고려할 필요가 없다면, toUpperCase()를 도메인에 특정한 메서드로 만들어 바이트코드 크기를 인라이닝 한계치 이하로 줄일 수 있다

#### 메서드를 작게 하면 좋은 점

인라이닝 가짓수가 늘어남

런타임 데이터가 다양해질수록 여러 가지 상이한 경로를 거치면서 코드가 '핫'하게 될 가능성이 있다

메서드를 작게 유지하면 다양한 인라이닝 트리를 구축해서 핫 결로를 더욱 최적화할 여지가 생김

메서드가 커지면 금세 인라이닝 크기 한계치를 초과해서 최적화 안 된 경로가 남아 있음

### 10.10.2 컴파일하기 적합한 메서드 크기 상한

핫스팟에는 메서드 크기가 어느 이상 초과하면 컴파일되지 않는 한계치가 있다

디버그 JVM에서는 ```-XX:HugeMethodLimit=<n>```으로 컴파일 가능한 메서드 바이트코드의 최대 크기 설정 가능

## 참조

1. Optimizing Java(자바 최적화)

2. https://github.com/AdoptOpenJDK/jitwatch