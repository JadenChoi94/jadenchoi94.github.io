---
title: '[Django] Model API'
excerpt: Model API에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-12T15:00:00+09:00
toc: true
---


# Django Model API

Django에서 Model을 정의하면 ORM을 통해 데이터베이스와 소통할 수 있는 API를 제공합니다. 여기서는 우리가 사용하는 Model API를 정리하고 조금 더 자세히 살펴보겠습니다.

## API란?

앞에서 데이터베이스를 조작할 때 사용했던 아래와 같은 모든 명령어들이 바로 API 입니다.

```
<model>.objects.all() # 모든 데이터 가져오기
<model>.objects.get() # 조건에 맞는 데이터 1개 가져오기
```

API란 Application Programming Interface의 약자로 어플리케이션에서 시스템의 기능을 제어할 수 있도록 만든 인터페이스를 말합니다. 쉽게 말하면 어떤 기능을 쉽게 사용할 수 있도록 만든 체계라고 할 수 있는데요, 예를 들어 여러분이 식당에 가면 주문을 받는 직원이 있죠? 우리는 해당 직원을 통해서 먹고 싶은 음식을 주문하고 전달 받아서 맛있게 먹으면 됩니다. 직접 요리사에게 먹고 싶은 음식에 대해 설명하거나 만드는 법을 알려줄 필요가 없죠. 여기서 직원에 해당하는 것이 바로 API 입니다.

## Queryset

Queryset은 Django Model의 데이터가 담겨있는 목록으로 파이썬의 리스트와 비슷한 형태를 가지고 있습니다. 우리는 이러한 Queryset을 얻기 위해서 아래와 같은 'objects'를 이용합니다. 이 'objects'는 'Model Manager'라고 하는데 Model과 데이터베이스 간에 연산을 수행하는 역할을 합니다. 이 'objects'를 통해 데이터베이스와 연산해서 얻은 여러 모델 데이터가 담겨 있는 것이 바로 Queryset 인거죠.

```
<model>.objects.all() # <model>의 모든 데이터 Queryset 가져오기 
```

## Queryset API

자, 어려운 내용은 잠시 내려두고 쉽게 말해서 Queryset은 데이터베이스로 부터 가져온 여러개의 model 데이터 입니다. 우리는 이러한 Queryset을 우리가 원하는 조건에 맞게 만들 수 있으면 되는거죠.

Queryset을 반환 하는 API

| API        | 설명                                                         | 예시                                          |
| ---------- | ------------------------------------------------------------ | --------------------------------------------- |
| all()      | 해당 모델 테이블의 모든 데이터 조회                          | Post.objects.all()                            |
| filter()   | 특정 조건에 맞는 모든 데이터 조회                            | Post.objects.filter(content__contains='coke') |
| exclude()  | 특정 조건을 제외한 모든 데이터 조회                          | Post.objects.exclude(title__contains='code')  |
| order_by() | 특정 조건으로 정렬된 데이터 조회 (-를 붙이면 오름차순으로 정렬) | Post.objects.order_by('-dt_created')          |
| values()   | Queryset에 있는 모든 모델 데이터의 정보를 사전형으로 갖는 리스트 반환 | Post.objects.all().values()                   |

하나의 데이터 객체를 반환하는 API

| API             | 설명                                                         | 예시                                                         |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| get()           | 조건에 맞는 하나의 데이터 조회                               | Post.objects.get(id=1)                                       |
| create()        | 하나의 데이터를 생성하고 해당 모델 데이터를 반환             | Post.objects.create(title='Learning Django', context='Codeit Django') |
| get_or_create() | 조건에 맞는 데이터를 조회하고 해당 데이터가 없다면 새로 생성 후 생성된 모델 데이터를 반환 | Post.objects.get_or_create(title='Learning Python', context='It's good’) |
| latest()        | 주어진 필드 기준으로 가장 최신의 모델 데이터를 반환          | Post.objects.latest('dt_created')                            |
| first()         | 쿼리셋의 가장 첫번째 모델 데이터를 반환, 정렬하지 않은 쿼리셋이라면 pk를 기준으로 정렬 후 반환, 만약 데이터가 없다면 None | Post.objects.order_by('title').first()                       |
| last()          | 연산된 쿼리셋의 가장 가지막 모델 데이터를 반환, 만약 데이터가 없다면 None | Post.objects.order_by('title').last()                        |

그 외 API

| API      | 설명                                                         | 예시                                                         |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| exists() | 연산된 쿼리셋에 데이터가 있다면 True 반환                    | Post.objects.get(pk=812).exists()                            |
| count()  | 쿼리셋의 데이터 개수를 정수로 반환                           | Post.objects.all().count()                                   |
| update() | 데이터를 수정할 때 사용 (여러 데이터 또는 여러 필드를 한 번에 수정 가능), 수정된 데이터의 개수를 정수로 반환 | Post.objects.filter('dt_created'__yeaer=2021).update(context='codeit') → 생성일이 2021년인 모든 포스트 데이터들의 context를 'codeit'으로 바꾸고 변경된 데이터의 개수를 리턴 |
| delete() | 데이터를 삭제할 때 사용                                      | post = Post.objects.get(pk=1) post.delete()                  |

## 필드 조건 옵션 (Field Lookups)

Queryset 연산을 할 때 사용할 수 있는 여러 필드 조건 옵션입니다. 필드명 뒤에 `__` 를 쓰고 사용할 옵션 인자를 적어 주면 됩니다. 아래의 조건 옵션 말고도 더 많은 옵션 들이 있습니다. 아래의 공식 문서를 참고하세요. [필드 조건 옵션 공식문서 바로 가기](https://docs.djangoproject.com/en/2.2/ref/models/querysets/#field-lookups)

| lookup       | 설명                                                         | 예시                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| __contains   | 대소문자를 구분하여 문자열 포함 여부 확인                    | Post.objects.get(title__contains='Codeit')                   |
| __icontains  | 대소문자를 구분하여 문자열 불포함 여부 확인                  | Post.objects.get(title__icontains='Codeit')                  |
| __in         | 반복 가능한 객체 안에서의 포함 여부를 확인                   | Post.objects.filter(id__in=[1, 2, 3])                        |
| __gt         | 초과 여부 확인 (Greater than)                                | Post.objects.filter(id__gt=4)                                |
| __gte        | 이상 여부 확인 (Greater than or equal to)                    | Post.objects.get(id__gte=4)                                  |
| __lt         | 미만 여부 확인 (Less than)                                   | Post.objects.get(id__lt=4)                                   |
| __lte        | 이하 여부 확인 (Less than or equal to)                       | Post.objects.get(id__lte=4)                                  |
| __startswith | 대소문자를 구분하여 해당 문자열로 시작하는지 여부 확인       | Post.objects.filter(title__startswith='code')                |
| __istatswith | 대소문자를 구분하여 해당 문자열로 시작하지 않는지 여부 확인  | Post.objects.filter(context__istartswith='code')             |
| __endswith   | 대소문자를 구분하여 해당 문자열로 끝나는지 여부 확인         | Post.objects.filter(title__endswith='code')                  |
| __iendswith  | 대소문자를 구분하여 해당 문자열로 끝나지 않는지 여부 확인    | Post.objects.filter(title_iendswith='code')                  |
| __range      | range로 제시하는 범위 내에 포함되는지 확인 (시작과 끝 범위 모두 포함) | import datetime start_date = datetime.date(2021, 1, 1) end_date = datetime.date(2021, 3, 1) Post.objects.filter(dt_created__range=(start_date, end_date)) |
| __isnull     | 해당 필드가 Null 인지 여부를 확인                            | Post.objects.filter(context__isnull=True)                    |



## Lazy Evaluation (지연연산)

위에서 작성한 Django의 모든 Query 연산은 병합(Chain)이 가능합니다. 예를들어, Post 중에 id가 10 이상이면서 제목에 'codeit'이 들어가는 모든 데이터 중 가장 마지막에 작성된 데이터를 가져오고 싶다고 할게요. 그러면 아래 처럼 작성할 수 있습니다.

```
Post.objects.filter(id__gte=10, content__contains='codeit').order_by('-dt_created').last()
```

이렇게 하나의 Query 연산에 여러개를 체인으로 엮어서 구현하는 것이 코드를 짧게 작성하니까 좋다고 생각할 수 있지만 너무 많은 연산을 묶는 것은 지양해야 합니다. 모든 코드는 항상 명확하게 작성해야 합니다. 위처럼 하나의 Query에서 여러 연산을 수행 하도록 하는 것은 가독성을 매우 떨어뜨리므로 복잡한 Query를 한 번에 체인으로 묶는 것은 피하는 것이 좋습니다. 대신 아래처럼 작성하는 거죠. 이전보다 이해하기 쉽고 명확한 코드가 되었습니다.

```
post_data = Post.objects.filter(id__gte=10, content__contains='codeit')
post_data = post_data.order_by('dt_created')
post_data = post_data.last()
```

그런데 이렇게 여러 번에 나누어서 작성하면 코드가 훨씬 느려지는 것은 아닐까요? 그에 대한 답은 '그렇지 않다' 입니다. Django의 Query는 기본적으로 지연 연산을 지원합니다. 지연 연산이란 실제로 데이터가 필요하기 전 까지 Query 연산을 수행하지 않고 지연(Lazy)되는 것을 말합니다. 우리가 위에서 처럼 모두 체인으로 묶어서 한 줄로 적는 것과 아래처럼 여러 줄로 나누어 적는 것이 결국 같은 시점에 같은 연산을 수행하게 되는 것이죠. 그래서 우리는 한 줄에 모든 Query 연산과 기능을 작성 하는 것 대신 여러 줄에 얼마든지 Query를 연결해서 작성할 수 있고, 이것은 가독성을 엄청나게 크게 향상시키고 유지보수를 편리하게 해줍니다.