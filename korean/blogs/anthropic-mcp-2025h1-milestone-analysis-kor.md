# Analysis of Anthropic MCP 2025H1 Milestones

Anthropic MCP가 2025년 상반기 로드맵을 방금 발표했습니다: [https://modelcontextprotocol.io/development/roadmap#roadmap](https://modelcontextprotocol.io/development/roadmap#roadmap).

우리는 agent 통신 프로토콜을 연구해 왔고 MCP에 세심한 관심을 기울여 왔으며, 커뮤니티에 우리의 제안 중 일부를 제출하기도 했습니다.

오늘 MCP에 대한 우리의 이해를 바탕으로 공식 마일스톤을 분석하고 MCP와 agent 통신 프로토콜에 대한 우리의 생각을 공유하겠습니다.

이 글의 주요 포인트는 다음과 같습니다:

- 원격 서버 지원. 이는 매우 중요합니다. 원격 서버 지원 중에서 인증 스킴 지원이 가장 중요한 측면입니다.
- 사용 임계값 낮추기. 현재 MCP의 사용 임계값이 너무 높습니다; 일반적으로 효과적으로 사용하려면 어느 정도의 개발 배경이 필요합니다.
- 커뮤니티와 표준. 미래에 MCP는 업계 표준이 되기 위해 노력할 것입니다.

## 마일스톤 분석

### 원격 MCP 지원

이전 글에서 MCP의 원격 데이터 접근 지원 부족이 포괄적인 신원 인증 스킴의 부재라는 중요한 결함 때문이라고 언급했습니다.

커뮤니티가 이 문제를 인식했다는 것을 알 수 있습니다. 첫 번째 마일스톤이 원격 MCP 연결을 지원하는 것이고, 인증과 권한 부여가 주요 초점이기 때문입니다.

현재 OAuth 2.0을 사용한 신원 인증에 대한 제안이 검토 중입니다. 우리는 커뮤니티의 관리자들과 소통했으며, 이 솔루션이 OAuth 2.0의 표준화와 광범위한 채택을 고려할 때 가장 실용적인 접근 방식인 것 같습니다.

이 제안의 가장 좋은 측면은 OAuth 2.0을 표준 프로세스로 취급하면서도 플러그 가능한 인증 스킴을 제안한다는 것입니다. 이는 다른 인증 방법들이 실험적 솔루션으로 사양에 통합될 수 있게 하여 사양의 유연성과 개방성을 크게 향상시킵니다.

이 제안을 바탕으로, 우리는 W3C DID 기반 인증 스킴을 개발했습니다 (<https://github.com/agent-network-protocol/AgentNetworkProtocol/blob/main/03-did%3Awba%20Method%20Design%20Specification.md>), 플러그 가능한 스킴이 병합되기를 기다린 후 PR을 제출할 예정입니다. W3C DID 인증과 OAuth 2.0의 차이점에 대해서는 이 글을 참조하세요: [Agent에게 가장 적합한 신원 인증 기술: OpenID Connect, API keys, did:wba 비교](https://github.com/agent-network-protocol/AgentNetworkProtocol/blob/main/blogs/Comparison%20of%20did%3Awba%20with%20OpenID%20Connect%20and%20API%20keys.md)

원격 MCP 지원의 다른 두 항목은:

- 서비스 발견으로, 이는 암묵적으로 서비스 설명을 포함합니다. 그들은 OpenAI의 GPT와 유사한 제품을 개발할 수 있으며, MCP 클라이언트가 인터페이스를 호출하여 선호하는 서버를 찾을 수 있습니다.
- 무상태 작업으로, 주로 서버 개발 복잡성을 줄이기 위함입니다.

### 배포 및 발견

이것은 세 번째 마일스톤이지만, 두 번째보다 더 중요하다고 생각합니다.

MCP의 현재 가장 큰 문제는 사용성이 좋지 않다는 것입니다. OpenAI의 GPT와 비교했을 때, MCP의 능력이 훨씬 강력하다고 할 수 있고 개발자에게 LLM과 상호작용하는 강력한 수단을 제공하지만, 사용성은 GPT에 훨씬 뒤처집니다. 현재 사용자는 일반적으로 어느 정도의 개발이나 컴퓨터 전문 지식을 가져야 하고 다양한 소프트웨어와 서비스를 스스로 설치해야 합니다. 이는 MCP 사용의 진입 장벽을 상당히 높입니다.

이 마일스톤의 네 가지 구성요소 - 패키지 관리, 설치 도구, 샌드박싱, 서버 등록 - 은 실제로 MCP를 더 사용자 친화적으로 만들 수 있습니다.

내 비전에서 MCP가 널리 퍼지려면 사용 장벽을 낮춰야 합니다. 그렇게 하는 동시에 커뮤니티 생태계와 상호작용해야 하므로 사용자가 다양한 MCP 서버를 쉽게 로드할 수 있게 하는 브라우저 플러그인과 유사한 메커니즘이 필요합니다. 이 네 가지 구성요소가 이 목표를 향해 나아가고 있는 것 같습니다.

또한 사용성을 개선하기 위해서는 원격 MCP 서버의 비율이 증가해야 합니다. 원격으로 할 수 있는 것은 로컬에서 하지 말아야 합니다. 이것이 원격 서버가 중요한 또 다른 이유입니다.

### Agent 지원

여기에는 세 가지 항목이 있습니다: 계층화된 agent 시스템, 상호작용 워크플로우, 스트리밍 결과. 이 영역은 커뮤니티 논의가 적었고, 마지막 항목을 제외하고는 다른 두 항목을 아직 완전히 이해하지 못했습니다. 우리는 이를 계속 모니터링할 것이며, 이 영역에 대한 좋은 통찰이 있으시면 언제든지 댓글을 남겨주세요.

### 더 많은 공식 생태계

MCP는 커뮤니티 주도의 표준 개발을 희망하고 있으며, 주요 관리자는 현재 Anthropic 개발자들입니다. 그들은 모든 AI 제공업체가 평등한 참여와 공유 거버넌스를 통해 MCP를 다양한 AI 애플리케이션과 사용 사례의 요구를 충족하는 개방 표준으로 형성하는 데 도움을 줄 수 있는 협업 생태계를 육성하는 것을 목표로 합니다. 그들은 또한 표준화 기구와 협력하여 표준화를 발전시키기를 희망합니다.

이는 좋은 접근 방식이며 업계 표준을 구축하는 일반적인 관행입니다. 현재 MCP 커뮤니티는 여전히 주로 제안 검토를 포함하여 Anthropic이 주도하고 있습니다. 더 많은 파트너를 영입하고 단일 공급업체 지배를 희석하는 것은 사양이 더 광범위한 채택을 얻는 데 도움이 될 것입니다. 우리는 이 과정에 계속 깊이 관여할 것입니다.

## 미래에 우리에게는 어떤 종류의 프로토콜이 필요한가

미래에 agent들이 통신을 위한 표준 프로토콜이 필요한지는 더 이상 논쟁거리가 아닌 것 같습니다. 사람들은 점차 그 필요성을 보기 시작했으며, 최근 Tencent Technology AI Future 기사([AI가 커피 주문을 도울 때, 앱들이 동의하는가?](https://mp.weixin.qq.com/s/vRSn0jKRwGa-Ut6G5keD8w))에서도 언급되었습니다. 업계 동료들과의 대화에서 이것이 합의가 되어가고 있다는 것을 발견했습니다.

불확실한 것은 미래에 우리가 어떤 종류의 프로토콜이 필요한지와 그것이 어떻게 업계의 수용을 얻을 것인지입니다.

미래 프로토콜의 개요에 관해서는 우리의 생각이 있습니다 ([Agentic Web을 다르게 만드는 것](https://github.com/agent-network-protocol/AgentNetworkProtocol/blob/main/blogs/What-Makes-Agentic-Web-Different.md)). 우리는 또한 우리의 사고를 바탕으로 이상적인 agent 통신 프로토콜인 AgentNetworkProtocol (ANP)를 설계하고 있습니다: [https://github.com/agent-network-protocol/AgentNetworkProtocol](https://github.com/agent-network-protocol/AgentNetworkProtocol).

우리는 미래의 agent들이 상호연결되어야 하고, 기본 데이터(API 또는 Protocol)를 통해 인터넷과 통신해야 하며, 동등한 존재로 존재해야 한다고 믿습니다. 이것들이 우리 ANP 설계의 핵심 원칙입니다.

MCP로 돌아가서, 우리 ANP와 MCP의 가장 큰 차이점은 세계관에 있습니다:

- MCP는 모델 중심적이며, 전체 인터넷이 그것의 컨텍스트와 도구 역할을 합니다
- 우리(Agent Network Protocol)는 agent 중심적이며, 각 agent가 동등한 지위를 가지고 분산형 agent 협업 네트워크를 형성합니다.

우리는 MCP와는 다소 다른 길을 가고 있으며, 성공하든 그렇지 않든 업계를 위한 가능성을 탐구하기를 희망합니다.

마지막으로, 우리의 프로토콜에도 관심이 있으시면 연락해 주세요. 특히 AI 휴대폰, AI 어시스턴트, 또는 agent 프레임워크 제품이나 개발에 관여하고 계시다면, 미래에 대한 우리의 비전을 논의하고 싶습니다.

WeChat: flow10240, 이메일: <chgaowei@gmail.com>으로 연락해 주세요.

## Copyright Notice

Copyright (c) 2024 GaoWei Chang  
This file is released under the [MIT License](./LICENSE). You are free to use and modify it, but you must retain this copyright notice.
