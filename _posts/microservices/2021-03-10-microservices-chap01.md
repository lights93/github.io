---
title: "마이크로서비스 소개"
date: 2021-03-10
excerpt: "마이크로서비스"
categories:
  - microservice
tags:
  - microservice
---

# 1. 마이크로서비스 소개

## 저자의 마이크로 서비스 경험

#### 독립 소프트웨어 컴포넌트의 장점

- 고객은 플랫폼의 일부분만을 자체 시스템 환경에 배포할 수 있으며, 명확한 API를 사용해 기존 시스템과 통합 가능
- 어떤 고객은 플랫폼 기능의 일부를 고객의 시스템 환경에 있는 기존 구현으로 대체하는 것을 선택할 수 있다. 이런 경우에는 기존 기능을 플랫폼 API에 맞추는 작업이 필요하다
- 플랫폼의 각 컴포넌트는 개별적으로 배포, 업그레이드할 수 있다. API가 명확하기 때문에 다른 컴포넌트의 수명 주기와 상관 없이 컴포넌트를 업그레이드할 수 있다.
- 명확한 API를 사용하면 플랫폼의 각 컴포넌트를 다른 컴포넌트와 상관 없이 여러 서버로 스케일 아웃할 수 있다.

#### 독립 소프트웨어 컴포넌트의 문제

- 컴포넌트의 새 인스턴스를 추가하려면 수동으로 로드 밸런서를 구성하고, 새 노드를 수동으로 설정해야 한다
- 통신하는 다른 시스템에서 오류가 발생했을 때 플랫폼으로 전이된다는 근본적인 문제가 있다. 플랫폼에서 보낸 요청에 다른 시스템이 제시간에 응답하지 않으면 플랫폼이 동시에 많은 양의 요청을 받았을 때 OS 스레드와 같은 주요 자원이 부족하게 된다. 이에 띠라 플랫폼의 컴포넌트가 지연되거나 중단된다. -> **연쇄 장애(chain of failures)**
- 모든 컴포넌트 인스턴스의 구성을 일관성 있게 최신 상태로 유지하는 작업에도 많은 문제점이 있어서 반복적인 수작업이 자주 발생하며, 이에 따른 품질 문제가 발생한다.
- 지연 시간 문제 및 하드웨어 사용랑(ex. CPU, 메모리, 디스크, 네트워크) 측면에서 플랫폼 상태를 모니터링하는 작업은 일체형 애플리케이션의 인스턴스 하나를 모니터링하는 것과 비교하면 훨씬 더 복잡하다.
- 분산된 여러 컴포넌트에서 로그 파일을 수집하고 관련된 컴포넌트 로그 이벤트를 상호 연관시키는 것도 어려운 작업이지만, 컴포넌트의 수를 미리 알고 있다면 실현 가능

#### 마이크로서비스 입문

시간이 지남에 따라 일체형 애플리케이션을 유지보수하고 개선하는 건 점점 더 어려워짐

**수직 스케일링(vertical scailing)**의 한계에 마주하게 됨

앞단에 로드 밸런서를 배치하고 여러 개의 소형 서버에 작은 컴포넌트를 배포하면 수평 스케일링이 가능



마이크로서비스 개발 및 마이크로서비스 기반 아키텍처에 관련된 문제를 해결하는 데 도움을 주는 몇 가지 오픈 소스 도구와 프레임워크

- **스프링 클라우드**는 동적 서비스 검색과 구성 관리, 분산 추적, 서킷 브레이커 등의 기능을 제공

- **도커**와 같은 컨테이너 엔진을 사용하면 개발 환경과 상용 환경 사이의 간격을 최소화 가능.

- 컨테이너를 실행하고 여러 대의 서버로 확장하는 고가용성을 지원하고 컴퓨팅 자원을 추가할 수 있는 무언가가 필요함 -> **컨테이너 오케스트레이터(container orchestrator)**

  ex. **쿠버네티스(Kubernetes)**

- **서비스 메시(service mesh)**를 사용하면 컨테이너 오케스트레이터를 보완해 마이크로서비스의 관리 편의성과 탄력성을 높일 수 있다.

## 마이크로서비스 정의

마이크로서비스 아키텍처는 두 가지 목표를 달성하고자 일체형 애플리케이션을 작은 컴포넌트로 나누는 것

- 빠르게 개발해 지속적으로 배포할 수 있어야 한다
- 수동 혹은 자동으로 쉽게 스케일링할 수 있어야 한다

마이크로서비스는 기본적으로 독자적인 업그레이드와 스케일링이 가능한 독립 소프트웨어 컴포넌트

독립 컴포넌트로 동작하기 위한 기준

- 아무것도 공유하지 않는 아키텍처를 유지해야 한다, 데이터베이스의 데이터를 공유하지 않는다
- 명확한 인터페이스를 통해서만 통신해야 한다. 사용하는 메시지 형식은 버전 관리 전략에 따라 안정적으로 문서화되고 개선돼야 한다
- 개별적인 런타임 프로세스로 배포해야 한다. 각 마이크로서비스 인스턴스는 도커 컨테이너와 같이 독립된 런타임 프로세스로 실행해야 한다
- 마이크로서비스 인스턴스는 상태가 없다(stateless)

조건을 충족한다면 가상 서버를 늘리는 등의 방법으로 단일 마이크로서비스를 여러 개의 인스턴스로 확장하는 것이 일체형 애플리케이션을 확장하는 것에 비해 훨씬 쉽다.

마이크로서비스의 규모

- 개발자가 다룰 수 있을 만한 크기
- 성능이나 데이터 일관성을 저해하지 않을 정도의 규모

본질적으로 마이크로서비스 아키텍처는 일체형 애플리케이션을 서로 협력하는 독립 소프트웨어 컴포넌트로 나누는 것이며, 애플리케이션을 쉽게 확장하고 빠르게 개발하기 위한 아키텍처

## 마이크로서비스의 문제

- 동기식 통신을 사용하는 다수의 소형 컴포넌트는 연쇄 장애를 일으킬 수 있다.
- 다수의 소형 컴포넌트를 최신 상태로 유지하는 건 어렵다.
- 많은 컴포넌트가 처리에 관여하는 요청은 추적하기 어렵다. 예를 들어, 각 컴포넌트가 로컬에 로그 이벤트를 저장하는 경우에는 근본 원인 분석을 수행하기 어렵다.
- 컴포넌트 수준의 하드웨어 자원 사용량 분석도 어렵다.
- 다수의 소형 컴포넌트를 수동으로 구성하고 관리하는 건 비용이 많이 들고 오류가 발생하기 쉽다
- 분산 시스템을 형성하게 되어 다루기가 매우 어렵다

가장 좋은 규칙은 시스템 환경에 항상 문제가 있다는 가정을 기반으로 마이크로서비스 아키텍처를 설계하는 것

마이크로서비스 아키텍처는 문제를 감지해 고장난 컴포넌트를 다시 시작하고 클라이언트 측에서는 고장난 마이크로서비스 인스턴스에 요청을 보내지 않도록 설계해야 한다

마이크로서비스 클라이언트는 탄력성이 있어서 문제가 해결되면 고장났던 마이크로서비스에 대한 요청을 다시 시작해야 한다.

모든 것은 완전히 자동화돼야 한다

## 마이크로서비스 디자인 패턴

#### 서비스 검색

- 문제점

  클라이언트가 마이크로서비스와 그 인스턴스를 찾을 수 있어야 한다.

  컨테이너 등에서 실행되는 마이크로서비스 인스턴스는 시작하면서 동적IP 주소를 할당 받는 게 일반적 -> REST API를 클라이언트에서 호출하기 어려움

- 해결책

  현재 사용 가능한 마이크로서비스와 그 인스턴스를 추적하는 새 컴포넌트(**서비스 검색** 서비스)를 시스템 환경에 추가

- 해결책의 필요 조건

  - 마이크로서비스와 마이크로서비스 인스턴스를 자동으로 등록 및 등록 해지
  - 클라이언트는 마이크로서비스의 논리 엔드포인트에 요청을 보낼 수 있어야 함. 요청은 사용 가능한 마이크로서비스 인스턴스 중 하나로 라우팅
  - 마이크로서비스에 대한 요청은 가용 인스턴스로 로드 밸런싱돼야 함
  - 요청을 라우팅하지 않고자 상태가 비정상인 인스턴스를 감지할 수 있어야 함

- 구현 참고

  - 클라이언트 측 라우팅: 클라이언트는 서비스 검색 서비스와의 통신을 지원하는 라이브러리를 사용해 요청을 보낼 만한 인스턴스를 찾는다
  - 서버 측 라우팅: 서비스 검색 서비스의 인프라는 모든 요청을 전달하는 리버스 프록시를 노출. 리버스 프록시는 클라이언트를 대신해 적절한 마이크로서비스 인스턴스로 요청을 전달

#### 에지 서버

- 문제점

  마이크로서비스 시스템 환경에선 일부 마이크로서비스만 시스템 환경 외부에 공개하고, 그 외의 마이크로서비스는 외부에서 접근하지 못하도록 숨기는 게 바람직하다. 공개된 마이크로서비스는 악의적인 클라이언트의 요청으로부터 보호해야 한다

- 해결책

  모든 요청이 거치는 시스템 환경에 새 컴포넌트(**에지 서버**)를 추가한다.

- 구현 참고

  일반적으로 에지 서버는 리버스 프록시로 동작하며, 동적 로드 밸런싱 기능을 제공하고자 검색 서비스와 통합될 수 있다.

- 해결책의 필요 조건

  - 외부 요청을 허용하는 마이크로서비스만 요청을 라우팅
  - 서비스를 외부로 공개하되 악의적인 요청으로부터 보호. 즉 표준 프로토콜과 OAuth, OIDC, JWT 토큰, API 키 등의 모범 사례를 사용해 신뢰할 수 있는 클라이언트인지 확인

#### 리액티브 마이크로서비스

- 문제점

  블로킹 IO를 사용하면 요청을 처리하는 동안 운영체제의 스레드를 점유 -> 동시 요청 수가 증가하거나 요쳥과 관련된 컴포넌트가 증가하면 운영체제의 가용 스레드가 부족해 응답 시간이 늦어지거나 서버가 중단되는 문제가 발생

  또한 블로킹 IO를 과도하게 사용하면 마이크로서비스 시스템에 오류가 발생하기 쉽다. -> 연쇄 장애 유발

- 해결책

  논블로킹 IO를 사용해 데이터베이스가 다른 마이크로서비스가 처리하길 기다리는 동안 스레드가 할당되지 않게 한다

- 해결책의 필요 조건

  - 가능하다면 비동기 프로그래밍 모델 사용
  - 동기식 프로그래밍 모델을 선호한다면 논블로킹 IO를 사용해 응답을 기다리는 동안에도 스레드 할당 없이 동기식 요청을 실행하는 리액티브 프레임워크 사용
  - 마이크로서비스는 의존하는 서비스가 중단되더라도 응답할 수 있도록 탄력성 있게 설계돼야 하며, 중단된 서비스가 재개되면 클라이언트가 서비스를 다시 사용할 수 있어야 한다 -> 자가 치유(self-healing)

#### 구성 중앙화

- 문제점

  일반적으로 애플리케이션은 여러 환경 변수나 파일에 담긴 구성 정보와 함께 배포되는데, 다수의 마이크로서비스 인스턴스가 배포된 마이크로서비스 아키텍처 기반의 시스템 환경에선 조금 문제가 있다.

  - 실행 중인 모든 마이크로서비스 인스턴스의 구성 정보를 한눈에 보려면 어떻게 해야 하는가?
  - 구성을 업데이트하고 관련된 모든 마이크로서비스 인스턴스가 올바르게 업데이트 되게 하려면 어떻게 해야 하는가?

- 해결책

  시스템 환경에 모든 마이크로서비스의 구성 정보를 저장하는 새 컴포넌트(**구성 서버**)를 추가

- 해결책의 필요 조건

  마이크로서비스 집합에 대한 구성 정보를 한 곳에 저장하고 환경별 설정을 지원

#### 로그 분석 중앙화

- 문제점

  여러 개의 소규모 서버에 다수의 마이크로서비스 인스턴스를 배포하는 마이크로서비스 아키텍처 기반의 시스템 환경에선 문제가 있다.

  - 각 마이크로서비스 인스턴스가 로컬에 로그 파일을 기록하는 상황에서 전체 시스템 환경에서 발생하는 사건을 개괄하려면 어떻게 해야 하는가?
  - 문제가 발생한 마이크로서비스 인스턴스를 찾아서 로그 파일에 오류 메시지르 써게 하려면 어떻게 해야 하는가?
  - 최종 사용자가 문제를 보고했을 때 이와 관련된 로그 메시지를 찾으려면 어떻게 해야 하는가?
  - 문제의 근본 원인이 되는 마이크로서비스 인스턴스를 찾으려면 어떻게 해야 하는가?

- 해결책

  로그를 중앙화해 관리하고, 다음과 같은 기능을 갖춘 새 컴포넌트를 추가

  - 새 마이크로서비스 인스턴스를 감지해 로그 이벤트를 수집
  - 로그 이벤트를 해석해 구조적이고 검색 가능한 형식으로 중안 데이터베이스에 저장
  - 로그 이벤트를 조회 및 분석하기 위한 API와 그래픽 도구를 제공

#### 분산 추적

- 문제점

  시스템 환경에 대한 외부 호출을 처리하는 동안 마이크로서비스 사이에서 흐르는 요청 및 메시지를 추적할 수 있어야 한다

  장애 시나리오 예시

  - 최종 사용자가 특정 장애에 대한 해결을 요청했을 때 문제를 일으킨 마이크로서비스를 찾고 근본 원인을 밝히려면 어떻게 해야 하는가?
  - 어떤 주문 번호에 문제가 발생했을 때 해당 주문의 처리에 관여한 모든 마이크로서비스의 로그 메시지를 찾으려면 어떻게 해야 하는가?

- 해결책

  관련된 모든 요청 및 메시지에 **상관ID(correlation ID)**를 넣어야 하고, 모든 로그 이벤트에 상관 ID가 있어야한다.

- 해결책의 필요 조건

  - 모든 수신 요청과 이벤트에 고유 상관 ID를 할당한다. 상관 ID는 헤더와 같이 찾기 쉬운 위치에 넣는다.
  - 마이크로서비스에서 외부로 요청이나 메시지를 보낼 때는 요청과 메시지에 상관 ID를 꼭 넣는다.
  - 모든 로그 이벤트에는 사전에 정의한 형식의 상관 ID가 있어야 한다. 중앙화된 로그 서비스는 로그 이벤트에서 상관ID를 추출해 검색할 수 있다.

#### 서킷 브레이커

- 문제점

  동기 방식으로 상호 통신하는 마이크로서비스 시스템 환경은 연쇄 장애가 발생할 여지가 있다.

  이 문제는 시스템 환경 전체에 재귀적으로 전파돼 중요한 부분까지 중단시킬 수 있다.

- 해결책

  대상 서비스에 문제가 있다는 것을 감지해 새 요청을 보내지 안혿록 차단하는 서킷 브레이커를 추가

- 해결책의 필요 조건

  - 서비스의 문제가 감지되면 시간 초과(timeout)를 무시하고 바로 실패하도록 서킷을 연다
  - **반열림 서킷(half-open circuit)**이라고도 하는 장애 복구용 프로브(probe)를 사용한다. 즉 서비스가 정상 작동하는지 확인하고자 주기적으로 요청을 보낸다
  - 프로브가 서비스의 정상 종작을 감지하면 서킷을 닫는다. 이런 기능은 시스템 환경을 탄력적으로 만들어서 자가 치유를 가능하게 하는 매우 중요한 기능

#### 제어 루프

- 문제점

  다수의 마이크로서비스 인스턴스가 여러 서버에 분산돼 있는 시스템 환경에선 중단되거나 지연된 마이크로서비스 인스턴스를 수동으로 감지하고 대처하는 것이 어렵다

- 해결책

  시스템 환경의 상태를 관찰하는 새 컴포넌트(**제어 루프(control loop)**)를 시스템 환경에 추가

  이 컴포넌트는 운영자가 지정한 상태와 실제 상태를 지속적으로 관찰하며, 두 상태가 다른 경우에는 현재 상태가 지정한 상태와 일치하도록 조치를 취

- 해결책의 필요 조건

  컨테이너를 기반으로 하는 환경에선 쿠버네티스와 같은 컨테이너 오케스트레이터로 이 패턴을 구현

#### 모니터링 및 경고 중앙화

- 문제점

  응답 시간이나 하드 웨어 자원 사용량이 지나치게 높은 경우 문제의 근본 원인을 찾는 게 매우 어렵다. 마이크로서비스별 하드웨어 자원 사용량을 분석할 수 있어야 함

- 해결책

  마이크로서비스 인스턴스가 사용하는 하드웨어 자원 사용량에 대한 메트릭(metric)을 수집하는 새 컴포넌트(**모니터 서비스**)를 시스템 환경에 추가

- 해결책의 필요 조건

  - 오토스케일링(auto-scaling)된 서버를 포함해 시스템 환경에서 사용하는 모든 서버의 메트릭을 수집해야 함
  - 서버에서 새로 시작된 마이크로서비스 인스턴스를 감지해 메트릭을 수집해야 함
  - 수집한 메트릭을 조회 및 분석하기 위한 API와 그래픽 도구를 제공해야 함

## 필수 소프트웨어

- 스프링 부트
- 스프링 클라우드/넷플릭스 OSS
- 도커
- 쿠버네티스
- 이스티오(서비스 메시)

## 다른 주요 고려 사항

- 데브옵스(DevOps): 마이크로서비스 아키텍처의 장점 중 하나는 빠른 전달이 가능하고 궁극적으로는 새 버전을 지속적으로 전달할 수 있다는 것. 소프트웨어의 새 버전을 개발팀에서 운영팀으로 전달하는 방식이 아닌, 개발과 운영 조직인 긴밀하게 협력해 마이크로서비스의 전체 수먕 주기를 전적으로 책임지는 팀을 구성. 다양한 배포 환경에 마이크로서비스를 빌드하고 테스트, 패키징, 배포하는 전달 체인 자동화도 필수

- 조직 구성과 콘웨이(Conway)의 법칙: "소프트웨어 구조는 그 소프트웨어를 개발한 조직의 의사소통 구조를 반영한다"

  마이크로서비스 아키텍처를 기반으로 애플리케이션을 잘 전달하려면 개별 마이크로서비스나 관련된 마이크로서비스 집합을 한 팀에서 맡도록 조직을 정비해야 한다.

- 일체형 애플리케이션을 마이크로서비스로 분해: 제대로 수행하지 못하면 다음과 같은 문제 발생

  - 느린 전달: 비즈니스 요구 사항이 변경되면 수많은 마이크로서비스에 영향을 미치기 때문에 추가 작업이 발생
  - 성능 저하: 특정 비즈니스 기능을 수행하려면 여러 마이크로서비스 간에 많은 요청이 오가야 하므로 응답시간이 길어짐
  - 일관성 없는 데이터: 마이크로서비스별로 관련 데이터가 나뉘기 떄문에 시간이 지남에 따라 보유한 데이터와 다른 마이크로서비스가 관리하는 데이터가 어긋남

  적절한 경계를 찾아 마이크로서비스를 나누려면 **도메인 주도 설계(Domain-Driven Design)**와 도메인 주도 설계의 핵심 개념인 **경계가 있는 콘텍스트(Bounded Context)**를 적용하는 것이 좋다

  경계가 있는 컨텍스트는 "서브 시스템이나 특정 팀의 담당 업무와 같은, 특정 모델을 정의하고 적용할 수 있는 경계를 명시한 것"

- API 설계: 쉽게 이해할 수 있어야 함

  - 여러 API에서 동일한 개념을 사용하는 경우에는 이름과 데이터 유형에 대한 설명이 일치해야 한다
  - API를 변경할 때는 통제된 방식을 사용, 클라이언트가 여유 있게 마이그레이션 할 수 있도록 여러 버전의 주요 API를 일정 기간 제공해야 한다

- 온프레미스에서 클라우드로 마이그레이션: 먼저 워크로드를 온프레미스의 쿠버네티스로 이동한 후 선호하는 클라우드 공급자가 서비스하는 관리형 쿠버네티스로 재배치하는 마이그레이션 전략이 매력적

- 마이크로서비스를 위한 설계 원칙: 클라우드 애플리케이션의 열두 가지 요소(https://12factor.net/)는 클라우드에 배포 가능한 소프트웨어를 구축하기 위한 설계 원칙


## 참고

- 마이크로서비스(http://www.acornpub.co.kr/book/microservices-spring)
- https://engineering.linecorp.com/ko/blog/diagramming-c4-model-c4-plantuml/#DiagrammingsoftwarearchitectureusingC4modelandC4PlantUML_ko-C4%EB%AA%A8%EB%8D%B8
- https://c4model.com/
