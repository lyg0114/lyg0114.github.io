---
title: "로컬환경에서 모니터링시스템을 구성해 보자"
date: 2024-03-05 16:40:00 +09:00
categories: [ 인프라 ]
tags:
  [
    Brew, install, prometheus, grafana
  ]
---

## 스프링 부트 actuator, prometheus  설정  파일 추가

```groovy
dependencies {
    // Spring Boot Starter Actuator
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    
    // Spring Boot Starter Web
    implementation 'org.springframework.boot:spring-boot-starter-web'
    
    // Micrometer Prometheus Registry (Runtime Scope)
    runtimeOnly 'io.micrometer:micrometer-registry-prometheus'
}
```

## 프로메테우스 설치

1. brew 를 통한 설치

```groovy
brew install prometheus
```

1. prometheus.yml 변경 [ /usr/local/etc/prometheus.yml ]

```yaml
global:
  scrape_interval: 15s

rule_files:

scrape_configs:
  - job_name: "application-name"
    metrics_path: '/actuator/prometheus' // spring-boot의 url
    scrape_interval: 3s
    static_configs:
      - targets: [ "localhost:3000" ] // spring-boot
```

1. 프로메테우스 접속
  1. http://localhost:9090/

## 그라파나 설치

1. brew를 통한 설치

```shell
brew install grafana
```

1. port 변경 [ /usr/local/etc/grafana/grafana.ini ]
2. spring 프로젝트의 경로와 겹쳐서 변경함 Default : 3000 

```shell
http_port = 4000
```

http://localhost:4000 접속
 
1. DataSource(프로메테우스) 연결
2. Connection : http://localhost:9090 (프로메테우스 경로)
3. DashBoard 구성 :  [여기](https://grafana.com/grafana/dashboards/?search=j) 에서 사람들이 미리 만들어 놓은 DashBoard를 불러올 수 있다.

## 참고

[Prometheus - Monitoring system & time series database](https://prometheus.io/)
<br>
[grafana labs](https://grafana.com/)
<br>
[Monitoring Made Simple: Empowering Spring Boot Applications with Prometheus and Grafana](https://medium.com/simform-engineering/revolutionize-monitoring-empowering-spring-boot-applications-with-prometheus-and-grafana-e99c5c7248cf)



