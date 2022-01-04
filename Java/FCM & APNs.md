# FCM

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

- 데이터 메시지

  - ```json
    {
      "message":{
        "token":"bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",
        "data":{
          "Nick" : "Mario",
          "body" : "great match!",
          "Room" : "PortugalVSDenmark"
        }
      }
    }
    ```

  - 클라이언트 앱이 `data`를 해석한다

  - 커스텀 키-값 쌍에 예약어를 사용하면 안된다

    - `from`, `notification`, `message_type`
    - `google`이나 `gcm`으로 시작하는 모든 단어

  - Android 전송 레이어에서는 지점간 암호화를 사용한다

- 데이터 페이로드가 포함된 알림 메시지

  - ```json
    {
      "message":{
        "token":"bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",
        "notification":{
          "title":"Portugal vs. Denmark",
          "body":"great match!"
        },
        "data" : {
          "Nick" : "Mario",
          "Room" : "PortugalVSDenmark"
        }
      }
    }
    ```

  - 앱이 백그라운드 상태일때 : 알림 페이로드(`notification`)가 앱의 알림 목록에 수신되며 사용자가 알림을 탭한 경우에만 앱이 데이터 페이로드(`data`)를 처리한다.

  - 앱이 포그라운드 상태일때 : 앱에서 페이로드가 둘 다 제공되는 메시지 객체를 수신한다

- 여러 플랫폼의 메시지 맞춤설정

  - 공통 필드를 사용해야 하는 경우

    - 모든 플랫폼에 보내고 싶을때
    - 토픽으로 보내고 싶을때

  - 플랫폼별 필드를 사용해야 하는 경우

    - 특정 플랫폼에만 보내고 싶을때
      - 예: Apple과 Web으로만 보내고 싶을땐 Apple용, Web용으로 각각 1개씩 개별 필드 모음을 사용해야 함
    - 공통 필드 외에도 플랫폼 별 필드를 보내야 할 때
      - 여러 플랫폼에 동일한 값을 설정하는 경우에도 플랫폼별 필드를 사용해야 한다 (플랫폼 별로 다르게 해석할 수 있기 때문)

  - 플랫폼에 관계 없이 다음 공통 필드는 모든 앱 인스턴스가 해석할 수 있다

    - [`message.notification.title`](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages?authuser=1#Notification.FIELDS.title)
    - [`message.notification.body`](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages?authuser=1#Notification.FIELDS.body)
    - [`message.data`](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages?authuser=1#Message.FIELDS.data)

  - 예

    - ```json
      {
        "message":{
           "token":"bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",
           "notification":{
             "title":"Match update",
             "body":"Arsenal goal in added time, score is now 3-0"
           },
           "android":{
             "ttl":"86400s",
             "notification"{
               "click_action":"OPEN_ACTIVITY_1"
             }
           },
           "apns": {
             "headers": {
               "apns-priority": "5",
             },
             "payload": {
               "aps": {
                 "category": "NEW_MESSAGE_CATEGORY"
               }
             }
           },
           "webpush":{
             "headers":{
               "TTL":"86400"
             }
           }
         }
       }
      ```

- 전송 옵션

  - 비축소형 메시지와 축소형 메시지

    - 비축소형 메시지
  
      - 각각의 개별 메시지가 기기로 전송됨
      - 메시지마다 콘텐츠가 다르기 때문에 모든 메시지가 의미가 있음
      - 예 : 채팅 메시지, 중요한 메시지
  
    - 축소형 메시지
  
      - 메시지가 아직 기기로 전송되지 않은 경우 새 메시지로 대체될 수 있음
      - 최근 메시지만 의미가 있는 메시지
      - 예 : 서버의 데이터와 동기화할 것을 알리는 데 사용되는 메시지, 스포츠 앱에서의 최신 득점 점수

    - |          | 사용 시나리오                                                | 전송 방법                                                    |
      | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
      | 비축소형 | 모든 메시지가 앱에서 중요해서 모두 전송되어야 할때           | 알림 메시지를 제외한 모든 메시지가 기본적으로 비축소형       |
      | 축소형   | - 가장 최근 메시지만 의미가 있어서 이전 메시지를 대체해도 될때<br />- 비축소형 메시지를 굳이 사용할 필요가 없을 때 성능 면에 있어서는 축소형 메시지가 좋으나 토큰 당 축소 키 개수 제한이 있음 | 메시지 요청에 적절한 매개변수를 설정해야 함<br />- Android: `collapseKey`<br />- Apple: `apns-collapse-id`<br />- 웹: `Topic`<br />- 기존 프로토콜(모든 플랫폼): `collapse_key`<br /><br /><br />기본적으로 축소 키는 Firebase Console에 등록된 앱 패키지 이름이다 |
  
  - 메시지 우선순위 설정
  
    - Android의 다운스트림 메시지에 2가지 전송 우선순위를 할당할 수 있음
  
      1. 보통 우선순위
         - 데이터 메시지의 기본 우선순위
         - 앱이 포그라운스 상태면 즉시 전송됨
         - 기기가 잠자기 상태면 전송이 지연될 수 있음 (배터리를 절약하기 위함)
         - 예 : 시간이 크게 중요하지 않은 메시지 (새로운 이메일, UI 동기화 유지, 백그라운드 앱 데이터 동기화 등)
         - 주의 : Apple 기기로 데이터 메시지를 전송할 때 우선순위를 5 또는 보통 우선순위로 설정해야 한다 (그렇지 않으면 `INVALID_ARGUMENT` 오류 발생)
      2. 높은 우선순위
         - 즉시 전송하려고 시도하며 필요한 경우 FCM에서 기기의 절전 모드를 해제하고 제한된 작업을 할 수 있음
         - 사용자가 알림에 반응하게 해야 한다. 그렇지 않음이 감지된다면 우선순위가 낮아질 수 있다
  
    - ```json
      // 예시
      // 상황 : 잡지 구독자에게 새 콘텐츠를 다운로드할 수 있다고 알림을 보내는 상황
      // 우선순위 : 보통 우선순위
      // 프로토콜 : FCM HTTP v1 프로토콜
      
      {
        "message":{
          "topic":"subscriber-updates",
          "notification":{
            "body" : "This week's edition is now available.",
            "title" : "NewsMagazine.com",
          },
          "data" : {
            "volume" : "3.21.15",
            "contents" : "http://www.news-magazine.com/world-week/21659772"
          },
          "android":{
            "priority":"normal"
          },
          "apns":{
            "headers":{
              "apns-priority":"5"
            }
          },
          "webpush": {
            "headers": {
              "Urgency": "high"
            }
          }
        }
      }
      ```
  
    - 메시지 우선순위 설정에 관한 플랫폼별 세부정보 -> [APN 문서](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CommunicatingwithAPNs.html#//apple_ref/doc/uid/TP40008194-CH11-SW1), [잠자기 및 앱 대기 최적화](https://developer.android.com/training/monitoring-device-state/doze-standby.html?authuser=1)(Android), [웹 푸시 메시지 긴급성](https://tools.ietf.org/html/rfc8030#section-5.3)
  
  - 메시지 수명 설정
  
    - FCM에서 바로 전송하지 못하는 경우가 있다 (기기가 꺼져 있는경우, 오프라인인 경우, 앱이 과도한 리소스를 소비하는것을 방지 등)
  
    - 이런 상황에서 FCM은 전송할 수 있게 되면 바로 전송한다. 하지만 뒤늦게 보내면 의미없는 경우도 있다
  
    - 그래서 메시지의 최대 수명을 지정할 수 있다
  
    - 범위 (단위 : 초) : 0 ~ 2,419,200 (28일)
  
      - Android, 웹, 자바스크립트에서 사용
      - 지정하지 않을때 : 2,419,200 (28일)
      - 0으로 지정할때 : 즉시 전송할 수 없는 메시지는 삭제됨 (즉시 전송 or 전송하지 않음)
      - n으로 지정할때 : FCM은 최대 n초동안 메시지를 저장하여 전송 시도한다
  
    - ```json
      {
        "message":{
          "token":"bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...",
          "data":{
            "Nick" : "Mario",
            "body" : "great match!",
            "Room" : "PortugalVSDenmark"
          },
          "apns":{
            "headers":{
              "apns-expiration":"1604750400"
            }
          },
          "android":{
            "ttl":"4500s"
          },
          "webpush":{
            "headers":{
              "TTL":"4500"
            }
          }
        }
      }
      ```
  
- 여러 발신자의 메시지 수신

  - 여러 발신자가 동일한 클라이언트 앱으로 메시지를 보낼 수 있음
  - 이 기능을 사용하려면 각 발신자의 [발신자 ID](https://firebase.google.com/docs/cloud-messaging/concept-options?authuser=1#senderid)가 있어야 함
  - 토큰 요청 하나에 여러 발신자 ID를 추가하지 않아야 함
  - 등록 토큰을 발신자와 공유함

- 메시지의 수명

  - 메시지를 게시한 후 메시지 ID를 반환받음 != 기기로 전송됨
  - 메시지를 게시한 후 메시지 ID를 반환받음 == 전송이 수락되었음
  - 기기가 연결되어 있지만 잠자기 상태인 경우 우선순위가 낮은 메시지는 잠자기 상태가 해제될 때까지 FCM이 보관함
  -  `collapse_key` 플래그가 이전 메시지를 축소할지, 새로운 메시지도 저장할 지 결정함
  - 앱이 제거된 경우 : FCM이 메시지를 즉시 삭제하고 등록 토큰을 무효화함. 이후 이 기기로 메시지를 보내려고 시도하면 `NotRegistered` 오류가 발생함.

- 제한 및 확장

  - 축소형 메시지 제한
    - 개발자가 앱에 동일한 메시지를 너무 자주 반복하는 경우에는 배터리에 미치는 영향을 줄이기 위해 메시지 전송을 지연(제한)함
    - 축소형 메시지를 기기 하나당 앱별로 메시지 20개로 제한하고 3분마다 메시지 1개를 다시 채움

  - XMPP 서버 제한
    - FCM XMPP 서버에 연결할 수 있는 속도를 프로젝트당 1분에 400개의 연결로 제한
    - 프로젝트마다 FCM에서 동시 연결 2,500개를 허용

  - 단일 기기에 대한 최대 메시지 속도 : 분당 240개, 시간당 5,000개
  - 업스트림 메시지 한도 : 프로젝트당 1,500,000/분, 기기당 업스트림 메시지를 1,000/분
  - 토픽 메시지 한도 : 토픽 구독 추가/삭제 속도는 프로젝트당 3,000QPS
  - 팬아웃 제한
    - 프로젝트당 동시 메시지 팬아웃 수는 1,000개

- FCM 포트 및 방화벽

  - 조직에 인터넷 트래픽 송수신을 제한하는 방화벽이 있으면 모바일 기기의 FCM 연결을 허용하도록 구성해야 네트워크의 기기에서 메시지를 수신할 수 있음
  - FCM은 대개 포트 5228을 사용하지만 443, 5229, 5230을 사용하는 경우도 있음

- 사용자 인증 정보

  - 구현한 FCM 기능에 따라 다음과 같은 Firebase 프로젝트의 사용자 인증 정보가 필요할 수도 있음
  - 프로젝트 ID
    - Firebase 프로젝트의 고유 식별자
    - FCM v1 HTTP 엔드포인트에 관한 요청에 사용됨

  - 등록 토큰
    - 각 클라이언트 앱 인스턴스를 식별하는 고유한 토큰 문자열
    - 단일 기기 및 기기 그룹 메시징에 필요함

  - 발신자 ID
    - Firebase 프로젝트를 만들 때 생성되는 고유한 숫자 값
    - Firebase Console [설정](https://console.firebase.google.com/project/_/settings/cloudmessaging/?authuser=1) 창의 클라우드 메시징 탭에서 확인할 수 있음
    - 발신자 ID는 클라이언트 앱에 메시지를 보낼 수 있는 각 발신자를 식별하는 데 사용됨

  - 액세스 토큰
    - HTTP v1 API에 대한 요청을 승인하는 단기 OAuth 2.0 토큰
    - 이 토큰이 Firebase 프로젝트에 속한 서비스 계정에 연결됨
    - 액세스 토큰을 만들고 순환하려면 -> [보내기 요청 승인](https://firebase.google.com/docs/cloud-messaging/auth-server?authuser=1)

  - 서버 키 (이전 프로토콜용)
    - 앱 서버가 Firebase 클라우드 메시징 이전 프로토콜을 통한 메시지 전송과 같은 Google 서비스에 액세스할 수 있도록 인증하는 서버 키
    - Firebase 프로젝트를 만들 때 서버 키를 부여받음. -> Firebase Console [설정](https://console.firebase.google.com/project/_/settings/cloudmessaging/?authuser=1) 창의 클라우드 메시징 탭에서 확인 가능
    - 중요 : 클라이언트 코드에 서버 키를 포함하면 안됨. 또한 앱 서버를 인증할 때 서버 키만 사용해야 함. Android, Apple 플랫폼, 브라우저 키는 FCM에서 거부함


## 메시지 전송 이해

- 메시지 전송 보고서
  - 전송된 메시지에 대해 다음과 같은 데이터를 확인할 수 있다
    - 전송 : 전송을 위해 대기열에 추가됨 or 전송을 위해 외부 서비스(APN 등)에 성공적으로 전달됨
    - 수신됨 (only Android) : 앱에 수신됨
    - 노출 (only Android, 알림메시지) : 백그라운드 상태일때 디스플레이 알림이 기기에 표시됨
    - 확인 : 사용자가 알림 메시지를 확인함. 백그라운드 상태일때 수신된 알림만 보고됨

- 알림 유입경로 분석
- FCM Data API를 통해 집계된 전송 데이터
- BigQuery 데이터 내보내기
- 내보낸 데이터로 무엇을 할 수 있나요?

## FCM 등록 토큰 관리

- 기본 모범 사례
- 등록 토큰 신선도 보장
- 배달 성공 측정

# APNs

- 푸시 알람을 위한 준비

  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdcC6a2%2FbtqObXnvFMm%2Fu14vDDM9A2kgrOgpjvgeB0%2Fimg.png" alt="?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdcC6a2%2FbtqObXnvFMm%2Fu14vDDM9A2kgrOgpjvgeB0%2Fimg" style="zoom:50%;" />

- 실제로 푸시를 보내고 싶을때

  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Ft0lKD%2FbtqN8ZGaHOj%2FkVnWM8bb6AKXHtrJIOWhXK%2Fimg.png" alt="?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Ft0lKD%2FbtqN8ZGaHOj%2FkVnWM8bb6AKXHtrJIOWhXK%2Fimg" style="zoom:50%;" />



- 참고자료
  - https://firebase.google.com/docs/cloud-messaging?hl=ko
  - https://babbab2.tistory.com/58

