---
title: '로드밸런싱(Load Balancing) vs 리버스 프록시(Reverse Proxy)'
excerpt: Network
categories:
    - Network

tag:
    - Network

comments: true
last_modified_at: 2021-10-02T10:00:00+09:00
toc: true
---

### **1. What is Load Balancing?**


**Load Balancing(로드밸런싱)** 은 여러 Server에 **workload(작업량)를 분산**시켜 웹사이트, 애플리케이션, 데이터 베이스 그리고 다른 서비스들의 퍼포먼스(performance)와 신뢰성(reliablity)을 향상하기 위한 인프라(서비스)입니다.



<p align="center"><img src = "https://blog.kakaocdn.net/dn/ddyrBN/btqDbkONaSB/OxIAMKMuBKkVPCYJVFHyn0/img.png"></p>

<p align="center">[로드밸런싱을 적용하지 않은 웹서비스]</p>



로드밸런싱을 적용하지 않은 웹서비스는 위 사진과 같습니다. 이때 **Server**에 접속하는 **User**가 한 두 명인 경우 Server의 부하는 문제가 없겠죠? 하지만 **100명, 1000명, 1만 명이라면?** 서버에 **장애**가 일어날 것입니다. 가령 서버 동시접속자가 많아서 서버가 다운되는 것도 이 문제와 관련 있겠죠?

 



![img](https://blog.kakaocdn.net/dn/bHrZA8/btqDceHkufm/xAkdmxyC8O6FyJ3wD2xIXk/img.png)<p align="center">[로드밸렁싱을 적용한 웹 서비스]</p>



로드밸런싱을 사용한다면, 한곳에 집중되는 부하를 여러 서버로 분산 시킬수 있습니다. 말 그대로 로드밸런싱(부하분산) 입니다.

 

 

### **2. Load Balancer가 다룰 수 있는 Traffic들은 무엇이 있을까?**

------

로드밸런서는 크게 다음 **네 가지 타입**의 트래픽(traffic)들을 위한 규칙들을 만들 수 있습니다.

 

- **HTTP** - 로드밸런서는 header에  **X-Forwarded-For**, **X-Forwarded-Proto** 그리고 **X-Forwarded-Port** 를 넣어 원래 요청에 대한 백엔드 정보를 제공합니다.
   
- **HTTPS** - HTTPS 밸런싱은 암호화를 추가하여 HTTP 밸런싱과 동일하게 작동합니다. 암호화는 백엔드까지 암호화를 유지하는 **SSL passthrough** 또는 Load Balancer에 암호 해독 부담을 주지만 암호화되지 않은 트래픽을 백엔드로 보내는 **SSL termination**와 같은 두 가지 방법 중 하나로 처리됩니다
   
- **TCP** - HTTP 또는 HTTPS를 사용하지 않는 애플리케이션을 위한 traffic이며, TCP traffic 또한 분산처리할 수 있습니다. 예를 들어, 데이터베이스 클러스터로의 트래픽은 모든 서버에 분산될 수 있습니다.
   
- **UDP** - 최근에는 일부 로드 밸런서가 UDP를 사용하는 DNS 그리고 syslogd(로그 관리 데몬)와 같은 핵심 인터넷 프로토콜의 로드밸런싱 지원을 추가했습니다.

 

### **3. What is Reverse Proxy?**

------

**리버스 프록시(reverse proxy)**는 컴퓨터 네트워크에서 클라이언트를 대신해서 한 대 이상의 서버로부터 자원을 추출하는 [프록시 서버](https://ko.wikipedia.org/wiki/프록시_서버) 일종이다. 그런 다음 이러한 자원들이 마치 웹 서버 자체에서 기원한 것처럼 해당 클라이언트로 반환된다.

 

관련 클라이언트들을 위해 임의의 서버에 접속하는 중간 매개체인 [포워드 프록시](https://ko.wikipedia.org/wiki/프록시_서버)(forward proxy)와는 반대로, 리버스 프록시는 관련 서버들을 위해 임의의 클라이언트가 해당 서버에 접속하는 중간 매개체이다.

 



![img](https://blog.kakaocdn.net/dn/c5FgJM/btqDbFSHx32/5LMPlwFlAlN8T38ryQwzDK/img.png) 
<p align="center">[리버스 프록시를 나타낸 그림]</p>



 

리버스 프록시의 구조는 위 그림과 같다. 클라이언트에서 서버로의 접근 요청이 오면 프록시서버에서 각각의 서버(endpoint)로 연결해준다.

이러한 리버스 프록시의 대표적인 예로는 **nginX** 가 있습니다. 

 

크게 프록시 서버를 사용하는 이유는 **보안, 캐시** 등의 이유가 있습니다. 프록시 서버는 요청된 내용들을 캐시를 이용해 저장해 둡니다.

 

 

### **4. 마무리**

------

**로드밸런싱(Load Balancing)과** **리버스 프록시(Reverse Proxy**)에 대해 간단한 개념 정도를 정리해봤습니다. 두 개의 개념이 비슷한 것 같으면서 다른 게 조금 헷갈리기도 하네요.

 

제 개인적인 생각으로 간단하게 한 줄 요약하자면

> \- 로드밸런싱 : 서버로 접근하는 부하를 분산하기 위한 서비스
> \- 리버스프록시(or 프록시): 서버로 접근하는 클라이언트로부터의 보안을 위한 서버

이 정도..? 인 것 같습니다. (설명이 부족하거나 틀린 부분이 있으면 댓글로 남겨주시면 수정하겠습니다)

 

추가로 **nginX**는 리버스 프록시의 용도로 사용할 수 있지만 **upstream이라는** 옵션으로 로드 밸런싱을 사용할 수 있다고 합니다. 

(관련 내용: https://seokjun.kim/haproxy-and-nginx-load-balancing/)

 

네트워크를 따로 공부한 적이 없어서, 조금 이해하는데 힘들었지만 이렇게 정리하면서 조금이나마 더 알게 되었던 것 같습니다.

 

**- 참고 사이트**

- https://www.digitalocean.com/community/tutorials/what-is-load-balancing#how-does-the-load-balancer-choose-the-backend-server
- https://www.joinc.co.kr/w/man/12/proxy