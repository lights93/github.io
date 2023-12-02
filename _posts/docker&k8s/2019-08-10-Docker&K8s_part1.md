---
title: "도커의 기초"
date: 2019-08-10
excerpt: "도커의 기초"
categories:
  - Docker
tags:
  - Docker
  - Kubernetes
---

# 1. 도커의 기초

도커 중심의 컨테이너 개발이 어떤 이유로 현재 시스템 개발 기법의 주류가 됐는지와 그 개념 및 역사와 함께 도커의 존재 의의를 알아봄

## 1.1 도커란 무엇인가

도커는 컨테이너형 가상화 기술을 구현하기 위한 상주 애플리케이션과 이 애플리케이션을 조작하기 위한 명령행 도구로 구성되는 프로덕트

애플리케이션 배포에 특화돼 있기 때문에 애플리케이션 개발 및 운영을 컨테이너 중심으로 할 수 있다.

로컬 환경에 도커만 설치하면 몇 줄짜리 구성 파일(Dockerfile)과 명령어 한 줄로 애플리케이션이자 미들웨어가 이미 갖춰진 테스트용 가상환경을 빠르게 구축 가능 -> 가상화 소프트웨어에 비해 오버헤드가 적어짐

조작이 간편하고 경량 컨테이너라는 장점 때문에 도커는 로컬 머신의 개발 환경 구축에 널리 사용

도커는 개발환경 구축뿐만 아니라 개발 후 운영 환경에 대한 배포나 애플리케이션 플랫폼으로 기능할 수 있다는 점에서 기존 가상 머신보다 더 뛰어남

도커는 기존 가상화 환경보다 더 가볍게 동작

도커는 이식성이 뛰어남(개발 환경과 운영 환경을 거의 동등하게 재현 가능)

#### 도커가 적합하지 않은 경우

도커 컨테이너의 내부는 리눅스 계열 운영 체제와 같은 구성을 취하는 것이 많은데, 컨테이너는 운영 체제의 동작을 완전히 재현하지는 못함

비리눅스 환경이 필요한 경우에도 도커는 적합하지 않다.

### 도커의 역사

현재 컨테이너 관련 기술의 사실상 표준 지위

개발 커뮤니티도 활발함

도커가 개발에서 주류가 됨

### 도커의 기본 개념

도커는 컨테이너형 가상화를 구현하기 위한 상주 애플리케이션과 이를 관리하는 명령형 도구로 구성됨

애플리케이션 배포에 특화됐고 컨테이너 활용에 유리한 생태계가 잘 갖춰져 있음

- 컨테이너형 가상화 기술

  도커가 등장하기 전에는 LXC

  컨테이너형 가상화를 사용하면 가상화 소프트웨어 없이도 운영 체제의 리소스를 격리해 가상 운영 체제로 만들 수있다. -> 컨테이너

  컨테이너를 만들면서 발생하는 오버헤드는 다른 가상화 소프트웨어보다 적다.

  운영 체제 위에서 가상화 소프트웨어를 사용해 하드웨어를 에뮬레이션하는 방법으로 게스트 운영 체제를 만드는 방식 -> 호스트 운영 체제 가상화

  컨테이너형 가상화 기술을 사용해서 컨테이너를 쉽게 만들고 사용하고 버릴 수 있다는 점이 도커의 주요 특징 중 하나

* 애플리케이션이 중심이 되는 도커

  LXC는 호스트 운영 체제 가상화보다 성능 면에서 유리하다는 장점으로 시스템 컨테이너(애플리케이션 배포가 아닌 단순한 리소스 분리 목적)로서 어느 정도 자리를 잡았지만, LXC는 애플리케이션 배포 및 운영이라는 관점에서 기능이 빠져있었다.

  도커와 LXC의 차이점

  1. 호스트 운영 체제의 영향을 받지 않는 실행 환경(Docker engine을 이용한 실행 환경 표준화)
  2. DSL(Dockerfile)을 이용한 컨테이너 구성 및 애플리케이션 배포 정의
  3. 이미지 버전 관리
  4. 레이어 구조를 갖는 이미지 포맷(차분 빌드 가능)
  5. 도커 레지스트리(이미지 저장 서버 역할을 함)
  6. 프로그램 가능한 다양한 기능의 API

  도커는 컨테이너 정보를 Dockerfile 코드로 관리 가능(재현성이 높음)

  도커 이전에는 애플리케이션을 호스트 운영 체제 또는 게스트 운영 체제에 배포하는 스타일이 주류

  반면 도커는 애플리케이션 실행 환경이 함께 배포되는 방식

### 도커 스타일 체험하기

1. 간단한 셸 스크립트 파일 생성

   도커 이미지 빌드: Dockerfile이나 애플리케이션 실행 파일을 사용해서 도커 컨테이너의 원형이 될 이미지를 만드는 과정

2. 셸 스크립트와 같은 폴더에 Dockerfile 작성

3. FROM 절은 컨테이너의 원형 역할을 할 도커 이미지(운영 체제)를 정의

4. COPY 절은 작성한 파일을 도커 컨테이너 안의 경로에 복사하라고 정의

5. RUN 절은 도커 컨테이너 안에서 어떤 명령을 수행하기 위한 것

6. CMD 절은 완성된 이미지를 도커 컨테이너로 실행하기 전에 먼저 실행할 명령을 정의

   ```dockerfile
   FROM ubuntu:16.04
   
   COPY helloworld /usr/local/bin
   RUN chmod +x /usr/local/bin/helloworld
   
   CMD ["helloworld"]
   ```

   ```bash
   $ docker image build -t helloworld:latest
   $ docker container run helloworld:latest
   ```

#### Moby 프로젝트

Moby는 도커 자체를 빌드하기 위한 프로젝트

Moby는 개발 프로젝트고, 도커는 Moby에서 개발하는 컴포넌트 및 도구의 집합체인 재품

#### LinuxKit

윈도우/맥용 도커는 LinuxKit를 경량 서브 시스템으로 사용해 도커를 실행

윈도와 macOS는 있는 도커를 있는 그대로 동작시킬 수 없다.(dockerd는 리눅스 본체에서만 동작)

기존에는 VirtualBox를 이용했지만 LinuxKit를 사용하여 대체

LinuxKit는 도커를 더 다양한 플랫폼 및 시스템으로 확산시키는 전략에서 기둥 역할을 하는 기술의 하나

## 1.2 도커를 사용하는 의의

1. 변화하지 않는 실행 환경으로 멱등성 확보
2. 코드를 통한 실행 환경 구축 및 애플리케이션 구성
3. 실행 환경과 애플리케이션의 일체화로 이식성 향상
4. 시스템을 구성하는 애플리케이션 및 미들웨어의 관리 용이성

### 환경 차이로 인한 문제 방지

애플리케이션은 항상 뭔가에 의존성을 갖는다.(운영 체제 ,CPU, 메모리, 언어 런타임, 라이브러리 ...)

각 서버에 배포된 애플리케이션이 동일하다면 애플리케이션이 의존하는 환경의 차이를 가능한 한 배제하는 것이 이 문제를 해결하는 지름길

* **코드로 관리하는 인프라와 불변 인프라**

  **코드로 관리하는 인프라는 코드 기반으로 인프라를 정의한다는 개념**

  서버 구성 및 라이브러리 도구 설치를 코드로 정의하고 앤서블같은 프로비저닝 도구로 서버를 구축

  수작업이 개입할 여지를 줄이고 코드 중심으로 바꿈으로써 쉽게 같은 구성의 서버 여러 대를 복제 가능

  환경 차이 문제를 피하려면 언제든 몇 번을 실행해 같은 결과가 보장되는 멱등성을 확보해야 한다.

  멱등성을 보장하기 위해 항구적인 코드를 계속 작성하는 것은 운영 업무에 부담을 주기 쉽다.

  이러한 문제에 대한 대책이 **불변 인프라 개념**

  **불변 인프라는 어떤 시점의 서버 상태를 저장해 복제할 수 있게 하자는 개념**

  서버에 변경을 가하고 싶은 경우에는 기존 인프라를 수정하는 대신 새로운 서버를 구축하고 그 상태를 이미지로 저장한 다음 그 이미지를 복제

  도커를 사용하면 코드로 관리하는 인프라와 불변 인프라의 두 개념을 간단하고 낮은 비용으로 실현 가능

  인프라 구성이 Dockerfile로 관리되므로 코드로 관리하는 인프라는 도커의 대원칙

  컨테이너형 가상화는 운영 체제 대부분을 호스트 운영 체제와 공유 -> 실행에 걸리는 시간이 적어 구성을 수정하지 않고 인프라를 완전히 새로 만드는 불변 인프라와 궁합이 잘 맞음

#### 코드로 관리하는 인프라와 불변 인프라를 클라우드에서 실현하기

물리 머신 위에서 가상 서버를 실행하는 방식

가상 머신의 특징으로 서버의 상태를 이미지로 저장한 후 그 이미지로 새로운 서버를 만들 수 있다.

호스트 가상화 기술은 가상화 소프트웨어로 컴퓨터 리소스를 추상화하는 방식이기 때문에 가상 머신을 시작하는 데 적어도 1분에서 수분까지 시간이 걸린다. (그래서 도커)

* **애플리케이션과 인프라 묶어 구축하기**

  기존 방법에서 애플리케이션은 이미 구축된 서버에 배포되는 것으로 인프라 복제와 애플리케이션 배포는 전혀 별개의 작업

  도커 컨테이너는 운영체제(인프라)와 애플리케이션을 함께 담은 상자같은 개념

  애플리케이선과 인프라를 함께 관리한 결과로 얻는 높은 이식성도 도커의 매력

### 애플리케이션 구성 관리의 용이성

도커는 필요한 컨테이너를 각각 실행하는 방법으로 시스템 구성

컨테이너의 의존 관계나 실행 순서가 어긋나면 제대로 동작하지 않는 등의 문제가 생기기 때문에 복잡한 시스템을 한 덩어리로 동작시키기는 쉽지 않다.

- **도커 컨테이너 오케스트레이션 시스템**

  여러 컨테이너를 사용하는 애플리케이션을 쉽게 관리할 수 잇도록 도커 컴포즈라는 도구를 제공

  도커 컴포즈를 사용하면 yaml 포맷으로 작성된 설정 파일로 컨테이너를 정의하거나 컨테이너 간의 의존 관계를 정의해 시작 순서 제어 가능

  도커와 도커 컴포즈를 통해 여러 애플리케이션과 미들웨어의 의존관계를 간결한 코드로 관리 가능

  도커 컴포즈가 단일 서버를 넘어 여러 서버에 걸쳐 있는 여러 컨테이너를 관리할 수 있도록 한 도구가 도커 스웜(Docker Swarm)

  여러 컨테이너를 관리하는 것만이 목적인 도커 컴포즈와 달리 도커 스웜은 컨테이너 증가 혹은 감소는 물론이고 노드의 리소스를 효율적으로 활용하기 위한 컨테이너 배치 및 로드 벨런싱 등 실용적인 기능을 갖추고 있다.

  또한, 배포 시에도 롤링 업데이트(오래된 컨테이너와 새로운 컨테이너를 단계적으로 서비스에 교체 투입하는 것)가 가능

  컨테이너 오케스트레이션: 여러 서버에 걸쳐 있는 여러 컨테이너를 관리하는 기법

  컨테이너 오케스트레이션 분야에서 사실상 표준으로 자리 잡은 것은 쿠버네티스

  쿠버네티스는 도커 스웜보다 기능이 충실하며 확장성이 높다.

### 운영 환경에서 빛을 발하는 도커

스케일 아웃이 쉽고, 오버헤드가 적음

도커에는 운영에 도움을 주기 위한 여러 도구 존재(GKE, ECS ...)

도커는 컨테이너 수를 변화시키기 쉬우므로 이런 수요 변동에도 유연하게 대응 가능

데이터 스토어처럼 도커를 운영하기 어려운 분야도 존재

스테이트리스(stateless)한 셩격을 갖는 부분은 큰 어려움없이 도커화 가능

높은 이식성이라는 장점을 고려하면 도커는 운영 환경에서 제 능력을 발휘할 수 있는 기술

### 새로운 개발 스타일

인프라와 애플리케이션 설정을 모두 코드 수준에서 쉽게 수정할 수 있어 인프라 엔지니어와 서버 사이드 엔지니어의 영역 구분이 희미해짐

서버 사이드 애플리케이션 분야에서 최근 마이크로서비스 아키텍처가 등장하면서 도커를 이용한 개발에도 유리한 점이 있어 애플리케이션을 잘게 분할해 만드는 스타일이 지지를 받음

## 1.3 로컬 도커 환경 구축하기

윈도우/맥용 도커에는 하이퍼바이저 가상화가 사용되는, 이 기술은 운영 체제 자체에 내장된 가상화 시스템을 이용해 오버헤드 최소화

#### 커뮤니티 에디션(CE)과 엔터프라이즈 에디션(EE)

도커 EE는 기업용 제품이므로 도커 사의 기술 지원을 받을 수 있으며 다양한 운영 체제 및 IaaS 지원

도커 CE는 무료이고. 버전 넘버 끝에 ce가 붙는다.(무료이지만 충분히 실용적)



## 참조

1. 도커/쿠버네티스를 활용한 컨테이너 개발 실전 입문