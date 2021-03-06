---
title: '[Django] MVC and MVT'
excerpt: MVC and MVT 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-08T19:00:00+09:00
toc: true
---
# 아키텍처 패턴(Architecture Pattern)

아키텍처 패턴(Architecture Pattern)은 소프트웨어 내부에 존재하는 구조적인 패턴을 말하는데요. 쉽게 말해서 오랜 시간 개발된 다양한 소프트웨어들이 결국 내부적으로 비슷한 구조를 갖게 되는 경우가 많다는 점이 발견되었고 발견된 구조들을 일반화해서 범주를 나눠 놓은 것입니다.

대표적인 10가지 아키텍처 패턴은 아래와 같습니다.

1. 계층화 패턴(Layered pattern)
2. 클라이언트-서버 패턴 (Client-server pattern)
3. 마스터-슬레이브 패턴 (Master-slave pattern)
4. 파이프-필터 패턴 (Pipe-filter pattern)
5. 브로커 패턴 (Broker pattern)
6. 피어 투 피어 패턴 (Peer-to-peer pattern)
7. 이벤트-버스 패턴 (Event-bus pattern)
8. MVC 패턴 (Model-view-controller pattern)
9. 블랙보드 패턴 (Blackboard- pattern)
10. 인터프리터 패턴 (Interpreter pattern)

여기서는 클라이언트-서버 패턴과 MVC 패턴에 대해서 살펴보겠습니다. 나머지 패턴들은 프로그래밍을 배우다 보면 하나씩 자연스럽게 접하게 될 겁니다.

## 클라이언트 - 서버 패턴

먼저 클라이언트-서버 패턴입니다. 클라이언트는 서비스를 요청하는 쪽이고 서버는 요청을 받아서 서비스를 제공하는 쪽이었죠? 소프트웨어도 이처럼 내부적으로 두 개의 구조로 나눠서 서버는 클라이언트의 요청을 기다리고 있다가 클라이언트로부터 요청이 들어오면 요청에 맞는 서비스를 제공하도록 하는 거죠. 우리 주위에서 많이 볼 수 있는 패턴으로 인터넷 웹 사이트, 온라인 게임 등이 모두 클라이언트 - 서버 패턴에 해당합니다.

## MVC 패턴

MVC 패턴은 하나의 소프트웨어를 역할에 따라 Model, View, Controller 세 가지의 파트로 나눠서 개발하는 패턴입니다. 하나씩 간단히 살펴보면 Model은 데이터를 저장, 보관하고 View는 사용자에게 보여지는 부분을 담당하며 Controller는 사용자의 입력을 받아서 내부 로직을 처리합니다. 이렇게 역할에 따라 분리해서 개발하게 되면 분업이 가능해지고 이로 인해 프로그램을 더 빠르고 완성도 있게 개발할 수 있습니다. 더해서 기능별로 나누어져 있으므로 추후 유지 보수가 용이하겠죠.

## MVC 아키텍처와 MVT 아키텍처

MVC는 이전에 배웠던 Django의 MVT 구조와 비슷한 형태를 보이고 있습니다.  MVT는 데이터 구조를 만들고 데이터베이스와의 소통을 담당하는 Model, 로직을 담당하는 View, 그리고 화면 구성을 담당하는 Template 이렇게 세 가지로 나누어져 있는 구조였는데 이러한 MVT 구조는 MVC 구조를 기반으로 만들어졌습니다. 차이점이 있다면 Model이 하는 역할은 그대로지만 MVC 아키텍처의 View가 하는 역할을 Template이 하고 Controller가 하는 역할을 View가 맡아서 처리합니다.

```bash
MVC 구조           MVT 구조 (Django)
Model       ->    Model
View        ->    Template
Controller  ->    View
```

그렇다면 왜 Django는 MVT 라는 구조를 만들었을까요? 기존 MVC 아키텍처에서는 Controller가 중심에서 모든 요청을 맡아 처리하지만 MVT 아키텍처에서는 실제로 요청을 받아들이는 부분, 즉 기존의 MVC의 Controller가 했던 역할의 일부를 분리해서 Django 프레임워크가 직접 처리합니다.

덕분에 개발자는 서비스에 중요한 Model, View, Template에 집중하고 나머지 모든 부분은 Django 프레임워크에 맡겨서 훨씬 편리하고 빠르게 개발할 수 있습니다. 이와 같은 개발의 간편함이 Django 프레임워크가 MVT 아키텍처를 통해 추구하고자 하는 것입니다.