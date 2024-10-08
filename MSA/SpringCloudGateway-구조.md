# 목차
1. 사용 기술
2. E-Commerce 프로젝트 구조

## 1. 사용 기술
- Eureka (Service Discovery, Registration) 
  - 마이크로서비스 등록 및 검색
- Spring Cloud Gateway (API Gateway)
  - 마이크로서비스 부하 분산 및 서비스 라우팅
  - Round Robin 방식으로 Load balancing
- Spring Security (Security)
  - 인증, 인가 구현 
- Kafka (Message Queue)
  - 마이크로서비스 간 메세지 발행 및 구독
- Spring Cloud Config (Config Server)
  - Git 저장소에 등록된 프로파일 정보 및 설정 정보 
- Spring Cloud Bus
  - 분산 시스템의 노드를 경량 메시지 브로커와 연결 (ex : RabbitMQ, Kafka)
  - Config 설정이 바뀌면 자동으로 모든 서버에서 적용이 되도록 일종의 미들웨어 역할을 해줌
![Gateway와 Eureka의 관계](../images/gateway-and-eukrea.png)

## 2. E-Commerce 프로젝트 구조
![E-Commerce 구조](../images/e-commerge.png)
![사용 기술](../images/e-commerce-tech.png)
![실제 기업에서 사용 기술](../images/img_3.png)

### API Gateway
```yml
server:
  port: 8000
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:8761/eureka
spring:
  application:
    name: api-gateway-service
  cloud:
    gateway:
      default-filters:
        - name: GlobalFilter
          args:
            baseMessage: Spring Cloud Gateway GlobalFilter
            preLogger: true
            postLogger: true
      routes:
        - id: user-service
          uri: lb://USER-SERVICE
          predicates:
            - Path=/user-service/**
```

### Eureka(Service Discovery)
- 해당 서버는 서비스 디스커버리 역할을 위해 상시 켜져있어야 함
```yml
server:
  port: 8761

spring:
  application:
    name: discoveryservice
eureka:
  client:
    register-with-eureka: false
    fetch-registry: false

```

- 이 외에 서비스가 등록되는 프로젝트는 아래와 같이 `application.yml` 작성
```yml
server:
  port: 0   # 랜덤 포트

spring:
  application:
    name: user-service

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka
  instance:
    instance-id: ${spring.application.name}:${spring.application.instance_id:${random.value}}
```

