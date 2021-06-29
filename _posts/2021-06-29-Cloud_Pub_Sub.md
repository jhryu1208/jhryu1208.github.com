---
layout: post
title:  "[GCP] Cloud Pub/Sub"
subtitle: "[GCP] Cloud Pub/Sub"
categories: gcp
tags: g-basic
comments: true
---
#### Contents
- [Cloud Pub/Sub이란?](#cloud-pubsub이란)
- [Pub/Sub의 구성](#pubsub의-구성)
- [Pub/Sub 메시지 흐름](#pubsub-메시지-흐름)
- [Pull(가져오기)/Push(내보내기) 구독 방법](#pullpush-구독-방법)
- [메시지 서비스의 성능 판단](#메시지-서비스의-성능-판단)
- [Pub/Sub 통합](#pubsub-통합)

본 포스팅은 GCP : Pub/Sub에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">Cloud Pub/Sub이란?</span>

- GCP의 **메시징 서비스**


> [ `메시징 서비스`란? ] <br>
> <br>
> 1) 정의 : 메시지들을 Queue에 넣고 차례대로 전달해주는 서비스  <br>
> <br>
> 2) 요청 메시지를 Queue에 쌓아놓고 처리할 수 있다. <br>
> 따라서, 응답을하는 서버가 다운되더라도  요청이 들어온 메시지들은 큐에 쌓여있기 때문에 서버가 살아나면 즉시 처리를 진행할 수 있다. <br>
> <br>
> 3) 부하 분산 처리에 유리하다. <br>
> 여러 대의 서버가 하나의 큐를 바라보도록 할 경우, 처리 데이터가 많아져도 서버는 자신의 처리량에 맞는 요청만 가져와서 처리할 수 있다.


- **서버리스 환경**이기 때문에 별도의 인스턴스가 필요 없다.
- GCP와 외부의 시스템을 빠르게 통합하는데 이용할 수 있다.

<br>

---

## <span style="color:navy">Pub/Sub의 구성</span>

- `Topic(주제)`
    - 메시지를 전송하는 이름이 지정된 리소스
    - 메시징 서비스에서 메시지가 쌓이는 Queue를 담당한다.
- `Subscription(구독)`
    - 특정 **Topic**의 메시지 수신 의향을 나타내는 이름이 지정된 항목
    - **Message**를 읽기 위해서는 **Subscription**채널을 설정해야만 한다.
    - **Subscription**은 하나의 **Topic**에 대해서 단일 혹은 여러개 생성할 수 있다.
- `Message(메시지)`
    - 서비스를 통해 이동하는 데이터
    - **Topic**에 쌓이는 데이터
- `Publisher(게시자)`
    - 특정 주제에 대한 메시지를 만들어 메시지 서비스를 전송하는 사람
    - 누가 Publisher가 될 수 있을까?
        - googleapis.com에 HTTPS를 요청할 수 있는 모든 애플리케이션
        - App Engine 앱
        - Coogle Compute Engine
        - 다른 타사 네트워크에서 호스팅되는 웹 서비스
        - 데스크톱
        - 휴대기기에 설치된 App
        - 브라우저
- `Subscriber(구독자)`
    - 지정한 **Subscription**에 대한 **Message**를 받는 사람

<br>

다음의 그림 자료에서는 **Publisher와 Subscriber의 관계**에 대해서 보여준다.

- `Publisher` 애플리케이션이 `Message`를 만들어 `Topic`으로 전송한다.
- `Subscriber` 애플리케이션은 `Topic`에 대한 `Subscription`을 만들어
`Topic`으로부터 `Message`를 수신한다.
- 다음과 같이 통신은 **일대다(fan-out), 다대일(fan-in), 다대다** 형태를 취할 수 있다.

![Untitled](https://user-images.githubusercontent.com/53929665/123814741-e646f680-d930-11eb-9d5b-4474f872960e.png)

<br>

---

## <span style="color:navy">Pub/Sub 메시지 흐름</span>

![Untitled 1](https://user-images.githubusercontent.com/53929665/123814736-e5ae6000-d930-11eb-8608-8d9dde20b89d.png)

1. `Publisher`애플리케이션은 Pub/Sub서비스에서 `Topic`을 생성하고, `Message`를 `Topic`으로 전달한다.

2.  `Message`는 **Cloud Pub/Sub의 Message Storge**에  `Subscriber`가  확인할 때까지 **보존**된다.

3. **Cloud Pub/Sub**은 `Message`를 `Topic`에서 개별 `Subscription`으로 전달한다.
    - 각각 구독은 구독자가 선택한 곳으로 push /pull된 메시지를 전송한다.
4. "`Subscriber`가 지정한 endpoint"로 **Pub/Sub**이 내보낸 메시지를 `Subscriber`가 받는다.

5. `Subscriber`는 수신된 각  `Message`에 대해서 acknowledgement를 **Pub/Sub**으로 보낸다. 

6. Pub/Sub서비스는 `Subscription`의 메시지 Queue에서 `Message`를 삭제한다.

<br>

---

## <span style="color:navy">Pull/Push 구독 방법<span>

### **Pull(가져오기)**

- `Subscriber`가 `Message`를 요청할 때 전달받는 구독 방식

    ![KakaoTalk_20210629_000101271](https://user-images.githubusercontent.com/53929665/123814724-e3e49c80-d930-11eb-94ad-0caaadec05b8.jpg)



### Push(내보내기)

- `Message`가 오면 바로 Subscriber에게 전달되는 방식
    ![KakaoTalk_20210629_000113950](https://user-images.githubusercontent.com/53929665/123814733-e515c980-d930-11eb-821e-2c4c253af197.jpg)

<br>

---

## <span style="color:navy">메시지 서비스의 성능 판단<span>

####  `확장성`
   - 지연 시간이나 가용성의 저하 없이 점점 증가하는 로드를 처리할 수 있어야 한다.
   - 판단 기준
       - 주제 수 / 게시자 수 / 구독 수 / 구독자 수 / 메시지 수&크기&처리량 등

#### `가용성`
 - 다양한 유형의 문제를 얼마나 잘 처리하는가

#### `지연 시간`
 - 시스템 성능을 시간 기준으로 측정한 것이며,  해당 시간을 최소화하고자함
 - 판단 기준
     - 게시된 메시지를 확인하는데 걸리는 시간
     - 게시된 메시지가 구독자에게 전달되는데 걸리는 시간

<br>

---

##  <span style="color:navy">Pub/Sub 통합<span>

이러한 Pub/Sub은 Google Cloud의 많은 구성요소과 통합할 수 있다...!

![Untitled 2](https://user-images.githubusercontent.com/53929665/123814739-e5ae6000-d930-11eb-9223-1ec09af48464.png)

<br>

---

### References

- [GCP : Pub/Sub](https://cloud.google.com/pubsub/docs/overview)
- 박정운, 『구글 클라우드 플랫폼 뽀개기』, 비제이퍼블릭(BJ퍼블릭)(2019)

