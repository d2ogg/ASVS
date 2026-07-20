# V17 WebRTC

## 控制目标

Web 实时通信（WebRTC）让现代应用能够进行实时语音、视频和数据交换。随着采用增加，保护 WebRTC 基础设施变得至关重要。本节为开发、托管或集成 WebRTC 系统的相关方提供安全要求。

WebRTC 市场大致可分为三个部分：

1. 产品开发者：创建并供应 WebRTC 产品和解决方案的专有及开源厂商。他们的重点是开发可供他人使用的稳健、安全 WebRTC 技术。

2. 通信平台即服务（CPaaS）：提供 API、SDK 以及必要基础设施或平台，以启用 WebRTC 功能的提供方。CPaaS 提供方可能使用第一类产品，也可能开发自己的 WebRTC 软件来提供这些服务。

3. 服务提供方：利用产品开发者或 CPaaS 提供方的产品，或开发自有 WebRTC 解决方案的组织。它们为在线会议、医疗、在线学习以及其他实时通信至关重要的领域创建和实现应用。

这里概述的安全要求主要面向以下产品开发者、CPaaS 和服务提供方：

* 使用开源解决方案构建其 WebRTC 应用。
* 将商业 WebRTC 产品作为其基础设施的一部分。
* 使用内部开发的 WebRTC 解决方案，或将多个组件集成为统一服务。

需要注意的是，这些安全要求不适用于完全使用 CPaaS 厂商提供的 SDK 和 API 的开发人员。对于这类开发人员，CPaaS 提供方通常负责其平台内大多数底层安全问题，像 ASVS 这样的通用安全标准可能无法完全满足他们的需求。

## V17.1 TURN 服务器

本节定义运行自有 TURN（Traversal Using Relays around NAT）服务器的系统安全要求。TURN 服务器帮助在受限网络环境中中继媒体，但配置错误时也可能带来风险。这些控制聚焦于安全地址过滤和防止资源耗尽。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **17.1.1** | 验证 Traversal Using Relays around NAT（TURN）服务只允许访问未保留作特殊用途的 IP 地址（例如内部网络、广播、回环）。注意，这同时适用于 IPv4 和 IPv6 地址。 | 2 |
| **17.1.2** | 验证当合法用户尝试在 TURN 服务器上打开大量端口时，Traversal Using Relays around NAT（TURN）服务不会受到资源耗尽影响。 | 3 |

## V17.2 媒体

这些要求只适用于托管自有 WebRTC 媒体服务器的系统，例如选择性转发单元（SFU）、多点控制单元（MCU）、录制服务器或网关服务器。媒体服务器处理并分发媒体流，其安全性对于保护对等方之间的通信至关重要。在 WebRTC 应用中，保护媒体流是重中之重，以防止窃听、篡改和拒绝服务攻击危及用户隐私和通信质量。

尤其需要实现针对洪泛攻击的防护，例如速率限制、验证时间戳、使用同步时钟匹配实时间隔，以及管理缓冲区以防止溢出并保持正确时序。如果某个媒体会话的数据包到达过快，应丢弃多余数据包。通过实现输入验证、安全处理整数溢出、防止缓冲区溢出，以及采用其他稳健错误处理技术来保护系统免受畸形数据包影响，也很重要。

完全依赖 Web 浏览器之间点对点媒体通信，且没有中间媒体服务器参与的系统，不适用这些特定媒体相关安全要求。

本节提到在 WebRTC 语境中使用 Datagram Transport Layer Security（DTLS）。关于记录密码学密钥管理策略的要求位于“密码学”章节。关于已批准密码学方法的信息，可在 ASVS 密码学附录中找到，也可参考 NIST SP 800‑52 Rev. 2 或 BSI TR‑02102‑2（2025‑01 版）等文档。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **17.2.1** | 验证按照成文的密码学密钥管理策略，对 Datagram Transport Layer Security（DTLS）证书密钥进行管理和保护。 | 2 |
| **17.2.2** | 验证媒体服务器已配置为使用并支持获批准的 Datagram Transport Layer Security（DTLS）密码套件，以及用于为 Secure Real-time Transport Protocol 建立密钥的 DTLS 扩展（DTLS-SRTP）的安全保护配置文件。 | 2 |
| **17.2.3** | 验证媒体服务器会检查 Secure Real-time Transport Protocol（SRTP）认证，以防止 Real-time Transport Protocol（RTP）注入攻击导致拒绝服务状况，或将音频、视频媒体插入媒体流。 | 2 |
| **17.2.4** | 验证媒体服务器在收到畸形 Secure Real-time Transport Protocol（SRTP）数据包时，仍能正常处理传入的媒体流量。 | 2 |
| **17.2.5** | 验证媒体服务器在合法用户产生 Secure Real-time Transport Protocol（SRTP）数据包洪泛时，仍能正常处理传入的媒体流量。 | 3 |
| **17.2.6** | 验证媒体服务器不会受到 Datagram Transport Layer Security（DTLS）中的 "ClientHello" 竞态条件漏洞影响，方式可以是检查该媒体服务器是否公开已知存在漏洞，或执行竞态条件测试。 | 3 |
| **17.2.7** | 验证与媒体服务器关联的音频或视频录制机制，在合法用户产生 Secure Real-time Transport Protocol（SRTP）数据包洪泛时，仍能正常处理传入的媒体流量。 | 3 |
| **17.2.8** | 验证会根据 Session Description Protocol（SDP）fingerprint 属性检查 Datagram Transport Layer Security（DTLS）证书；如果检查失败，则终止媒体流，以确保媒体流真实性。 | 3 |

## V17.3 信令

本节定义运行自有 WebRTC 信令服务器的系统安全要求。信令负责协调点对点通信，并且必须能够抵御可能破坏会话建立或控制的攻击。

为确保信令安全，系统必须能够优雅处理畸形输入，并在负载下保持可用。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **17.3.1** | 验证信令服务器在洪泛攻击期间仍能处理合法的传入信令消息。这应通过在信令层实施速率限制来实现。 | 2 |
| **17.3.2** | 验证信令服务器在收到可能导致拒绝服务的畸形信令消息时，仍能正常处理合法信令消息。可采用的措施包括输入验证、安全处理整数溢出、防止缓冲区溢出，以及其他稳健的错误处理技术。 | 2 |

## 参考资料

更多信息，另见：

* WebRTC DTLS ClientHello DoS 的最佳文档包括 [Enable Security 面向安全专业人员的博客文章](https://www.enablesecurity.com/blog/novel-dos-vulnerability-affecting-webrtc-media-servers/) 以及相关的[面向 WebRTC 开发人员的白皮书](https://www.enablesecurity.com/blog/webrtc-hello-race-conditions-paper/)
* [RFC 3550 - RTP: A Transport Protocol for Real-Time Applications](https://www.rfc-editor.org/rfc/rfc3550)
* [RFC 3711 - The Secure Real-time Transport Protocol (SRTP)](https://datatracker.ietf.org/doc/html/rfc3711)
* [RFC 5764 - Datagram Transport Layer Security (DTLS) Extension to Establish Keys for the Secure Real-time Transport Protocol (SRTP))](https://datatracker.ietf.org/doc/html/rfc5764)
* [RFC 8825 - Overview: Real-Time Protocols for Browser-Based Applications](https://www.rfc-editor.org/info/rfc8825)
* [RFC 8826 - Security Considerations for WebRTC](https://www.rfc-editor.org/info/rfc8826)
* [RFC 8827 - WebRTC Security Architecture](https://www.rfc-editor.org/info/rfc8827)
* [DTLS-SRTP Protection Profiles](https://www.iana.org/assignments/srtp-protection/srtp-protection.xhtml)
