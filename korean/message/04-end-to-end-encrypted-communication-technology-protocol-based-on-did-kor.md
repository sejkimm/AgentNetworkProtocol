# End-to-End Encrypted Communication Technology Protocol Based on did

## 1. 배경

End-to-End Encryption (E2EE)은 송신자와 수신자 간 전송 중에 정보가 암호화된 상태로 유지되어, 인터넷 서비스 제공업체, 중간자 공격자, 서버 자체를 포함한 제3자가 평문 내용에 무단으로 접근하는 것을 방지하는 통신 암호화 방법입니다.

[AgentNetworkProtocol 기술백서](01-AgentNetworkProtocol%20Technical%20White%20Paper.md)에서 우리는 DID를 기반으로 한 종단 간 암호화 통신 기술을 제안했습니다. 이 문서는 해당 기술의 구체적인 구현 내용을 상세히 설명합니다.

## 2. 솔루션 개요

이 솔루션은 실제로 검증된 TLS와 블록체인 등의 고보안 기술을 활용합니다. 이러한 기술들을 결합하여 우리는 DID를 기반으로 한 종단 간 암호화 통신 체계를 설계했으며, 이는 서로 다른 두 플랫폼의 사용자 간 보안 암호화 통신에 적합합니다.

우리는 WebSocket 프로토콜 위에 DID 기반 메시지 라우팅 메커니즘과 단기 키 협상 메커니즘 세트를 설계했습니다. DID를 보유한 양측은 각자의 DID 문서에 있는 공개 키와 자신의 개인 키를 사용하여 ECDHE (Elliptic Curve Diffie-Hellman Ephemeral)를 통해 단기 키 협상을 수행할 수 있습니다. 이후 메시지는 유효 기간 내에서 협상된 키를 사용하여 암호화되어 보안 통신을 보장합니다. ECDHE는 메시지가 제3자 메시지 프록시와 같은 중개자를 통해 전달되더라도 악의적으로 해독될 수 없음을 보장합니다.

우리가 WebSocket 프로토콜을 선택한 이유는 인터넷에서 널리 사용되고 많은 이용 가능한 인프라가 있기 때문이며, 이는 솔루션의 초기 채택에 중요합니다. 또한 WebSocket 위에 종단 간 암호화 체계를 설계했기 때문에 WebSocket Secure 프로토콜을 사용할 필요가 없어 중복적인 암호화 및 복호화 과정을 피할 수 있습니다.

현재 솔루션은 본질적으로 애플리케이션 계층 암호화를 사용하여 전송 계층 암호화를 대체합니다. 이 접근 방식은 기존 인프라를 활용하면서 프로토콜 채택의 어려움을 줄입니다.

전체 과정은 다음 다이어그램에 설명되어 있습니다:

![end-to-end-encryption](../../images/end-to-end-encryption-process.png)

**참고:** 제3자 메시지 서비스는 존재하지 않을 수 있으며, 사용자는 자신의 메시지 서비스를 사용할 수 있습니다.

현재 우리는 양방향 프로토콜인 WebSocket 프로토콜만 지원합니다. 향후 더 많은 시나리오로 확장하기 위해 HTTP 프로토콜을 지원할 계획입니다. 또한 더 많은 시나리오에서 사용할 수 있도록 전송 계층에서 종단 간 암호화 체계를 구현하는 것도 고려하고 있습니다.

## 3. 암호화 통신 과정

서로 다른 두 플랫폼의 사용자가 있다고 가정합니다. 하나는 A (DID-A)이고 다른 하나는 B (DID-B)입니다. A와 B 모두 DID SERVER에서 서로의 DID 문서를 획득할 수 있으며, 이 문서에는 각자의 공개 키가 포함되어 있습니다.

암호화 통신을 수행하기 위해 A와 B는 먼저 단기 키 생성 과정을 시작해야 합니다. 단기 키를 생성하는 과정은 TLS가 임시 암호화 키를 생성하는 방법과 유사합니다. 이 키는 유효 기간이 있으며, 만료되기 전에 단기 키 생성 과정을 다시 시작하여 키를 생성하고 업데이트해야 합니다.

A와 B가 협상된 단기 키를 보유하면, A가 B에게 메시지를 보내고 싶을 때 키를 사용하여 메시지를 암호화한 다음 메시지 서버를 통해 메시지 전송 프로토콜을 통해 B에게 보낼 수 있습니다. 메시지를 받은 B는 메시지의 키 ID를 사용하여 이전에 저장된 단기 키를 찾아 암호화된 메시지를 복호화합니다. 해당 키를 찾을 수 없거나 만료된 경우 오류 메시지를 보내 A에게 단기 키 업데이트 과정을 시작하도록 알립니다. 단기 키가 업데이트된 후 메시지가 다시 전송됩니다.

```plaintext
Client (A)                                      Client (B)
|                                                 |
| -- 단기 키 생성 과정 시작 --> |
|                                                 |
|      (임시 암호화 키 생성)          |
|                                                 |
| <---- 임시 키 생성됨 ----                |
|                                                 |
|       (키에 만료 시간 있음)              |
|                                                 |
|      (키 유효성 모니터링)                     |
|                                                 |
|   (만료 전에 생성 과정 재시작) |
|                                                 |
| (A와 B가 이제 협상된 단기 키를 보유)  |
|                                                 |
| ---- 암호화된 메시지 ---->                    |
|                                                 |
|     (단기 키를 사용하여 메시지 암호화)      |
|     (메시지 서버를 통해 전송)                   |
|                                                 |
| <---- 암호화된 메시지 수신 ----            |
|                                                 |
|     (키 ID를 사용하여 저장된 키 찾기)              |
|     (메시지 복호화)                           |
|      (키를 찾을 수 없거나 만료됨)              |
|                                                 |
| <---- 또는 오류 메시지 ----                     |
|                                                 |
|      (A에게 단기 키 업데이트 알림)        |
|                                                 |
```

## 4. 단기 키 협상 과정

단기 키를 생성하는 과정은 TLS 1.3의 키 교환 과정과 유사하며, ECDHE (Elliptic Curve Diffie-Hellman Ephemeral)를 사용합니다. 이는 타원 곡선을 기반으로 한 키 교환 프로토콜입니다. Elliptic Curve Cryptography (ECC)와 임시 키를 결합한 Diffie-Hellman 키 교환 프로토콜의 변형으로, 안전하지 않은 네트워크에서 암호화 키를 안전하게 교환하여 보안 통신을 달성합니다.

### ECDHE 과정의 간략한 설명

1. **키 쌍 생성:**
   - 클라이언트와 서버 모두 개인 키와 공개 키를 포함한 임시 타원 곡선 키 쌍을 생성합니다.

2. **공개 키 교환:**
   - 클라이언트는 생성된 공개 키를 서버에 보냅니다.
   - 서버는 생성된 공개 키를 클라이언트에 보냅니다.

3. **공유 비밀 계산:**
   - 클라이언트는 자신의 개인 키와 서버에서 받은 공개 키를 사용하여 공유 비밀을 계산합니다.
   - 서버는 자신의 개인 키와 클라이언트에서 받은 공개 키를 사용하여 공유 비밀을 계산합니다.
   - Elliptic Curve Diffie-Hellman 알고리즘의 특성으로 인해 두 계산 모두 동일한 공유 비밀을 생성합니다.

### TLS 과정과의 차이점

- 전체 과정은 `SourceHello`, `DestinationHello`, `Finished`의 세 메시지만 포함하며, 이는 TLS의 `ClientHello`, `ServerHello`, `Finished`에 해당합니다. 우리 과정에서는 클라이언트와 서버 대신 source와 destination만 있습니다.
- `EncryptedExtensions`, `Certificate`, `CertificateVerify`와 같은 다른 메시지는 필요하지 않습니다. 구체적으로:
  - `EncryptedExtensions`는 현재 필요하지 않지만 나중에 암호화 확장을 전달하기 위해 추가될 수 있습니다.
  - `Certificate`와 `CertificateVerify`는 불필요합니다. 이러한 메시지는 주로 서버의 공개 키가 안전함을 보장하기 때문입니다. 우리는 DID 주소와 공개 키 관계를 통해 DID에 해당하는 공개 키의 정확성을 확인합니다. 각 DID는 정확히 하나의 공개 키에 해당하며 그 반대도 마찬가지입니다.
- `Finished`는 더 이상 핸드셰이크 메시지를 해시하고 암호화하지 않습니다. `SourceHello`와 `DestinationHello`에 이미 메시지 무결성을 보장하는 서명이 포함되어 있기 때문입니다.
- `Source`와 `Destination`은 동시에 여러 단기 키 협상을 시작할 수 있어 여러 키가 동시에 존재하여 서로 다른 유형의 메시지를 암호화할 수 있습니다.

### 전체 과정 다이어그램

```plaintext
Client (A)                                          Server (B)
   |                                                    |
   |  ---------------- SourceHello ---------------->    |
   |                                                    |
   |                  (공개 키와 서명 포함)|
   |                                                    |
   |                                                    |
   |  <------------- DestinationHello ------------      |
   |                                                    |
   |                  (공개 키와 서명 포함)|
   |                                                    |
   |                                                    |
   |  -------- Finished (verify_data 포함) -------> |
   |                                                    |
   |  <-------- Finished (verify_data 포함) --------|
   |                                                    |
   |                                                    |
```

## 5. 프로토콜 정의

우리 프로토콜은 WebSocket을 기반으로 설계되었으며 JSON 형식을 사용합니다. DID 사용자의 메시지 수신 주소는 DID 문서의 "service" 필드에 `messageService` 엔드포인트 유형으로 저장됩니다. (DID all 방법 설계 사양 참조)

### 5.1 SourceHello 메시지

`SourceHello` 메시지는 암호화 통신 핸드셰이크를 시작하는 데 사용됩니다. 소스의 신원 정보, 공개 키, 지원되는 암호화 매개변수, 세션 ID, 버전 정보 및 메시지의 무결성과 인증을 보장하는 메시지 서명을 포함합니다.

**메시지 예시:**

```json
{
  "version": "1.0",
  "type": "sourceHello",
  "timestamp": "2024-05-27T12:00:00.123Z",
  "messageId": "randomstring",
  "sessionId": "abc123session",
  "sourceDid": "did:example:123456789abcdefghi",
  "destinationDid": "did:example:987654321abcdefghi",
  "verificationMethod": {
    "id": "did:example:987654321abcdefghi#keys-1",
    "type": "EcdsaSecp256r1VerificationKey2019",
    "publicKeyHex": "04a34b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6"
  },
  "random": "b7e4b4d5f6c4e4f7a6b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6",
  "supportedVersions": ["1.0", "0.9"],
  "cipherSuites": [
    "TLS_AES_128_GCM_SHA256",
    "TLS_AES_256_GCM_SHA384",
    "TLS_CHACHA20_POLY1305_SHA256"
  ],
  "supportedGroups": [
    "secp256r1",
    "secp384r1",
    "secp521r1"
  ],
  "keyShares": [
    {
      "group": "secp256r1",
      "expires": 864000,
      "keyExchange": "0488b21e000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
    },
    {
      "group": "secp384r1",
      "expires": 864000,
      "keyExchange": "0488b21e000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
    }
  ],
  "proof": {
    "type": "EcdsaSecp256r1Signature2019",
    "created": "2024-05-27T10:51:55Z",
    "verificationMethod": "did:example:987654321abcdefghi#keys-1",
    "proofValue": "eyJhbGciOiJFUzI1NksifQ..myEaggpdg0-GflPHibRZWfDEdDOqzZzBcBM5TKvaUzCUSv1_7anUvtgdFXMd12E_qM6RmAAaSWWBGwLY-Srvyg"
  }
}
```

#### 5.1.1 필드 설명

- **version**: 문자열, 현재 프로토콜의 버전 번호.
- **type**: 문자열, 메시지 유형, 예: "SourceHello".
- **timestamp**: 메시지 전송 시간, 밀리초 정밀도를 가진 ISO 8601 형식의 UTC 타임스탬프.
- **messageId**: 고유 메시지 ID, 16자 무작위 문자열.
- **sessionId**: 문자열, 세션 ID, 16자 무작위 문자열, 단일 단기 세션 협상 내에서 유효.
- **sourceDid**: 문자열, 메시지의 소스, 즉 발신자의 DID. 항상 발신자 자신의 DID.
- **destinationDid**: 문자열, 목적지, 즉 수신자의 DID. 항상 수신자의 DID.
- **verificationMethod**: 발신자의 DID에 해당하는 공개 키.
  - **id**: 문자열, 검증 방법 ID.
  - **type**: 문자열, DID 사양에서 정의한 공개 키 유형.
  - **publicKeyHex**: 문자열, 공개 키의 16진수 표현.
- **random**: 문자열, 핸드셰이크 과정의 고유성을 보장하는 32자 무작위 문자열, 키 교환에 참여.
- **supportedVersions**: 배열, 발신자가 지원하는 프로토콜 버전 목록.
- **cipherSuites**: 배열, 지원되는 암호 모음 목록. 현재 TLS_AES_128_GCM_SHA256 지원.
- **supportedGroups**: 배열, 지원되는 타원 곡선 그룹 목록.
- **keyShares**: 배열, 여러 공개 키 교환 정보 포함.
  - **group**: 문자열, 사용된 타원 곡선 그룹. 현재 secp256r1 지원.
  - **keyExchange**: 문자열, 발신자가 생성한 키 교환용 공개 키의 16진수 표현.
  - **expires**: 숫자, 최종 암호화 키의 유효 기간(초). 키 만료로 인한 협상 실패를 방지하기 위해 상대방에게 유효 기간을 전달.
- **proof**:
  - **type**: 문자열, 서명 유형.
  - **created**: 문자열, 서명 생성 시간, 초 정밀도를 가진 ISO 8601 형식의 UTC 타임스탬프.
  - **verificationMethod**: 서명에 사용된 검증 방법의 ID, 최상위 `verificationMethod` 필드 참조.
  - **proofValue**: 발신자의 개인 키를 사용한 메시지 서명, 메시지의 무결성 보장.

#### 5.1.2 proofValue 생성 과정

1. **`sourceHello` 메시지의 모든 필드 구성**, `proof` 사전의 `proofValue` 필드 제외.
2. **`proofValue` 없는 메시지를 JSON 문자열로 변환**, 쉼표와 콜론을 구분자로 사용하고 키 정렬.
3. **JSON 문자열을 UTF-8 바이트로 인코딩**.
4. **ECDSA 알고리즘과 SHA-256을 사용하여 개인 키로 바이트 데이터 서명**.
5. **생성된 서명 값을 JSON 메시지의 `proof` 사전의 `proofValue` 필드에 추가**.

**Python 예시:**

```python
# 1. proofValue 필드를 제외한 JSON 메시지의 모든 필드 생성
msg = {
    # 기타 필요한 필드
    "proof": {
        "type": "EcdsaSecp256r1Signature2019",
        "created": "2024-05-27T10:51:55Z",
        "verificationMethod": "did:example:123456789abcdefghi#keys-1"
        # proofValue 필드 제외
    }
}

# 2. msg를 JSON 문자열로 변환, 키로 정렬, 쉼표와 콜론을 구분자로 사용
msg_str = JSON.stringify(msg, separators=(',', ':'), sort_keys=True)

# 3. JSON 문자열을 UTF-8 바이트로 인코딩
msg_bytes = UTF8.encode(msg_str)

# 4. ECDSA와 SHA-256을 사용하여 바이트 데이터 서명
signature = ECDSA.sign(msg_bytes, private_key, algorithm=SHA-256)

# 5. 서명 값을 JSON 메시지의 proof 사전의 proofValue 필드에 추가
msg["proof"]["proofValue"] = Base64.urlsafe_encode(signature)
```

#### 5.1.3 SourceHello 메시지 검증

1. **메시지 구문 분석**: 수신자가 `SourceHello` 메시지를 구문 분석하고 각 필드를 추출합니다.
2. **DID와 공개 키 검증**: `sourceDid`와 `verificationMethod`에서 공개 키를 읽습니다. DID all 방법 설계 사양에서 정의한 DID 생성 방법을 사용하여 공개 키에서 DID를 생성하고 `sourceDid`와 일치하는지 확인합니다.
3. **서명 검증**: `sourceDid`에 해당하는 공개 키를 사용하여 `proof` 필드의 서명을 검증합니다.
4. **기타 필드 검증**: `random` 필드의 무작위성을 확인하여 재생 공격을 방지합니다. proof의 `created` 필드를 확인하여 서명 시간이 만료되지 않았는지 확인합니다.

### 5.2 DestinationHello 메시지

`DestinationHello` 메시지는 목적지에서 키 교환 핸드셰이크를 시작하기 위해 보냅니다. 목적지의 신원 정보, 공개 키, 협상된 암호화 매개변수, 세션 ID, 버전 정보 및 메시지의 무결성과 인증을 보장하는 메시지 서명을 포함합니다.

**메시지 예시:**

```json
{
  "version": "1.0",
  "type": "destinationHello",
  "timestamp": "2024-05-27T12:00:00Z",
  "messageId": "randomstring",
  "sessionId": "abc123session",
  "sourceDid": "did:example:987654321abcdefghi",
  "destinationDid": "did:example:123456789abcdefghi",
  "verificationMethod": {
    "id": "did:example:987654321abcdefghi#keys-1",
    "type": "EcdsaSecp256r1VerificationKey2019",
    "publicKeyHex": "04a34b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6"
  },
  "random": "e4b4d5f6c4e4f7a6b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6b4c8d2e48f37a6c6c6f6d7b7a6e4b4d5f6c4e4f7a6",
  "selectedVersion": "1.0",
  "cipherSuite": "TLS_AES_128_GCM_SHA256",
  "keyShare": {
    "group": "secp256r1",
    "expires": 864000,
    "keyExchange": "0488b21e000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
  },
  "proof": {
    "type": "EcdsaSecp256r1Signature2019",
    "created": "2024-05-27T10:51:55Z",
    "verificationMethod": "did:example:987654321abcdefghi#keys-1",
    "proofValue": "eyJhbGciOiJFUzI1NksifQ..myEaggpdg0-GflPHibRZWfDEdDOqzZzBcBM5TKvaUzCUSv1_7anUvtgdFXMd12E_qM6RmAAaSWWBGwLY-Srvyg"
  }
}
```

#### 5.2.1 필드 설명

`DestinationHello` 메시지의 필드는 `SourceHello` 메시지의 필드와 대부분 유사합니다. 주요 차이점은 다음과 같습니다:

- **selectedVersion**: 선택된 프로토콜 버전 번호.
- **cipherSuite**: 선택된 암호 모음. 현재 TLS_AES_128_GCM_SHA256 지원.
- **keyShare**:
  - **group**: 문자열, 사용된 타원 곡선 그룹. 현재 secp256r1 지원.
  - **keyExchange**: 문자열, 목적지에서 생성한 키 교환용 공개 키의 16진수 표현.
  - **expires**: 숫자, 목적지에서 설정한 키의 유효 기간. 유효 기간이 `SourceHello`에서 설정한 것을 초과하면 키 협상자는 여전히 자신의 유효 기간을 사용할 수 있으며, 자신의 유효 기간이 만료된 후 이 키로 암호화된 메시지 수락을 거부하고 재협상을 위한 오류를 보낼 수 있습니다.

### 5.3 Finished 메시지

TLS 1.3에서 `Finished` 메시지의 내용은 이전의 모든 핸드셰이크 메시지의 해시로, HMAC (Hash-based Message Authentication Code)를 통해 처리되어 양측의 핸드셰이크 메시지가 변조되지 않았음을 보장하고 재생 공격을 방지합니다.

우리 과정에서 `sourceHello`와 `destinationHello` 메시지 모두 서명을 포함하여 메시지가 변조될 수 없음을 보장합니다. 우리 과정에서 `Finished` 메시지의 주요 목적은 재생 공격을 방지하는 것입니다. 구체적으로 `sourceHello`와 `destinationHello` 메시지의 무작위 번호를 연결하고 해시하여 키 ID를 얻은 다음, 협상된 키로 이 키 ID를 암호화하여 `Finished` 메시지에 포함합니다. 메시지를 복호화함으로써 키 ID가 일치하는지 확인하여 재생 공격을 방지할 수 있습니다.

**메시지 예시:**

```json
{
  "version": "1.0",
  "type": "finished",
  "timestamp": "2024-05-27T12:00:00Z",
  "messageId": "randomstring",
  "sessionId": "abc123session",
  "sourceDid": "did:example:987654321abcdefghi",
  "destinationDid": "did:example:123456789abcdefghi",
  "verifyData": {
    "iv": "iv_encoded",
    "tag": "tag_encoded",
    "ciphertext": "ciphertext_encoded"
  }
}
```

#### 5.3.1 필드 설명

- **version**: 문자열, 현재 프로토콜의 버전 번호.
- **type**: 문자열, 메시지 유형.
- **timestamp**: 메시지 전송 시간, 밀리초 정밀도를 가진 ISO 8601 형식의 UTC 타임스탬프.
- **messageId**: 고유 메시지 ID, 16자 무작위 문자열.
- **sessionId**: 문자열, 세션 ID, `sourceHello` 메시지의 `sessionId` 사용.
- **sourceDid**: 문자열, 메시지의 소스, 즉 발신자의 DID. 항상 발신자 자신의 DID.
- **destinationDid**: 문자열, 목적지, 즉 수신자의 DID. 항상 수신자의 DID.
- **verifyData**: 검증 데이터. AES-GCM 모드는 `iv`와 `tag`를 포함.
  - **iv**: 초기화 벡터, 무작위 또는 의사 무작위 바이트 시퀀스, AES-GCM 모드의 경우 일반적으로 12바이트(96비트).
  - **tag**: AES-GCM 모드에서 생성된 인증 태그, 데이터의 무결성과 진위성을 확인하는 데 사용. 일반적으로 16바이트(128비트).
  - **ciphertext**: 단기 암호화 키 ID를 포함하는 암호화된 데이터. 자세한 생성 방법은 섹션 5.3.2 참조.

#### 5.3.2 verifyData 생성 방법

협상된 단기 암호화 키를 사용하여 다음 JSON을 암호화하여 `ciphertext`를 얻습니다:

```json
{
    "secretKeyId":"0123456789abcdef"
}
```

**Python 예시:**

```python
# TLS_AES_128_GCM_SHA256 암호화 함수
def encrypt_aes_gcm_sha256(data: bytes, key: bytes) -> Dict[str, str]:
    # 키 길이가 16바이트(128비트)인지 확인
    if len(key) != 16:
        raise ValueError("Key must be 128 bits (16 bytes).")
    
    # 무작위 IV 생성
    iv = os.urandom(12)  # GCM에 권장되는 IV 길이는 12바이트
    
    # 암호 객체 생성
    encryptor = Cipher(
        algorithms.AES(key),
        modes.GCM(iv),
        backend=default_backend()
    ).encryptor()
    
    # 데이터 암호화
    ciphertext = encryptor.update(data) + encryptor.finalize()
    
    # 인증 태그 가져오기
    tag = encryptor.tag
    
    # Base64로 인코딩
    iv_encoded = base64.b64encode(iv).decode('utf-8')
    tag_encoded = base64.b64encode(tag).decode('utf-8')
    ciphertext_encoded = base64.b64encode(ciphertext).decode('utf-8')
    
    # JSON 객체 생성
    encrypted_data = {
        "iv": iv_encoded,
        "tag": tag_encoded,
        "ciphertext": ciphertext_encoded
    }
        
    return encrypted_data
```

`secretKeyId`는 `sourceDid`와 `destinationDid` 간의 단기 암호화 키 ID입니다. 나중에 암호화된 메시지를 보낼 때 이 키 ID가 포함되어 어떤 키가 암호화에 사용되었는지 나타냅니다. 이 키 ID는 키의 유효 기간 내에서만 유효하며 키가 만료되면 폐기되어야 합니다.

**secretKeyId 생성 방법:**

1. **연결** `sourceHello`와 `destinationHello`의 무작위 번호를 단일 문자열로 연결, `sourceHello`가 먼저, `destinationHello`가 두 번째, 구분자 없음.
2. **문자열을 인코딩** UTF-8을 사용하여 바이트 시퀀스로.
3. **HKDF (HMAC-based Extract-and-Expand Key Derivation Function) 초기화** SHA-256을 해시 알고리즘으로, 솔트 없음(기본값), 빈 컨텍스트 정보. **Python 예시:**

    ```python
    hkdf = HKDF(
        algorithm=hashes.SHA256(),  # cryptography 라이브러리의 해시 알고리즘 인스턴스 사용
        length=8,  # 8바이트 키 생성
        salt=None,
        info=b'',  # 다양한 목적의 키를 구분하기 위한 선택적 컨텍스트 정보
        backend=default_backend()  # 기본 암호화 백엔드 사용
    )
    ```

4. **8바이트 시퀀스 도출** 입력 바이트 시퀀스에서 HKDF의 `derive` 방법을 사용.
5. **도출된 8바이트 시퀀스를 인코딩** 16자 16진수 문자열로, 이것이 `secretKeyId`가 됨.

**secretKeyId 생성 Python 예시:**

```python
from cryptography.hazmat.primitives.kdf.hkdf import HKDF
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.backends import default_backend

def generate_16_char_from_random_num(random_num1: str, random_num2: str):
    content = random_num1 + random_num2
    random_bytes = content.encode('utf-8')
    
    # HKDF를 사용하여 8바이트 키 도출
    hkdf = HKDF(
        algorithm=hashes.SHA256(),  # cryptography 라이브러리의 해시 알고리즘 인스턴스 사용
        length=8,  # 8바이트 키 생성
        salt=None,
        info=b'',  # 다양한 목적의 키를 구분하기 위한 선택적 컨텍스트 정보
        backend=default_backend()  # 기본 암호화 백엔드 사용
    )
    
    derived_key = hkdf.derive(random_bytes)
    
    # 도출된 키를 16진수 문자열로 인코딩
    derived_key_hex = derived_key.hex()
    
    return derived_key_hex
```

#### 5.3.3 Finished 메시지 검증

협상된 단기 암호화 키를 사용하여 메시지의 암호화된 데이터를 복호화하고 `secretKeyId`를 추출합니다. 로컬에서 생성된 `secretKeyId`와 일치하는지 확인합니다.

### 5.4 단기 암호화 키 생성 방법

소스와 목적지가 모두 `sourceHello`와 `destinationHello`를 보유하면 단기 암호화 키를 계산할 수 있습니다.

단기 키를 생성하는 과정은 TLS 1.3의 키 교환 과정과 유사하며, ECDHE (Elliptic Curve Diffie-Hellman Ephemeral)를 사용합니다. 이는 타원 곡선을 기반으로 한 키 교환 프로토콜입니다.

1. **상대방의 공개 키 가져오기:**
   - 16진수 문자열(`keyExchange`)에서 상대방의 타원 곡선 공개 키를 추출합니다.

2. **공유 비밀 생성:**
   - 로컬 개인 키와 상대방의 공개 키를 사용하여 ECDH (Elliptic Curve Diffie-Hellman) 알고리즘을 통해 공유 비밀을 생성합니다. 이는 양측이 개인 키를 직접 전송하지 않고도 동일한 공유 비밀을 계산할 수 있음을 보장합니다.

3. **키 길이 결정:**
   - 선택된 암호 모음을 기반으로 필요한 암호화 키 길이를 결정합니다. 예를 들어, TLS_AES_128_GCM_SHA256는 128비트(16바이트) 키 길이에 해당합니다.

4. **암호화 및 복호화 키 생성:**
   - **HKDF 추출 단계 초기화:**
     - 먼저 지정된 해시 알고리즘(예: SHA-256)과 초기 솔트 값(모든 0바이트)으로 HKDF 추출기를 초기화합니다. HKDF 추출기는 공유 비밀에서 의사 무작위 키를 도출하는 데 사용됩니다.
   - **의사 무작위 키 추출:**
     - HKDF 추출 단계를 통해 공유 비밀을 추출된 키로 변환합니다. 이 추출된 키는 후속 키를 도출하기 위한 기초 역할을 합니다.
   - **핸드셰이크 트래픽 키 생성:**
     - 소스와 목적지 핸드셰이크 키를 생성합니다. 이러한 키는 추출된 키, 특정 레이블("s ap traffic" 및 "d ap traffic"), `sourceHello`와 `destinationHello`의 연결된 무작위 문자열을 결합합니다.

    ```python
    def derive_secret(secret: bytes, label: bytes, messages: bytes) -> bytes:
        hkdf_expand = HKDFExpand(
            algorithm=hash_algorithm,
            length=hash_algorithm.digest_size,
            info=hkdf_label(hash_algorithm.digest_size, label, messages),
            backend=backend
        )
        return hkdf_expand.derive(secret)

    # 핸드셰이크 트래픽 비밀 생성
    source_data_traffic_secret = derive_secret(extracted_key, b"s ap traffic", source_hello_random + destination_hello_random)
    destination_data_traffic_secret = derive_secret(extracted_key, b"d ap traffic", source_hello_random + destination_hello_random)
    ```

   - **실제 핸드셰이크 키 생성을 위한 확장:**
     - HKDF 확장 단계를 사용하여 핸드셰이크 트래픽 키에서 실제 암호화 키를 도출합니다. 이 과정은 HKDF 레이블("key")과 원하는 키 길이를 사용합니다.

    ```python
    # 실제 핸드셰이크 키 생성을 위한 확장
    source_data_key = HKDF(
            algorithm=hash_algorithm,
            length=key_length,  # AES-256용 256비트 키
            salt=None,
            info=hkdf_label(32, b"key", source_data_traffic_secret),
            backend=backend
        ).derive(source_data_traffic_secret)

    destination_data_key = HKDF(
            algorithm=hash_algorithm,
            length=key_length,  # AES-256용 256비트 키
            salt=None,
            info=hkdf_label(32, b"key", destination_data_traffic_secret),
            backend=backend
        ).derive(destination_data_traffic_secret)
    ```

## 6. 제한사항

현재 솔루션에는 몇 가지 제한사항이 있습니다. 예를 들어, 암호화된 데이터는 WebSocket을 통해 JSON으로 전송됩니다. 비디오 파일과 같은 대용량 바이너리 데이터가 전송되면 효율성이 낮습니다. 텍스트 및 제어 명령 메시지에만 사용할 것을 권장합니다. 비디오 파일과 같은 대용량 바이너리 데이터의 경우 WebSocket JSON 메시지를 통해 비디오의 URL과 복호화 키를 전송할 것을 제안합니다. 그러면 수신자는 HTTPS와 같은 다른 프로토콜을 통해 비디오 파일을 다운로드하고 복호화할 수 있습니다.

향후 데이터 전송 효율성 문제를 해결하기 위해 WebSocket을 기반으로 한 바이너리 프로토콜을 설계할 계획입니다.

## 7. 요약 및 전망

이 솔루션은 DID를 기반으로 한 종단 간 암호화 통신 기술을 제안합니다. TLS와 블록체인과 같은 고보안 기술을 결합하여 DID 기반 단기 키 협상 메커니즘을 설계했습니다. 이 체계는 양쪽 사용자 간의 보안 통신을 보장하여 제3자가 무단으로 평문 내용에 접근하는 것을 방지합니다.

구체적으로, 이 솔루션은 WebSocket 프로토콜 위에 종단 간 암호화 통신을 구현하고 단기 키 협상을 위해 ECDHE (Elliptic Curve Diffie-Hellman Ephemeral)를 활용하여 메시지가 중개자를 통해 전달되더라도 복호화될 수 없음을 보장합니다. 우리는 `SourceHello`, `DestinationHello`, `Finished` 메시지의 생성과 검증을 포함한 암호화 통신 과정, 단기 키 협상 과정, 프로토콜 정의를 자세히 설명했습니다.

현재 버전은 기존 인프라를 활용하기 위해 WebSocket 프로토콜을 기반으로 하지만, 향후 전송 효율성과 적용 범위를 더욱 향상시키기 위해 TCP 또는 UDP 전송 계층을 기반으로 한 종단 간 암호화 체계를 도입할 계획입니다.

이 체계를 통해 서로 다른 플랫폼의 사용자 간에 보안적이고 효율적인 암호화 통신을 달성하고 분산 신원 인증을 위한 신뢰할 수 있는 기술적 기반을 제공하고자 합니다. 향후 작업에는 기존 프로토콜 최적화, 더 많은 보안 기능 추가, 더 많은 애플리케이션 시나리오로의 확장이 포함될 것입니다.

## 저작권 고지

Copyright (c) 2024 GaoWei Chang  
이 파일은 [MIT 라이선스](./LICENSE)에 따라 배포됩니다. 자유롭게 사용하고 수정할 수 있지만, 이 저작권 고지는 반드시 유지해야 합니다.
