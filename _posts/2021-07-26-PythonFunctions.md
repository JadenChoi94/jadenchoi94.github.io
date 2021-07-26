---
title: '[python]Functions'
excerpt: Back to Basic
categories:
    - python

tag:
    - python
    

comments: true
last_modified_at: 2021-07-26T10:00:00+09:00
toc: true
---

# Back to Basic

> Python functions

**abs()**

: 절댓값을 돌려주는 함수



![img](https://blog.kakaocdn.net/dn/CPRXf/btqFt5aQQlX/04vxKb38Q13lZ2A9aVXvsK/img.png)



 



![img](https://blog.kakaocdn.net/dn/x3p41/btqFt5232Sg/AMtuaVoM8Xb7ag6aJaP3kk/img.png)



 

**all()**

: 반복 가능한 자료형 x를 입력 인수로 받으며 이 x가 모두 참이면 true, 거짓이 하나라도 있으면 false를 반환한다.

 



![img](https://blog.kakaocdn.net/dn/blAmGd/btqFvvNfBWq/UuS9IxmWnzSQSXLedMEg70/img.png)



 

**any()**

: x중 하나라도 참이 있으면 true를 반환하고, x가 모두 거짓일 때에만 false를 돌려준다. all()의 반대.

 



![img](https://blog.kakaocdn.net/dn/bqm63W/btqFvHGENsu/mCRKALFdZvQWR6Xd7Knybk/img.png)



 



![img](https://blog.kakaocdn.net/dn/bt3iUa/btqFvHmk24S/n9tWKpkkBciK29BBwYVoc0/img.png)



 

**chr()**

: [아스키 코드](https://m.blog.naver.com/PostView.nhn?blogId=jamduino&logNo=220833819592&proxyReferer=https:%2F%2Fwww.google.com%2F) 값을 입력받아 그 코드에 해당하는 문자를 출력하는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/dgrzqo/btqFt5BWldy/XfrNz015ySdMamOMAam0UK/img.png)



 

**dir()**

: 객체가 자체적으로 가지고 있는 변수나 함수를 보여준다.

 



![img](https://blog.kakaocdn.net/dn/n7PRW/btqFt5BWogY/RTEY1R7Kv5Kev9Yj8Xs55K/img.png)



 

**divmod(a,b)**

: 2개의 숫자를 입력받아 a를 b로 나눈 몫과 나머지를 튜플 형태로 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/bkyXo1/btqFwkqGMb9/OD37QtblwTAkynCa5mTluk/img.png)



 

**enumerate()**

: 순서가 있는 자료형을 입력 받아 인덱스 값을 포함하는 enumerate 객체를 돌려준다.

 



![img](https://blog.kakaocdn.net/dn/mwSOS/btqFtKrezMy/ZfJkLpzb65iD990KgESKD0/img.png)



 

**eval()**

: 실행 가능한 문자열을 입력받아 문자열을 실행한 결괏값을 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/b6QKXB/btqFt5Wfx8e/IiRuxA0MIjxbKjfK6XitHk/img.png)



 

**filter()**

: 첫 번째 인수로 함수 이름을, 두 번째 인수로 그 함수에 차례로 들어갈 반복 가능한 자료형을 받는다. 그리고 두 번째 인수인 반복 가능한 자료형 요소가 첫 번째 인수인 함수에 입력되었을 때 반환 값이 참인 것만 묶어서 돌려준다.

 



![img](https://blog.kakaocdn.net/dn/cWdAvT/btqFvIZTLF5/RykCwIztNQUhiEzaK11MXk/img.png)

![img](https://blog.kakaocdn.net/dn/bBxrDT/btqFtK5QvKF/nBoXiFMKg7kEeKiQGsosO1/img.png)



 

 

**lambda()**

: 좀 더 간결한 코드를 작성할 수 있다.

 



![img](https://blog.kakaocdn.net/dn/bUz0cZ/btqFt59Lwgx/RzRLTIdsvq6OpwCDnUoqEK/img.png)



 

**hex()**

: 정수 값을 입력받아 16진수로 변환하여 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/b2XqNb/btqFuVMmWp5/ShgOqKiaLKrWQ8iAJaLG9k/img.png)



 

 

**id()**

: 객체를 입력받아 객체의 고유 주소 값을 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/VCopo/btqFwkc941m/Ncbt9htesMmVI4kfs48qe0/img.png)



 

**input()**

: 사용자 입력을 받는 함수이다.



![img](https://blog.kakaocdn.net/dn/b98SLd/btqFvgQlPkd/IuODIjmjWqWNTpmdS4pu80/img.png)



 

**int()**

: 문자열 형태의 숫자나 소수점이 있는 숫자 등을 정수 형태로 돌려주는 함수다.



![img](https://blog.kakaocdn.net/dn/RY2I6/btqFvfDVkEp/LqhBdGBHZTq44I9m3RSdg0/img.png)



 

**isinstance()**

: 첫 번째 인수로 인스턴스, 두 번째 인수로 클래스 이름을 받는다. 입력받은 인스턴스가 그 클래스의 인스턴스인지를 판단하여 참이면 true, 거짓이면 false를 돌려준다.

 



![img](https://blog.kakaocdn.net/dn/cXdmCc/btqFtw0460k/Suoq86uMW34yTAtXy5DM0k/img.png)

![img](https://blog.kakaocdn.net/dn/cB2M7S/btqFv2jvf4a/MXfJ59daaDejB9jUBiCKc0/img.png)



 



![img](https://blog.kakaocdn.net/dn/sI0aF/btqFtwGMvpi/Ke2fKYt99tlDDQLoA0XE9K/img.png)

![img](https://blog.kakaocdn.net/dn/dOTBoP/btqFvHzT3Bf/sxiwtL0NSXufq6SXPt5sSK/img.png)



 

**len()**

: 입력값 s의 길이를 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/oEabv/btqFv1Zb6HV/Aop1f2GLK6A6tclWqLb99K/img.png)



 

**list()**

: 반복 가능한 자료형 s를 입력받아 리스트로 만들어 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/bYy02i/btqFwiNbC7k/6pGHjO7ufMwQ3HkAPakzeK/img.png)



 

**map(f, iterable)**

: 함수와 반복 가능한 자료형을 입력받아 입력받은 자료형의 각 요소를 함수 f가 수행한 결과를 묶어서 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/bzN7B4/btqFvIMmlZj/DCggdsGNsLdbbgqNc0ukhk/img.png)

![img](https://blog.kakaocdn.net/dn/bNac1W/btqFvwehz7q/sSFwBBkMLJCYXWVTUHUwuk/img.png)



 

**max()**

: 인수로 반복 가능한 자료형을 입력받아 그 최댓값을 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/9zB91/btqFuXclpNE/kKgAyBAe25CIPZ2GjTfw1K/img.png)



 

**min()**

: max와 반대되는 함수로, 인수로 반복 가능한 자료형을 입력받아 그 최솟값을 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/bre7YK/btqFvHGF02W/FdtbS20lpk5VlznrCrYryK/img.png)



 

**oct()**

: 정수 형태의 숫자를 8진수 문자열로 바꾸어 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/cmrUBU/btqFuWR3PlO/4KqE37HquARgJ19iuDY7tk/img.png)



 

**open()**

: '파일 이름'과 '읽기 방법'을 입력받아 파일 객체를 돌려주는 함수이다. 읽기 방법을 생략하면 디폴트 값인 읽기 전용 모드로 파일 객체를 만들어 돌려준다.

| 모드 | 설명                      |
| ---- | ------------------------- |
| w    | 쓰기 모드로 파일 열기     |
| r    | 읽기 모드로 파일 열기     |
| a    | 추가 모드로 파일 열기     |
| b    | 바이너리 모드로 파일 열기 |

 

 

**ord()**

: 문자의 아스키 코드 값을 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/bAZYN1/btqFtxTe3Zx/uiQVltbSAmkLcmzbNQ3zaK/img.png)



*※ ord 함수는 chr 함수와 반대이다.*

 

 

**pow(x,y)**

: x의 y제곱한 결괏값을 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/WJ0hI/btqFujtbKLQ/2Pgyn36N6EoMK1iiKmbJKk/img.png)



 

 

**range()**

: 입력받은 숫자에 해당하는 범위 값을 반복 가능한 객체로 만들어 반환한다.

 



![img](https://blog.kakaocdn.net/dn/byBnaG/btqFtJFUufH/KnUx8tq9YyuwYwaMTNkaM0/img.png)



 

 

**round()**

: 숫자를 입력받아 반올림 해주는 함수이다.



![img](https://blog.kakaocdn.net/dn/UK7m6/btqFtxTe8JU/iyEp3bQzwfQQQUCkf42mcK/img.png)



 

**sorted()**

: 입력값을 정렬한 후 그 결과를 리스트로 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/bbj14Q/btqFtwfI3ro/zn4ueqmXZoLYOu1szBpcuK/img.png)



 

**str()**

: 문자열 형태로 객체를 반환하여 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/bcAIue/btqFt6gC1bC/ZznYmAKD6WuBuflZ2yISuk/img.png)



 

**sum()**

: 입력받은 리스트나 튜플의 모든 요소의 합을 돌려주는 함수이다.



![img](https://blog.kakaocdn.net/dn/bs2OCU/btqFtKkyaUF/OVvt90sKizuJKXZgKFfB21/img.png)



 

**tuple()**

: 반복 가능한 자료형을 입력받아 튜플 형태로 바꾸어 돌려주는 함수이다.

 



![img](https://blog.kakaocdn.net/dn/3pZI1/btqFuh91yxZ/JkaUoGGy6eak4h7KG9nf3k/img.png)



 

 

**type()**

: 입력값의 자료형이 무엇인지 알려 주는 함수이다.



![img](https://blog.kakaocdn.net/dn/pvb7J/btqFvJkcuoq/zliuf9TL3QppFNxsYaka5K/img.png)



 

**zip()**

: 동일한 개수로 이루어진 자료형을 묶어 주는 역할을 하는 함수이다.



![img](https://blog.kakaocdn.net/dn/SfKm9/btqFvuAMvlZ/pJUy5QbydD48h4aH9dzODk/img.png)



출처: https://code-nen.tistory.com/109 [넨이의 코딩]