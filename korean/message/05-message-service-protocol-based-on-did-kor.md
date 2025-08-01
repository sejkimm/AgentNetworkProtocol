# Message Service Protocol Based on did:all Method

## 1. 배경

DID를 기반으로 한 플랫폼 간 신원 인증 및 종단 간 암호화 통신 기술에 대한 글에서, 우리는 DID를 기반으로 신원 인증과 종단 간 암호화 통신을 수행하는 방법을 소개했습니다. 단기 키를 협상하고 암호화 통신을 수행할 때, 두 DID 사용자는 서로를 찾고 핸드셰이크 메시지나 암호화된 메시지를 서로에게 보낼 수 있어야 합니다. 이 글은 DID 사용자가 서로를 찾고 메시지를 보내는 방법을 설명합니다.

## 1. 과정 및 아키텍처

전체 구조는 다음 다이어그램에 나와 있습니다:

![DID 기반 메시지 통신 아키텍처](../../images/message-flow-architecture.png)

위 다이어그램은 DID 기반 메시지 통신의 전체 아키텍처를 보여주며, 세 가지 주요 참가자가 있습니다:

- User Server: 사용자의 백엔드 서비스로, 사용자 DID 문서 관리 및 사용자의 메시지 송수신을 도와주는 역할을 담당합니다.
- DID Server: DID 문서 호스팅 서비스로, DID 생성, 조회 및 기타 서비스를 담당합니다.
- Message Proxy: 메시지 프록시로, 사용자에게 메시지 송수신 서비스를 제공하는 역할을 담당합니다.

우리의 DID all 방법 설계 사양에서 언급했듯이, DID 호스팅 서비스(DID server)와 메시지 서비스(Message Proxy)는 선택 사항이며, 사용자는 자체 구축 서비스를 사용할 수 있지만 과정은 기본적으로 동일합니다.

## 2. User Server와 Message Proxy 연결

User Server는 자체 메시지 서비스 제공업체를 선택할 수 있습니다. 선택 후 서비스 제공업체의 웹사이트에서 API 키와 서비스 엔드포인트를 신청합니다. API 키는 User Server와 Message Proxy 간의 연결 인증에 사용되며, 서비스 엔드포인트는 DID 문서에 작성되어 이 DID가 이 엔드포인트의 메시지 서비스를 사용함을 나타낼 수 있습니다.

메시지 서비스는 일반적으로 WSS 장기 연결을 사용하여 User Server가 Message Proxy에 적극적으로 연결하고 하트비트를 유지해야 합니다.

User Server가 Message Proxy에 연결한 후, 이 연결에 대한 라우팅 정보(DID 문서의 router로, 이것도 DID임)와 관련 서명을 제공하여 경로의 소유권을 증명해야 합니다. router는 Message proxy에게 이 연결이 담당하는 라우팅 정보를 알리는 데 사용됩니다. Proxy는 라우팅 정보, WSS 연결, DID를 함께 바인딩합니다. 이후 이 DID에 대한 모든 메시지는 이 WSS 연결을 통해 Server로 전송됩니다. 이 설계의 장점은 Server가 Proxy에 연결한 후 소수의 router만 등록하면 되고 모든 DID를 Proxy에 보낼 필요가 없다는 것입니다. 이는 대량의 DID를 처리할 때 특히 유용합니다.

하나의 라우팅 정보는 여러 WSS 연결에 바인딩될 수 있으며, 전송 시 Proxy에서 로드 밸런싱이 수행됩니다.

router 등록, 바인딩 및 DID에 따른 메시지 전송 과정은 다음과 같습니다:

![Router 등록, 바인딩 및 DID 기반 메시지 전송 과정](../../images/message-did-register-flow.png)

## 3. Message Proxy 인증 메커니즘

Message Proxy는 API 키를 사용하여 User Server를 인증합니다. 전체 과정은 User Server가 DID Server에 연결할 때의 인증 과정과 유사합니다(DID all 방법 설계 사양).

### 3.1 WSS HTTP 요청 매개변수

요청 헤더:

- Content-Type: application/json
- Authorization: API Key와 토큰 인증 방법을 모두 지원

### 3.2 WSS HTTP 사용자 인증

인터페이스를 호출할 때 두 가지 인증 방법을 지원합니다:

1. API Key를 사용한 인증
2. 인증 토큰을 사용한 인증

#### 3.2.1 API Key 얻기

서비스 제공업체의 API Keys 페이지에 로그인하여 최신 생성된 사용자 API Key를 얻습니다. API Key는 "사용자 식별자 id"와 "서명 키 secret"을 모두 포함하며, 형식은 {id}.{secret}입니다.

#### 3.2.2 API Key를 사용한 요청 만들기

사용자는 API Key를 HTTP Authorization 헤더에 배치해야 합니다.

#### 3.2.3 JWT 조립 토큰을 사용한 요청 만들기

사용자는 해당 JWT 관련 유틸리티 클래스를 가져와서 다음과 같이 JWT 헤더와 페이로드 부분을 조립해야 합니다.

1. 헤더 예시

```json
{
  "alg": "HS256",
  "sign_type": "SIGN"
}
```

- alg: 속성은 서명에 사용되는 알고리즘을 나타내며, 기본값은 HMAC SHA256(HS256으로 작성)입니다.
- sign_type: 속성은 토큰 유형을 나타내며, JWT 토큰의 경우 SIGN으로 통일하여 작성합니다.

2. 페이로드 예시

```json
{
  "api_key": "{ApiKey.id}",
  "exp": 1682503829130,
  "timestamp": 1682503820130
}
```

- api_key: 속성은 사용자 식별자 id를 나타내며, 사용자 API Key의 {id} 부분입니다.
- exp: 속성은 생성된 JWT의 만료 시간을 나타내며, 클라이언트에서 제어하고 밀리초 단위입니다.
- timestamp: 속성은 현재 타임스탬프를 밀리초 단위로 나타냅니다.

예시: Python에서 인증 토큰 조립 과정

```python
import time
import jwt
 
def generate_token(apikey: str, exp_seconds: int):
    try:
        id, secret = apikey.split(".")
    except Exception as e:
        raise Exception("invalid apikey", e)
    
    payload = {
        "api_key": id,
        "exp": int(round(time.time() * 1000)) + exp_seconds * 1000,
        "timestamp": int(round(time.time() * 1000)),
    }
    
    return jwt.encode(
        payload,
        secret,
        algorithm="HS256",
        headers={"alg": "HS256", "sign_type": "SIGN"},
    )
```

3. HTTP 요청 헤더에 인증 토큰 배치
사용자는 생성된 인증 토큰을 HTTP Authorization 헤더에 배치해야 합니다:

- Authorization: Bearer <your_token>

## 4. Message Proxy 간 연결

서비스 Proxy들도 서로 연결해야 하며, 특히 서로 다른 플랫폼이 서로 다른 서비스를 사용할 때 그렇습니다. 서비스들은 WSS를 통해 서로 연결할 수 있으며, 연결 후 각자의 서비스 엔드포인트를 서로에게 알려 라우팅을 용이하게 합니다.

## 5. 메시지 송수신 과정

이 섹션에서는 사용자 A가 B의 DID를 사용하여 사용자 B에게 메시지를 성공적으로 보낼 수 있는 방법을 설명합니다.
전체 과정은 다음 다이어그램에 나와 있습니다:

![메시지 송수신 과정](../../images/message-send-receive-flow.png)

과정 설명:

1. 사용자 A는 WeChat, SMS, 오프라인 또는 기타 채널을 통해 사용자 B의 DID를 얻고, A의 서버를 통해 B에게 메시지를 보내는 요청을 시작합니다.
2. 사용자 A의 서버는 B에게 보내는 메시지 전송 요청을 받고 B의 DID를 포함한 요청을 A Server의 Message proxy에 보냅니다.
3. A의 Message Proxy는 메시지 전송 요청을 받고, B의 DID를 사용하여 DID Server에서 B의 DID 문서를 조회하고, B의 메시지 서비스 엔드포인트를 얻으며, 향후 더 빠른 연결을 위해 B의 DID 문서를 캐시합니다.
4. A의 Message Proxy는 B의 메시지 서비스 엔드포인트(B의 Message Proxy)에 연결을 시작하고 메시지를 보냅니다.
5. B의 Message Proxy는 요청을 받고, B의 DID를 사용하여 B의 DID 문서를 조회하여 B의 메시지 라우팅 정보(DID 문서의 router 필드)를 얻습니다. 메시지 라우팅을 기반으로 B Server의 WSS 연결을 조회하고 메시지를 전달합니다.
6. B의 Server는 메시지를 받고, 확인 및 처리하여 B에게 보냅니다.
7. B가 A에게 보내는 후속 메시지의 과정은 A가 B에게 보내는 것과 동일한 과정을 따릅니다.

참고: 새로운 DID에 처음 접근할 때 시스템에 캐시가 없을 수 있어 조회 과정이 시간이 많이 걸릴 수 있지만, 후속 접근은 캐시에 직접 접근할 수 있어 지연 시간이 낮습니다.

## 6. 프로토콜 정의

DID 기반 종단 간 암호화 통신 기술에서 설명한 바와 같이, 우리 프로토콜은 전송에 WSS를 사용하고 JSON 형식을 사용합니다.

### 6.1 메시지 서비스 등록 메시지

사용자 Server가 Message Proxy에 등록하는 데 사용됩니다. 등록 시 여러 router를 포함할 수 있습니다. 각 router는 router DID, 생성 시간, nonce(재생 공격 방지용), 서명(해당 router의 JSON 하위 객체에 대한 서명)을 포함합니다.

참고: 이것은 모든 router를 포함해야 하는 전체 인터페이스입니다. 등록 메시지에 포함되지 않은 router는 삭제됩니다.

예시:

```json
{
  "version": "1.0",
  "type": "register",
  "timestamp": "2024-05-27T12:00:00.123Z",
  "messageId": "randomstring",
  "routers": [
    {
      "router": "did:all:14qQqsnEPZy2wcpRuLy2xeR737ptkE2Www",
      "nonce": "randomNonceValue1",
      "proof": {
        "type": "EcdsaSecp256r1Signature2019",
        "created": "2024-05-27T10:51:55Z",
        "verificationMethod": "did:example:14qQqsnEPZy2wcpRuLy2xeR737ptkE2Www#keys-1",
        "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
      }
    },
    {
      "router": "did:all:14qQqsnEPZy2wcpRuLy2xeR737ptkE2Www",
      "nonce": "randomNonceValue1",
      "proof": {
        "type": "EcdsaSecp256r1Signature2019",
        "created": "2024-05-27T10:51:55Z",
        "verificationMethod": "did:example:14qQqsnEPZy2xcpRuLy2xeR737ptkE2Www#keys-1",
        "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
      }
    }
  ]
}
```

#### 6.1.1 필드 설명

- version: 프로토콜 버전
- type: 요청 유형, 이것이 router 등록 요청임을 나타내며, 값은 "register"
- timestamp: 요청 타임스탬프로 요청이 언제 보내졌는지 나타내며, 밀리초까지 정확한 ISO 8601 형식 UTC 시간 문자열
- messageId: 고유 메시지 id, 16자 무작위 문자열
- routers: 여러 router 객체를 포함하는 배열, 각 router 객체는 다음 필드를 포함:
  - router: Router의 DID, router를 식별하는 데 사용
  - nonce: 무작위 값, 32바이트 문자열, 재생 공격 방지용, 각 요청의 고유성 보장
  - proof: DID 기반 종단 간 암호화 통신 기술의 SourceHello에서 proof 필드와 동일. 현재 router만 서명.

#### 6.1.2 Proof 서명 생성 단계

과정은 기본적으로 DID 기반 종단 간 암호화 통신 기술의 sourceHello 메시지 서명 방법과 동일합니다. 차이점은 여기서 서명이 단일 router만 보호한다는 것입니다.

- 서명할 router 객체를 JSON 문자열로 변환(proofValue 필드 제외), 쉼표와 콜론을 구분자로 사용하고 키로 정렬.
- JSON 문자열을 UTF-8 바이트로 인코딩.
- 타원 곡선 디지털 서명 알고리즘(EcdsaSecp256r1Signature2019)과 SHA-256 해시 알고리즘을 사용하여 바이트 데이터에 서명.
- 생성된 서명 값 proofValue를 메시지 json의 proof 사전의 proofValue 필드에 추가.

```python
# 1. proofValue 필드를 제외한 json 메시지의 모든 필드 생성
router = {
    # 기타 필요한 필드
    "proof": {
        "type": "EcdsaSecp256r1Signature2019",
        "created": "2024-05-27T10:51:55Z",
        "verificationMethod": "did:example:123456789abcdefghi#keys-1"
        # proofValue 필드 제외
    }
}

# 2. JSON 문자열로 변환, 키로 정렬, 쉼표와 콜론을 구분자로 사용
router_str = JSON.stringify(router, separators=(',', ':'), sort_keys=True)

# 3. JSON 문자열을 UTF-8 바이트로 인코딩
router_bytes = UTF8.encode(router_str)

# 4. 개인 키와 ECDSA 알고리즘을 사용하여 바이트 데이터에 서명
signature = ECDSA.sign(router_bytes, private_key, algorithm=SHA-256)

# 5. json 메시지의 proof 필드에 서명 값 추가
router["proof"]["proofValue"] = Base64.urlsafe_encode(signature)
```

#### 6.1.3 Proof 서명 검증 과정

router의 DID에 따라 DID 문서를 가져오고, verificationMethod에 따라 문서에서 해당 공개 키를 가져와서 공개 키와 위의 서명 과정에 따라 서명을 검증합니다.

#### 6.1.4 Nonce 생성 및 검증 단계

Nonce의 목적은 재생 공격을 방지하는 것입니다.

1. Nonce 생성: 클라이언트가 무작위 번호 또는 고유 식별자(nonce)를 생성하며, 각 요청에 대해 새로운 nonce를 사용해야 합니다.
2. Nonce 기록: 서버가 요청을 처리할 때 nonce를 기록합니다.
3. Nonce 검증: 서버가 특정 시간 기간 내에 nonce가 사용되었는지 확인합니다. 사용되었다면 요청을 거부합니다.

#### 6.1.5 타임스탬프 검증

- 타임스탬프: 요청이 언제 생성되었는지 나타내는 타임스탬프를 요청에 포함.
- 유효성 검사: 서버가 타임스탬프가 합리적인 시간 범위(예: 5분 이내) 내에 있는지 확인. 이 시간 범위를 벗어난 요청은 거부됩니다.

### 6.2 Message Proxy 간 연결

Proxy A가 대상 DID의 메시지 서비스 엔드포인트 B를 해결한 후, 이 엔드포인트 B에 WSS 연결을 시작할 수 있으며, 그 후 등록할 필요 없이 직접 메시지를 보낼 수 있지만 살아있음을 유지하기 위해 하트비트가 필요합니다.
B는 A에게 메시지를 보내기 위해 새로운 WSS 연결을 설정할 수 있으며, A에서 B로의 이전 연결을 사용하지 않습니다.
따라서 하나의 연결은 연결 시작자가 메시지를 보내는 데만 사용됩니다.

### 6.3 하트비트 유지 메시지

하트비트 메시지는 연결하는 쪽에서 시작됩니다. 서버는 주기적으로(60초) keep-alive 메시지가 없는 연결을 정리합니다.

예시:

```json
{
  "version": "1.0",
  "type": "heartbeat",
  "timestamp": "2024-06-04T12:34:56Z",
  "messageId": "randomstring",
  "message": "ping"
}
```

#### 6.3.1 필드 설명

- version: 프로토콜 버전
- type: 요청 유형, 이것이 하트비트 요청임을 나타내며, 값은 "heartbeat"
- timestamp: 요청 타임스탬프로 요청이 언제 보내졌는지 나타내며, 밀리초까지 정확한 ISO 8601 형식 UTC 시간 문자열
- messageId: 고유 메시지 id, 16자 무작위 문자열
- message: 요청에서 보내는 하트비트 메시지는 "ping"이고, 응답 메시지는 "pong"

### 6.4 암호화 통신 메시지

두 사용자가 DID를 통해 단기 암호화 키를 협상한 후, 메시지 메시지를 통해 암호화된 메시지를 보낼 수 있습니다.

예시:

```json
{
  "version": "1.0",
  "type": "message",
  "timestamp": "2024-06-04T12:34:56.123Z",
  "messageId": "randomstring",
  "sourceDid": "did:example:987654321abcdefghi",
  "destinationDid": "did:example:123456789abcdefghi",
  "secretKeyId": "abc123session",
  "encryptedData": {
    "iv": "iv_encoded_base64",
    "tag": "tag_encoded_base64",
    "ciphertext": "ciphertext_encoded_base64"
  }
}
```

#### 6.4.1 프로토콜 필드

- version: 문자열, 현재 프로토콜 버전 번호
- type: 문자열, 메시지 유형
- timestamp: 메시지 전송 시간, 밀리초까지 정확한 ISO 8601 형식 UTC 시간 문자열
- messageId: 고유 메시지 id, 16자 무작위 문자열
- sourceDid: 문자열, 메시지 소스 또는 발신자의 DID, 여기에는 항상 메시지 발신자 자신의 DID를 입력
- destinationDid: 문자열, 목적지 또는 메시지 수신자의 DID, 여기에는 항상 메시지 수신자의 DID를 입력
- secretKeyId: 단기 암호화 키의 ID로, 이를 통해 이전에 협상된 대칭 암호화 키 알고리즘, 암호화 키 및 기타 정보를 찾을 수 있음, 유형: 문자열. 자세한 내용은 DID 기반 종단 간 암호화 통신 기술 참조
- encryptedData: 암호화된 데이터로, 암호화 알고리즘에 따라 서로 다른 데이터를 포함할 수 있습니다. 다음은 TLS_AES_128_GCM_SHA256 암호화 모음에 필요한 데이터입니다: iv(초기화 벡터), tag(인증 태그, 암호화 알고리즘에 따라 다름)를 포함합니다. ciphertext(암호화된 텍스트)는 모든 암호화 알고리즘에 존재합니다.
  - iv: 초기화 벡터, 무작위 또는 의사 무작위 바이트 시퀀스, AES-GCM 모드의 경우 일반적으로 12바이트(96비트)
  - tag: AES-GCM 모드에서 생성된 인증 코드로, 데이터 무결성과 진위성을 확인하는 데 사용. Tag는 일반적으로 16바이트(128비트)
  - ciphertext: 암호화된 데이터로, 암호화된 암호문은 Base64로 인코딩되고 인코딩 결과는 UTF-8 문자열로 변환됩니다
  - encryptedData 생성 방법은 finished 메시지 verifyData 생성 방법과 동일: DID 기반 종단 간 암호화 통신 기술

### 6.5 응답 메시지

모든 WSS 메시지에 대해 일반적인 응답 메시지가 있으며, 주요 목적은 단기 키 협상 메시지, wss 등록 메시지, 암호화 통신 메시지 등을 포함한 메시지 처리의 예외를 알리는 것입니다. 이 메시지는 WSS json 메시지 수준 수신 확인에 사용되어서는 안 됩니다. 애플리케이션 계층에서 메시지를 보내고 상대방이 메시지를 올바르게 받았는지 확인해야 하는 경우, 애플리케이션 계층 프로토콜에서 보호 메커니즘을 설계해야 합니다.

```json
{
  "version": "1.0",
  "type": "response",
  "timestamp": "2024-06-04T12:34:56.123Z",
  "messageId": "randomstring",
  "sourceDid": "did:example:987654321abcdefghi",
  "destinationDid": "did:example:123456789abcdefghi",
  "originalType": "register",
  "originalMessageId": "randomstring",
  "code": 200,
  "detail": "invalid json"
}
```

#### 6.5.1 필드 설명

- originalType: 원본 메시지 유형
- originalMessageId: 원본 메시지 메시지 id
- code: 오류 코드, HTTP 오류 코드와 유사한 기본 설계, 다음은 일반적인 오류들:
  - 200: 정상
  - 404: DID를 찾을 수 없음
  - 403: 인증 실패
  - 4000: ENCRYPTION_ERROR: 암호화 중 오류 발생
  - 4001: DECRYPTION_ERROR: 복호화 중 오류 발생
  - 4002: INVALID_ENCRYPTION_KEY: 암호화 키가 유효하지 않거나 일치하지 않음
  - 4003: INVALID_DECRYPTION_KEY: 복호화 키가 유효하지 않거나 일치하지 않음
  - 4004: ENCRYPTION_KEY_EXPIRED: 암호화 키가 만료됨
  - 4005: DECRYPTION_KEY_EXPIRED: 복호화 키가 만료됨
- detail: 오류에 대한 자세한 설명

### 6.6 DID 업데이트 알림

DID의 내부 필드가 수정되거나, DID에 해당하는 개인 키가 손상되어 원래 DID 문서를 폐기해야 할 때, DID 알림 메시지를 보내 다른 관련 DID들이 적시에 DID 문서를 다시 조회하도록 알려야 합니다.
잠재적인 악의적 공격을 방지하기 위해 DID 업데이트 알림은 DID 문서의 다중 서명 메커니즘과 함께 시작될 예정이며, 다음 버전에서 지원할 계획입니다. 이렇게 하면 하나의 키가 손상되더라도 여전히 관련 당사자들에게 안전하게 알릴 수 있습니다.

## 저작권 고지

Copyright (c) 2024 GaoWei Chang  
이 파일은 [MIT 라이선스](./LICENSE)에 따라 배포됩니다. 자유롭게 사용하고 수정할 수 있지만, 이 저작권 고지는 반드시 유지해야 합니다.
