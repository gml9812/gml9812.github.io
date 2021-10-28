---
title: "검색창에 naver.com을 입력했을 때 일어나는 일은?"
last_modified_at: 2021-10-26T16:20:02-05:00
categories:
  - Network
tags:
  - general
toc: true
toc_label: "목차"
---


## URL 파싱

가장 먼저, 브라우저는 URL을 해석하여 HTTP Request 메시지를 만든다. 

![image](https://user-images.githubusercontent.com/28294925/138905963-0589e3f3-6834-47e3-961e-8ab9d0ec7455.png)

URL을 해석한다는 말은, *어떤 프로토콜을 사용하여, 어떤 도메인 네임을 가진 서버의, 어떤 포트번호로 요청을 보낼 것인지* 알아낸다는 뜻이다. 위 사진에서는 https 프로토콜을 사용하여, www.naver.com의, 443번 포트로 요청을 보내고 있다. 

따로 프로토콜이나 포트 번호를 입력하지 않는다면, 브라우저 기본값에 따라 프로토콜과 포트 번호가 부여된다. 기본 프로토콜이 http일 경우에는 포트 번호 80이, https일 경우 포트 번호 443이 부여된다. 

만약에 https://naver.com 대신 http://naver.com을 입력한다면 무슨 일이 벌어질까? 겉보기에는 서버가 http를 사용한 요청을 Redirect 하는 것처럼 보인다. 그러나 실제로 일어나는 과정은 좀 더 복잡하다. 

HSTS란 HTTPS로만 연결되도록 요청한 웹사이트의 목록을 말한다. 브라우저는 요청한 도메인이 HSTS에 존재하는지 먼저 확인한 뒤, 만약 존재한다면 http 대신 https를 사용하여 요청을 보낸다. Http를 사용한다면 여러 보안상의 문제가 생길 수 있기 때문에 굳이 이런 방식을 취하는 것이다. 

## DNS 검색

DNS(Domain Name System)이란 도메인 네임과 그에 해당하는 IP주소를 저장하고 있는 데이터베이스를 말한다. 컴퓨터는 도메인 네임을 이해할 수 없기에, DNS를 사용하여 도메인 네임을 IP로 변환해야만 한다. 

브라우저는 DNS 서버에 요청을 보내기 전, 먼저 4가지의 캐시에서 DNS 기록을 확인한다.

1. 먼저 브라우저 캐시를 확인한다. 브라우저는 일정 기간 동안의 DNS 기록을 저장하고 있다.
2. 브라우저 캐시에서 도메인 네임에 해당하는 IP주소가 발견되지 않았다면, systemcall을 사용하여 OS 캐시를 확인한다. 
3. 여전히 발견되지 않았다면, 브라우저는 DNS 기록을 캐싱하고 있는 router와 통신하여 IP를 찾으려 시도한다.
4. 마지막으로, ISP 캐시를 확인한다.

요청한 도메인이 캐시에 없으면, 비로소 DNS 서버에 요청을 보낸다. 

![image](https://user-images.githubusercontent.com/28294925/138911987-6500bb53-9f4d-4b12-bd91-00f066045082.png)

+++

## 라우터를 이용해 접속하려는 네트워트로 가는 최적 경로 찾기

## IP 주소를 MAC 주소로 변환

IP주소를 통해서 해당 IP 서버가 있는 곳이 로컬 네트워크가 아닌 경우 그 지역 라우터까지 패킷이 전달된다. 라우터에서는 IP 주소에 해당하는 컴퓨터가 누군지 알아내기 위해 MAC 주소가 필요하다.

 

즉, 우리가 접속하려는 서버의 네트워크를 찾기 위해 IP주소를 사용하고, 그 네트워크 내부에 있는 컴퓨터와 통신하기 위해 MAC 주소가 필요한 것이다.

 

ARP(address resolution protocol)는 논리주소인 IP 주소를 물리주소인 MAC 주소로 변환해주는 역할을 한다.

송신측은 목적지의 MAC주소가 필요하므로 ARP 요청 패킷을 브로드캐스트 방식으로 전달한다. 브로드캐스트 방식으로 전달하는 이유는 최종 목적지의 물리주소를 모르기 때문에 모두에게 요청하는 것이다.
모든 Host와 Router는 송신자가 보낸 ARP 요청을 수락한다.
해당되는 수신자만 자신의 IP주소와 MAC 주소를 넣어 응답한다.

## TCP 소켓 연결

보통 웹사이트의 http 요청의 경우 TCP를 사용한다. 따라서 naver.com 서버와 통신을 하기 위해서는 TCP 소켓을 연결해야만 한다.

[이 과정에서 Three-way handshake가 사용된다](https://gml9812.github.io/network/what-is-handshaking/)

추가적으로 https의 경우, TLS handshake(Transport Layer Security, SSL)가 추가적으로 일어난다. 

## HTTP 요청/응답

TCP 연결까지 끝났다면, 드디어 첫 번째 과정에서 생성했던 HTTP Request를 서버에 전달할 수 있다. 서버는 HTTP Response를 작성하여 브라우저에 전달한다. 

## 브라우저 렌더링


참고: 
- https://1-7171771.tistory.com/134
- https://github.com/SantonyChoi/what-happens-when
- https://devjin-blog.com/what-happen-browser-search/