---
title: 'ORM'
excerpt: 쿼리를 줄이자
categories:
    - DRF

tag:
    - DRF
    - ORM
    

comments: true
last_modified_at: 2021-09-15T10:00:00+09:00
toc: true
---

그동안 select_relaed와 prefetch_related의 차이를 잘 몰랐었는데 이 글을 읽고 한방에 정리되었다.

너무 친철히 잘 설명되어 있어 가져왔다.

원본: https://jay-ji.tistory.com/35

오늘은 Django ORM 사용할 때 Query 개수를 줄일 수 있는 방법 중 **select_relaed, prefetch_related**에 대해서 간단히 알아보도록 하겠습니다.

 

*** 쿼리셋과 캐싱 (알고 가야 할 내용)**

각각의 쿼리셋은 DB에 Access 할 때 캐시 메모리를 포함하고 있습니다. 처음 쿼리셋이 연산될 때, 캐시가 비었기 때문에 Query가 발생합니다. 그 이후 동일한 쿼리셋을 사용할 경우 추가적인 Query는 발생하지 않고, 캐시에서 꺼내서 사용하게 됩니다.

(상세 내용은 아래 장고 문서(?) 에서 확인할 수 있습니다)

django document : https://docs.djangoproject.com/en/2.2/topics/db/queries/#caching-and-querysets

------

#### **1. ORM, QuerySet 이란?**



![img](https://blog.kakaocdn.net/dn/bdsqdj/btqwWoWQAkT/CfYqQLICQHFGjbTTmN8YIK/img.png)이미지 출처 : Django Book(https://djangobook.com/)



ORM(Object-relational Mapping)은 데이터 베이스와 객체지향 프로그래밍 언어 간의 호환되지 않는 데이터를 변환하는 프로그래밍 기법입니다.(from wiki) 간단히 말하면 SQL문을 몰라도 Django에서 지원하는 ORM을 통해 database에 접근해서 원하는 data를 가져오고, 저장도 할 수 있게 해주는 문법(?)입니다.

 

QuerySet(쿼리셋) 은 ORM에서 django model objects를 통해 반환되는 객체입니다.(python data structure)

------

#### **2. select_releated 란?**

select_realted는 쿼리셋을 반환할 때 **foreign-key, OneTonOneFeild** 관계인 모델들 함께 가져오는 ORM입니다.

 

select_related는 좀 더 복잡한 쿼리가 되지만, **foreign-key, OneTonOneFeild** 관계인 모델들을 코드에서 재사용할 경우 추가적인 쿼리가 필요 없습니다. (캐싱되기 때문에)

 

모델을 만들어서 확인해 보겠습니다.



![img](https://blog.kakaocdn.net/dn/dGww1S/btqwUDOrfZe/jERIKuhcb0eiWDQ2RHmc91/img.png)![img](https://blog.kakaocdn.net/dn/c5hDZ9/btqwVDG9Foy/NuCxjoQ7bKyafE0ldCWFV0/img.png)



위처럼 Company 모델과 MyUser 모델이 이 정의 있으며, 두 모델의 관계는 **OneToOne입니다.**



![img](https://blog.kakaocdn.net/dn/UGJF9/btqwWApdBkJ/pxJcSQ6iBIzrWDenPFqGHk/img.png)



위 테이블을 보시면 **3명**의 user들은 **1개**의 회사들과 **OneToOne** 관계로 연결되어 있습니다.

 

이때, 모든 user들의 쿼리셋을 가져온다면 일반적으로 **MyUser.objects.all()** 이렇게 사용할 수 있습니다.

 

이때 쿼리는 총 몇 개가 실행될까요?? 



![img](https://blog.kakaocdn.net/dn/nJ17L/btqwWZPS6kB/GitjAxToC4rbsQu18KtH40/img.png)



정답은 총 4번의 쿼리입니다.

 

-  MyUser 모델의 data를 가져오는 **쿼리 1개**
-  MyUser 모델에서 가져온 3개의 data에 OneToOne으로 연결된 Company 모델을 가져오는 **쿼리 3개**

자! 이제 그럼 **MyUser.objects.all().select_related('company')**를 사용하여 Company 모델들까지 모두 가져와 보겠습니다. 

 

이번에는 몇 개의 쿼리가 실행될까요?



![img](https://blog.kakaocdn.net/dn/dGU4Le/btqwXeF1KeL/ktzIlWyXkJCl5gSSPCQQQ1/img.png)



아...너무 불필요한 데이터가 많아서...안보이네요 ㅎㅎ 정답은 1개입니다. 쿼리를 자세히 살펴보면

SELECT `user_myuser`.`id`, `user_myuser`.`password`, (중간 생략) `company_company`.`date_establish` FROM `user_myuser` **INNER JOIN** `company_company` ON (`user_myuser`.`company_id` = `company_company`.`id`) LIMIT 21; args=()

뭐가 보이시나요?? **select_related**는 **INNER JOIN**으로 data를 가져오기 때문에 **쿼리를 1개만 사용**합니다. 

 

만약, MyUser에 100개의 company와 OneToOne 관계를 가지고 있는 100명의 User가 있다면 

 

**MyUser.objects.all()**을 사용할 때 **101번**의 쿼리를 **MyUser.objects.all().select_related('company')** 로 **단 1개의** 쿼리로 모든 데이터를 가져올 수 있겠죠?

 

------

#### **3. prefetch_related 란?**

prefetch_related는 쿼리셋을 반환할 때 **foreign-key, OneTonOneFeild** 관계뿐만 아니라 **ManyToMany, ManyToOne** 관계의 모델들 함께 가져오는 ORM입니다.

 

**prefetch_related는** **select_related** 와 비슷하지만 다릅니다(?) ㅎㅎ. 동일한 조건의 DB에서 **MyUser.objects.all().select_related('company')**로 쿼리셋을 가져와 보겠습니다.



![img](https://blog.kakaocdn.net/dn/bK3Ii2/btqwUDgB2F6/WKYE6Hvh8Znb8bRmdbcOJ1/img.png)



이번에는 **2개의 쿼리**가 실행되었습니다. **prefetch_related** 는 **select_related** 의 차이를 아시겠나요?

 

select_related는 inner join으로 쿼리셋을 가져오고, prefetch_related는 myuser, company 에 대해 각각의 쿼리로 쿼리셋을 가져옵니다.

 

------

#### **4. 정리**

- select_related 는 INNER JOIN 으로 쿼리셋을 가져온다.
- prefetch_related 는 모델별로 쿼리를 실행해 쿼리셋을 가져온다.
- 이 모든건 qeryset들이 캐싱되기 때문에 가능