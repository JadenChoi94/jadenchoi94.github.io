---
title: '[Django] Migration'
excerpt: Migration에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-09T10:00:00+09:00
toc: true
---

# Django의 Migration

마이그레이션(Migration)은 모델(Model)의 변경 사항 즉 Django 프로젝트의 데이터 구조 변경 사항을 관리하기 위한 Django만의 관리 방법입니다. 변경될 때마다 히스토리를 하나씩 만들어 두고 마치 블럭을 갈아 끼우듯 생성한 히스토리를 실제 데이터베이스에 반영하는 거죠. 그리고 이 모든 과정은 Django의 **ORM(Object-Relational Mapping)**을 통해 진행됩니다.

4가지 명령어를 간단히 다시 짚어보겠습니다.

- makemigrations
- migrate
- showmigrations
- sqlmigrate

## makemigrations

```shell
python manage.py makemigrations
```

모델의 변경 사항을 인식해서 새로운 마이그레이션을 만듭니다. 이때 마이그레이션은 각 앱 디렉토리 내 migrations 디렉토리 안쪽에 생성됩니다.

## migrate

```shell
python manage.py migrate
```

생성된 최신 버전의 마이그레이션을 데이터베이스에 반영합니다. 만약 이전 마이그레이션으로 되돌리고 싶다면 python manage.py migrate {앱 이름} {되돌릴 마이그레이션 번호}를 사용할 수 있습니다.

## showmigrations

```shell
python manage.py showmigrations
```

현재 django 프로젝트의 모든 마이그레이션과 반영 상태를 나타냅니다. 만약 특정 앱에 대한 것만 보고 싶다면 python manage.py showmigrations {앱 이름}을 사용할 수 있습니다.

## sqlmigrate

```shell
python manage.py sqlmigrate {앱 이름} {마이그레이션}
```

인수로 넘겨준 마이그레이션이 ORM을 통해 변경된 SQL문을 출력합니다. sqlmigrate를 통해 모델이 의도한 대로 SQL문으로 변경되어 데이터베이스에 반영되었는지 확인할 수 있습니다.

지금은 마이그레이션에 대해 여기까지 이해하는 것으로 하고 다음 레슨으로 넘어가겠습니다. 천천히 조금씩 Django를 정복해봅시다! 만약 Django의 마이그레이션에 대해 더 알고 싶다면 아래의 공식 문서를 참고하세요. https://docs.djangoproject.com/en/2.2/topics/migrations/