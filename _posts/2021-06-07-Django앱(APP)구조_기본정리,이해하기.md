---
title: '[Django] 앱(APP)구조 기본정리'
excerpt: 앱(APP)구조 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-07T16:00:00+09:00
toc: true
---

# Django 앱(App) 구조

## 01. Project와 App의 차이

Django에서 웹 서비스 각각의 기능은 앱 단위로 구현합니다. 서로 다른 기능을 하는 앱을 여러 개 모아서 하나의 프로젝트로 만드는 거죠. 실제로 하나의 프로젝트는 여러 개의 앱과 약간의 설정 파일들로 구성되어 있고 하나의 앱은 여러 개의 프로젝트에서 사용할 수 있습니다.

## 02. App 생성하기

Django에서 앱을 생성할 때는 **manage.py**를 이용합니다.

```bash
python manage.py startapp {생성할 App 이름}

#예시
python manage.py startapp foods
```

## 03. Django App 구조

```bash
{app_name}/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

### 3.1 admin.py

각각의 앱을 Django의 관리자 기능과 연결하거나 관리자 기능에 대해 설정을 하는 파일입니다.

### 3.2 apps.py

각각의 App마다 추가적인 기능 및 설정을 넣어 주기 위한 파일입니다. 
 만약 조금 더 알고 싶으신 분은 아래의 공식 문서를 참조하세요.
 [https://docs.djangoproject.com/en/2.2/ref/applications](https://docs.djangoproject.com/en/2.2/ref/applications/)

### 3.3 migrations 디렉토리

Django 앱의 데이터 구조에 대한 변경 사항인 migration 파일이 저장되는 디렉토리 입니다. 

### 3.4 models.py

앱에서 사용하는 데이터 구조를 정의하고 데이터베이스와의 소통을 담당하는 파일입니다. 

### 3.5 tests.py

앱에 대한 테스트 코드를 작성하는 파일입니다. 테스트는 말 그대로 Django 프로젝트의 모든 기능이 의도한 대로 잘 동작하는지 체크하는 것을 말합니다. 그리고 이런 테스트는 작은 함수 하나를 체크하는 테스트부터 여러 함수의 상호작용이 있는 전반적인 큰 로직을 테스트하는 것까지 다양한 크기의 테스트가 있습니다. 그렇기 때문에 프로젝트를 모두 완성한 다음 테스트를 준비하는 것이 아니라 앱 별로 작은 단위의 자동화된 테스트를 미리 만들어서 프로젝트 전체에 대한 테스트가 효율적으로 이루어질 수 있도록 작성하는 것이 좋습니다. 

### 3.6 views.py

앱에서 어떤 기능을 할지에 대한 메인 로직을 담당하는 파일입니다. 