# cnabookstore
도서 조회/주문/배송, 고객 등록, 결제 시스템

# 체크 포인트
- 분석 설계
 - 이벤트스토밍:
    - 스티커 색상별 객체의 의미를 제대로 이해하여 헥사고날 아키텍처와의 연계 설계에 적절히 반영하고 있는가?
    - 각 도메인 이벤트가 의미있는 수준으로 정의되었는가?
    - 어그리게잇: Command와 Event 들을 ACID 트랜잭션 단위의 Aggregate 로 제대로 묶었는가?
    - 기능적 요구사항과 비기능적 요구사항을 누락 없이 반영하였는가?
 - 서브 도메인, 바운디드 컨텍스트 분리
    - 팀별 KPI 와 관심사, 상이한 배포주기 등에 따른  Sub-domain 이나 Bounded Context 를 적절히 분리하였고 그 분리 기준의 합리성이 충분히 설명되는가?
    - 적어도 3개 이상 서비스 분리
    - 폴리글랏 설계: 각 마이크로 서비스들의 구현 목표와 기능 특성에 따른 각자의 기술 Stack 과 저장소 구조를 다양하게 채택하여 설계하였는가?
    - 서비스 시나리오 중 ACID 트랜잭션이 크리티컬한 Use 케이스에 대하여 무리하게 서비스가 과다하게 조밀히 분리되지 않았는가?
 - 컨텍스트 매핑 / 이벤트 드리븐 아키텍처
   - 업무 중요성과  도메인간 서열을 구분할 수 있는가? (Core, Supporting, General Domain)
   - Request-Response 방식과 이벤트 드리븐 방식을 구분하여 설계할 수 있는가?
   - 장애격리: 서포팅 서비스를 제거 하여도 기존 서비스에 영향이 없도록 설계하였는가?
   - 신규 서비스를 추가 하였을때 기존 서비스의 데이터베이스에 영향이 없도록 설계(열려있는 아키택처)할 수 있는가?
   - 이벤트와 폴리시를 연결하기 위한 Correlation-key 연결을 제대로 설계하였는가?
 - 헥사고날 아키텍처
   - 설계 결과에 따른 헥사고날 아키텍처 다이어그램을 제대로 그렸는가?
   
- 구현
 - [DDD] 분석단계에서의 스티커별 색상과 헥사고날 아키텍처에 따라 구현체가 매핑되게 개발되었는가?
   - Entity Pattern 과 Repository Pattern 을 적용하여 JPA 를 통하여 데이터 접근 어댑터를 개발하였는가
   - [헥사고날 아키텍처] REST Inbound adaptor 이외에 gRPC 등의 Inbound Adaptor 를 추가함에 있어서 도메인 모델의 손상을 주지 않고 새로운 프로토콜에 기존 구현체를 적응시킬 수 있는가?
   - 분석단계에서의 유비쿼터스 랭귀지 (업무현장에서 쓰는 용어) 를 사용하여 소스코드가 서술되었는가?
 - Request-Response 방식의 서비스 중심 아키텍처 구현
   - 마이크로 서비스간 Request-Response 호출에 있어 대상 서비스를 어떠한 방식으로 찾아서 호출 하였는가? (Service Discovery, REST, FeignClient)
   - 서킷브레이커를 통하여  장애를 격리시킬 수 있는가?
 - 이벤트 드리븐 아키텍처의 구현
   - 카프카를 이용하여 PubSub 으로 하나 이상의 서비스가 연동되었는가?
   - Correlation-key: 각 이벤트 건 (메시지)가 어떠한 폴리시를 처리할때 어떤 건에 연결된 처리건인지를 구별하기 위한 Correlation-key 연결을 제대로 구현 하였는가?
   - Message Consumer 마이크로서비스가 장애상황에서 수신받지 못했던 기존 이벤트들을 다시 수신받아 처리하는가?
   - Scaling-out: Message Consumer 마이크로서비스의 Replica 를 추가했을때 중복없이 이벤트를 수신할 수 있는가
   - CQRS: Materialized View 를 구현하여, 타 마이크로서비스의 데이터 원본에 접근없이(Composite 서비스나 조인SQL 등 없이) 도 내 서비스의 화면 구성과 잦은 조회가 가능한가?
 - 폴리글랏 플로그래밍
   - 각 마이크로 서비스들이 하나이상의 각자의 기술 Stack 으로 구성되었는가?
   - 각 마이크로 서비스들이 각자의 저장소 구조를 자율적으로 채택하고 각자의 저장소 유형 (RDB, NoSQL, File System 등)을 선택하여 구현하였는가?
 - API 게이트웨이
   - API GW를 통하여 마이크로 서비스들의 집입점을 통일할 수 있는가?
   - 게이트웨이와 인증서버(OAuth), JWT 토큰 인증을 통하여 마이크로서비스들을 보호할 수 있는가?
   
- 운영
 - SLA 준수
   - 셀프힐링: Liveness Probe 를 통하여 어떠한 서비스의 health 상태가 지속적으로 저하됨에 따라 어떠한 임계치에서 pod 가 재생되는 것을 증명할 수 있는가?
   - 서킷브레이커, 레이트리밋 등을 통한 장애격리와 성능효율을 높힐 수 있는가?
   - 오토스케일러 (HPA) 를 설정하여 확장적 운영이 가능한가?
   - 모니터링, 앨럿팅:
 - 무정지 운영 CI/CD (10)
   - Readiness Probe 의 설정과 Rolling update을 통하여 신규 버전이 완전히 서비스를 받을 수 있는 상태일때 신규버전의 서비스로 전환됨을 siege 등으로 증명
   - Contract Test : 자동화된 경계 테스트를 통하여 구현 오류나 API 계약위반를 미리 차단 가능한가?

# 서비스 시나리오
```
* 북 관리자는 판매하는 책의 종류와 보유 수량을 생성하고 수정할 수 있다.
* 고객은 책의 주문과 취소가 가능하며 주문 정보의 수정은 없다.
* 고객이 주문을 생성할 때 Book 정보가 있어야 한다. 
  - Order -> BookInventory 동기호출
* 주문 시에 재고가 없더라도 주문이 가능하다.
  - 주문 상태는 “Ordered”
* 주문 취소는 "Ordered" 상태일 경우만 가능하다.
* 배송준비는 주문 정보를 받아 이뤄지며 재고가 부족한 경우, 책 입고가 이뤄져서 재고 수량이 충분한 경우에 배송 준비가 완료되었음을 알린다.
* 배송은 주문 생성 정보를 받아서 배송을 준비하고 주문 상품 준비 정보를 받아서 배송을 시작하며 배송이 시작되었음을 주문에도 알린다.
  - 주문 생성 시 배송 생성
  - 상품 준비 시 배송 시작  
* 배송을 시행하는 외부 시스템(물류 회사 시스템) 또는 배송 담당자는 배송 단계별로 상태는 변경한다. 변경된 배송 상태는 주문에 알려 반영한다.
* 쿠폰 관리자는 고객 별 쿠폰을 발행할 수 있다
* 고객이 주문을 생성 시 쿠폰 적용하면 쿠폰 정보가 있어여 한다
  - couponId 존재 시 Order -> Coupon 동기호출(Req/Res)
  - 주문 완료 되면 쿠폰 상태는 "Used(Not Available)"로 변경 된다(Pub/Sub)
  - 주문 취소 되면 쿠폰 상태는 "Refund(Available)"로 변경 된다(Pub/Sub)
* 쿠폰 발행/사용 이력은 myCoupon에서 조회할 수 있다 
```

# 이벤트 스토밍 & CNA 모델
```
주문 : https://github.com/crossorbit/bs-order.git
도서 : https://github.com/crossorbit/bs-bookinventory.git
게이트웨이 : https://github.com/crossorbit/bs-gateway.git
쿠폰 : https://github.com/crossorbit/bs-coupon.git
마이쿠폰 :  https://github.com/crossorbit/bs-mycoupon.git

```
```
![Alt text](CNA-model.PNG?raw=true "Optional Title")
```

# 구현점검
### 모든 서비스 정상 기동 
```
* Httpie Pod 접속
kubectl exec -it httpie -- bash

* API 
http http://gateway:8080/customers
http http://gateway:8080/myPages
http http://gateway:8080/books
http http://gateway:8080/deliverables
http http://gateway:8080/stockInputs
http http://gateway:8080/orders
http http://gateway:8080/deliveries
http http://gateway:8080/coupons
http http://gateway:8080/mycoupons
```

### Kafka 기동 및 모니터링 용 Consumer 연결
```
kubectl -n kafka exec -ti my-kafka-0 -- /usr/bin/kafka-console-consumer --bootstrap-server my-kafka:9092 --topic cnabookstore --from-beginning
```

## API 테스트
```
 1) Book
	(도서등록) http POST http://gateway:8080/books bookName=LondonTour stock=100
	(재고추가) http POST http://gateway:8080/stockInputs bookId=1 quantity=10 inCharger=CHOI
	(도서조회) http http://gateway:8080/books
	(배송대상조회) http http://gateway:8080/deliverables
	(재고조회) http http://gateway:8080/stockInputs
 2) Coupon
	(쿠폰등록) http POST http://gateway:8080 couponStatus=Registered customerId=1 
	(쿠폰조회) http http://gateway:8080/coupons/1

 3) Order 
	(쿠폰미사용) http POST http://gateway:8080/orders customerId=1 bookId=1 quantity=10 deliveryAddress=Seoul
	(쿠폰사용) http POST http://gateway:8080/orders customerId=2 bookId=1 quantity=200 deliveryAddress=Wien couponId=2
	(쿠폰사용) http POST http://gateway:8080/orders customerId=2 bookId=1 quantity=200 deliveryAddress=Wien couponId=1
	(주문취소 쿠폰사용) http DELETE http://gateway:8080/orders/2
	(주문조회) http http://gateway:8080/orders/2
	
 4) myCoupon
	(이력조회) http http://gateway:8080/mycoupons/1
```

## CQRS - myCoupon 로직
```
```

## Req-Res
```
```

## Pub-Sub
```
```

## Gateway
```
```

## CI/CD
```
```

## Circuit Breaker
```
```


## Autoscale 점검
### 설정 확인
```
application.yaml 파일 설정 변경
(https://k8s.io/examples/application/php-apache.yaml 파일 참고)
 resources:
  limits:
    cpu: 500m
  requests:
    cpu: 200m
```
### 점검 순서
```
1. HPA 생성 및 설정
	kubectl autoscale deploy coupon --min=1 --max=10 --cpu-percent=50
	kubectl get hpa coupon -o yaml
2. 모니터링 걸어놓고 확인
	kubectl get hpa coupon -w
3. Siege 실행
  siege -c10 -t60S -v http://gateway:8080/coupons/
```
### 점검 결과
![Alt text](HPA_test.PNG?raw=true "Optional Title")

## Readiness Probe 점검
### 설정 확인
```
readinessProbe:
  httpGet:
    path: '/orders'
    port: 8080
  initialDelaySeconds: 12
  timeoutSeconds: 2
  periodSeconds: 5
  failureThreshold: 10
```
### 점검 순서
#### 1. Siege 실행
```
siege -c2 -t120S  -v 'http://gateway:8080/orders
```
#### 2. Readiness 설정 제거 후 배포
#### 3. Siege 결과 Availability 확인(100% 미만)
```
Lifting the server siege...      done.

Transactions:                    330 hits
Availability:                  70.82 %
Elapsed time:                 119.92 secs
Data transferred:               0.13 MB
Response time:                  0.02 secs
Transaction rate:               2.75 trans/sec
Throughput:                     0.00 MB/sec
Concurrency:                    0.07
Successful transactions:         330
Failed transactions:             136
Longest transaction:            1.75
Shortest transaction:           0.00
```
#### 4. Readiness 설정 추가 후 배포

#### 6. Siege 결과 Availability 확인(100%)
```
Lifting the server siege...      done.

Transactions:                    443 hits
Availability:                 100.00 %
Elapsed time:                 119.60 secs
Data transferred:               0.51 MB
Response time:                  0.01 secs
Transaction rate:               3.70 trans/sec
Throughput:                     0.00 MB/sec
Concurrency:                    0.04
Successful transactions:         443
Failed transactions:               0
Longest transaction:            0.18
Shortest transaction:           0.00
 
FILE: /var/log/siege.log
```

## Liveness Probe 점검
### 설정 확인
```
livenessProbe:
  httpGet:
    path: '/isHealthy'
    port: 8080
  initialDelaySeconds: 120
  timeoutSeconds: 2
  periodSeconds: 5
  failureThreshold: 5
```
### 점검 순서 및 결과
#### 1. 기동 확인
```
http http://gateway:8080/orders
```
#### 2. 상태 확인
```
oot@httpie:/# http http://order:8080/isHealthy
HTTP/1.1 200 
Content-Length: 0
Date: Wed, 09 Sep 2020 02:14:22 GMT
```

#### 3. 상태 변경
```
root@httpie:/# http http://order:8080/makeZombie
HTTP/1.1 200 
Content-Length: 0
Date: Wed, 09 Sep 2020 02:14:24 GMT
```
#### 4. 상태 확인
```
root@httpie:/# http http://order:8080/isHealthy
HTTP/1.1 500 
Connection: close
Content-Type: application/json;charset=UTF-8
Date: Wed, 09 Sep 2020 02:14:28 GMT
Transfer-Encoding: chunked

{
    "error": "Internal Server Error", 
    "message": "zombie.....", 
    "path": "/isHealthy", 
    "status": 500, 
    "timestamp": "2020-09-09T02:14:28.338+0000"
}
```
#### 5. Pod 재기동 확인
```
root@httpie:/# http http://order:8080/isHealthy
http: error: ConnectionError: HTTPConnectionPool(host='order', port=8080): Max retries exceeded with url: /makeZombie (Caused by NewConnectionError('<requests.packages.urllib3.connection.HTTPConnection object at 0x7f5196111c50>: Failed to establish a new connection: [Errno 111] Connection refused',))

root@httpie:/# http http://order:8080/isHealthy
HTTP/1.1 200 
Content-Length: 0
Date: Wed, 09 Sep 2020 02:36:00 GMT
```
