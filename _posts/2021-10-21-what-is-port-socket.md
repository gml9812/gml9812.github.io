---
title: "포트와 소켓이란 무엇인가?"
last_modified_at: 2021-10-20T16:20:02-05:00
categories:
  - Network
tags:
  - transport layer
toc: true
toc_label: "목차"
---

전송 계층(Transport layer)를 이해하기 위해서는 포트와 소켓이 무엇인지 정확히 설명할 수 있어야 한다. 

## 호스트(Host)와 프로세스

네트워크에 연결된 장치를 노드(Node)라고 부르고, 네트워크 주소(IP)가 할당된 노드를 호스트(Host)라고 부른다. 인터넷에 연결된 노트북, 데스크톱, 서버 등이 호스트의 예시라고 할 수 있다.

하나의 호스트 안에서는 여러 프로세스가 동시에 동작한다. 네트워크 공간 상에서 데이터를 주고받을 때는, 목적지 호스트에서 동작하는 여러 프로세스 중 데이터를 받아야 하는 프로세스에 데이터를 전달할 수 있어야 한다. 예를 들어, 카카오톡을 통해 전송한 메시지는 인스타그램이 아니라 카카오톡에 도착해야 한다. 


## 포트란?

위에서 설명한 기능을 수행하기 위해, 포트(Port)가 사용된다. 포트는 *네트워크를 통해 주고받는 프로세스를 식별하기 위해 호스트 내부적으로 프로세스가 할당받는 고유한 값*이다. 데이터는 목적지 호스트에 도착한 뒤, 호스트에서 

## 소켓이란?



## 하나의 프로세스, 여러 개의 소켓






참고: 
- https://blog.naver.com/PostView.nhn?blogId=myca11&logNo=221389847130&categoryNo=24&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView