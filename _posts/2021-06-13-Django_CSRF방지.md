---
title: '[Django] CSRF방지'
excerpt: CSRF방지에 대해 이해하기
categories:
    - Django

tag:
    - Django
    - python

comments: true
last_modified_at: 2021-06-13T15:00:00+09:00
toc: true
---

# CSRF 방지

크로스 사이트 요청 위조(CSRF, Cross-Site Request Forgery)는 간단히 말하면 웹 사이트에서 유저가 서버로 요청을 보내는 행위를 악의적으로 변경해서 요청을 전송하는 것입니다. 내가 요청하지 않은 일인데 내가 요청한 것처럼 처리되는 거죠.

## 크로스 사이트 요청 위조

Cross-Site라는 말이 붙은 이유는 악성 사이트에서 보안이 취약한 사이트로 우회 요청을 보내기 때문인데요. 예를들면 다음과 같은 단계로 요청 위조가 일어날 수 있습니다.

1. 유저가 보안이 취약한 사이트(www.example-weak.com)에 로그인을 합니다. 아이디와 패스워드등의 유저 입력을 받아서 서버로 전송해야 하니까 폼(Form)을 사용하겠죠?

2. 서버가 유저가 전송한 정보를 보고 이 유저가 정상유저임을 인증합니다. 로그인이 성공한 상태죠. 이때 보이지 않지만 서버로부터 인증된 유저라는 정보가 유저에게도 전달되게 됩니다. 이 정보를 사용해서 서버는 매번 로그인을 요청 하지 않고도 이 유저가 로그인 한 유저라는 것을 식별하는거죠.

3. 이 상태에서 유저가 로그아웃 하지 않은 채, 악성 사이트(www.example-malicious.com)로 이동합니다. 우리가 웹 서핑을 할 때 흔히 하는 동작이죠.

4. 그러면 이제 악성 사이트에서 다음과 같은 유저의 정보를 가져오거나, 돈을 송금하는 등의 요청을 전송하는 폼을 누르게 하거나 해당 폼을 굳이 작성하지 않아도 자동으로 전송 되는 형태로 요청을 시도합니다. action을 보면 지금 악성 사이트에서 취약한 사이트로 요청을 보내고 있죠? 크로스 사이트 요청이 일어나는 부분입니다.


   ```html
   <form action="www.example-weak.com/user-info/account" method="post">
       <div>응모에 당첨되셨습니다!</div>
       <label type="hidden" name="withdraw" value="withdraw"> # 숨김 처리된 input
       <label type="hidden" name="amount" value="1000000">  # 숨김 처리된 input
       <input type="submit" value="경품받기">
   </form>
   ```

5. 요청을 보낼 때 유저가 가지고 있는 인증 정보도 함께 서버로 전송됩니다.

6. 취약한 사이트에서는 인증된 유저가 보낸 요청이므로 요청을 수행하게 됩니다.

## 위조 방지 토큰

위에서 보았던 크로스 사이트 요청 위조를 방지하는 방법으로 많이 사용하는 것이 바로 CSRF 위조 방지 토큰(Cross Site Request Forgery Token)입니다. 요청 위조 방지 토큰은 서버로부터 폼을 요청할 때 발행되어 유저가 폼에 데이터를 입력하고 서버로 전송할 때 이 토큰 값을 함께 전달해서 서버에서 토큰 값을 비교한 뒤 요청을 처리하는 방식을 말합니다. 그래서 요청 검증 토큰(Request Verification Token)라고 부르기도 합니다. 이렇게 처리하면 위의 예시에서 악성 사이트가 폼을 전송할 때 이 위조 방지 토큰 값을 알 수 없기 때문에 서버에서 사용자가 직접 보낸 요청인지를 검증할 수 있게 되는 거죠.

## Django의 CSRF 방지

Django는 CSRF 위조 방지를 기본 기능으로 제공해서 위조 방지 토큰을 삽입하고 검증하는 과정을 간단하게 구현할  수 있습니다. 폼을 사용하는 템플릿에 아래 처럼 {% raw %} {% csrf_token %} {% endraw %}템플릿 태그를 적어 주면 됩니다.


```html
<form action="/user" method="post">{% raw %}{% csrf_token %}{% endraw %}
    ...
</form>
```


이 밖에도 다양한 외부 라이브러리를 함께 이용할 때 사용할 수 있도록 Django는 개별적으로 CSRF 방지를 구현할 수 있는 방법도 제공하는데 지금은 CSRF 방지가 무엇인지 그리고 Django에서는 어떻게 기본적으로 구현할 수 있는지만 기억해야합니다.