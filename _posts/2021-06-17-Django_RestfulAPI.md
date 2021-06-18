---
title: '[Django] RestfulAPI'
excerpt: REST framework에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-17T11:00:00+09:00
toc: true
---

Django와 RESTful API개발에 도움을 주는 ***Django REST framework*** 를 이용해 RESTful API를 개발해보았습니다.

# 프로젝트 생성

우선 기본적인 Django 프로젝트를 생성합니다. Django를 처음 접하시는 분은 [*Make a Simple Blog with Django*](https://medium.com/wasd/django로-간단한-블로그-만들기-1-개요-프로젝트-구성-83d03ec74395)를 읽고 오시면 도움이 됩니다.

저는 다음과 같은 디렉터리 구조로 생성했습니다.

```
django_rest_tutorial
├── api
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── env
│   └── <python virtual environment>
├── manage.py
└── tutorial
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

이제 *Django REST framework*를 설치해봅시다.

```
(devenv)
→ pip install djangorestframework
```

*Django REST framework*를 사용하기 위해선 settings.py의 `INSTALLED_APPS` 에 `‘rest_framework’`를 추가해주어야 합니다. 프로젝트 생성하면서 같이 생성한 `‘api’` 앱도 같이 추가해줍시다.

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'api',
]
```

# Models 생성

모델은 전 글의 Post 모델을 그대로 가져다 사용해보겠습니다. 소스를 그대로 가져옵니다.

```
# api/models.pyfrom django.db import models
from django.contrib.auth.models import Userclass Post(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=144)
    subtitle = models.CharField(max_length=144, blank=True)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)    def __str__(self):
        return '[{}] {}'.format(self.user.username, self.title)
```

평범한 Post 모델입니다. 

# Serializers 생성

기존 Django를 이용한 웹 개발에서 Django ORM의 Queryset은 Django template로 넘겨지며 HTML로 렌더링되어 Response로 보내지게 됩니다.

하지만 JSON으로 데이터를 보내야 하는 RESTful API는 HTML로 렌더링 되는 Django template를 사용할 수 없습니다. 그래서 ***Queryset을 Nested한 JSON
으로 매핑하는 과정\***을 거쳐야 하는데, 이 작업을  Serializer이 하게 됩니다.

![img](https://miro.medium.com/max/60/1*AjXUSAQ5WPHi2lvfRKD08g.jpeg?q=20)

![img](https://miro.medium.com/max/1024/1*AjXUSAQ5WPHi2lvfRKD08g.jpeg)

Serializer의 역할

*Django REST framework*가 제공하는 ***ModelSerializer\***을 사용하겠습니다. api에 serializers.py 파일을 생성합니다.

```
# api/serializers.pyfrom rest_framework import serializers
from .models import Post
from django.contrib.auth.models import User
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'email')
class PostSerializer(serializers.ModelSerializer):
    user = UserSerializer(read_only=True)    class Meta:
        model = Post
        fields = (
            'id',
            'title',
            'subtitle',
            'content',
            'created_at',
        )
        read_only_fields = ('created_at',)
```

끝! 그리 복잡하지 않습니다. 

# Views 생성

이제 실질적으로 Queryset을 컨트롤하고 데이터를 조작해 Serializer을 통해 매핑시켜주는 View를 작성해보겠습니다. 이번에는 CBV를 이용해 여러개의 뷰를 작성하지 않고, Viewset을 이용해 Model 하나를 컨트롤하는 CRUD를
1개의 View로 구현시켜보겠습니다.

```
# api/views.pyfrom rest_framework import viewsets
from .serializers import PostSerializer
from .models import Post
from rest_framework import permissions
class PostView(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = (permissions.IsAuthenticated,)    def perform_create(self, serializer):
        serializer.save(user=self.request.user)
```

이렇게 코드 몇 줄로 간편하게 CRUD를 구현할 수 있습니다.

이제 마지막으로 URL을 매핑시켜줍시다. 한 자원을 아우르는 CRUD는 크게
2가지 URL패턴으로 분리되기 때문에 각각 따로 매핑 시켜주어야 합니다.

```
# api/urls.pyfrom django.urls import path, include
from rest_framework.urlpatterns import format_suffix_patterns
from .views import PostViewpost_list = PostView.as_view({
    'post': 'create',
    'get': 'list'
})post_detail = PostView.as_view({
    'get': 'retrieve',
    'put': 'update',
    'patch': 'partial_update',
    'delete': 'destroy'
})urlpatterns = format_suffix_patterns([
    path('auth/', include('rest_framework.urls', namespace='rest_framework')),
    path('posts/', post_list, name='post_list'),
    path('posts/<int:pk>/', post_detail, name='post_detail'),
])
```

그리고 다시 루트 URL에 매핑시켜줍시다.

```
# tutorial/urls.pyfrom django.contrib import admin
from django.urls import path, includeurlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
]
```

소스 작성이 끝났습니다! 이제 Migration 생성하고 실행시켜봅시다.

# 실행

우선 Migration 생성하고 Migrate 합시다.

```
(devenv)
→ python manage.py makemigrations(devenv)
→ python manage.py migrate(devenv)
→ python manage.py createsuperuser(devenv)
→ python manage.py runserver
```

그리고 `localhost:8000/api/posts/` 로 접속하면 다음과 같은 화면을 볼 수 있습니다.

![img](https://miro.medium.com/max/60/1*Bt_4ldSbkiqTTN9yd3aluA.png?q=20)

![img](https://miro.medium.com/max/2560/1*Bt_4ldSbkiqTTN9yd3aluA.png)



Django REST framework가 API 조작을 돕기 위해 기본적으로 제공해주는
조작 페이지입니다. *insomnia*같은 테스트 도구를 이용하지 않아도 API의 동작을 확인할 수 있게 해줍니다. 이제 방금 만든 관리자 계정으로 로그인 한 뒤 재접속 해봅시다.

![img](https://miro.medium.com/max/60/1*X7uKGHtld60H5o6YJzzXpw.png?q=20)

![img](https://miro.medium.com/max/2560/1*X7uKGHtld60H5o6YJzzXpw.png)

Serializer에 정의해준 대로 Form이 생겼습니다.

RESTful API가 정확히 무엇인지 모르는 사람도 조작 가능할 정도로 직관적인 인터페이스를 제공합니다. 포스트를 생성한 뒤 `localhost:8000/api/posts/1/` 로 접근해봅시다.

![img](https://miro.medium.com/max/58/1*PLvLebFNOBmdVZMoIeprNg.png?q=20)

![img](https://miro.medium.com/max/1270/1*PLvLebFNOBmdVZMoIeprNg.png)

해당 경로에서 가능한 모든 동작을 테스트할 수 있습니다.

`posts/1/`에 대해 다룰 수 있는 모든 동작을 이곳에서 테스트가 가능합니다.

물론 테스트 툴(insomnia)에서 요청하면 JSON으로 결과가 날라옵니다.

![img](https://miro.medium.com/max/60/1*ojx5ObaoyCSQb7YjMFCNrQ.png?q=20)

![img](https://miro.medium.com/max/1084/1*ojx5ObaoyCSQb7YjMFCNrQ.png)

지금까지 Django REST framework를 이용해 RESTful API를 개발해 보았습니다. 정말 간단히 코드 몇 줄로 이처럼 강력하면서 세세한 기능구현이 가능하다는 것이 저는정말 놀라움을 느꼈습니다. 새로운 기술을 배우는 즐거움을 다시금 깨닫는 계기가 되었습니다.

