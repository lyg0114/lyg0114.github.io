---
title: "데이터 크기의 관점에서 바라본 OSI 7"
date: 2024-04-24 08:40:00 +09:00
categories: [ cs ]
tags: [ cs, computer, os, network, tcp/ip, osi 7 layer, 네트워크 ]
---

## OSI 7계층

| 계층                                       | 설명                                                                  | 식별자     |
|------------------------------------------|---------------------------------------------------------------------|---------|
| **LAYER 7. 응용 계층 (Application Layer)**   | HTTP,FTP,SMTP                                                       |         |
| **LAYER 6. 표현 계층 (Presentation Layer)**  | -                                                                   |         |
| **LAYER 5. 세션 계층 (Session Layer)**       | TLS, SSL                                                            |         |
| **LAYER 4. 전송 계층 (Transport Layer)**     | TCP(Transmission Control Protocol) <br/> UDP(User Datagram Protocol | Port 번호 |
| **LAYER 3. 네트워크 계층 (Network Layer)**     | Internet(유통되는 단위 : 패킷)                                              | IP 주소   |
| **LAYER 2. 데이터 링크 계층 (Data Link Layer)** | Ethernet(유통되는 단위 : 프레임)                                             | MAC 주소  |
| **LAYER 1. 물리 계층 (Physical Layer)**      | 케이블, 무선                                                             |

### LAYER 2 Ethernet

#### 데이터 단위 : Frame

- NIC (Network Interface Card), LAN(Local Area Network) 카드
  - MAC주소 : NIC 의 식별자 (48 bit)
  - 데이터 단위 : frame(10kb)

### LAYER 3 Internet

#### 데이터 단위 : Packet

- 인터넷에 유통되는 패킷의 ** 최대크기(MTU) ** : 1500byte (1.4KB)
- Header + Payloas 로 구성되어 있음.
  - Header 에 포함되는 내용 : src(출발지) -> dst(목적지)

#### IPV4
IP를 실별할 수 있는 주소 체계를 의미한다. 총 32bit로 이루어져 있다.

| 192       | 168       | 0         | 11        |
|-----------|-----------|-----------|-----------|
| 1100 0000 | 1010 1000 | 0000 0000 | 0000 1010 |

- Network id : 192.168.0
- Host id : 11

#### A 클래스
- **범위**: 0.0.0.0에서 127.255.255.255까지
- **첫 번째 1바이트 사용**: 네트워크 식별
- **나머지 3바이트**: 호스트 식별
- **네트워크 수**: 2^7 - 2 = 126개
  - 첫 번째 비트는 항상 0 (네트워크 ID 표시)
  - 마지막 네트워크는 예약되어 있으므로 사용할 수 없음

#### B 클래스
- **범위**: 128.0.0.0에서 191.255.255.255까지
- **첫 번째 2바이트 사용**: 네트워크 식별
- **나머지 2바이트**: 호스트 식별
- **네트워크 수**: 2^14 - 2 = 16,382개
  - 첫 번째 비트는 항상 10 (네트워크 ID 표시)

#### C 클래스
- **범위**: 192.0.0.0에서 223.255.255.255까지
- **첫 번째 3바이트 사용**: 네트워크 식별
- **나머지 1바이트**: 호스트 식별
- **네트워크 수**: 2^21 - 2 = 2,097,150개
  - 첫 번째 비트는 항상 110 (네트워크 ID 표시)

### LAYER 4 TCP / IP

#### 데이터 단위 : Segment

- **최대크기(MSS)** : 1460byte (1.4KB)

#### 데이터 단위 : Datagram (UDP)

### LAYER 4 ~ 7

#### 데이터 단위 : Stream

- **최대크기** : Stream형태의 데이터로 최종 크기는 프로세스에 의해 결정된다.

#### Segmentation

- 커다란 stream데이터를 한번에 전송할 수는 없다. 이것을 위해선 크기를 잘게 쪼개햐 한다. 즉 Segmentation이란 Segment 크기를 초과하는 데이터를 전송하기위해 stream데이터를 분할하는 과정을 말한다.
