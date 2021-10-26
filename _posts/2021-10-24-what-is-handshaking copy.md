---
title: "3-way, 4-way handshake란?"
last_modified_at: 2021-10-24T16:20:02-05:00
categories:
  - Network
tags:
  - transport layer
toc: true
toc_label: "목차"
---

TCP는 전송 계층(Transport layer)에서 사용하는 프로토콜로, *서버와 클라이언트 간에 데이터를 신뢰성 있게 전달하기 위해 만들어진 프로토콜*이다. TCP는 데이터의 전송 순서를 보장하며, 수신 여부를 확인하고 수신 중 오류가 발생할 시 데이터를 재전송한다. 신뢰성 있는 전송을 위해 TCP는 실제 데이터를 전송하기 전 연결을 설정(Connection Establish)하는데, 이 때 사용되는 것이 3-way handshake다.

## PORT 상태와 플래그 비트

**PORT 상태**
- CLOSED: 포트가 닫힌 상태
- LISTEN: 포트가 열린 상태로 연결 요청 대기 중
- SYN_RCV: SYNC 요청을 받고 상대방의 응답을 기다리는 중
- ESTABLISHED: 포트 연결 상태

**TCP HEADER 안의 플래그**
- TCP HEADER에는 6bit 크기의 플래그 비트(CONTROL BIT) 가 존재하며, 각각의 비트는 URG-ACK-PSH-SYN-FIN를 의미한다.
- SYN은 Sequence Number를 무작위로 설정하여 세션을 연결하는 데 사용한다.
- ACK는 패킷을 받았다는 사실을 표시하는 데 사용된다.
- FIN은 세션 연결을 종료시킬 때 사용되며, 더 이상 전송할 데이터가 없음을 의미한다.

## 3-way handshake

프로세스 A(Client)가 프로세스 B(Server)에게 연결을 요청하는 상황을 가정해 보자.  

![image](https://user-images.githubusercontent.com/28294925/138667854-dac3bf4f-7d5e-49e2-80de-de4ec0cc5168.png)

1. A가 B에게 연결 요청 메시지(SYN)을 전송한다. 송신자가 최초로 데이터를 전송할 때, Sequence Number를 임의의 숫자로 지정하고, SYN 플래그 비트를 1로 설정한 세그먼트를 전송한다. (PORT 상태 - A:CLOSED, B:LISTEN)
2. B가 A에게 요청 수락 메시지(ACK) 와 함께, 연결 요청 메시지(SYN)를 전송한다. 수신자는 Acknowledgement Number를 Sequence Number+1로 지정하고, SYN과 ACK 플래그 비트를 1로 설정한 세그먼트를 전송한다. (PORT 상태 - A:CLOSED, B:SYN_RCV)
3. (PORT 상태 - A: ESTABLISHED, B:SYN_RCV) A가 B에게 요청 수락 메시지(ACK)를 보내 연결을 맺는다. 이후 원하는 데이터를 전송할 수 있다. (PORT 상태 - A:ESTABLISHED, B:ESTABLISHED)

그런데 왜 2-way나 4-way가 아니라 3-way일까? 그 이유는, Client에서 Server로 데이터를 전송할 수 있으며, 반대로 Server에서 Client로 데이터를 전송할 수 있음을 확인하려면 최소한 세 번의 전송이 있어야 하기 때문이다. 

화상 인터뷰에서 면접자와 면접관이 서로의 마이크와 스피커를 점검하는 상황을 생각해 보자. 먼저 면접자가 면접관에게 말을 건다(1). 면접관이 대답한다면(2), 면접자의 마이크와 면접관의 스피커에는 문제가 없다는 사실이 확인된다. 그러나 아직 면접관의 마이크와 면접자의 스피커가 제대로 작동하는지는 알 수 없다. 면접관의 대답을 들은 면접자가 다시 한 번 대답해야만(3), 면접관의 마이크와 면접자의 스피커에도 문제가 없다는 사실이 확인된다. 

그럼 왜 Sequence Number를 0이 아닌 임의의 숫자로 지정하는 것일까? Connection을 맺을 때 사용하는 포트 번호는 유한하며, 시간이 지남에 따라 재사용된다. 따라서 두 통신 호스트가 과거에 사용했던 포트 번호 쌍을 재활용하는 상황도 나올 수 있다. 서버 측에서는 패킷의 SYN을 보고 패킷을 구분하게 되는데, 난수가 아닌 순차적인 number가 전송된다면 이전의 Connection에서 온 패킷으로 착각할 수 있다. 임의의 숫자를 사용하는 이유는 이런 가능성을 줄이기 위해서이다. 

## 4-way handshake

4-way handshake는 설정한 연결을 해제할 때 사용된다. 프로세스 A(Client)가 프로세스 B(Server)에게 연결 해제를 요청하는 상황을 가정해 보자. 

![image](https://user-images.githubusercontent.com/28294925/138667898-bbf593e7-44eb-4400-b453-6c44ac3a732d.png)

$$$사진 중간 data(5) 더하는 부분??

1. A가 B에게 연결 종료를 의미하는 FIN 플래그를 전송한다. (PORT 상태 - A:FIN_WAIT)
2. B는 요청 수락 메시지(ACK)를 보내고, 자신의 통신이 끝날 때까지 기다린다. (PORT 상태 - B:CLOSE_WAIT)
3. B가 통신이 끝났다면, 연결이 종료되었다는 표시로 A에게 FIN 플래그를 전송한다. (PORT 상태 - B:LAST_ACK)
4. 클라이언트는 확인 메시지(ACK)를 보낸다. (PORT 상태 - A:TIME_WAIT)

이 때, B(Server)에서 FIN을 전송하기 전에 전송한 패킷이 Routing 지연, 패킷 유실로 인한 재전송 등으로 FIN보다 늦게 도착하는 상황이 벌어질 수 있다. 이런 패킷들이 유실되는 걸 막기 위해, A에서는 FIN을 수신하더라도 일정 시간(디폴트 240초)동안 세션을 남겨 놓고 잉여 패킷을 기다리며, 이 과정을 TIME_WAIT라고 한다. TIME_WAIT가 끝나면 비로소 세션이 만료되고 연결이 완전히 종료되며, PORT 상태가 CLOSE로 바뀐다. 

참고: 
- https://gmlwjd9405.github.io/2018/09/19/tcp-connection.html
- https://asfirstalways.tistory.com/356
- https://bangu4.tistory.com/74