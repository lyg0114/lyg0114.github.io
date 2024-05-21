---
title: "그라파나,프로메테우스,도커 활용 모니터링 시스템구축"
date: 2024-03-07 16:40:00 +09:00
categories: [ 인프라 ]
tags:
  [
    Docker, install, prometheus, grafana
  ]
---

그라파나와 프로메테우스를 각가 도커로 만들어 하나의 서버에 실행하는 방법에 대해 알아 보자.

### 1. 도커 네트워크 설정

그라파나는 프로메테우스의 데이터를 가져와 대시보드로 만들어 준다. 그라파나, 프로메테우스가 각각 다른 서버에서 구동되고 있다면 간단하게 그라파나에 프로메테우스의 서버경로를 설정해 주면 된다.

하지만 나는 프로메테우스, 그라파나를 각가 도커로 패키징 하여 하나의 서버에서 구동시킬 예정이다. 이때 발생할 문제는 그라파나에서 아무리 localhost로 프로메테우스에 연결을 시도 하여도 두프로그램은 서로 다른 컨테이너에서 실행되기 때문에 접속 되지 않는다.

이때 필요한것이 [docker network](https://docs.docker.com/network/) 이다. 두 컨테이너간의 네트워크 연결을 가능하게 해주는 것인데 이 설정을 통해 두개의 컨테이너간에 네트워크 연결이 가능해 진다.

```docker
docker network create monitoring-net
```

위의 명령어로 네트워크가 하나 생성 되었다 앞으로 위의 네트워크를 활용해서 도커간에 통신이 가능하도록 할 것이다.

```bash

docker run \
       -d \
       --network=monitoring-net \ # 생성된 네트워크를 사용한다.
       -p 4000:3000 \
       --name=grafana grafana/grafana-enterprise
```

### 2. 스토리지 설정

스토리지 설정을 하지 않으면 도커가 종료될때 실행 중 만들어진 모든 데이터는 사라진다. 이런 상황을 방지하고자 저장해야할 데이터가 있다면 데이터를 호스트에 저장하는 방식을 사용한다.

```bash
docker volume create grafana-storage # 그라파나 스토리지 생성
```

위의 명령어로 grafana-storage 하나 생성 되었다 앞으로 그라파나 실행중 발생하는 데이터는 모두 호스트의 grafana-storage 에 저장될 것이다.

```bash
docker run \
       -d \
       --network=monitoring-net \
       -p 4000:3000 \
       -v grafana-storage:/var/lib/grafana \ # 스토리지 연결
       --name=grafana grafana/grafana-enterprise
```

storage를 생성하지 않고 직접 경로를 지정할 수 도 있다. (권장하지는 않음)

```bash
mkdir data # 데이터를 저장할 디렉토리 생성
docker run \
  -d \
  -p 4000:3000 \
  --network=monitoring-net \
  --volume "$PWD/data:/var/lib/grafana" \ # 직접 경로 설정
  --name grafana grafana/grafana-enterprise
```

### 3. 실행

전체 스크립트는 아래와 같다. 프로메테우스의 호스트 설정파일을 변경해주고 사용하면 된다.

```bash
docker network create monitoring-net # 도커 네트워크 설정

docker volume create prometheus-storage # 프로메테우스에서 발생하는 데이터 저장을 위한 호스트 저장소 생성
docker run \
	     -d \ # 백그라운드 실행
	     --network=monitoring-net \ # 도커 네트워크
	     -p 9090:9090 \ # 포트설정
	     -v /host/path/prometheus.yml:/etc/prometheus/prometheus.yml \ # 프로메테우스 설정파일 
	     -v prometheus-storage:/prometheus \  # 스토리지 설정
	     --name=prometheus prom/prometheus # name 설정
	     
	     
	     
docker volume create grafana-storage # 그라파나에서 발생하는 데이터 저장을 위한 호스트 저장소 생성
docker run \
       -d \ # 백그라운드 실행
       --network=monitoring-net \ # 도커 네트워크
       -p 4000:3000 \ # 포트설정
       -v grafana-storage:/var/lib/grafana \ # 스토리지 설정
       --name=grafana grafana/grafana-enterprise # name 설정

```

### 4. 그라파나 프로메테우스 연동

1. 그라파나에 접속 및 로그인
2. Add your first data source  클릭
3. Prometheus 클릭
4. Connection
  - 이부분에 http://<프로메테우스_컨테이너_IP>:9090
  - 또는  http://<프로메테우스_컨테이너_이름>:9090  적는다.

### 참조
[Installation | Prometheus](https://prometheus.io/docs/prometheus/latest/installation/) <br>
[Run Grafana Docker image |  Grafana documentation](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/)
