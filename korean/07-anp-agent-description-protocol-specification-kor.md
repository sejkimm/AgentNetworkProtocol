# ANP Agent Description Protocol Specification

## 초록

이 사양은 지능형 에이전트를 설명하기 위한 표준화된 프로토콜인 Agent Description Protocol (ADP)을 정의합니다. 에이전트가 자신의 공개 정보와 지원하는 인터페이스를 게시하는 방법을 정의합니다. 다른 에이전트는 이 에이전트의 설명을 얻고 정보 교환 및 협업에 참여할 수 있습니다.

이 사양은 ANP 프로토콜을 기반으로 한 두 에이전트 간의 정보 상호작용 패턴을 정의합니다.

사양의 핵심 내용은 다음과 같습니다:
1. JSON을 기본 데이터 형식으로 사용하고, 링크드 데이터 및 시맨틱 웹 기능을 지원
2. 에이전트 기본 정보, 제품, 서비스, 인터페이스 등에 대한 핵심 어휘 정의
3. 크로스 플랫폼 신원 인증을 가능하게 하는 통합 보안 메커니즘으로 did:wba 방법 채택. 신원 인증 방법은 확장 가능하며 향후 다른 방법을 지원할 수 있음
4. 기존 표준 프로토콜(OpenAPI, JSON-RPC 등)과의 상호운용성 지원

이 사양은 에이전트 간의 상호운용성과 통신 효율성을 향상시켜 에이전트 네트워크 구축을 위한 기반 지원을 제공하는 것을 목표로 합니다.

정보 상호작용 패턴 설계, 호환성 설계.

## 개요

Agent Description (AD) 문서는 에이전트에 액세스하기 위한 진입점으로, 웹사이트의 홈페이지와 유사합니다. 다른 에이전트는 이 AD 문서를 기반으로 에이전트의 이름, 엔티티 소유권, 기능, 제품, 서비스, 상호작용 API 또는 프로토콜에 대한 정보를 얻을 수 있습니다. 이 정보를 통해 에이전트 간의 데이터 통신과 협업이 달성될 수 있습니다.

이 사양은 주로 두 가지 문제를 해결합니다: 첫째, 두 에이전트 간의 정보 상호작용 패턴을 정의하고, 둘째, 에이전트 설명 문서의 형식을 정의합니다.

### 정보 상호작용 패턴

ANP는 "웹 크롤링"과 유사한 정보 상호작용 패턴을 채택하여, 에이전트가 URL을 사용하여 외부에 제공하는 데이터(파일, API 등)와 그 설명을 네트워크로 연결합니다. 다른 에이전트는 크롤러처럼 작동하여, 데이터 설명 정보를 기반으로 적절한 데이터를 선택하여 로컬에서 읽고, 로컬에서 의사결정과 처리를 수행할 수 있습니다. API 파일인 경우, API를 호출하여 에이전트와 상호작용할 수도 있습니다.

![anp-information-interact](../images/anp-information-interact.png)

"웹 크롤링" 정보 상호작용 패턴의 장점은 다음과 같습니다:
- 기존 인터넷 아키텍처와 유사하여 검색 엔진이 공개 에이전트 정보를 인덱싱하고 효율적인 에이전트 데이터 네트워크를 만드는 데 도움
- 원격 데이터를 로컬로 가져와서 모델 컨텍스트로 처리하는 것은 사용자 프라이버시 누출을 방지하는 데 도움. 작업 위임 상호작용 패턴은 작업 중에 사용자 프라이버시를 누출할 수 있음
- 자연스러운 계층 구조로 에이전트가 많은 수의 다른 에이전트와 상호작용하는 데 도움

### 핵심 개념

Information과 Interface는 ANP 에이전트 설명 사양의 핵심 개념입니다.

Information은 에이전트가 외부에 제공하는 데이터로, 텍스트 파일, 이미지, 비디오, 오디오 등을 포함합니다.

Interface는 에이전트가 외부에 제공하는 인터페이스입니다. 인터페이스는 두 가지 유형으로 나뉩니다:
- 한 유형은 자연어 인터페이스로, 에이전트가 자연어를 통해 더 개인화된 서비스를 제공할 수 있게 합니다;
- 다른 유형은 구조화된 인터페이스로, 에이전트가 더 효율적이고 표준화된 서비스를 제공할 수 있게 합니다.

자연어 인터페이스는 모델 기능을 사용하여 대부분의 시나리오를 충족할 수 있지만, 구조화된 인터페이스는 많은 시나리오에서 에이전트 간의 통신 효율성을 향상시킬 수 있습니다. 따라서 현재 구조화된 인터페이스와 자연어 인터페이스를 모두 지원하여 효율성과 개인화 사이의 균형을 달성합니다. 구조화된 인터페이스가 사용 가능한 경우, 모델은 구조화된 인터페이스를 우선시해야 합니다. 구조화된 인터페이스가 사용자 요구를 충족할 수 없는 경우, 자연어 인터페이스를 사용할 수 있습니다.

모든 에이전트가 자연어 인터페이스를 지원하는 것이 권장됩니다.

## 에이전트 설명 문서 형식

에이전트 설명 문서는 에이전트의 외부 진입점입니다.

향상된 AI 기능의 혜택으로, 에이전트 설명 문서는 완전히 자연어로 설명될 수 있고, 다른 에이전트는 기본적으로 그들이 표현하는 정보를 이해할 수 있습니다. 그러나 에이전트는 다양한 기능을 가진 서로 다른 모델을 사용하므로, 대부분의 모델이 데이터에 대해 통일되고 정확한 이해를 가질 수 있도록 보장하기 위해, 여전히 에이전트 설명 문서가 구조화된 설명을 사용하는 것을 권장합니다. 향후 개인화된 표현 기능을 향상시키기 위해, 문서는 내부적으로 자연어를 광범위하게 사용할 수 있습니다.

### ANP 호환 에이전트 설명

에이전트 설명 형식의 경우, JSON 형식을 사용하는 것을 권장합니다. 우리는 표준 JSON을 기반으로 ANP 호환 에이전트 설명 문서 형식을 정의했습니다.

#### 에이전트 설명 문서

다음은 에이전트 설명 문서의 예시입니다:

```json
{
  "protocolType": "ANP",
  "protocolVersion": "1.0.0",
  "type": "AgentDescription",
  "url": "https://grand-hotel.com/agents/hotel-assistant",
  "name": "Grand Hotel Assistant",
  "did": "did:wba:grand-hotel.com:service:hotel-assistant",
  "owner": {
    "type": "Organization",
    "name": "Grand Hotel Management Group",
    "url": "https://grand-hotel.com"
  },
  "description": "Grand Hotel Assistant is an intelligent hospitality agent providing comprehensive hotel services including room booking, concierge services, guest assistance, and real-time communication capabilities.",
  "created": "2024-12-31T12:00:00Z",
  "securityDefinitions": {
    "didwba_sc": {
      "scheme": "didwba",
      "in": "header",
      "name": "Authorization"
    }
  },
  "security": "didwba_sc",
  "Infomations": [
    {
      "type": "Product",
      "description": "Luxury hotel rooms with premium amenities and personalized services.",
      "url": "https://grand-hotel.com/products/luxury-rooms.json"
    },
    {
      "type": "Product", 
      "description": "Comprehensive concierge and guest services including dining, spa, and local attractions.",
      "url": "https://grand-hotel.com/products/concierge-services.json"
    },
    {
      "type": "Information",
      "description": "Complete hotel information including facilities, amenities, location, and policies.",
      "url": "https://grand-hotel.com/info/hotel-basic-info.json"
    },
    {
      "type": "VideoObject",
      "description": "Hotel virtual tour showcasing rooms, facilities, dining areas, and recreational amenities.",
      "url": "https://grand-hotel.com/media/hotel-tour-video.mp4"
    }
  ],
  "interfaces": [
    {
      "type": "NaturalLanguageInterface",
      "protocol": "YAML",
      "version": "1.2.2",
      "url": "https://grand-hotel.com/api/nl-interface.yaml",
      "description": "Natural language interface for conversational hotel services and guest assistance."
    },
    {
      "type": "StructuredInterface",
      "protocol": "YAML",
      "humanAuthorization": true,
      "version": "1.1",
      "url": "https://grand-hotel.com/api/booking-interface.yaml",
      "description": "Structured interface for hotel room booking and reservation management."
    },
    {
      "type": "StructuredInterface",
      "protocol": "JSON-RPC 2.0",
      "url": "https://grand-hotel.com/api/services-interface.json",
      "description": "JSON-RPC 2.0 interface for accessing hotel services and amenities."
    },
    {
      "type": "StructuredInterface",
      "protocol": "MCP",
      "version": "1.0",
      "url": "https://grand-hotel.com/api/mcp-interface.json",
      "description": "MCP-compatible interface for seamless integration with MCP-based systems."
    },
    {
      "type": "StructuredInterface",
      "protocol": "WebRTC",
      "version": "1.0",
      "url": "https://grand-hotel.com/api/webrtc-interface.yaml",
      "description": "WebRTC interface for real-time video communication and streaming services."
    }
  ],
  "proof": {
    "type": "EcdsaSecp256r1Signature2019",
    "created": "2024-12-31T15:00:00Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:wba:grand-hotel.com:service:hotel-assistant#keys-1",
    "challenge": "1235abcd6789",
    "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
  }
}
```

##### 호텔 에이전트 설명 문서 필드 설명

| 필드명 | 타입 | 필수 | 설명 |
|-----------|------|----------|-------------|
| protocolType | string | 필수 | 프로토콜 타입 식별자, 고정값 "ANP" |
| protocolVersion | string | 필수 | ANP 프로토콜 버전 번호, 현재 "1.0.0" |
| type | string | 필수 | 문서 타입 식별자, 에이전트 설명 문서의 경우 "AgentDescription" |
| url | string | 선택 | 에이전트의 네트워크 액세스 주소, 에이전트 위치 식별용 |
| name | string | 필수 | 에이전트의 사람이 읽을 수 있는 이름, 예: "Grand Hotel Assistant" |
| did | string | 선택 | 에이전트의 탈중앙화 식별자, 신원 검증용 |
| owner | object | 선택 | 에이전트 소유자 정보, 조직 이름 및 URL 포함 |
| description | string | 선택 | 에이전트의 상세한 기능 설명 및 서비스 설명 |
| created | string | 선택 | 에이전트 설명 문서 생성 시간, ISO 8601 형식 |
| securityDefinitions | object | 필수 | 보안 메커니즘 정의, 인증 방법 및 매개변수 위치 포함 |
| security | string | 필수 | 활성화된 보안 구성 이름, securityDefinitions의 정의를 참조 |
| Infomations | array | 선택 | 에이전트가 제공하는 정보 리소스 목록, 제품, 서비스, 미디어 파일 등 포함 |
| interfaces | array | 선택 | 에이전트가 지원하는 상호작용 인터페이스 목록, 다양한 프로토콜 및 API 포함 |
| proof | object | 선택 | 디지털 서명 및 무결성 검증 정보, 문서 변조 방지 |

##### Information 객체 필드 설명

| 필드명 | 타입 | 설명 |
|-----------|------|-------------|
| type | string | 정보 타입, 예: "Product", "Information", "VideoObject" 등 |
| description | string | 정보 내용의 상세 설명 |
| url | string | 정보 리소스의 액세스 주소 |

##### Interface 객체 필드 설명

| 필드명 | 타입 | 설명 |
|-----------|------|-------------|
| type | string | 인터페이스 타입, 예: "NaturalLanguageInterface", "StructuredInterface" |
| protocol | string | 인터페이스가 사용하는 프로토콜, 예: "YAML", "JSON-RPC 2.0", "MCP", "WebRTC" |
| version | string | 인터페이스 버전 번호 |
| url | string | 인터페이스 정의 문서의 주소 |
| description | string | 인터페이스 기능 및 목적 설명 |
| humanAuthorization | boolean | 인간 승인이 필요한지 여부, 예약 결제와 같은 민감한 작업에 적용 |

#### 제품 설명 문서

다음은 Product 설명의 예시입니다:

```json
{
  "protocolType": "ANP",
  "protocolVersion": "1.0.0",
  "type": "Product",
  "url": "https://grand-hotel.com/products/deluxe-suite.json",
  "identifier": "deluxe-suite-001",
  "name": "Deluxe Suite",
  "description": "A luxurious suite with separate living room and bedroom, equipped with panoramic floor-to-ceiling windows, premium sound system, and smart home controls. Suitable for business travelers and high-end customers.",
  "security": {
    "didwba": {
      "scheme": "didwba",
      "in": "header",
      "name": "Authorization"
    }
  },
  "brand": {
    "type": "Brand",
    "name": "Grand Hotel"
  },
  "additionalProperty": [
    {
      "type": "PropertyValue",
      "name": "Room Area",
      "value": "80 square meters"
    },
    {
      "type": "PropertyValue", 
      "name": "Bed Type",
      "value": "King Size Bed"
    }
  ],
  "offers": {
    "type": "Offer",
    "price": "1288",
    "priceCurrency": "CNY",
    "availability": "https://schema.org/InStock",
    "priceValidUntil": "2025-12-31"
  },
  "amenityFeature": [
    {
      "type": "LocationFeatureSpecification",
      "name": "Free WiFi",
      "value": true
    },
    {
      "type": "LocationFeatureSpecification", 
      "name": "Air Conditioning",
      "value": true
    }
  ],
  "category": "Hotel Room",
  "sku": "GH-DELUXE-SUITE-001",
  "image": [
    {
      "type": "ImageObject",
      "url": "https://grand-hotel.com/images/deluxe-suite-bedroom.jpg",
      "caption": "Deluxe Suite - Bedroom Area",
      "description": "Spacious bedroom with king-size bed and premium bedding"
    },
    {
      "type": "ImageObject",
      "url": "https://grand-hotel.com/images/deluxe-suite-living.jpg", 
      "caption": "Deluxe Suite - Living Area",
      "description": "Separate living room with sofa, coffee table, and work area"
    },
    {
      "type": "ImageObject",
      "url": "https://grand-hotel.com/images/deluxe-suite-view.jpg",
      "caption": "Deluxe Suite - City View",
      "description": "Panoramic floor-to-ceiling windows offering excellent city views"
    }
  ],
  "audience": {
    "type": "Audience",
    "audienceType": "Business travelers, luxury guests",
    "geographicArea": "Global"
  },
  "manufacturer": {
    "type": "Organization", 
    "name": "Grand Hotel Management Group",
    "url": "https://grand-hotel.com"
  }
}
```

##### 제품 설명 문서 필드 설명

| 필드명 | 타입 | 필수 | 설명 |
|-----------|------|----------|-------------|
| protocolType | string | 필수 | 프로토콜 타입 식별자, 고정값 "ANP" |
| protocolVersion | string | 필수 | ANP 프로토콜 버전 번호 |
| type | string | 필수 | 객체 타입, 제품의 경우 "Product" |
| url | string | 선택 | 제품 정보의 네트워크 주소 |
| identifier | string | 선택 | 제품의 고유 식별자 |
| name | string | 필수 | 제품명 |
| description | string | 필수 | 제품의 상세 설명 |
| brand | object | 선택 | 제품 브랜드 정보 |
| additionalProperty | array | 선택 | 제품의 추가 속성 및 기능 |
| offers | object | 선택 | 제품의 가격 및 가용성 정보 |
| amenityFeature | array | 선택 | 제품이 제공하는 시설 및 기능 |
| category | string | 선택 | 제품 카테고리 |
| sku | string | 선택 | 제품 SKU 코드 |
| image | array | 선택 | 제품 이미지 목록 |
| audience | object | 선택 | 타겟 고객층 |
| manufacturer | object | 선택 | 제품 공급자 정보 |

#### JSON-RPC 2.0 인터페이스 설명 문서

다음은 JSON-RPC 2.0 인터페이스 설명 문서의 예시입니다:

```json
{
  "protocolType": "ANP",
  "protocolVersion": "1.0.0",
  "type": "JSON-RPC 2.0",
  "url": "https://grand-hotel.com/api/jsonrpc-interface.json",
  "security": {
    "didwba": {
      "scheme": "didwba",
      "in": "header",
      "name": "Authorization"
    }
  },
  "transport": {
    "protocol": "HTTPS",
    "host": "grand-hotel.com",
    "path": "/api/v1/jsonrpc",
    "method": "POST",
    "port": 443,
    "contentType": "application/json"
  },
  "info": {
    "title": "Grand Hotel Services API",
    "version": "1.0.0",
    "description": "JSON-RPC 2.0 API for hotel services including room management, booking, and guest services"
  },
  "methods": [
    {
      "name": "searchRooms",
      "description": "Search available hotel rooms based on criteria",
      "params": {
        "type": "object",
        "properties": {
          "checkIn": {
            "type": "string",
            "format": "date",
            "description": "Check-in date in YYYY-MM-DD format"
          },
          "checkOut": {
            "type": "string", 
            "format": "date",
            "description": "Check-out date in YYYY-MM-DD format"
          },
          "guests": {
            "type": "integer",
            "minimum": 1,
            "maximum": 8,
            "description": "Number of guests"
          },
          "roomType": {
            "type": "string",
            "enum": ["standard", "deluxe", "suite", "presidential"],
            "description": "Preferred room type"
          }
        },
        "required": ["checkIn", "checkOut", "guests"]
      },
      "result": {
        "type": "object",
        "properties": {
          "rooms": {
            "type": "array",
            "items": {
              "$ref": "#/definitions/Room"
            }
          },
          "total": {
            "type": "integer",
            "description": "Total number of available rooms"
          }
        }
      }
    },
    {
      "name": "makeReservation",
      "description": "Create a new hotel reservation",
      "params": {
        "type": "object",
        "properties": {
          "roomId": {
            "type": "string",
            "description": "Unique room identifier"
          },
          "guestInfo": {
            "$ref": "#/definitions/GuestInfo"
          },
          "checkIn": {
            "type": "string",
            "format": "date"
          },
          "checkOut": {
            "type": "string",
            "format": "date"
          },
          "specialRequests": {
            "type": "string",
            "description": "Special requests or preferences"
          }
        },
        "required": ["roomId", "guestInfo", "checkIn", "checkOut"]
      },
      "result": {
        "type": "object",
        "properties": {
          "reservationId": {
            "type": "string",
            "description": "Unique reservation identifier"
          },
          "confirmationNumber": {
            "type": "string",
            "description": "Booking confirmation number"
          },
          "totalAmount": {
            "type": "number",
            "description": "Total reservation amount"
          }
        }
      }
    }
  ],
  "definitions": {
    "Room": {
      "type": "object",
      "properties": {
        "id": {"type": "string"},
        "type": {"type": "string"},
        "price": {"type": "number"},
        "amenities": {"type": "array", "items": {"type": "string"}},
        "availability": {"type": "boolean"}
      }
    },
    "GuestInfo": {
      "type": "object",
      "properties": {
        "firstName": {"type": "string"},
        "lastName": {"type": "string"},
        "email": {"type": "string", "format": "email"},
        "phone": {"type": "string"}
      },
      "required": ["firstName", "lastName", "email"]
    }
  }
}
```

##### JSON-RPC 2.0 인터페이스 문서 필드 설명

| 필드명 | 타입 | 필수 | 설명 |
|-----------|------|----------|-------------|
| jsonrpc | string | 필수 | JSON-RPC 프로토콜 버전, 고정값 "2.0" |
| security | object | 필수 | 보안 메커니즘 정의, 인증 방법 및 매개변수 구성 포함 |
| transport | object | 필수 | 전송 계층 구성, 프로토콜, 엔드포인트, 메서드 등 포함 |
| info | object | 필수 | API 기본 정보, 제목, 버전, 설명 포함 |
| methods | array | 필수 | 호출 가능한 메서드 목록 |
| definitions | object | 선택 | 재사용을 위한 데이터 구조 정의 |

##### Transport 객체 필드 설명

| 필드명 | 타입 | 설명 |
|-----------|------|-------------|
| protocol | string | 전송 프로토콜, 예: "HTTPS", "HTTP" |
| host | string | 서버 호스트명 |
| basePath | string | API 기본 경로 |
| endpoint | string | 특정 API 엔드포인트 경로 |
| port | integer | 포트 번호, HTTPS 기본값 443, HTTP 기본값 80 |
| method | string | HTTP 요청 메서드, JSON-RPC는 일반적으로 "POST" 사용 |
| contentType | string | 콘텐츠 타입, 일반적으로 "application/json" |
| fullUrl | string | 완전한 API 액세스 주소 |

##### Method 객체 필드 설명

| 필드명 | 타입 | 설명 |
|-----------|------|-------------|
| name | string | RPC 호출을 위한 메서드명 |
| description | string | 메서드 기능 설명 |
| params | object | 입력 매개변수의 JSON Schema 정의 |
| result | object | 반환 결과의 JSON Schema 정의 |

#### MCP Server 인터페이스 문서 설명

완료 예정

### JSON-LD 형식

### 보안 메커니즘

Agent Description Protocol은 현재 보안 메커니즘으로 did:wba 방법을 사용합니다. did:wba 방법은 크로스 플랫폼 신원 인증 및 에이전트 통신의 요구를 충족하도록 설계된 웹 기반 Decentralized Identifier (DID) 사양입니다.

향후 필요에 따라 다른 신원 인증 체계를 확장할 수 있습니다.

#### DIDWBASecurityScheme (DID WBA 보안 체계)

did:wba 방법을 기반으로 한 보안 메커니즘 구성의 메타데이터를 설명합니다. 체계 이름에 할당된 값은 에이전트 설명에 포함된 어휘에서 정의되어야 합니다.

모든 보안 체계의 경우, 액세스를 직접 제공하는 키, 비밀번호 또는 기타 민감한 정보는 AD에 저장하지 않고, 다른 메커니즘을 통해 대역 외에서 공유하고 저장해야 합니다. AD의 목적은 에이전트에 액세스하는 방법을 설명하는 것이지(소비자가 이미 승인된 경우), 해당 승인을 부여하는 것이 아닙니다.

보안 체계는 일반적으로 디지털 서명과 같은 추가 인증 매개변수가 필요합니다. 이 정보의 위치는 name과 연결된 값으로 표시되며, 일반적으로 in의 값과 함께 사용됩니다. in과 연결된 값은 다음 중 하나를 취할 수 있습니다:

- header: 매개변수는 프로토콜에서 제공하는 헤더에 제공되며, 헤더 이름은 name의 값으로 제공됩니다. did:wba 방법에서는 인증 정보가 Authorization 헤더를 통해 전달됩니다.
- query: 매개변수는 URI에 쿼리 매개변수로 추가되며, 쿼리 매개변수 이름은 name으로 제공됩니다.
- body: 매개변수는 요청 페이로드의 본문에 제공되며, name으로 제공되는 데이터 스키마 요소를 사용합니다.
- cookie: 매개변수는 name 값으로 식별되는 쿠키에 저장됩니다.
- uri: 매개변수는 URI 자체에 포함되며, 관련 상호작용에서 정의된 URI 템플릿 변수에 의해 인코딩됩니다(name의 값으로 정의).
- auto: 위치는 프로토콜의 일부로 결정되거나 협상됩니다. SecurityScheme의 in 필드가 auto 값으로 설정된 경우, name 필드는 설정되지 않아야 합니다.

표 2: 보안 체계 수준 어휘 용어

| 어휘 용어 | 설명 | 필수 | 타입 |
|----------------|-------------|----------|------|
| type | 객체에 시맨틱 레이블을 추가하기 위한 객체 타입 식별자. | 선택 | string |
| description | 기본 언어를 기반으로 한 추가적인(사람이 읽을 수 있는) 정보. | 선택 | string |
| scheme | 보안 메커니즘 식별자 | 필수 | string |
| in | 인증 매개변수의 위치. | 필수 | string |
| name | 인증 매개변수의 이름. | 필수 | string |

다음은 did:wba 방법을 사용한 보안 구성의 예시입니다:

```json
{
    "securityDefinitions": {
        "didwba_sc": {
            "scheme": "didwba",
            "in": "header",
            "name": "Authorization"
        }
    },
    "security": "didwba_sc"
}
```

AD에서 보안 구성은 필수입니다. 보안 정의는 에이전트 수준의 security 멤버를 통해 활성화되어야 합니다. 이 구성은 에이전트와 상호작용하는 데 필요한 보안 메커니즘입니다.

보안이 AD 문서의 최상위 수준에 나타나면, 모든 리소스가 액세스될 때 이 보안 메커니즘을 사용하여 검증해야 함을 의미합니다. 특정 리소스 내부에 나타나면, 이 보안 메커니즘이 만족될 때만 해당 리소스에 액세스할 수 있음을 의미합니다. 최상위 수준에서 지정된 보안과 리소스에서 지정된 보안이 다른 경우, 리소스에서 지정된 보안이 우선합니다.

### 인간 수동 승인

인터페이스가 호출될 때 구매 인터페이스와 같이 인간의 수동 승인이 필요한 경우, 인터페이스 정의에 humanAuthorization 필드를 추가할 수 있습니다. True는 인터페이스 호출이 액세스하기 위해 인간의 수동 승인이 필요함을 나타냅니다.

### Proof (무결성 검증)

AD 문서가 악의적으로 변조, 가장 또는 재사용되는 것을 방지하기 위해, AD 문서에 검증 정보 Proof를 추가했습니다. Proof 정의는 사양을 참조할 수 있습니다: [https://www.w3.org/TR/vc-data-integrity/#defn-domain](https://www.w3.org/TR/vc-data-integrity/#defn-domain).

여기서:
- domain: AD 문서가 저장된 도메인명을 정의합니다. 사용자는 문서를 얻은 후 문서를 얻은 도메인명이 domain에서 정의된 도메인명과 일치하는지 확인해야 합니다. 일치하지 않으면 이 문서는 가짜일 수 있습니다.
- challenge: 변조를 방지하기 위한 검증을 위한 도전 정보를 정의합니다. domain을 지정할 때는 challenge도 지정해야 합니다.
- verificationMethod: 검증 방법을 정의하며, 현재 did:wba 문서의 검증 방법을 사용합니다. 향후 더 많은 방법을 확장할 수 있습니다.
- proofValue: 디지털 서명을 담습니다. 생성 규칙은 다음과 같습니다:
  - proofValue 필드 없이 웹사이트의 AD 문서를 생성합니다
  - [JCS (JSON Canonicalization Scheme)](https://www.rfc-editor.org/rfc/rfc8785)를 사용하여 위의 AD 문서를 정규화하고 정규화된 문자열을 생성합니다.
  - SHA-256 알고리즘을 사용하여 정규화된 문자열을 해시하고 해시 값을 생성합니다.
  - 클라이언트의 개인 키를 사용하여 해시 값에 서명하고, 서명 값을 생성하여 URL-safe Base64 인코딩을 수행합니다.
  - 서명 검증 과정은 위 과정의 역순입니다.

## 공통 정의 표준화

커피 한 잔이나 장난감과 같은 특정 제품이나 서비스의 경우, schema.org Product 속성의 하위 집합을 사용하여 특정 타입을 정의하고 제품 설명 방법을 명확히 할 수 있습니다. 이렇게 하면 모든 에이전트가 제품 데이터를 구성할 때 통합된 정의를 사용할 수 있어 서로 다른 에이전트 간의 상호운용성을 가능하게 합니다.

인터페이스에도 유사한 접근 방식을 사용할 수 있습니다. 예를 들어, 제품 구매 인터페이스의 경우, 모든 에이전트가 사용할 수 있는 통합된 구매 인터페이스 사양을 정의하여 서로 다른 에이전트 간의 상호운용성을 가능하게 할 수 있습니다.

## 저작권 고지  
Copyright (c) 2024 GaoWei Chang  
이 파일은 [MIT License](./LICENSE) 하에 릴리스됩니다. 자유롭게 사용 및 수정할 수 있지만 이 저작권 고지를 유지해야 합니다.