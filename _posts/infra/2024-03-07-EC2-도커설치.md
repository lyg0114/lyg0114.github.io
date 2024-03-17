---
title: "EC2 에 도커를 설치해 보자"
date: 2024-03-07 16:40:00 +09:00
categories: [ 인프라 ]
tags:
  [
    Docker, Aws, EC2
  ]
---
1. EC2 접속
2. yum 최신화

```bash
sudo yum update
```

1. 도커 설치

```bash
sudo yum install docker
```

1. ec2-user 에 권한 추가

```bash
sudo usermod -a -G docker ec2-user
id ec2-user
```

1. ec2 재 부팅시 도커 자동실행 설정

```bash
sudo systemctl enable docker.service
```

1. 도커 실행

```bash
sudo systemctl start docker.service
```

### 참고

[install docker linux](https://www.cyberciti.biz/faq/how-to-install-docker-on-amazon-linux-2/)
