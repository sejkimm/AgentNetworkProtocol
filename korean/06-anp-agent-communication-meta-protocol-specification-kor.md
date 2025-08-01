# ANP-Agent Communication Meta-Protocol Specification(Draft)

Note:

- This chapter is in the draft stage and may undergo significant adjustments based on actual conditions.
- The current protocol implementation focuses on end-to-end message encryption and will later be modified to a solution based on did:wba and HTTP.

## 배경

**Meta-Protocol**은 통신 프로토콜 사용을 협상하는 프로토콜로, 프로토콜이 어떻게 작동하고, 파싱하고, 결합하고, 상호작용하는지 구체적으로 정의합니다. 이는 프로토콜을 위한 규칙과 패턴을 제공하여 일반적이고 확장성이 높은 통신 메커니즘을 설계하는 데 도움을 줍니다. Meta-protocol은 일반적으로 특정 데이터 전송을 처리하지 않고, 프로토콜 운영을 위한 통신 프레임워크와 기본 제약을 정의합니다.

Meta-protocol은 에이전트 간 통신 효율성을 크게 향상시키고 통신 비용을 줄일 수 있습니다. 에이전트가 자연어를 사용하여 데이터를 전송하는 경우, 에이전트 내에서 데이터를 입출력 처리하는 데 LLM을 사용해야 하므로 정보 처리 효율성이 떨어지고 비용이 높습니다. Meta-protocol을 사용하여 AI 생성 코드와 결합하여 프로토콜 코드를 처리하면 다음과 같은 효과를 얻을 수 있습니다:

- 데이터 전송 효율성 향상: 데이터가 LLM에 들어가기 전에 프로토콜을 협상함으로써 LLM이 처리하는 데이터 양을 줄여 데이터 전송 효율성을 향상시킬 수 있습니다.
- 데이터 이해 정확도 향상: 소스에서 데이터를 구조화함으로써 LLM이 직접 비구조화 데이터를 처리하는 것보다 데이터 이해 정확도를 향상시킬 수 있습니다.
- 데이터 처리 복잡성 감소: 데이터 복잡성이 높은 특정 도메인에는 이미 산업에서 많은 프로토콜 사양이 있으며, 이는 오디오 및 비디오 데이터와 같이 자연어로 전송할 수 없습니다.

동시에 인공지능의 지원으로 meta-protocol은 에이전트 네트워크를 자기 조직화, 자기 협상하는 협업 네트워크로 변화시킬 수 있습니다. 자기 조직화와 자기 협상은 네트워크의 에이전트가 자율적으로 연결하고, 프로토콜을 협상하고, 프로토콜 합의에 도달할 수 있음을 의미합니다. 자연어와 meta-protocol을 통해 에이전트는 능력, 데이터 형식, 사용 프로토콜을 전달할 수 있으며, 궁극적으로 최적의 통신 프로토콜을 선택하여 네트워크 전체에서 효율적인 협업과 정보 전송을 보장할 수 있습니다.

Meta-protocol 계층에서는 [Agora Protocol](https://arxiv.org/html/2410.11905v1)의 아이디어를 참조하고 차용하여, 특정 시나리오에서의 모범사례와 과제를 결합하여 AgentNetworkProtocol의 meta-protocol 사양을 설계합니다.

## 현재 프로토콜이 협상되는 방법

현재 소프트웨어 시스템에서 API가 외부에 제공되는 경우, 일반적으로 API 호출 매개변수, 반환 값, 사용 프로토콜을 포함한 API 호출 방법이 제공됩니다. 이 과정은 본질적으로 프로토콜 협상 과정입니다. 다음과 같은 단점이 있습니다:

- 프로토콜을 수동으로 설계하고 프로토콜 처리 코드를 작성해야 합니다. 해당 프로토콜이 없으면 통신을 진행할 수 없습니다.
- 프로토콜 도킹에는 많은 수동 개입이 필요하며, 여러 차례의 커뮤니케이션과 확인이 필요합니다.
- 산업 표준이 없는 경우, 여러 시스템이 서로 다른 정의를 사용하므로 호출자가 개별적으로 협상하고 도킹해야 합니다.

## Meta-Protocol 협상 과정

LLM으로 강화된 지능형 에이전트가 meta-protocol과 결합하면 기존 소프트웨어 시스템의 프로토콜 협상 문제를 효과적으로 해결할 수 있습니다. 주요 과정은 다음과 같습니다:

```plaintext
  Agent (A)                                       Agent (B)
    |                                                 |
    | ------------- Protocol Negotiation -----------> |
    |                                                 |
    |         (Multiple negotiations may occur)       |
    |                                                 |
    | <------------- Protocol Negotiation ----------- |
    |                                                 |
    |---------------                                  |
    |              |                                  |
    |   Protocol Code Generated                       |
    |              |                                  |
    | <-------------                                  |
    | --------------- Code Generation --------------> |
    |                                                 |---------------  
    |                                                 |              |
    |                                                 |   Protocol Code Generated
    |                                                 |              |
    |                                                 | <-------------  
    | <-------------- Code Generation --------------- |
    |                                                 |
    |                                                 |
    | ------------ Test Cases Negotiation ----------> |
    |                  (Optional)                     |
    |         (Multiple negotiations may occur)       |
    |                                                 |
    | <----------- Test Cases Negotiation ----------- |
    |                                                 |
    |                                                 |
    |    (Start Communication Using Final Protocol)   |
    |                                                 |
    | <------- Application Protocol Message --------> |
    |                                                 |
    |                                                 |
```

그림에서 보여주는 Agent A가 Agent B에게 시작하는 프로토콜 협상 과정은 다음과 같습니다:

- Agent A는 먼저 자연어를 사용하여 Agent B와 프로토콜 협상을 시작하며, A의 요구사항, 능력, 예상 프로토콜 사양을 전달하고 B가 선택할 수 있는 여러 옵션을 제공합니다.
- Agent B는 A의 협상 요청을 받은 후, A가 제공한 정보를 바탕으로 B의 능력과 결정된 프로토콜 사양을 자연어로 A에게 응답합니다.
- Agent A와 Agent B 사이에 여러 차례의 협상이 발생할 수 있으며, 궁극적으로 에이전트 간 통신을 위한 프로토콜 사양이 결정됩니다.
- 협상 결과를 바탕으로 Agent A와 Agent B는 AI를 사용하여 프로토콜을 처리할 코드를 생성합니다. 보안 고려사항으로 인해 생성된 코드는 샌드박스에서 실행하는 것이 권장됩니다.
- 에이전트들은 프로토콜 상호운용성 테스트를 실시하여, AI를 사용하여 프로토콜 메시지가 협상된 사양에 부합하는지 판단합니다. 부합하지 않는 경우, 자연어 상호작용을 통해 자동 해결을 수행합니다.
- 마지막으로 최종 프로토콜이 결정되고, Agent A와 Agent B는 최종 프로토콜을 사용하여 통신합니다.
위 과정을 통해 meta-protocol을 사용하는 지능형 에이전트가 코드 생성 기술과 결합하여 프로토콜 협상의 효율성을 크게 향상시키고 프로토콜 협상 비용을 줄일 수 있음을 알 수 있습니다. 동시에 지능형 에이전트 네트워크를 자기 조직화, 자기 협상하는 협업 네트워크로 변화시킵니다.

## 메시지 형식 정의

협상 메시지는 [종단 간 암호화](04-End-to-End%20Encrypted%20Communication%20Technology%20Protocol%20Based%20on%20did.md) 메시지의 encryptedData를 기반으로 하며, 이는 암호화된 메시지의 상위 계층 메시지입니다.

암호화된 메시지 encryptedData의 복호화된 암호문의 메시지 형식은 다음과 같이 설계됩니다:

```plaintext
0               1               2               3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 0 ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|PT |  Reserved |              Protocol data                    | ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

```

- PT: Protocol Type, 2 bits, 프로토콜 타입을 나타냄
  - 00: meta protocol
  - 01: application protocol
  - 10: natural language protocol
  - 11: verification protocol
- Reserved: 6 bits, 예약 필드, 아직 사용되지 않음
- Protocol Data: 가변 길이, 프로토콜의 구체적인 내용을 나타냄

모든 메시지는 1바이트의 바이너리 헤더를 가지며, 헤더의 주요 정보는 프로토콜 데이터의 프로토콜 타입입니다:

- 프로토콜 타입 값이 00인 경우, 이 메시지가 프로토콜 협상에 사용되는 meta-protocol임을 나타냅니다;
- 프로토콜 타입 값이 01인 경우, 이 메시지가 실제 데이터 전송에 사용되는 application protocol임을 나타냅니다;
- 프로토콜 타입 값이 10인 경우, 이 메시지가 자연어를 직접 사용하여 데이터 전송하는 natural language protocol임을 나타냅니다;
- 프로토콜 타입 값이 11인 경우, 이 메시지가 협상된 프로토콜을 검증하는 데 사용되는 verification protocol임을 나타냅니다. 검증 후 이 프로토콜을 사용하여 데이터 전송합니다. 검증 프로토콜은 실제 사용자 데이터가 아닙니다.

현재 바이너리 헤더는 1바이트입니다. 미래에 1바이트로 요구사항을 만족할 수 없는 경우, 여러 바이트로 확장할 수 있습니다. Hello 메시지에 메시지 형식 버전 정보를 전달함으로써 하위 및 상위 호환성을 유지할 수 있습니다.

### Meta-Protocol 협상 메시지 정의

프로토콜 타입(PT)이 00인 경우, 메시지의 Protocol Data는 meta-protocol 메시지를 전달하며, 이는 두 에이전트 간 통신에 사용되는 프로토콜을 협상하는 데 사용됩니다. Meta-protocol 협상 과정은 미리 정의되어 있으며 협상이 필요하지 않습니다. 미리 정의된 문서가 바로 이 문서입니다.

우리는 meta-protocol 메시지를 반구조화 형식으로 정의합니다. 핵심 프로토콜 협상 부분은 자연어를 사용하여 협상의 유연성을 유지하고, 프로세스 제어는 구조화된 JSON을 사용하여 프로토콜 협상 과정을 제어 가능하게 유지합니다.

Meta-protocol 협상 메시지는 여러 카테고리로 나뉩니다:

- Protocol Negotiation Message: 프로토콜 내용 협상에 사용
- Code Generation Message: 프로토콜을 처리하는 코드 생성에 사용
- Debug Protocol Message: 디버깅 프로토콜 협상에 사용
- Natural Language Message: 자연어를 사용한 통신 협상에 사용

다음 프로토콜에서 사용되는 JSON 형식은 JSON 사양 [RFC8259](https://tools.ietf.org/html/rfc8259)를 준수합니다.

#### Protocol Negotiation Message 정의

프로토콜 협상 메시지의 JSON 형식은 다음과 같습니다:

```json
{
    "action": "protocolNegotiation",
    "sequenceId": 0,
    "candidateProtocols": "",
    "modificationSummary": "",
    "status": "negotiating"
}
```

필드 설명:

- action: protocolNegotiation으로 고정
- sequenceId: 협상 순서 번호, 협상 라운드를 식별하는 데 사용.
  - 0부터 시작하며, 각 협상 메시지의 sequenceId는 이전 것을 기준으로 1씩 증가해야 함.
  - 협상 라운드가 너무 커지는 것을 방지하기 위해, 코드 구현자는 비즈니스 시나리오에 따라 협상 라운드의 상한선을 설정할 수 있음. 10라운드를 초과하지 않는 것이 권장됨.
  - sequenceId를 처리할 때, 상대방이 반환한 sequenceId가 사양에 따라 증가했는지 확인해야 함.
- candidateProtocols: 후보 프로토콜
  - 후보 프로토콜의 목적, 과정, 데이터 형식, 오류 처리 등을 설명하는 자연어 텍스트.
  - 이 텍스트는 일반적으로 AI에 의해 처리되며, 명확하고 간결하게 유지하기 위해 markdown 형식이 권장됨.
  - 후보 프로토콜은 전체 프로토콜 내용을 설명하거나 기존 프로토콜을 수정할 수 있으며, 기존 프로토콜의 URI와 수정사항을 전달함.
  - 후보 프로토콜은 매번 전체 프로토콜 내용을 전달해야 함.
- modificationSummary: 프로토콜 수정 요약
  - 협상 과정에서 현재 후보 프로토콜이 이전 후보 프로토콜과 비교하여 어떤 내용이 수정되었는지 설명하는 자연어 텍스트.
  - 이 텍스트는 일반적으로 AI에 의해 처리되며, 명확하고 간결하게 유지하기 위해 markdown 형식이 권장됨.
  - 처음 협상을 시작할 때 이 필드는 생략할 수 있음.
- status: 협상 상태, 현재 협상 상태를 나타내는 데 사용. 상태 값은 다음과 같음:
  - negotiating: 협상 중
  - rejected: 협상 실패
  - accepted: 협상 성공
  - timeout: 협상 시간 초과

협상 당사자들은 협상 라운드가 최대 한계를 초과하기 전까지 반복적으로 협상할 수 있으며, 어느 한 쪽이 상대방이 제공한 프로토콜이 자신의 요구사항을 충족한다고 결정하면 협상이 성공합니다. 그렇지 않으면 협상이 실패하고, 사람 엔지니어가 협상 과정에 개입할 수 있습니다.

##### candidateProtocols 예시

- 다음은 전체 프로토콜 설명을 전달하는 candidateProtocols의 예시입니다:

```plaintext
# Requirements
제품 정보 검색

# Process Description
요청자가 제품 id 또는 이름을 전달하여 제품 공급자에게 보냅니다. 제품 공급자는 제품 id 또는 이름을 기반으로 상세한 제품 정보를 반환합니다.
예외 처리:
- 오류 코드는 HTTP 오류 코드를 사용
- 오류 메시지는 자연어로 설명
- 15초 내에 반환이 없으면 시간 초과로 간주

# Data Format Description
요청과 응답 모두 JSON 형식을 사용하며, 사양 https://tools.ietf.org/html/rfc8259를 따릅니다.

## Request Message
요청 JSON schema는 다음과 같이 정의됩니다:
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "ProductInfoRequest",
  "type": "object",
  "properties": {
    "messageId": {
      "type": "string",
      "description": "A random string identifier for the message"
    },
    "type": {
      "type": "string",
      "description": "Indicates whether the message is a REQUEST or RESPONSE"
    },
    "action": {
      "type": "string",
      "description": "The action to be performed"
    },
    "productId": {
      "type": "string",
      "description": "The unique identifier for a product"
    },
    "productName": {
      "type": "string",
      "description": "The name of the product"
    }
  },
  "required": ["messageId", "type", "action", "productId"]
}

## Response Message
응답 JSON schema는 다음과 같이 정의됩니다:
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "ProductInfoResponse",
  "type": "object",
  "properties": {
    "messageId": {
      "type": "string",
      "description": "The messageId from the request json"
    },
    "type": {
      "type": "string",
      "description": "Indicates whether the message is a REQUEST or RESPONSE"
    },
    "status": {
      "type": "object",
      "properties": {
        "code": {
          "type": "integer",
          "description": "HTTP status code"
        },
        "message": {
          "type": "string",
          "description": "Status message"
        }
      },
      "required": ["code", "message"]
    },
    "productInfo": {
      "type": "object",
      "properties": {
        "productId": {
          "type": "string",
          "description": "The unique identifier for a product"
        },
        "productName": {
          "type": "string",
          "description": "The name of the product"
        },
        "productDescription": {
          "type": "string",
          "description": "A detailed description of the product"
        },
        "price": {
          "type": "number",
          "description": "The price of the product"
        },
        "currency": {
          "type": "string",
          "description": "The currency of the price"
        }
      },
      "required": ["productId", "productName", "productDescription", "price", "currency"]
    }
  },
  "required": ["messageId", "type", "status", "productInfo"]
}

```

- 기존 프로토콜을 기반으로 수정한 candidateProtocols의 예시:

```plaintext
# Requirement
제품 정보 검색

# Process Description
이 과정은 기존 프로토콜(URI: https://agent-network-protocol.com/protocols/product-info-0-1-1)을 기반으로 구현됩니다.

# Modifications
- 응답 메시지에 사용자 정의 오류 코드 추가
  - 100001: 제품 재고 부족
  - 100002: 제품 단종
  - 100003: 제품 가격 불명

```

##### modificationSummary 예시

modificationSummary도 자연어 텍스트이며, 다음과 같습니다:

```plaintext

수정사항:
- 응답에 필드 추가: productImageUrl, productVideoUrl, productTags
- 명시적인 응답 시간 초과 설정: 15초. 15초 내에 응답을 받지 못하면 시간 초과로 간주.

```

#### Protocol Code Generation Message 정의

프로토콜 협상이 완료된 후, 에이전트들은 프로토콜을 처리할 코드를 준비해야 하며, 이는 AI에 의해 생성되거나 네트워크에서 로드될 수 있습니다. 코드가 준비되기 전에 사용자 메시지를 받으면 프로토콜 처리가 실패할 수 있습니다.

따라서 협상이 완료된 후, 양쪽 에이전트는 서로에게 코드 생성 메시지로 응답하여, 코드가 생성되었으며 메시지 처리를 진행할 수 있음을 상대방에게 알려야 합니다.

코드 생성 메시지를 오랫동안 받지 못하면 코드 생성이 실패한 것으로 간주되어 통신이 종료됩니다. 권장 시간 초과 기간은 15초입니다.

```json
{
    "action": "codeGeneration",
    "status": "generated"
}
```

필드 설명:

- action: codeGeneration으로 고정
- status: 상태
  - generated: 코드가 생성되었음을 나타냄
  - error: 코드 생성이 실패했으며, 통신을 종료함을 나타냄

#### Test Cases Negotiation Message 정의

프로토콜 협상이 완료되고 코드가 준비된 후, 두 에이전트가 이 프로토콜을 기반으로 정상적으로 통신할 수 있는지 확인하기 위한 테스트 과정이 필요할 수 있습니다. 테스트 케이스 협상 메시지는 주로 이 목적을 위해 설계되어, 두 에이전트가 테스트 케이스를 협상하고 서로에게 테스트를 실시하도록 알리는 역할을 합니다.

테스트 케이스 협상 메시지는 과정에서 필수 기능이 아닙니다. 에이전트나 사람 엔지니어가 프로토콜에 테스트가 필요하지 않다고 판단하는 경우, 이 단계를 건너뛰고 후속 통신 과정을 직접 진행할 수 있습니다.

테스트 케이스 협상 메시지의 JSON 형식은 다음과 같습니다:

```json
{
    "action": "testCasesNegotiation",
    "testCases": "",
    "modificationSummary": "",
    "status": "negotiating"
}
```

필드 설명:

- action: testCasesNegotiation으로 고정
- testCases: 테스트 케이스 집합, 테스트 케이스 집합의 내용을 설명하는 자연어 텍스트로, 여러 테스트 케이스를 포함. 각 테스트 케이스는 테스트 요청 데이터, 테스트 응답 데이터, 예상 테스트 결과를 포함함.
- modificationSummary: 테스트 케이스 수정 요약, 협상 과정에서 현재 테스트 케이스 집합이 이전 것과 비교하여 어떤 변경사항이 있는지 설명하는 자연어 텍스트.
- status: 협상 상태, 현재 협상 상태를 나타내는 데 사용. 상태 값은 다음과 같음:
  - negotiating: 협상 중
  - rejected: 협상 실패
  - accepted: 협상 성공

testCases 예시:

```plaintext
 # Test Case 1

 - **Test Request Data**:
 {
   "messageId": "msg001",
   "type": "REQUEST",
   "action": "getProductInfo",
   "productId": "P12345"
 }

 - **Test Response Data**:
 {
   "messageId": "msg001",
   "type": "RESPONSE",
   "status": {
     "code": 200,
     "message": "Success"
   },
   "productInfo": {
     "productId": "P12345",
     "productName": "고성능 노트북",
     "productDescription": "최신 프로세서와 대용량 메모리를 장착한 고성능 노트북.",
     "price": 1299.99,
     "currency": "USD"
   }
 }

 - **Expected Test Result**:
 제품 정보를 성공적으로 검색, 상태 코드 200.

 # Test Case 2

 - **Test Request Data**:
 {
   "messageId": "msg002",
   "type": "REQUEST",
   "action": "getProductInfo",
   "productId": "P99999"
 }

 - **Test Response Data**:
 {
   "messageId": "msg002",
   "type": "RESPONSE",
   "status": {
     "code": 404,
     "message": "Product Not Found"
   },
   "productInfo": null
 }

 - **Expected Test Result**:
 요청한 제품이 존재하지 않음, 상태 코드 404 반환, 제품 정보는 null.

```

#### Fix Error Negotiation Message 정의

프로토콜의 테스트나 실제 운영 중에 상대방의 메시지가 프로토콜 정의에 부합하지 않거나 오류가 포함되어 있다는 것을 발견하면, 상대방에게 오류 정보를 알리고 함께 오류를 해결하기 위해 협상해야 합니다. 이 과정은 여러 라운드를 거칠 수 있는데, 프로토콜 도킹 과정의 오류는 양쪽 모두의 문제일 수 있으므로 공동 협상을 통해 해결해야 합니다.

예를 들어, Agent A가 오류 수정 메시지를 보내어 Agent B가 보낸 메시지가 프로토콜 정의에 부합하지 않거나 오류가 포함되어 있다고 지적합니다. Agent B는 오류 수정 메시지를 받은 후, 오류 정보와 프로토콜 정의를 기반으로 오류가 있는지 분석합니다. 오류가 있는 경우, 오류를 받아들이고 코드를 수정하여 코드 생성 과정에 들어갑니다. 오류가 없는 경우, 오류 수정 메시지로 응답하여 수정을 거부하고 Agent A에게 상세한 이유를 알립니다.

오류 수정 협상 메시지의 JSON 형식은 다음과 같습니다:

```json
{
    "action": "fixErrorNegotiation",
    "errorDescription": "",
    "status": "negotiating"
}
```

필드 설명:

- action: "fixErrorNegotiation"으로 고정값
- errorDescription: 오류 설명, 오류 정보를 설명하는 자연어 텍스트.
- status: 협상 상태, 현재 협상 상태를 나타내는 데 사용. 상태 값은 다음과 같음:
  - negotiating: 협상 중
  - rejected: 협상 실패
  - accepted: 협상 성공

errorDescription 예시:

```plaintext
# Error Description
- 응답 메시지의 status 필드에 code 필드가 누락됨.

```

#### Natural Language Negotiation Message

이전에 정의한 프로토콜 협상, 코드 생성, 오류 수정 메시지는 에이전트 간 대부분의 협상 과정을 만족시킬 수 있습니다. 안타깝게도 경험상 현실 세계는 종종 매우 복잡하여 우리가 예견할 수 없는 많은 측면이 있습니다. 이전에는 이 문제를 해결하기 어려웠지만, 이제 생성형 AI와 자연어를 기반으로 이 문제를 잘 해결할 수 있습니다.

따라서 우리는 미리 정의된 메시지로 협상할 수 없는 문제를 해결하기 위해 순수한 자연어 상호작용 메시지를 설계했습니다.

자연어 협상 메시지는 필수가 아니며, 에이전트는 이를 지원할지 자유롭게 선택할 수 있습니다. 우리는 미리 정의된 메시지를 사용한 협상을 우선적으로 사용하는 것을 권장하는데, 이것이 더 효율적이기 때문입니다.

자연어 상호작용 메시지는 요청-응답 모델을 채택하며, JSON 형식은 다음과 같습니다:

```json
{
    "action": "naturalLanguageNegotiation",
    "type": "REQUEST",
    "messageId": "",
    "message": ""
}
```

필드 설명:

- action: "naturalLanguageNegotiation"으로 고정값
- type: 메시지 타입, 메시지의 타입을 식별하는 데 사용, 값은 REQUEST 또는 RESPONSE.
- messageId: 메시지 ID, 메시지를 식별하는 데 사용되는 16자리 무작위 문자열. 상대방이 응답할 때 동일한 messageId를 전달해야 함.
- message: 자연어 내용, 자연어 텍스트. 에이전트는 프로토콜 협상, 통신 등에 관한 자신의 특별한 요구사항을 메시지에 포함할 수 있음.

### Application Protocol Message 정의

프로토콜 타입(PT)이 01인 경우, 메시지의 Protocol Data는 application protocol 메시지를 전달하며, 이는 두 에이전트 간 상호작용 데이터를 전송하는 데 사용됩니다. 메시지의 형식은 프로토콜 협상 과정에서 협상된 구체적인 프로토콜에 따라 달라집니다. 바이너리 데이터이거나 JSON, XML 등과 같은 구조화된 데이터일 수 있습니다.

### Natural Language Protocol Message 정의

프로토콜 타입(PT)이 02인 경우, 메시지의 Protocol Data는 natural language protocol 메시지를 전달하며, 이는 두 에이전트 간 상호작용 데이터를 전송하는 데 사용됩니다.

일부 특별한 경우에 에이전트들이 소량, 저빈도, 심지어 단일 상호작용만을 수행하는 경우가 있습니다. 최고의 통신 효율성을 달성하기 위해 프로토콜 협상 과정을 건너뛰고 자연어를 직접 사용하여 데이터 상호작용을 할 수 있습니다.

Protocol Data의 데이터는 UTF-8로 인코딩된 자연어 텍스트입니다. AI 처리를 용이하게 하기 위해 명확하고 간결한 설명과 함께 markdown 형식을 사용하는 것이 권장됩니다.

이 메시지는 필수 지원 메시지가 아니며, 에이전트는 이를 지원할지 자유롭게 선택할 수 있습니다.

Natural Language Protocol Message 예시:

요청 예시:

```plaintext
# Requirement
제품 정보 가져오기. 제품 ID를 기반으로 제품 ID, 제품명, 제품 설명, 제품 가격, 제품 통화 단위를 포함한 제품의 상세 정보를 반환합니다.

# Input
- Product ID: P12345
```

응답 예시:

```plaintext
# Output
- Product ID: P12345
- Product Name: 고성능 노트북
- Product Description: 최신 프로세서와 대용량 메모리를 장착한 고성능 노트북.
- Product Price: 1299.99
- Product Currency Unit: USD
```

### Verification Protocol Message

프로토콜 타입(PT)이 03인 경우, 메시지의 Protocol Data는 verification protocol 메시지를 전달하며, 이는 두 에이전트 간 검증 데이터를 전송하는 데 사용됩니다. 메시지의 형식은 프로토콜 협상 과정에서 협상된 구체적인 프로토콜에 따라 달라집니다. verification protocol 메시지는 실제 비즈니스 데이터가 아니라 프로토콜 과정이 정상인지 검증하는 데 사용됩니다. verification protocol 메시지의 내용은 일반적으로 프로토콜 협상 과정에서 verificationProtocol 메시지를 통해 협상됩니다.

이 메시지는 필수 지원 메시지가 아니며, 에이전트는 이를 지원할지 자유롭게 선택할 수 있습니다.

## Meta-Protocol 능력 협상 메커니즘 및 확장성 설계

위의 프로토콜 협상 과정은 두 에이전트가 특정 프로토콜을 협상하는 방법을 보여줍니다. 그러나 현실의 다양한 이유로 인해 에이전트들이 모든 meta-protocol 능력을 지원할 수 없을 수 있습니다. 예를 들어, 일부 에이전트는 natural language protocol을 지원하지 않고, 일부 에이전트는 verification protocol을 지원하지 않습니다.

이 문제를 해결하기 위해, 우리는 에이전트가 연결하기 전에 meta-protocol 능력을 협상하여 자신이 지원하는 meta-protocol 능력을 상대방에게 알려 협상 실패를 방지하는 meta-protocol 능력 협상 메커니즘을 설계했습니다.

이 문제와 meta-protocol의 확장성은 본질적으로 동일하므로 함께 논의합니다. meta-protocol 과정을 업그레이드해야 하는 경우, 예를 들어 프로토콜 타입을 1바이트에서 2바이트로 확장하는 경우, 새로운 버전의 meta-protocol이 생성되며 신구 버전 간의 호환성 문제를 고려해야 합니다.

우리의 해결책은 연결 핸드셰이크 메시지, 즉 sourceHello와 destinationHello 메시지에 meta-protocol의 버전과 지원되는 meta-protocol 능력을 전달하는 것입니다. 한 에이전트가 버전 V1을 지원하고 다른 에이전트가 버전 V1과 V2를 지원하는 경우, V1 버전의 meta-protocol을 사용하여 협상합니다.

meta-protocol 능력 협상에 관해서는, 모든 에이전트가 기본 meta-protocol 능력을 지원하도록 요구하며, natural language protocol과 verification protocol과 같은 선택적 meta-protocol 능력은 에이전트가 자유롭게 선택할 수 있습니다.

sourceHello와 destinationHello 메시지의 수정사항은 다음과 같습니다:

```json
{
  "version": "1.0",
  "type": "sourceHello",  // destinationHello도 동일
  "metaProtocol": {
    "version": "1.0",
    "supportedCapabilities": [
        "naturalLanguageProtocol",
        "verificationProtocol",
        "naturalLanguageNegotiation",
        "testCasesNegotiation",
        "fixErrorNegotiation"
    ]
  },
  // 기타 필드 생략
}
```

필드 설명:

- version: Meta-protocol 버전, 현재 버전은 1.0
- supportedCapabilities: 지원되는 meta-protocol 능력, 배열 타입, 배열의 각 요소는 지원되는 meta-protocol 능력의 이름으로, 다음 기능에 해당:
  - naturalLanguageProtocol: Natural Language Protocol
  - verificationProtocol: Verification Protocol
  - naturalLanguageNegotiation: Natural Language Negotiation
  - testCasesNegotiation: Test Cases Negotiation
  - fixErrorNegotiation: Fix Error Negotiation

## Meta-Protocol 협상 효율성 최적화

에이전트가 사용하는 meta-protocol 협상 메커니즘은 이기종 시스템 간 프로토콜 도킹으로 인한 수동 비용 문제를 해결할 수 있어 에이전트가 자기 조직화, 자기 협상하는 네트워크를 형성할 수 있게 하지만, 동시에 일부 새로운 문제도 가져옵니다.

먼저, 프로토콜 협상 과정은 통신 RTT를 크게 증가시키고, AI를 사용하여 자연어를 처리하는 것도 새로운 지연을 가져옵니다. 프로토콜 협상, 코드 생성, 테스트 케이스 협상(선택사항), 오류 수정 협상(선택사항)까지 전체 과정에서 최소 2 RTT가 추가되며, 여러 라운드의 협상이 발생하면 RTT는 더욱 늘어납니다. LLM을 사용하여 자연어를 처리하는 것도 새로운 지연을 생성하며, 길이는 입출력 길이와 모델 처리 속도에 따라 달라지고, 단일 지연은 몇 초에서 십수 초까지 걸릴 수 있습니다.

둘째, 협상 과정은 AI의 요구사항 이해, 프로토콜 설계, 프로토콜 처리 코드 생성 능력에 의존합니다. 이러한 능력은 AI에 높은 요구사항을 제시하며, 현재 AI의 고유한 결함, 예를 들어 LLM의 환각 문제로 인해 AI가 100% 성공적인 처리를 보장할 수 없어 협상 성공률이 저하됩니다.

사용자의 실제 비즈니스 과정에서 네트워크의 한 기능은 에이전트 간 많은 상호작용을 포함합니다. 지연과 성공률 문제를 잘 해결하지 못하면 사용자 경험에 심각한 영향을 미칩니다.

이러한 문제를 해결하기 위해, 우리는 0RTT meta-protocol 협상 메커니즘과 합의 기반 meta-protocol 협상 메커니즘을 설계했습니다.

### 0RTT Meta-Protocol 협상 메커니즘

현대 통신 프로토콜 설계에서는 연결 과정의 RTT를 줄이기 위해 일반적으로 0RTT 통신 메커니즘이 설계됩니다. 예를 들어, TLS1.3에서는 초기 연결 시 세션 티켓을 생성하고 캐시하여, 클라이언트가 후속 연결에서 티켓과 이전에 협상된 키를 사용하여 암호화된 0-RTT 데이터를 직접 전송할 수 있게 하여 빠른 재연결과 즉시 데이터 전송을 달성합니다.

ANP의 0RTT meta-protocol 협상 메커니즘은 두 에이전트 간 첫 번째 연결 시 완전한 meta-protocol 협상 과정을 포함합니다. 양쪽은 협상된 프로토콜을 캐시하여 프로토콜 내용과 프로토콜 내용의 해당 해시 값을 저장합니다. 프로토콜 내용은 합의에 도달했을 때 protocolNegotiation 메시지의 candidateProtocols 필드를 사용합니다.

두 번째 연결 시, 첫 번째 연결의 협상 결과를 직접 재사용하여 통신할 수 있습니다. 핸드셰이크 메시지 설계에서 연결 개시자는 sourceHello 메시지에 이전 협상의 결과를 전달할 수 있으며, 주로 프로토콜 해시 값을 사용합니다. 연결 수신자는 destinationHello 메시지에서 개시자의 프로토콜을 확인하고, 양쪽은 협상 과정을 직접 건너뛰고 이전에 협상된 프로토콜을 사용하여 통신할 수 있습니다.

sourceHello 메시지 예시:

```json
{
  "version": "1.0",
  "type": "sourceHello",  
  "metaProtocol": {
    "version": "1.0",
    "supportedCapabilities": [
        "naturalLanguageProtocol",
        "verificationProtocol",
        "naturalLanguageNegotiation",
        "testCasesNegotiation",
        "fixErrorNegotiation"
    ],
    "usedProtocolHash": "1234567890abcdef..." 
  },
  // 기타 필드 생략
}
```

destinationHello 메시지 예시:

```json
{
  "version": "1.0",
  "type": "destinationHello",  
  "metaProtocol": {
    "version": "1.0",
    "supportedCapabilities": [
        "naturalLanguageProtocol",
        "verificationProtocol",
        "naturalLanguageNegotiation",
        "testCasesNegotiation",
        "fixErrorNegotiation"
    ],
    "usedProtocolHash": "1234567890abcdef..."
  },
  // 기타 필드 생략
}
```

필드 설명:

- usedProtocolHash: 최종 합의된 프로토콜 내용의 해시 값, 사용 중인 프로토콜을 식별하는 데 사용. 두 번째 연결 시 협상 과정을 건너뛰고 프로토콜을 직접 사용하여 통신하는 데 사용. 수신자가 이 프로토콜을 지원하지 않는 경우, destinationHello 메시지에서 usedProtocolHash 필드를 제거하여 프로토콜 협상을 다시 해야 함을 나타냄.

프로토콜 내용의 해시 값은 SHA-256 알고리즘을 사용하여 생성되며, 64자리 16진수 문자열입니다. usedProtocolHash 생성 예시 코드:

```python
import hashlib

candidateProtocols = "..."  # 합의에 도달했을 때 protocolNegotiation 메시지의 candidateProtocols 필드에서 가져옴

def generate_protocol_hash(protocol_content):
    return hashlib.sha256(protocol_content.encode('utf-8')).hexdigest()

usedProtocolHash = generate_protocol_hash(candidateProtocols)
```

### 합의 프로토콜 기반 Meta-Protocol 협상 메커니즘

0RTT meta-protocol 협상 메커니즘을 사용하면 RTT를 줄일 수 있지만, 첫 번째 연결에는 여전히 협상이 필요합니다. 협상 과정에서 여전히 AI를 사용하여 자연어를 처리해야 하므로 시간 소모와 성공률 문제가 여전히 존재합니다. 에이전트 네트워크에서는 동일하거나 유사한 요구사항과 기능을 가진 많은 통신 행동이 있습니다. 에이전트가 다른 에이전트들이 이미 협상한 프로토콜과 프로토콜 코드를 재사용할 수 있다면 통신 효율성을 크게 향상시킬 수 있습니다.

이를 위해 우리는 합의 프로토콜 기반 meta-protocol 협상 메커니즘을 설계했습니다.

합의 프로토콜은 두 가지 카테고리로 나뉩니다:

- 인간이 제정한 산업 표준 프로토콜: W3C, IETF 등과 같은 산업 기관이나 표준화 기구에서 제정한 프로토콜
- 에이전트 네트워크가 도달한 합의 프로토콜: 에이전트 네트워크 내의 에이전트들이 공동으로 협상하고 선출한 프로토콜

모든 합의 프로토콜의 각 버전에 대해 고유한 식별자 URI를 생성하고, 이 버전의 프로토콜에 해당하는 프로토콜 처리 코드를 생성할 수 있습니다. 에이전트가 프로토콜을 협상할 때, 필요에 따라 하나 이상의 합의 프로토콜을 후보 프로토콜로 선택하여 상대방에게 직접 연결 요청을 시작할 수 있습니다. 상대방은 자신이 지원하는 프로토콜을 선택하여 반환합니다. 그러면 양쪽은 이 프로토콜과 해당 프로토콜 코드를 사용하여 통신할 수 있습니다.

더 나아가, 에이전트는 자신이 지원하는 합의 프로토콜을 온라인 문서에 게시할 수 있습니다. 다른 에이전트는 이 온라인 문서를 보고 문서에 나열된 프로토콜을 기반으로 대상 에이전트와 통신 프로토콜을 협상할 수 있습니다. 이는 프로토콜 협상 과정을 더욱 가속화합니다.

에이전트가 URI를 기반으로 프로토콜과 해당 코드를 다운로드하는 방법은 다른 사양에서 논의될 것입니다.

sourceHello 메시지 예시:

```json
{
  "version": "1.0",
  "type": "sourceHello",  
  "metaProtocol": {
    "version": "1.0",
    "supportedCapabilities": [
        "naturalLanguageProtocol",
        "verificationProtocol",
        "naturalLanguageNegotiation",
        "testCasesNegotiation",
        "fixErrorNegotiation"
    ],
    "candidateProtocols": [
        "https://example.com/protocol/1.0",
        "https://example.com/protocol/2.0"
    ]
  },
  // 기타 필드 생략
}
```

destinationHello 메시지 예시:

```json
{
  "version": "1.0",
  "type": "destinationHello",  
  "metaProtocol": {
    "version": "1.0",
    "supportedCapabilities": [
        "naturalLanguageProtocol",
        "verificationProtocol",
        "naturalLanguageNegotiation",
        "testCasesNegotiation",
        "fixErrorNegotiation"
    ],
    "selectedProtocol": "https://example.com/protocol/1.0"
  },
  // 기타 필드 생략
}
```

필드 설명:

- selectedProtocol: candidateProtocols에서 선택한 프로토콜, 양쪽이 사용하는 프로토콜을 식별하는 데 사용.

#### 에이전트 네트워크에서 합의 프로토콜 도달

에이전트 네트워크가 합의 프로토콜에 도달하고 이러한 프로토콜을 네트워크에 게시하는 방법은 다른 사양에서 논의될 것입니다.

## 미래

이 사양은 주로 meta-protocol의 설계와 meta-protocol 협상 메커니즘의 설계에 대해 논의합니다. 우리는 더 유연하고 비용 효율적인 에이전트 프로토콜 협상 사양을 설계했습니다. 이 사양을 사용하여 에이전트는 인간의 개입 없이 자율적으로 협상, 코드 생성, 디버깅, 통신을 완료할 수 있어 자기 조직화, 자기 협상하는 에이전트 네트워크의 견고한 기반을 마련했습니다.

동시에 우리는 meta-protocol의 지원으로 에이전트 네트워크에서 수많은 에이전트 간 합의를 달성하는 통신 프로토콜이 등장할 것이며, 이는 인간이 제정한 프로토콜 수를 훨씬 초과할 것이라고 믿습니다.

그러나 합리적인 프로토콜 선출 합의 알고리즘을 설계하고, 에이전트가 협상한 합의 프로토콜을 보고하도록 인센티브를 제공하며, 에이전트가 다른 에이전트들이 협상한 합의 프로토콜을 쉽게 얻을 수 있도록 하는 방법은 여전히 추가 논의가 필요합니다.

## 저작권 고지

Copyright (c) 2024 GaoWei Chang  
This file is released under the [MIT License](./LICENSE). You are free to use and modify it, but you must retain this copyright notice.
