---
title: '[Django] paginator'
excerpt: paginator에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-15T15:00:00+09:00
toc: true
---


# 페이지네이션(Pagination)

페이지네이션이란 데이터를 일정 길이로 나누어서 전달하는 기능입니다. 일반적으로 가지고 있는 데이터가 한 화면에 모두 보여 주기에 너무 많은 경우 사용하죠. 우리가 여러 웹 페이지에서 '이전 페이지로 가기' 또는  '다음 페이지로 가기' 등으로 자주 볼 수 있는 기능입니다.

페이지네이션은 프론트엔드와 백엔드에서 모두 구현이 필요합니다. 백엔드에서는 페이지 별 데이터를 데이터베이스로부터 가져와서 프론트에게 넘겨 주어야 하고 프론트엔드에서는 받은 데이터 목록과 페이지에 대한 정보를 화면에 표시하도록 만들어 주어야 합니다.

## Django의 페이지네이션

Django는 페이지네이션을 쉽게 구현할 수 있도록 하는 Paginator를 제공합니다. Paginator는 총 두 개의 파라미터만 넘겨주면 쉽게 정의할 수 있는데 첫 번째 파라미터는 각각의 페이지로 나뉘게 될 데이터의 목록, 두 번째 파라미터는 한 페이지에 보여줄 데이터의 수입니다.

```python
from django.core.paginator import Paginator # Django의 Paginator
from .models import Post # 작성한 모델 클래스

posts = Post.object.all() # 모든 데이터를 가져와서
paginator = Paginator(posts, 6) 
# 첫 번째 파라미터 : 페이지로 나뉘게 될 데이터의 목록
# 두 번째 파라미터 :  한 페이지에 보옂루 데이터의 수
```

이렇게 만들어진 Paginator는 자동으로 데이터를 개수에 따라 페이지를 나누어서 갖고 있고 우리는 페이지의 번호를 이용해서 Paginator로부터 페이지를 가져와서 제공하는 여러 기능을 사용하면 됩니다. Paginator와 각각의 Page가 가지고 있는 기능 중 자주 사용하는 것은 아래와 같습니다.

| 메소드&속성                   | 설명                                                         | 예시                          |
| ----------------------------- | ------------------------------------------------------------ | ----------------------------- |
| {paginator}.count             | paginator가 가지고 있는 데이터의 개수                        | {paginator}.count             |
| {paginator}.num_pages         | paginator가 가지고 있는 모든 페이지 수                       | {paginator}.num_pages         |
| {paginator}.page_range        | paginator가 가지고 있는 페이지의 범위(range)                 | {paginator}.page_range        |
| {paginator}.page(num)         | paginator가 가지고 있는 페이지 중 num번째 페이지 객체        | {paginator}.page(1)           |
| {page}.has_next()             | page객체가 다음 페이지가 있는지 여부                         | {page}.has_next()             |
| {page}.has_previous()         | page객체가 이전 페이지가 있는지 여부                         | {page}.has_previous()         |
| {page.has_other_pages()       | page객체가 다른 페이지를 가지고 있는지 여부                  | {page}.has_other_pages()      |
| {page}.number                 | page객체의 현재 페이지 번호                                  | {page}.number                 |
| {page}.object_list            | page객체가 가지고 있는 데이터 목록                           | {page}.object_list            |
| {page}.paginator              | page객체의 Paginator                                         | {page}.paginator              |
| {page}.next_page_number()     | page객체의 다음 페이지 번호                                  | {page}.next_page_number()     |
| {page}.previous_page_number() | page객체의 이전 페이지 번호                                  | {page}.previous_page_number() |
| {page}.start_index()          | page객체가 가지고 있는 데이터의 시작 index (시작 index가 0이 아닌 1 기준) | {page}.start_index()          |
| {page}.end_index()            | page객체가 가지고 있는 데이터의 끝 index (시작 index가 0이 아닌 1 기준) | {page}.end_index()            |

이와 같은 메소드들을 이용하면 쉽게 페이지네이션을 구현할 수 있습니다. 그럼 View와 Template로 나누어 페이지네이션을 구현해 보겠습니다.

### View 작성하기

Django 페이지네이션은 Paginator를 이용해서 구현합니다.

```python
from django.core.paginator import Paginator
```

먼저 페이지네이션을 구현하려면 데이터를 가져와야겠죠? 모델(Model)을 이용해서 가져오면 됩니다.

```python
def post_list(request):
    posts = Post.object.all() # 모든 Post 데이터를 가져옵니다.
```

그 다음은 Django의 Paginator에 페이지네이션을 구현할 데이터 목록과 각 페이지마다 보여줄 데이터의 개수를 전달합니다.

```python
def post_list(request):
    posts = Post.object.all() 
    paginator = Paginator(posts, 8) # Post를 한 페이지에 8개씩 할당합니다.
```

그리고 이제 이 paginator 안에 있는 여러 유용한 메소드를 이용해서 구현하면 됩니다.

이제 Template으로 넘겨주기 위한 페이지를 Paginator로 부터 가져와야 합니다. 이때 가져올 페이지의 번호는 URL의 쿼리스트링에 있는 데이터를 이용하겠습니다.

```python
def post_list(request):
    posts = Post.object.all() 
    paginator = Paginator(posts, 8)
    page_number = request.GET.get('page') # 쿼리 스트링으로 부터 페이지 번호 조회
```

그 후 .page(num) 메소드를 이용해서 페이지를 가져온 후 Template으로 넘겨줍니다.

```python
def post_list(request):
    posts = Post.object.all() 
    paginator = Paginator(posts, 8)
    page_number = request.GET.get('page')
    page_obj = paginator.page(page_number) # 페이지 번호에 해당하는 페이지를 가져옴
    return render(request, 'post_list.html', {'page_obj': page_obj})
```

### Template 작성하기

Template에서는 View에서 넘어온 Page를 이용해서 화면을 구성하면 됩니다. 먼저 페이지의 모든 데이터를 표시할 때는 아래와 같이 for 템플릿 태그를 이용해서 작성할 수 있습니다.

```html
...

{% for post in page_obj.object_list %}
    <p>post로 부터 조회한 데이터</p>
    <p>{{ post.title }}</p>
{% endfor %}

...
```

그리고 이전 페이지, 다음 페이지로 가는 오브젝트들도 Page가 제공하는 메소드를 이용하면 쉽게 구현할 수 있습니다.

```html
...

{% if page_obj.has_previous %} <!-- 만약 현재 페이지의 이전 페이지가 있다면 -->
    <a href="?page=1"> first</a>
    <a href="?page={{ page_obj.previous_page_number }}">prev</a> <!-- 이전 페이지 번호 -->
{% endif %}

<span>
    Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}.
    <!-- page_obj.number : 페이지(page_obj)의 번호 -->
    <!-- page_obj.paginator.num_pages : 페이지를 관리하는 Paginator가 가지고 있는 전체 페이지 수 -->
</span>

{% if page_obj.has_next %} <!-- 만약 현재 페이지의 다음 페이지가 있다면 -->
    <a href="?page={{ page_obj.next_page_number }}">next</a> <!-- 다음 페이지 번호 -->
    <a href="?page={{ page_obj.paginator.num_pages }}">last </a> <!-- 전체 페이지의 개수 = 마지막 페이지 번호 -->
{% endif %}

...
```

이외에 Django의 Pagination에 대해 더 많이 알고 싶다면 아래 공식문서를 참고하세요. https://docs.djangoproject.com/en/2.2/topics/pagination/