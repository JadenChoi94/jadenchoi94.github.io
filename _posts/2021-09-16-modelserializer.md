---
title: 'DRF'
excerpt: Modelserializer
categories:
    - DRF

tag:
    - DRF
    

comments: true
last_modified_at: 2021-09-16T10:00:00+09:00
toc: true
---

#### [1] ModelSerializer 개념 

django rest framework의 [ModelSerializer](https://www.django-rest-framework.org/api-guide/serializers/#modelserializer) 에 대해 먼저 알아보겠습니다.

ModelSerializer은 모델을 JSON으로 쉽게 바꿀 수 있도록 해주는 것입니다. 

 

예를 들어 Account라는 모델이 있다고 할때 AccountSerializer를 만들어서 

내가 원하는 모델의 필드들만 json으로 만들어줄 수 있어요 

 



![img](https://blog.kakaocdn.net/dn/bEmPkW/btqIvDBhTX2/pztkFisPvi7SU031xkyva1/img.png)



 

모델의 모든 필드를 다 json으로 만들어줄 거라면  __all__ 을 해줄 수 도 있고 

 



![img](https://blog.kakaocdn.net/dn/bSL3aT/btqIqmf39hX/Kq3z6Tugo4Nc4goelm8smk/img.png)



 

특정 필드만 제외하고 싶다면 exclude 해줄 수 도 있답니다. 

 



![img](https://blog.kakaocdn.net/dn/cfaRrT/btqIkueSA4a/Oz2Vfl9vj9Bzg9TAycTJu0/img.png)



 

그리고 nested json을 만들고 싶으면 depth 옵션을 사용할 수도 있습니다. 

 



![img](https://blog.kakaocdn.net/dn/JFM2j/btqInsnKV9Z/xPYCT6lOX3ory6tQlKz3BK/img.png)

#### [3] 모델 Serializer 만들기 

 

그 다음 question앱에 serializers.py 라는 파일을 만들어주세요 

그리고 Question모델에 대한 Serializer를 만들어줍니다. 

 

Question모델의 모든 필드를 json으로 만들겠습니다. (이렇게 하면 위에서 말한 id도 같이 json에 나옵니다)

 



![img](https://blog.kakaocdn.net/dn/cCozhs/btqIl83H5CK/HUGKA9ctpUfF0PfSKeaZ50/img.png)



 

#### [4] 파이썬 쉘에서 JSON 출력해보기 

 

일단 파이썬 쉘을 실행시켜주세요

 

```
 python manage.py shell
```

 

쉘에 아까 만든 시리얼라이저랑 모델을 import 해줍니다. 

```
from question.serializers import QuestionSerializer
from question.models import Question
```

 

이렇게 serializer를 만들고 data를 출력해보면..!! 

```
question = Question(title="안녕하세요 질문입니다")
serializer = QuestionSerializer(question)
serializer.data
```

 

json이 잘 출력되는 것을 볼 수 있습니다. 

 



![img](https://blog.kakaocdn.net/dn/bF4v2S/btqIxXzf0m0/W1MtyZceyiGcBPGJrL6qH0/img.png)



 

(모델이 DB에 저장이 안되어서 id가 None으로 나오는 것 같아요)

 

control+D 또는 quit()을 입력해서 파이썬 쉘을 종료합니다. 

 

 

#### [추가] - id를 안나오게 해주려면

만약 id가 나오는 게 싫다면 두가지 방법 중 하나로 해줄 수 있겠습니다.

 



![img](https://blog.kakaocdn.net/dn/cx2qoO/btqIrIXEd8X/0kw654LV2vOb9kASwuMKNK/img.png)



 



![img](https://blog.kakaocdn.net/dn/bc8s0k/btqIrtTucEa/xPdDmKTbKg9sObLOfRjfn1/img.png)



 



![img](https://blog.kakaocdn.net/dn/bMRXOr/btqIvD2mLyx/97IANGAF0lGYgKQLRDTtqk/img.png)



 

 

#### [추가] - 리스트 형태의 json을 내려주고 싶다면

 



![img](https://blog.kakaocdn.net/dn/miOyY/btqJ4GRdlDK/7zl3wC5BcsWmzuTkC7Wx3k/img.png)



원본: https://eunjin3786.tistory.com/237?category=891871