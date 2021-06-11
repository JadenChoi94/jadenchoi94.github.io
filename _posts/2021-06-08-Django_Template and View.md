---
title: '[Django] Template and View'
excerpt: Template와 View 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-08T19:00:00+09:00
toc: true
---

# Django의 URL 처리

Django의 URL 처리 방식은 직관적이고 자주 변경되지 않는 구조로 되어 있습니다.

## URLconf (urls.py)

Djang에서 URL을 처리하기 위해서는 URLconf 모듈 즉 `urls.py`를 작성해야 합니다. 아래는 URLconf의 예시입니다.

```python
# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('URL', views.view)
]
```

## URL 처리 순서

1. 처음 요청이 들어오면 Django는 맨 처음 사용할 URLConf 모듈을 찾습니다. 따로 변경하지 않았다면 프로젝트 앱 내 settings.py의 `ROOT_URLCONF` 설정을 사용합니다.
2. ROOT_URLconf로 정의된 URLConf 모듈을 보고 `urlpatterns 리스트` 안쪽의 django.urls.path() 함수를 순서대로 실행합니다.
3. 요청과 일치하는 URL 패턴이 있다면 django.urls.path() 함수에 따라 view를 호출하거나, 일치하는 URL 패턴 다음의 URL을 하위 URLconf 모듈로 넘깁니다.

```python
from django.urls import path
from django.urls import include
from . import views

urlpatterns = [
    path('music/', views.player),
    path('video/', include('videoApp.urls')),
    # URL이 '~/video/lecture/django' 라면
    # videoApp의 URLconf에는 'lecture/django'가 넘어갑니다.
]
```

## Path 함수 알아보기

path 함수는 다음과 같이 정의되어 있고 총 4개의 인자를 갖습니다.

```
django.urls.path

path(route, view, kwargs=None, name=None)
```

### route

URL 문자열을 인수로 받으며 경로 변수(Path Converter)를 사용하여 URL의 일부를 view의 인수로 보낼 수 있습니다.

### Path converters

Django에서 기본으로 제공하는 경로 변환기 이하 경로 변수는 5가지가 있습니다.

- str : 경로 구분 기호(/)를 제외한 모든 문자열과 매칭됩니다.
- int : 0 또는 양의 정수와 매칭됩니다.
- slug : 문자, 숫자, 하이픈(-), 밑줄(_)로 구성된 문자열과 매칭됩니다.
- uuid : 범용 고유 식별자(UUID)와 매칭 됩니다. UUID에 관한 내용은 여기서 다루지 않겠습니다.
  다음 문서를 참고하세요. ([https://ko.wikipedia.org/wiki/범용*고유*식별자](https://ko.wikipedia.org/wiki/범용_고유_식별자))
- path : 경로 구분 기호(/)를 포함한 모든 문자열과 매칭 됩니다.

### view

함수형 view 또는 클래스 기반 view가 들어갈 수 있고 우리가 위에서 적어준 것처럼 include를 사용해서 다른 URFconf 모듈로 연결할 수도 있습니다.

### kwargs

view에 추가 인자를 전달 할 때 사용 합니다.

### name

path 함수가 가지는 URL 패턴에 이름을 붙여 주기 위해 사용하는데 URL을 직접 템플릿에 적지 않게 해주고 URL을 직관적으로 참조할 수 있게 해줍니다.

kwargs와 name 전달 인자는 Django가 더 익숙해진 다음에 뒤에서 같이 다뤄 볼 예정입니다. Django의 URL 구조에 대해 더 알고 싶다면 아래 공식문서를 참고 :) 

https://docs.djangoproject.com/en/2.2/topics/http/urls/