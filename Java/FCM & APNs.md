# FCM & APNs

## 소개

- FCM의 주요 기능
  1. 알림 메시지 또는 데이터 메시지를 보낼 수 있다
     - 알림 메시지 : FCM SDK가 클라이언트 앱을 대신하여 앱푸시를 표시해준다
     - 데이터 메시지 : 커스텀 키-값 쌍만 있고, 그 값으로 클라이언트 앱이 처리한다
  2. 수신자를 3가지 방식으로 지정할 수 있다 (단일 기기에만, 기기 그룹에게, 토픽을 구독한 기기에게 모두)
  3. 클라이언트 앱에서 메시지를 전송할 수 있다
- FCM 구현에는 다음 2가지가 필요하다
  1. 메시지를 작성, 타겟팅, 전송할 수 있는 신뢰할 수 있는 환경(Firebase용 Cloud Functions 또는 앱서버 등..)
  2. 클라이언트 앱(Apple, Android, 웹 등..)
- 메시지를 보내는 방식 (택1)
  1. Firebase Admin SDK
  2. FCM 서버 프로토콜
- 구현 순서
  1. FCM SDK 설정 (앱에서 Firebase 및 FCM을 설정한다)
  2. 클라이언트 앱 개발 (메시지 처리, 토픽 구독 로직 등을 구현한다. 개발 중에는 알림 작성기에서 테스트 메시지를 보낼 수 있음)
  3. 앱 서버 개발
     - 인증, 앱푸시 요청, 응답 처리등을 구현할 때 사용할 방식 결정해야 한다 (Firebase Admin SDK or FCM 서버 프로토콜)
     - 신뢰할 수 있는 환경에서 로직을 구축한다
     - 참고 : 클라이언트 애플리케이션에서 업스트림 메시징을 사용하려면 XMPP를 사용해야 함. 또한 Cloud Functions는 XMPP에 필요한 영구적 연결을 지원하지 않음

## FCM 아키텍쳐 개요

![이 페이지에 설명된 세 가지 아키텍처 레이어의 다이어그램](https://firebase.google.com/docs/cloud-messaging/images/diagram-FCM.png?hl=ko)

1. 메시지 생성과 타게팅
   - 메시지 작성, 구현하는 도구
   - 모든 메시지 유형(알림 메시지, 데이터 메시지)을 자동화하고 지원하려면 신뢰할 수 있는 서버 환경에서 메시지 요청을 해야 함
2. FCM 백엔드
   - 메시지 요청 수락
   - 토픽을 통해 메시지 확장
   - 메시지 메타데이터(메시지 id ..) 생성 등
3. 플랫폼 수준의 메시지 전송
   - 메시지 라우팅하여 전송
   - 전송 레이어
     - ATL (Android)
     - APN (Apple)
     - 웹 앱용 웹 푸시 프로토콜
4. 사용자 기기의 FCM SDK
   - 앱의 상태 또는 로직에 따라 알림 표시

- 수명 주기 흐름

  - FCM에서 메시지를 수신하도록 기기를 등록한다. 클라이언트 앱의 인스턴스가 메시지를 수신하도록 등록하여 앱 인스턴스를 고유하게 식별하는 등록 토큰을 받는다.

  - 다운스트림 메시지 전송 및 수신
    - 메시지 보내기 앱 서버가 클라이언트 앱에 메시지를 보낸다.
      1. 메시지는 알림 작성기 또는 신뢰할 수 있는 환경에서 작성되며 메시지 요청이 FCM 백엔드로 전송됨
      2. FCM 백엔드는 메시지 요청을 수신하고 메시지 ID와 기타 메타데이터를 생성하여 플랫폼별 전송 레이어로 보냄
      3. 기기가 온라인 상태이면 메시지가 플랫폼별 전송 레이어를 통해 기기로 전송됨
      4. 기기에서 클라이언트 앱이 메시지 또는 알림을 수신함

## FCM 메시지 정보

- FCM을 통해 2가지 유형의 메시지를 보낼 수 있다

  1. 알림 메시지(= 표시 메시지)
     - FCM SDK에서 자동으로 처리한다
     - 키 모음이 사전에 정의되어 있다.
     - 데이터 페이로드가 포함될 수 있다 (선택사항)
     - 최대 페이로드는 4000바이트
     - `notification` 키를 설정하여 전송할 수 있다
  2. 데이터 메시지
     - 클라이언트 앱에서 처리한다
     - 사용자가 정의한 커스텀 키-값 쌍만 포함된다
     - 최대 페이로드는 4000바이트
     - `data` 키를 설정하여 전송할 수 있다

- 알림 메시지

  - ```json
    {
      "message":{
        "token":"bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",
        "notification":{
          "title":"Portugal vs. Denmark",
          "body":"great match!"
        }
      }
    }
    ```

  - `notification` 키 안에 사전 정의된 키 옵션 모음(`title`, `body` 등)으로 설정한다

    - 사전 정의된 키 전체 목록 : [HTTP v1](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages?authuser=1#Notification), [기존 HTTP](https://firebase.google.com/docs/cloud-messaging/http-server-ref?authuser=1#notification-payload-support), [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref?authuser=1#notification-payload-support)

  - 앱이 백그라운드 상태이면 알림 메시지가 알림 목록으로 전송된다

  - 앱이 포그라운드 상태이면 콜백 함수가 메시지를 처리한다

  