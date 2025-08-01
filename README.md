<div align="center">
  
[English](README.md) | [中文](README.cn.md) | [한국어](README.kr.md)

</div>

## AgentNetworkProtocol(ANP)

> TL;DR: ANP aims to become the HTTP of the agent internet era.

<!-- TOC -->
### Table of Contents

- [Vision and Positioning](#vision-and-positioning)
- [Why We Need ANP](#why-we-need-anp)
- [Three-Layer Protocol Architecture](#three-layer-protocol-architecture)
- [Quick Start](#quick-start)
- [Protocol Implementation](#protocol-implementation)
- [Further Reading](#further-reading)
- [Milestones](#milestones)
- [Contact Us](#contact-us)
- [Contributing](#contributing)
- [License](#license)

## Vision and Positioning

AgentNetworkProtocol(ANP) is an open-source communication protocol for intelligent agents.

AgentNetworkProtocol(ANP) aims to become **the HTTP of the agent internet era**.

Our vision is to **define how agents connect with each other, building an open, secure, and efficient collaboration network for billions of agents**.

<p align="center">
  <img src="/images/agentic-web3.png" width="50%" alt="Agentic Web"/>
</p>

We believe that the agent internet represents the next generation of information infrastructure following the human internet, which will fundamentally transform how the digital world connects and collaborates. In this vision:

- **From Platform-Centric to Protocol-Centric**: The current internet ecosystem is built around platforms, where data and services are locked in "digital silos." The agent internet will reshape this imbalance, returning the internet from a closed, fragmented state to its open, freely connected origins.

- **Connection is Power**: In a truly open, interconnected network, free interaction between nodes maximizes innovation potential and creates tremendous value. In the future, each agent will be both an information consumer and a service provider, with every node able to discover, connect, and interact with any other node in the network without barriers.

- **AI-Native Network**: Unlike webpages and interfaces designed for humans, the agent internet will build an AI-friendly native data network where all nodes are describable, discoverable, and callable agents or data units, and every link is a semantically clear, structurally unified protocol connection.

This vision requires a foundational protocol similar to what HTTP is for the human internet—this is precisely why ANP was created.

**Note**: This project has not issued any digital currency on any platform or blockchain.

## Why We Need ANP

While current internet infrastructure is quite comprehensive, there remains a lack of optimal communication and connection solutions for the specific needs of agent networks. We are committed to addressing three major challenges facing agent networks:

- 🌐 **Interconnection**: Enabling all agents to communicate with each other, breaking down data silos, and allowing AI to access complete contextual information.
- 🖥️ **Native Interfaces**: AI should not need to mimic human internet browsing; instead, AI should interact with the digital world using its most proficient methods (APIs or communication protocols).
- 🤝 **Efficient Collaboration**: Using AI, agents can self-organize and self-negotiate to build a more cost-effective and efficient collaboration network than the existing internet.

## Three-Layer Protocol Architecture

<p align="center">
  <img src="/images/anp-architecture.png" width="50%" alt="Protocol Layer Diagram"/>
</p>

- 🔒 **Identity and Secure Communication Layer**: Based on the W3C DID (Decentralized Identifiers) specification, built on existing mature web infrastructure to create a decentralized identity authentication scheme and end-to-end encrypted communication solution. It allows agents from any platform to authenticate each other without relying on any centralized system.
- 🌍 **Meta-Protocol Layer**: A protocol for negotiating communication protocols between agents. It is key to evolving the agent network into a self-organizing, self-negotiating, efficient collaboration network.
- 📡 **Application Protocol Layer**: Based on semantic web specifications, enabling agents to describe their capabilities and supported application protocols, and efficiently manage these protocols.

## Quick Start

If you want to quickly understand the basic concepts and usage of ANP, you can check our getting started guide: [ANP Getting Started Guide](docs/chinese/ANP入门指南.md)

If you want to quickly run ANP-related demos, you can check our sample program documentation: [ANP Sample Programs](docs/chinese/ANP示例程序.md)

## Protocol SDK

We are developing an open-source implementation of AgentNetworkProtocol, repository at: [https://github.com/agent-network-protocol/AgentConnect](https://github.com/agent-network-protocol/AgentConnect)

## Further Reading

- Complete materials at [Extended Reading](docs/links.md)  
- For detailed design, please read the [ANP Technical White Paper](/01-agentnetworkprotocol-technical-white-paper.md)  
- Reference open-source implementation [AgentConnect Examples](https://github.com/agent-network-protocol/AgentConnect)

## Milestones

For both the protocol and open-source code implementation, we are gradually advancing in the following order:

- [x] Build identity authentication and end-to-end encrypted communication protocol and implementation. This is the foundation and core of our entire project, with protocol design and code basically completed.
- [x] Meta-protocol design and meta-protocol code implementation. Current protocol design and code development basically completed.
- [x] Application layer protocol design and development.
  - [x] Support for agent description.
  - [x] Support for agent discovery.
  - [ ] Application protocol design for specific domains.

## Contact Us

We have established an ANP open-source technical community to advance ANP development through an open-source community approach. We sincerely invite you to join our open-source technical community. Our founding committee, community advisors, technical committee, development committee, enterprise observers, and other teams are continuously recruiting.

Email: <chgaowei@gmail.com>  

- Discord: [https://discord.gg/sFjBKTY7sB](https://discord.gg/sFjBKTY7sB)  
- Official website: [https://agent-network-protocol.com/](https://agent-network-protocol.com/)  
- GitHub: [https://github.com/agent-network-protocol/AgentNetworkProtocol](https://github.com/agent-network-protocol/AgentNetworkProtocol)
- WeChat: flow10240

## Contributing

We welcome contributions in any form. Please refer to the [CONTRIBUTING.md](CONTRIBUTING.md) file.

## License

This project is open-sourced under the MIT License. For details, please refer to the [LICENSE](LICENSE) file. However, copyright belongs to GaoWei Chang. Any user of this project must retain the original copyright notice and license file.

## Copyright Notice  

Copyright (c) 2024 GaoWei Chang  
This file is released under the [MIT License](./LICENSE). You are free to use and modify it, but must retain this copyright notice.
