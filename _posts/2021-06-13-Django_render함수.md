---
title: '[Django] render함수'
excerpt: render함수에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-13T15:00:00+09:00
toc: true
---

# render( )

**render( request, template_name, context=None, context_type=None, status=None, using=None )**

render 함수는 인자로 주어진 템플릿을 사전형(dict) 인자인 context와 결합해서 렌더링을 거친 다음 HttpResponse 객체로 반환하는 함수입니다. 쉽게 말하면 인자로 넘겨주는 템플릿과 context 데이터를 합쳐서 HttpResponse 객체로 돌려주는 함수인 거죠.

## 필수 인자

이 중에서 필수인자는 `request`와 `template_name` 입니다.

- `request`에는 데이터를 요청한 요청 객체를 넘겨줍니다. 함수형 view의 인자로 들어온 request를 render에 사용합니다.

```python
...
def view(request):
    return render(request, ...)
...
```

이 request를 넘겨주는 이유는 요청에 대한 정보에 접근해서 user, session 등 여러 가지 기능을 구현하기 위해서인  데 이 부분은 다른 토픽에서 함께 다루도록 하겠습니다. 지금은 아 request 객체가 필요하구나! 정도만 알아두면 됩니다.

- `template_name`은 렌더링에 사용할 대상 템플릿을 명시합니다.

## 선택 인자

다음으로 선택 인자를 보면

- `context` 는 템플릿에 추가할 값들이 들어 있는 사전형 인자로 기본값은 아무것도 없는 None입니다.

- `content_type` 은 결과로 만들어 내는 문서의 유형을 말하며 기본값은 'text/html' 즉 HTML 웹 페이지입니다.

- `status` 는 상태 코드(Status Code) 값이며 기본값은 200(성공)입니다. 상태 코드는 클라이언트의 요청이 성공적으로 처리되었는지에 대한 정보를 주는 코드인데 뒤에서 자세하게 다루도록 하겠습니다.

- `using` 은 템플릿을 렌더하는 템플릿 엔진을 지정할 수 있는 인자입니다. 원하는 경우 다른 템플릿 엔진을 사용하여 템플릿을 렌더링할 수 있습니다.

  더 자세히 알고 싶으신 분들은 아래 공식 문서를 참고하세요 :) [render 공식문서 바로가기](https://docs.djangoproject.com/en/2.2/topics/http/shortcuts/#django.shortcuts.render)

# 두 개의 render

사실 우리가 알고 있는 과정 중 'render'라고 불리는 과정은 두 가지가 있습니다. 우리가 Django Template Language를 써서 작성한 코드를 읽어서 일반적인 HTML 코드로 바꿔주는 과정도 rendering이라고 하고, 이 HTML 파일을 브라우저가 읽어서 우리가 실제로 보는 이쁜 웹 페이지로 바꿔주는 과정도 rendering이라고 합니다. 그러니까 서버에서 Django 코드를 render 해서 HTML 파일로 만들고 그 다음 웹 브라우저에서 HTML 파일을 render 해서 우리가 보는 웹페이지로 바꿔주는거죠. Django 강의에서 말하는 render는 대부분 Template Language를 사용한 Django 코드를 HTML로 바꿔주는 과정을 일컫기는 하지만 이 두 가지 과정 모두 render라는 것을 기억해주세요.

# 모르는 함수가 너무 많아요!

처음 접하는 함수를 사용할 때 함수 내부를 몰라도 사용할 수 있지만 그런 함수가 많아질수록 내가 작성하는 코드가 어떻게 동작하는지 파악하기가 어려워지고 필요할 때 내가 원하는 방향으로 수정해서 사용할 수 없게 됩니다.

모르는 함수가 나올 때 가장 좋은 방법은 바로 공식 문서를 보는 건데요, 내가 궁금한 함수를 문서에서 검색해서 찾은 다음 나와 있는 모두 이해할 필요 없이 한 번 함수를 훑어보는 것만으로도 큰 도움이 됩니다. 그리고 필요하다면 함수의 숨겨진 인자가 무엇인지, 우리가 넘겨주는 값은 어떤 인자로 들어가게 되는지, 어떤 로직으로 동작하는지, 반환 값은 무엇인지 등을 파악하게 되면 함수에 대한 막연한 두려움이 사라지고 코드를 작성할 때 조금 더 빠르고 정확하게 작성할 수 있습니다.