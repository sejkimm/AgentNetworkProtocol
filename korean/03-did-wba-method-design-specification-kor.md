# did:wba 방법 사양(V0.1)

## 초록

wba DID 방법은 크로스 플랫폼 신원 인증 및 지능형 에이전트 통신의 요구사항을 충족하도록 설계된 웹 기반 분산 식별자(DID) 사양입니다. 이 방법은 did:web 사양을 확장하고 최적화하여 did:wba로 명명하였으며, 호환성을 유지하면서 에이전트 시나리오에 대한 적응성을 향상시켰습니다. 이 사양에서는 did:wba 방법과 HTTP 프로토콜을 기반으로 하여 서버가 상호작용 빈도를 증가시키지 않고도 다른 플랫폼의 클라이언트 신원을 빠르게 검증할 수 있는 프로세스를 설계합니다.

## 1. 서론

### 1.1 서문

wba DID 방법 사양은 Decentralized Identifiers V1.0 [[DID-CORE](https://www.w3.org/TR/did-core/)]에서 지정된 요구사항을 준수합니다.

이 사양은 did:web 방법 사양을 기반으로 DID 문서 제약 조건, 크로스 플랫폼 신원 인증 프로세스, 에이전트 설명 서비스 사양을 추가하여 새로운 방법명 did:wba(Web-Based Agent)를 제안합니다.

did:web 방법 사양이 여전히 초안 상태이며 에이전트 통신 시나리오에 적합하지 않은 변경사항을 겪을 수 있고, 원작자들과 사양 수정에 대한 합의를 이루는 것이 장기적인 과정이라는 점을 고려하여, 새로운 방법명을 사용하기로 결정했습니다.

우리는 향후 did:wba 사양을 did:web 사양에 병합할 가능성을 배제하지 않으며, 이러한 목표 달성을 위해 노력할 것입니다.

did:wba 방법은 2024년 7월 31일 버전의 [https://w3c-ccg.github.io/did-method-web](https://w3c-ccg.github.io/did-method-web)에 있는 did:web 방법 사양을 참조합니다. 관리 목적으로 did:wba에서 현재 사용하는 did:web 방법 사양의 사본을 백업했습니다: [did:web Method Specification](/references/did_web%20Method%20Specification.html).

### 1.2 설계 원칙

did:wba 방법을 설계할 때, 우리의 핵심 원칙은 탈중앙화를 달성하면서 기존의 성숙한 기술과 견고한 Web 인프라를 충분히 활용하는 것입니다. did:wba를 사용하면 플랫폼들이 중앙집중식 방식으로 계정 시스템을 구현하면서도 플랫폼 간 상호운용성을 유지하는 이메일과 같은 기능을 활성화할 수 있습니다.

또한, 다양한 유형의 식별자 시스템이 DID 지원을 추가하여 중앙집중식, 연합식, 분산식 식별자 시스템 간의 상호운용성을 연결할 수 있습니다. 이는 기존 중앙집중식 식별자 시스템이 완전한 재구성을 요구하지 않음을 의미합니다. 기존 인프라 위에 DID를 생성하기만 하면 시스템 간 상호운용성을 달성할 수 있어 기술 구현 복잡성을 크게 줄일 수 있습니다.

## 2. WBA DID 방법 사양

### 2.1 방법명

이 DID 방법을 식별하는 데 사용되는 방법명 문자열은: wba입니다. 이 방법을 사용하는 DID는 다음 접두사로 시작해야 합니다: did:wba. DID 사양에 따라 이 문자열은 소문자여야 합니다. DID의 나머지 부분(접두사 이후)은 아래에 지정됩니다.

### 2.2 방법별 식별자

방법별 식별자는 TLS/SSL 인증서로 보호되는 완전한 도메인명으로, DID 문서로의 경로를 선택적으로 포함합니다. 유효한 도메인명 구문을 설명하는 공식 규칙은 [(RFC1035)](https://www.rfc-editor.org/rfc/rfc1035), [(RFC1123)](https://www.rfc-editor.org/rfc/rfc1123), [(RFC2181)](https://www.rfc-editor.org/rfc/rfc2181)에 지정되어 있습니다.

방법별 식별자는 SSL/TLS 인증서에서 사용되는 공통명과 일치해야 하며 IP 주소를 포함해서는 안 됩니다. 포트 번호는 포함될 수 있지만 경로와의 충돌을 방지하기 위해 콜론은 퍼센트 인코딩되어야 합니다. 디렉토리와 하위 디렉토리는 선택적으로 포함될 수 있으며, 슬래시 대신 콜론을 구분자로 사용합니다.

wba-did = "did:wba:" domain-name
wba-did = "did:wba:" domain-name * (":" path)

```plaintext
예시: wba 방법 DID 예시

did:wba:example.com

did:wba:example.com:user:alice

did:wba:example.com%3A3000
```

### 2.4 키 자료 및 문서 처리

대부분의 웹 서버가 콘텐츠를 렌더링하는 방식으로 인해, 특정 did:wba 문서는 미디어 유형 application/json으로 제공될 가능성이 높습니다. did.json이라는 이름의 문서를 검색할 때 다음 처리 규칙을 따라야 합니다:

1. JSON 문서의 루트에 @context가 존재하면 문서는 JSON-LD 규칙에 따라 처리되어야 합니다. 처리가 실패하거나 수행될 수 없으면 문서는 did:wba 문서로 거부되어야 합니다.

2. 루트에 @context가 존재하고 문서가 JSON-LD로 성공적으로 처리되어 `https://www.w3.org/ns/did/v1` 컨텍스트를 포함하면, [[did-core specification section 6.3.2](https://www.w3.org/TR/did-core/#consumption-0)]에 따라 DID 문서로 추가 처리될 수 있습니다.

3. @context가 존재하지 않으면 문서는 [[did-core specification section 6.2.2](https://www.w3.org/TR/did-core/#consumption)]에 지정된 일반 JSON 규칙에 따라 DID로 처리되어야 합니다.

4. DID URL이 did:wba 문서에 나타날 때는 절대 URL이어야 합니다.

> 참고: 여기에는 임베디드 키 자료 및 기타 메타데이터의 URL이 포함되며, 이는 키 혼동 공격을 방지합니다.

### 2.5 DID 문서 사양

DID Core 사양 외에도 대부분의 다른 사양은 여전히 초안 단계입니다. 이 섹션에서는 인증에 사용되는 DID 문서의 하위 집합을 보여줍니다. 시스템 간 상호운용성을 향상시키기 위해 필수로 표시된 모든 필드는 모든 시스템에서 지원되어야 하며, 선택사항으로 표시된 필드는 선택적으로 지원될 수 있습니다. 여기에 나열되지 않은 다른 표준에서 정의된 필드는 선택적으로 지원될 수 있습니다.

**DID 문서 예시:**

```json
{
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/jws-2020/v1",
      "https://w3id.org/security/suites/secp256k1-2019/v1",
      "https://w3id.org/security/suites/ed25519-2020/v1",
      "https://w3id.org/security/suites/x25519-2019/v1"
    ],
    "id": "did:wba:example.com%3A8800:user:alice",
    "verificationMethod": [
      {
        "id": "did:wba:example.com%3A8800:user:alice#WjKgJV7VRw3hmgU6--4v15c0Aewbcvat1BsRFTIqa5Q",
        "type": "EcdsaSecp256k1VerificationKey2019",
        "controller": "did:wba:example.com%3A8800:user:alice",
        "publicKeyJwk": {
          "crv": "secp256k1",
          "x": "NtngWpJUr-rlNNbs0u-Aa8e16OwSJu6UiFf0Rdo1oJ4",
          "y": "qN1jKupJlFsPFc1UkWinqljv4YE0mq_Ickwnjgasvmo",
          "kty": "EC",
          "kid": "WjKgJV7VRw3hmgU6--4v15c0Aewbcvat1BsRFTIqa5Q"
        }
      }
    ],
    "authentication": [
      "did:wba:example.com%3A8800:user:alice#WjKgJV7VRw3hmgU6--4v15c0Aewbcvat1BsRFTIqa5Q",
      {
        "id": "did:wba:example.com%3A8800:user:alice#key-1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:wba:example.com%3A8800:user:alice",
        "publicKeyMultibase": "zH3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
      }
    ],
    "keyAgreement": [
      {
        "id": "did:wba:example.com%3A8800:user:alice#key-2",
        "type": "X25519KeyAgreementKey2019", 
        "controller": "did:wba:example.com%3A8800:user:alice",
        "publicKeyMultibase": "z9hFgmPVfmBZwRvFEyniQDBkz9LmV7gDEqytWyGZLmDXE"
      }
    ],
    "humanAuthorization": [
      "did:wba:example.com%3A8800:user:alice#WjKgJV7VRw3hmgU6--4v15c0Aewbcvat1BsRFTIqa5Q",
      {
        "id": "did:wba:example.com%3A8800:user:alice#key-3",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:wba:example.com%3A8800:user:alice",
        "publicKeyMultibase": "z9XK2BVwLNv6gmMNbm4uVAjZpfkcJDwDwnZn6z3wweKLo"
      }
    ],    
    "service": [
      {
        "id": "did:wba:example.com%3A8800:user:alice#agent-description",
        "type": "AgentDescription",
        "serviceEndpoint": "https://agent-network-protocol.com/agents/example/ad.json"
      }
    ]
}
```

**필드 설명:**

- **@context**: 필수 필드. JSON-LD 컨텍스트는 DID 문서에서 사용되는 시맨틱과 데이터 모델을 정의하여 문서의 이해 가능성과 상호운용성을 보장합니다. `https://www.w3.org/ns/did/v1`은 필수입니다. 필요에 따라 다른 것들을 추가할 수 있습니다.

- **id**: 필수 필드. IP 주소를 포함할 수 없지만 포트는 포함할 수 있습니다. 포트가 포함될 때 콜론은 %3A로 인코딩되어야 합니다. 경로는 콜론을 사용하여 구분됩니다.

- **verificationMethod**: 필수 필드. DID 주체를 검증하기 위한 공개키 정보를 정의하는 검증 방법의 배열을 포함합니다.
  - **하위 필드**:
    - **id**: 검증 방법의 고유 식별자
    - **type**: 검증 방법의 유형
    - **controller**: 이 검증 방법을 제어하는 DID
    - **publicKeyJwk**: JSON Web Key 형식의 공개키 정보

- **authentication**: 필수 필드. 인증에 사용되는 검증 방법을 나열하며, 문자열 또는 객체일 수 있습니다.
  - **하위 필드**:
    - **id**: 검증 방법의 고유 식별자
    - **type**: 검증 방법의 유형
    - **controller**: 이 검증 방법을 제어하는 DID
    - **publicKeyMultibase**: Multibase 형식의 공개키 정보

- **keyAgreement**: 선택적 필드. 암호화된 통신을 위한 DID 간 키 합의에 사용되는 공개키 정보를 정의합니다. 검증 방법은 일반적으로 X25519KeyAgreementKey2019와 같은 키 교환에 적합한 키 합의 알고리즘을 사용합니다.
  - **하위 필드**:
    - **id**: 키 합의 방법의 고유 식별자
    - **type**: 키 합의 방법의 유형
    - **controller**: 이 키 합의 방법을 제어하는 DID
    - **publicKeyMultibase**: Multibase 형식의 공개키 정보

- **humanAuthorization**: 인간 권한 부여에 사용되는 공개키 정보를 정의하는 선택적 필드입니다. 해당 개인키는 인간 권한 부여가 필요한 경우에만 사용되며, 중요한 신원 확인 시나리오에 사용됩니다.
  - **하위 필드**:
    - **id**: 인간 권한 부여 방법의 고유 식별자.
    - **type**: 인간 권한 부여 방법의 유형.
    - **controller**: 이 인간 권한 부여 방법을 제어하는 DID.
    - **publicKeyMultibase**: Multibase 형식의 공개키 정보.

- **service**: DID 주체와 연관된 서비스 목록을 정의하는 선택적 필드입니다.
  - **id**: 서비스의 고유 식별자.
  - **type**: 서비스 유형. 에이전트 설명 서비스의 경우 "AgentDescription"을 사용합니다.
  - **serviceEndpoint**: 서비스의 URL 엔드포인트. 에이전트 설명 서비스의 경우 이 URL은 [ANP-Agent Description Protocol Specification](/07-anp-agent-description-protocol-specification.md)을 따르는 문서를 가리킵니다.

> 참고:
>
> 1. 공개키 정보는 현재 publicKeyJwk와 publicKeyMultibase 두 가지 형식을 지원합니다. 자세한 내용은 [https://www.w3.org/TR/did-extensions-properties/#verification-method-properties](https://www.w3.org/TR/did-extensions-properties/#verification-method-properties)를 참조하세요.
> 2. 검증 방법 유형의 정의는 [https://www.w3.org/TR/did-extensions-properties/#verification-method-types](https://www.w3.org/TR/did-extensions-properties/#verification-method-types)에서 찾을 수 있습니다. 현재 지원되는 유형은: EcdsaSecp256k1VerificationKey2019, Ed25519VerificationKey2018, X25519KeyAgreementKey2019입니다. (Ed25519VerificationKey2020, JsonWebKey2020 등은 현재 지원되지 않습니다.)
> 3. AgentDescription은 에이전트 설명 문서의 발견을 지원하기 위해 새로 추가된 서비스 유형입니다.

### 2.5 DID 방법 연산

#### 2.5.1 생성(등록)

did:wba 방법 사양은 특정 HTTP API 연산을 지정하지 않고, 대신 프로그래밍 방식의 등록과 관리를 각 구현체가 Web 환경 요구사항에 따라 수행하도록 맡깁니다.

DID를 생성하려면 다음 단계가 필요합니다:

1. 도메인명 등록기관에 도메인명 신청
2. 호스팅 서비스의 위치와 IP 주소를 DNS 쿼리 서비스에 저장
3. 적절한 키 쌍을 포함하는 DID 문서 JSON-LD 파일을 생성하고 전체 도메인명을 대표하는 잘 알려진 URL에 did.json 파일을 저장합니다. 같은 도메인명 하에 여러 DID를 파싱해야 하는 경우 지정된 경로에 저장합니다.

예를 들어, 도메인명 example.com의 경우 did.json은 다음 URL에서 사용할 수 있습니다:

```plaintext
예시: DID 생성
did:wba:example.com
 -> https://example.com/.well-known/did.json
```

베어 도메인명 대신 지정된 선택적 경로가 사용되는 경우, did.json은 지정된 경로에서 사용할 수 있습니다:

```plaintext
예시 5: 선택적 경로를 사용한 DID 생성
did:wba:example.com:user:alice
 -> https://example.com/user/alice/did.json
```

도메인명에 지정된 포트가 사용되는 경우, 호스트와 포트 사이의 콜론은 경로와의 충돌을 방지하기 위해 퍼센트 인코딩되어야 합니다.

```plaintext
예시 6: 선택적 경로와 포트를 사용한 DID 생성
did:wba:example.com%3A3000:user:alice
 -> https://example.com:3000/user/alice/did.json
```

#### 2.5.2 읽기(파싱)

DID 문서에서 Web DID를 파싱하려면 다음 단계를 수행해야 합니다:

- 방법별 식별자의 ":"를 "/"로 바꿔서 완전한 도메인명과 선택적 경로를 얻습니다.
- 도메인명에 포트가 포함된 경우 포트를 디코딩합니다.
- DID 문서의 예상 위치 앞에 `https://`를 붙여서 HTTPS URL을 생성합니다.
- URL이 경로를 지정하지 않으면 `/.well-known`을 추가합니다.
- URL을 완성하기 위해 `/did.json`을 추가합니다.
- 보안 HTTPS 연결을 성공적으로 협상할 수 있는 프록시를 사용하여 URL에 HTTP GET 요청을 실행합니다. 프록시는 [2.6 보안 및 프라이버시 고려사항](https://w3c-ccg.github.io/did-method-web/#security-and-privacy-considerations)에 설명된 보안 및 프라이버시 요구사항을 적용합니다.
- 파싱된 DID 문서의 ID가 파싱되는 Web DID와 일치하는지 확인합니다.
- HTTP GET 요청 중에 클라이언트는 파싱되는 신원의 추적을 방지하기 위해 [[RFC8484](https://w3c-ccg.github.io/did-method-web/#bib-rfc8484)]를 사용해야 합니다.

#### 2.5.3 업데이트

DID 문서를 업데이트하려면 DID에 해당하는 did.json 파일을 업데이트해야 합니다. DID 자체는 변경되지 않지만 DID 문서의 내용은 새로운 검증 키나 서비스 엔드포인트 추가와 같이 변경될 수 있습니다.

> 참고:
> git과 같은 버전 제어 시스템과 GitHub Actions와 같은 지속적 통합 시스템을 사용하여 DID 문서의 업데이트를 관리하면 신원 확인과 감사 이력에 대한 지원을 제공할 수 있습니다.

> 참고: HTTP API
> 업데이트 프로세스는 특정 HTTP API를 지정하지 않고, 대신 프로그래밍 방식의 등록과 관리를 각 구현체가 필요에 따라 수행하도록 맡깁니다.

#### 2.5.4 비활성화(폐기)

DID 문서를 삭제하려면 did.json 파일을 제거하거나 다른 이유로 더 이상 공개적으로 사용할 수 없도록 해야 합니다.

### 2.6 보안 및 프라이버시 고려사항

보안 및 프라이버시 고려사항은 [[did:web method specification section 2.6](https://w3c-ccg.github.io/did-method-web/#security-and-privacy-considerations)]을 기반으로 합니다.

## 3. did:wba 방법과 HTTP 프로토콜 기반 크로스 플랫폼 신원 인증

클라이언트가 다른 플랫폼의 서비스에 요청을 할 때, 클라이언트는 도메인명과 TLS를 결합하여 서비스를 인증할 수 있습니다. 그러면 서비스는 클라이언트의 DID 문서에 있는 검증 방법을 기반으로 클라이언트의 신원을 확인합니다.

클라이언트는 첫 번째 HTTP 요청 중에 HTTP 헤더에 DID와 서명을 포함할 수 있습니다. 상호작용 횟수를 늘리지 않고도 서비스는 클라이언트의 신원을 빠르게 확인할 수 있습니다. 초기 확인이 성공한 후 서비스는 클라이언트에게 액세스 토큰을 반환할 수 있습니다. 그러면 클라이언트는 후속 요청에서 액세스 토큰을 전달할 수 있으며, 서비스는 매번 클라이언트의 신원을 확인할 필요 없이 액세스 토큰만 확인하면 됩니다.

```mermaid
sequenceDiagram
    participant Agent A Client
    participant Agent B Server 
    participant Agent A DID Sever

    Note over Agent A Client,Agent B Server: 첫 번째 요청

    Agent A Client->>Agent B Server: HTTP Request: DID,Signature
    Agent B Server->>Agent A DID Sever: Get DID Document
    Agent A DID Sever->>Agent B Server: DID Document

    Note over Agent B Server: 인증

    Agent B Server->>Agent A Client: HTTP Response: access token

    Note over Agent A Client, Agent B Server: 후속 요청

    Agent A Client->>Agent B Server: HTTP Request: access token
    Agent B Server->>Agent A Client: HTTP Response
```

### 3.1 초기 요청

클라이언트가 서비스에 처음 HTTP 요청을 할 때 다음 방법에 따라 인증해야 합니다.

#### 3.1.1 요청 헤더 형식

클라이언트는 `Authorization` 헤더 필드를 통해 서비스에 다음 정보를 보냅니다:

- **DIDWba**: did:wba 프로토콜 사용을 나타냅니다
- **did**: 신원 확인에 사용되는 클라이언트의 did 식별자.
- **nonce**: 재생 공격을 방지하기 위해 무작위로 생성된 문자열. 각 요청에 대해 고유해야 합니다. 16바이트 무작위 문자열을 권장합니다.
- **timestamp**: 요청이 시작된 시간, 일반적으로 ISO 8601을 사용한 UTC 형식으로 초 단위까지 정확합니다.
- **verification_method**: 서명에 사용된 검증 방법을 식별하며, DID 문서에서 검증 방법의 DID 프래그먼트입니다. 예를 들어, 검증 방법 "did:wba:example.com%3A8800:user:alice#key-1"의 경우 검증 방법의 DID 프래그먼트는 "key-1"입니다.
- **signature**: `nonce`, `timestamp`, 서비스 도메인, 클라이언트 DID에 서명합니다. ECDSA 서명의 경우 R|S 형식을 사용합니다. 다음 필드들을 포함합니다:
  - `nonce`
  - `timestamp`
  - `service` (서비스의 도메인명)
  - `did` (클라이언트의 DID)

클라이언트 요청 예시:

```plaintext
Authorization: DIDWba did="did:wba:example.com%3A8800:user:alice", nonce="abc123", timestamp="2024-12-05T12:34:56Z", verification_method="key-1", signature="base64url(signature_of_nonce_timestamp_service_did)"
```

#### 3.1.2 서명 생성 프로세스

1. 클라이언트는 다음 정보를 포함하는 문자열을 생성합니다:

```json
{ 
  "nonce": "abc123", 
  "timestamp": "2024-12-05T12:34:56Z", 
  "service": "example.com", 
  "did": "did:wba:example.com:user:alice" 
}
```

2. [JCS(JSON Canonicalization Scheme)](https://www.rfc-editor.org/rfc/rfc8785)를 사용하여 JSON 문자열을 정규화하여 정규화된 문자열을 생성합니다.

3. SHA-256 알고리즘을 사용하여 정규화된 문자열을 해시하여 해시값을 생성합니다.

4. 클라이언트의 개인키를 사용하여 해시값에 서명하여 서명값 `signature`를 생성하고 URL-safe Base64로 인코딩합니다.

5. 위 형식으로 `Authorization` 헤더를 구성하여 서비스에 보냅니다.

### 3.2 서비스 검증

#### 3.2.1 요청 헤더 검증

클라이언트의 요청을 받은 후 서비스는 다음 검증을 수행합니다:

1. **타임스탬프 검증**: 요청의 타임스탬프가 합리적인 시간 범위 내에 있는지 확인합니다. 권장 시간 범위는 1분입니다. 타임스탬프가 범위를 벗어나면 요청이 만료된 것으로 간주되고 서비스는 인증 챌린지와 함께 `401 Unauthorized`를 반환합니다.

2. **Nonce 검증**: 요청의 `nonce`가 사용되었거나 존재하는지 확인합니다. `nonce`가 사용되었거나 존재하면 재생 공격으로 간주되고 서비스는 인증 챌린지와 함께 `401 Unauthorized`를 반환합니다.

3. **DID 권한 검증**: 요청의 DID가 서비스 리소스에 액세스할 권한이 있는지 확인합니다. 그렇지 않으면 서비스는 `403 Forbidden`을 반환합니다.

4. **서명 검증**:

- 클라이언트의 DID를 기반으로 DID 문서를 읽습니다.
- 요청의 `verification_method`를 기반으로 DID 문서에서 해당하는 검증 방법을 찾습니다.
- 검증 방법의 공개키를 사용하여 요청의 서명을 검증합니다.

5. **검증 결과**: 서명 검증이 성공하면 요청이 검증을 통과합니다. 그렇지 않으면 서비스는 인증 챌린지와 함께 `401 Unauthorized`를 반환합니다.

#### 3.2.2 서명 검증 프로세스

1. **정보 추출**: `Authorization` 헤더에서 `nonce`, `timestamp`, `service`, `did`, `verification_method`를 추출합니다.

2. **검증 문자열 구축**: 클라이언트가 구성한 것과 동일한 JSON 문자열을 구성합니다:

```json
{ 
    "nonce": "abc123", 
    "timestamp": "2024-12-05T12:34:56Z", 
    "service": "example.com", 
    "did": "did:wba:example.com:user:alice" 
}
```

3. **문자열 정규화**: [JCS(JSON Canonicalization Scheme)](https://www.rfc-editor.org/rfc/rfc8785)를 사용하여 JSON 문자열을 정규화하여 정규화된 문자열을 생성합니다.

4. **해시값 생성**: SHA-256 알고리즘을 사용하여 정규화된 문자열을 해시하여 해시값을 생성합니다.

5. **공개키 획득**: `did`와 `verification_method`를 기반으로 DID 문서에서 해당하는 공개키를 얻습니다.

6. **서명 검증**: 얻은 공개키를 사용하여 `signature`를 검증하여 해당하는 개인키로 생성되었는지 확인합니다.

#### 3.2.3 인증 성공 시 액세스 토큰 반환

서비스가 클라이언트의 신원을 성공적으로 확인한 후 응답에서 액세스 토큰을 반환할 수 있습니다. 액세스 토큰은 JWT(JSON Web Token) 형식을 권장합니다. 그러면 클라이언트는 후속 요청에서 액세스 토큰을 전달할 수 있으며, 서비스는 매번 클라이언트의 신원을 확인할 필요 없이 액세스 토큰만 확인하면 됩니다.

다음 생성 프로세스는 사양에서 요구되지 않지만 참고용으로 제공됩니다. 구현자는 필요에 따라 정의하고 구현할 수 있습니다.

JWT 생성 방법은 [RFC7519](https://www.rfc-editor.org/rfc/rfc7519)를 참조합니다.

1. **액세스 토큰 생성**

서비스가 **JWT(JSON Web Token)**를 액세스 토큰 형식으로 사용한다고 가정하면, JWT는 일반적으로 다음 필드들을 포함합니다:

- **header**: 서명 알고리즘을 지정
- **payload**: 사용자 관련 정보를 저장
- **signature**: `header`와 `payload`에 서명하여 무결성을 보장

페이로드는 다음 필드들을 포함할 수 있습니다(필요에 따라 다른 필드들을 추가할 수 있습니다):

```json
{
  "sub": "did:wba:example.com:user:alice",  // 사용자 DID 
  "iat": "2024-12-05T12:34:56Z",            // 발행 시간
  "exp": "2024-12-06T12:34:56Z",            // 만료 시간
}
```

구현자는 범위 사용이나 IP 주소 바인딩과 같은 다른 보안 조치를 페이로드에 추가할 수 있습니다.

2. **액세스 토큰 반환**
생성된 헤더, 페이로드, 서명을 연결하고 URL-safe Base64로 인코딩하여 최종 액세스 토큰을 형성합니다. 그런 다음 Authorization 헤더를 통해 액세스 토큰을 반환합니다:

```plaintext
Authorization: Bearer <access_token>
```

3. **클라이언트 액세스 토큰 전송**
클라이언트는 Authorization 헤더 필드를 통해 서비스에 액세스 토큰을 보냅니다:

```plaintext
Authorization: Bearer <access_token>
```

4. **서비스 액세스 토큰 검증**
클라이언트의 요청을 받은 후 서비스는 Authorization 헤더에서 액세스 토큰을 추출하여 서명 검증, 만료 시간 검증, 페이로드 필드 검증을 포함한 검증을 수행합니다. 검증 방법은 [RFC7519](https://www.rfc-editor.org/rfc/rfc7519)를 기반으로 합니다.

#### 3.2.4 오류 처리

#### 3.2.4.1 401 응답

서버가 서명 검증에 실패하고 클라이언트가 요청을 다시 시작하도록 요구할 때 401 응답을 반환해야 합니다.

또한, 서버가 클라이언트 요청 Nonce 기록을 지원하지 않거나 클라이언트가 항상 서버 생성 Nonce를 서명에 사용하도록 요구하는 경우, 각 초기 클라이언트 요청에 대해 Nonce가 포함된 인증 챌린지와 함께 401 응답을 반환할 수 있습니다. 그러나 이는 클라이언트 요청 수를 증가시키므로 구현자는 이 접근법을 사용할지 선택할 수 있습니다.

오류 정보는 `WWW-Authenticate` 헤더 필드를 통해 반환됩니다. 예:

```plaintext
WWW-Authenticate: Bearer error="invalid_nonce", error_description="Nonce has already been used. Please provide a new nonce.", nonce="xyz987"
```

다음 필드들을 포함합니다:

- **error**: 필수 필드, 오류 유형, 다음 문자열 값들을 포함합니다:
  - **invalid_request**: 요청 형식 오류, 필수 필드 누락, 또는 지원되지 않는 매개변수 포함.
  - **invalid_nonce**: Nonce가 이미 사용되었습니다.
  - **invalid_timestamp**: 타임스탬프가 범위를 벗어났습니다.
  - **invalid_did**: DID 형식 오류, 또는 해당하는 DID 문서를 찾을 수 없습니다.
  - **invalid_signature**: 서명 검증 실패.
  - **invalid_verification_method**: 검증 방법을 기반으로 해당하는 공개키를 찾을 수 없습니다.
  - **invalid_access_token**: 액세스 토큰 검증 실패.
  - **forbidden_did**: DID가 서버 리소스에 액세스할 권한이 없습니다.
- **error_description**: 선택적 필드, 오류 설명.
- **nonce**: 선택적 필드, 서버 생성 무작위 문자열. 존재하는 경우 클라이언트는 이 Nonce를 사용하여 서명을 재생성하고 요청을 다시 시작해야 합니다.

클라이언트가 401 응답을 받으면, 응답에 Nonce가 포함되어 있으면 클라이언트는 서버의 Nonce를 사용하여 서명을 재생성하고 요청을 다시 시작해야 합니다. 응답에 Nonce가 포함되지 않은 경우 클라이언트는 클라이언트 생성 Nonce를 사용하여 서명을 재생성하고 요청을 다시 시작해야 합니다.

클라이언트와 서버 구현 모두 무한 루프를 방지하기 위해 재시도 횟수를 제한해야 한다는 점이 중요합니다.

#### 3.2.4.2 403 응답

서버 인증이 성공했지만 DID가 서버 리소스에 액세스할 권한이 없는 경우 403 응답을 반환해야 합니다.

## 4. did:wba 방법과 JSON 형식 데이터 기반 크로스 플랫폼 신원 인증 프로세스

이전 장에서는 did:wba 방법과 HTTP 프로토콜을 기반으로 한 크로스 플랫폼 신원 인증 프로세스를 소개했습니다. 그러나 did:wba 방법을 사용한 신원 인증은 전송 프로토콜에 구애받지 않습니다. 여기서는 did:wba 방법과 JSON 형식 데이터를 기반으로 한 크로스 플랫폼 신원 인증 프로세스를 정의하며, 이는 JSON 형식을 통신에 사용하는 시나리오에서 사용할 수 있습니다. 이 사양은 JSON을 사용한 신원 인증 프로세스만 설명하며, JSON을 전달하는 방법은 구현자가 결정하도록 맡깁니다.

이론적으로 다른 데이터 형식을 기반으로 한 프로토콜도 did:wba 방법에 대한 지원을 추가할 수 있습니다.

전체 프로세스는 다음과 같습니다:

```mermaid
sequenceDiagram
    participant Agent A Client
    participant Agent B Server 
    participant Agent A DID Sever

    Note over Agent A Client,Agent B Server: 초기 요청

    Agent A Client->>Agent B Server: Authentication Request Info: DID,Signature
    Agent B Server->>Agent A DID Sever: Get DID Document
    Agent A DID Sever->>Agent B Server: DID Document

    Note over Agent B Server: 인증

    Agent B Server->>Agent A Client: Authentication Response Info: access token

    Note over Agent A Client, Agent B Server: 후속 요청

    Agent A Client->>Agent B Server: Request: access token
    Agent B Server->>Agent A Client: Response
```

### 4.1 초기 요청

클라이언트가 서버에 처음 요청을 시작할 때 다음 방법에 따라 인증을 수행해야 합니다.

#### 4.1.1 인증 정보 데이터 형식

클라이언트는 서버에 다음 정보를 보내야 합니다:

- **did**: 신원 확인에 사용되는 요청의 클라이언트 DID 식별자.
- **nonce**: 재생 공격을 방지하기 위해 무작위로 생성된 문자열. 각 요청에 대해 고유해야 합니다. 16바이트 무작위 문자열을 권장합니다.
- **timestamp**: 요청이 시작된 시간, 일반적으로 ISO 8601 UTC 형식으로 초 단위까지 정확합니다.
- **verification_method**: 서명에 사용된 검증 방법을 식별하며, DID 문서에서 검증 방법의 DID 프래그먼트입니다. 예를 들어, 검증 방법 ID "did:wba:example.com%3A8800:user:alice#key-1"의 경우 검증 방법의 DID 프래그먼트는 "key-1"입니다.
- **signature**: `nonce`, `timestamp`, 서버 도메인, 클라이언트 DID에 서명합니다. ECDSA 서명의 경우 R|S 형식을 사용합니다. 다음 필드들을 포함합니다:
  - `nonce`: 무작위로 생성된 문자열
  - `timestamp`: 요청이 시작된 시간
  - `service`: 서버의 도메인명 (참고: 도메인명에는 포트가 포함되지 않음)
  - `did`: 클라이언트의 DID

클라이언트 요청 예시:

```json
{
  "did": "did:wba:example.com%3A8800:user:alice",
  "nonce": "abc123",
  "timestamp": "2024-12-05T12:34:56Z",
  "verification_method": "key-1",
  "signature": "base64url(signature_of_nonce_timestamp_service_did)"
}
```

인증 정보는 별도의 요청으로 보내거나 비즈니스 요청 데이터와 함께 보낼 수 있습니다.

#### 4.1.2 서명 생성 프로세스

[섹션 3.1.2 서명 생성 프로세스](#312-서명-생성-프로세스)와 동일합니다.

### 4.2 서버 검증

#### 4.2.1 인증 요청 검증

검증 프로세스는 [섹션 3.2.1 요청 헤더 검증](#321-요청-헤더-검증)과 동일합니다. 차이점은 요청 데이터에서 did, nonce, timestamp, verification_method, signature 필드를 추출해야 한다는 것입니다.

검증이 통과한 후 서버는 액세스 토큰을 반환할 수 있으며, 클라이언트는 후속 요청에서 액세스 토큰을 전달합니다. 서버는 매번 클라이언트의 신원을 확인할 필요 없이 액세스 토큰만 확인하면 됩니다.

액세스 토큰 생성 방법은 [섹션 3.2.4 인증 성공 시 액세스 토큰 반환](#324-인증-성공-시-액세스-토큰-반환)과 동일합니다.

응답 JSON 예시:

```json
{
  "code": 200,
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
}
```

필드 설명:

- **code**: 상태 코드, HTTP 상태 코드 사용.
- **access_token**: 인증 성공 후 반환되는 액세스 토큰.

클라이언트가 200 응답을 받으면 후속 요청에서 액세스 토큰을 전달할 수 있습니다.

후속 요청 예시:

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
}
```

#### 4.2.2 오류 처리

오류 처리는 [섹션 3.2.4 오류 처리](#324-오류-처리)와 동일한 원칙을 따릅니다.

JSON 형식의 401 응답 예시:

```json
{
  "code": 401,
  "error": "invalid_nonce",
  "error_description": "Nonce has already been used. Please provide a new nonce.",
  "nonce": "1234567890"
}
```

JSON 형식의 403 응답 예시:

```json
{
  "code": 403,
  "error": "forbidden_did",
  "error_description": "did not have permission to access the resource."
}
```

## 5 인간 권한 부여와 에이전트 자동 권한 부여 구분

그리 중요하지 않은 요청의 경우 사용자 에이전트가 자동으로 권한을 부여할 수 있습니다. 예를 들어, 호텔 에이전트에 액세스하여 호텔 정보를 읽을 때는 인간의 수동 확인이 필요하지 않으며, 사용자 에이전트가 인간을 대신하여 요청을 시작할 수 있습니다.

호텔 객실 예약과 같은 중요한 요청의 경우, 호텔 에이전트는 인간의 수동 확인을 요구할 수 있습니다. 사용자 에이전트가 예약 요청을 시작할 때 humanAuthorization에서 정의된 방법을 사용하여 서명해야 합니다. 이때 사용자 에이전트는 예약 요청을 진행하기 전에 수동 확인을 위해 인간에게 권한 부여를 요청해야 합니다.

서명 방법은 [섹션 3.1 초기 요청](#31-초기-요청)과 동일합니다.

사용자 에이전트 개발자는 humanAuthorization의 개인키를 안전하게 보관하고 사용자의 생체 인증(지문, 얼굴 인식 등) 후에만 humanAuthorization을 서명에 사용할 수 있도록 하는 등의 접근 제어를 구현해야 합니다.

에이전트는 에이전트 설명 문서에서 문서나 인터페이스의 권한 부여 유형을 정의할 수 있습니다. 기본적으로 모든 일반 권한 부여로 충분합니다. 요청이 인간의 수동 권한 부여를 요구하는 경우 문서에서 명시적으로 정의되어야 합니다(정의 방법은 에이전트 설명 사양 참조).

## 6 프라이버시 보호 전략

프라이버시 보호는 분산 네트워크에서 중요합니다. 예를 들어, 악성 소프트웨어가 사용자의 DID를 사용하여 그들의 행동을 기록하고 추적하여 프라이버시 침해로 이어질 수 있습니다.

따라서 DID 제공자가 다중 DID 전략을 채택할 것을 권장합니다. 단일 사용자에 대해 여러 DID가 생성됩니다. 각 DID는 서로 다른 역할과 권한을 가지며, 서로 다른 키 쌍을 사용하여 프라이버시 보호와 세밀한 접근 제어를 달성합니다.

예를 들어, 사용자를 위해 메인 DID를 생성할 수 있으며, 이는 일반적으로 변경되지 않고 사회적 관계 유지에 사용됩니다. 또한 쇼핑, 음식 주문, 티켓 예약과 같은 시나리오를 위해 일련의 서브 DID를 생성할 수 있습니다. 이러한 서브 DID는 메인 DID에 종속되며 주기적으로 만료된 DID를 비활성화하고 새로운 DID를 신청하여 프라이버시와 보안 보호를 향상시킬 수 있습니다.

## 7 보안 고려사항

구현자는 구현할 때 다음 보안 문제를 고려해야 합니다:

1. 키 관리

- DID에 해당하는 개인키는 **반드시** 적절히 보관되어야 하며 절대 유출되어서는 안 됩니다. 또한 개인키에 대한 정기적인 갱신 메커니즘을 **구축해야** 합니다.
- 사용자는 **여러 DID를 생성해야** 하며, 각각은 서로 다른 역할과 권한을 가지고 서로 다른 키 쌍을 사용하여 세밀한 접근 제어를 달성해야 합니다.

2. 공격 방지 조치

- 서버는 **반드시** 요청의 Nonce 기록을 보관하여 재생 공격을 방지해야 합니다.
- 서버는 **반드시** 요청의 타임스탬프를 확인하여 시간 롤백 공격을 방지해야 합니다. 일반적으로 서버의 nonce 캐시 지속 시간은 **타임스탬프 만료 지속 시간보다 길어야** 합니다.
- Nonce를 생성할 때 **반드시** 운영체제에서 제공하는 보안 난수 생성기를 사용하여 현대 암호학 보안 표준과 규범을 충족해야 합니다. 예를 들어, Python의 `secrets` 모듈을 사용하여 보안 난수를 생성할 수 있습니다.

3. 전송 보안

- DID 문서를 검색할 때 서버는 **보안을 향상시키기 위해** DNS-over-HTTPS(DoH) 프로토콜을 사용해야 합니다.
- 전송 프로토콜은 **반드시** HTTPS를 사용해야 하며, 클라이언트는 **반드시** 상대방의 CA 인증서가 신뢰할 수 있는지 엄격히 확인해야 합니다.

4. 토큰 보안

- 클라이언트와 서버 모두 **반드시** Access Token을 적절히 보관해야 하며 **반드시** 합리적인 만료 시간을 설정해야 합니다.
- 토큰 남용을 방지하기 위해 클라이언트 IP 바인딩, User-Agent 바인딩 등과 같은 추가 보안 정보를 Access Token에 **포함해야** 합니다.

## 8. 사용 사례

1. 사용 사례 1: 사용자가 지능형 비서를 통해 다른 웹사이트의 파일에 액세스

Alice는 example.com에 파일을 저장했으며 지능형 비서를 통해 액세스하고 싶어합니다. 먼저 Alice는 지능형 비서에서 did:wba 기반 DID를 생성하고, example.com에 로그인하여 이 DID를 계정과 연결하고 DID에 파일 액세스 권한을 부여합니다. 설정 후 지능형 비서는 이 DID를 사용하여 example.com에 로그인할 수 있습니다. 인증이 성공하면 example.com은 지능형 비서가 Alice의 저장된 파일에 액세스할 수 있도록 허용합니다. 이 DID는 지능형 비서가 여러 플랫폼에서 파일에 액세스할 수 있도록 다른 웹사이트에도 구성할 수 있습니다.

2. 사용 사례 2: 사용자가 지능형 비서를 통해 제3자 서비스 API 호출

Alice는 지능형 비서를 통해 "example"이라는 제3자 서비스의 API를 호출하고 싶어합니다. 먼저 Alice는 지능형 비서에서 did:wba 기반 DID를 생성하고 이를 사용하여 example 플랫폼의 서비스를 구독합니다. example 서비스는 DID를 인증하고 Alice를 구매자로 확인하며 그녀의 DID를 기록합니다. 인증 후 Alice는 지능형 비서를 사용하여 이 DID로 example 서비스 API를 호출할 수 있습니다.

> 참고: 현재 사용 사례에서는 클라이언트-서버 인증이 설명되지 않았지만, 이 프로세스는 여전히 효과적으로 작동할 수 있습니다.

## 9. 요약

이 사양은 did:web 방법 사양을 기반으로 DID 문서 제약 조건, 크로스 플랫폼 인증 프로세스, 에이전트 설명 서비스를 추가하여 새로운 방법명 did:wba(Web-Based Agent)를 제안합니다. 우리는 did:wba 방법과 HTTP 프로토콜을 기반으로 한 크로스 플랫폼 인증 프로세스를 설계하고 자세한 구현 방법을 제공했습니다.

앞으로 에이전트 능력과 프로토콜 설명 서비스 엔드포인트 추가, 에이전트 간 양방향 인증 프로세스 등을 통해 did:wba 방법을 더욱 개선할 예정입니다.

## 참고문헌

1. **DID-CORE**. Decentralized Identifiers (DIDs) v1.0. Manu Sporny; Amy Guy; Markus Sabadello; Drummond Reed. W3C. 19 July 2022. W3C Recommendation. Retrieved from [https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/)

2. **did:web**. Retrieved from [https://w3c-ccg.github.io/did-method-web/](https://w3c-ccg.github.io/did-method-web/)

3. **JSON Canonicalization Scheme (JCS)**. Retrieved from [https://www.rfc-editor.org/rfc/rfc8785](https://www.rfc-editor.org/rfc/rfc8785)

4. **RFC 1035**. Domain names - implementation and specification. P. Mockapetris. IETF. November 1987. Internet Standard. Retrieved from [https://www.rfc-editor.org/rfc/rfc1035](https://www.rfc-editor.org/rfc/rfc1035)

5. **RFC 1123**. Requirements for Internet Hosts - Application and Support. R. Braden, Ed. IETF. October 1989. Internet Standard. Retrieved from [https://www.rfc-editor.org/rfc/rfc1123](https://www.rfc-editor.org/rfc/rfc1123)

6. **RFC 2119**. Key words for use in RFCs to Indicate Requirement Levels. S. Bradner. IETF. March 1997. Best Current Practice. Retrieved from [https://www.rfc-editor.org/rfc/rfc2119](https://www.rfc-editor.org/rfc/rfc2119)

7. **RFC 2181**. Clarifications to the DNS Specification. R. Elz; R. Bush. IETF. July 1997. Proposed Standard. Retrieved from [https://www.rfc-editor.org/rfc/rfc2181](https://www.rfc-editor.org/rfc/rfc2181)

8. **RFC 8174**. Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words. B. Leiba. IETF. May 2017. Best Current Practice. Retrieved from [https://www.rfc-editor.org/rfc/rfc8174](https://www.rfc-editor.org/rfc/rfc8174)

9. **RFC 8484**. DNS Queries over HTTPS (DoH). P. Hoffman; P. McManus. IETF. October 2018. Proposed Standard. Retrieved from [https://www.rfc-editor.org/rfc/rfc8484](https://www.rfc-editor.org/rfc/rfc8484)

10. **DID Use Cases**. Decentralized Identifier Use Cases. Joe Andrieu; Kim Hamilton Duffy; Ryan Grant; Adrian Gropper. W3C. 24 June 2021. W3C Note. Retrieved from [https://www.w3.org/TR/did-use-cases/](https://www.w3.org/TR/did-use-cases/)

11. **DID Extensions**. Decentralized Identifier Extensions. Orie Steele; Manu Sporny. W3C. 24 June 2021. W3C Note. Retrieved from [https://www.w3.org/TR/did-extensions/](https://www.w3.org/TR/did-extensions/)

12. **DID Extension Properties**. Decentralized Identifier Extension Properties. Orie Steele; Manu Sporny. W3C. 24 June 2021. W3C Note. Retrieved from [https://www.w3.org/TR/did-extensions-properties/](https://www.w3.org/TR/did-extensions-properties/)

13. **DID Extension Methods**. Decentralized Identifier Extension Methods. Orie Steele; Manu Sporny. W3C. 24 June 2021. W3C Note. Retrieved from [https://www.w3.org/TR/did-extensions-methods/](https://www.w3.org/TR/did-extensions-methods/)

14. **DID Extension Resolution**. Decentralized Identifier Extension Resolution. Orie Steele; Manu Sporny. W3C. 24 June 2021. W3C Note. Retrieved from [https://www.w3.org/TR/did-extensions-resolution/](https://www.w3.org/TR/did-extensions-resolution/)

15. **Controller Document**. Controller Document. Manu Sporny; Markus Sabadello. W3C. 24 June 2021. W3C Note. Retrieved from [https://www.w3.org/TR/controller-document/](https://www.w3.org/TR/controller-document/)

## 저작권 고지

Copyright (c) 2024 GaoWei Chang  
이 파일은 [MIT License](./LICENSE) 하에 배포됩니다. 자유롭게 사용하고 수정할 수 있지만 이 저작권 고지를 유지해야 합니다.
