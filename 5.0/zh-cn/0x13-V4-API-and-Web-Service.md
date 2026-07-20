# V4 API 和 Web 服务

## 控制目标

对于向 Web 浏览器或其他调用方开放 API 的应用（通常使用 JSON、XML 或 GraphQL），需要考虑若干特有的安全问题。本章介绍应采用的相关安全配置和机制。

请注意，其他章节中的认证、会话管理和输入验证相关关注点同样适用于 API，因此不能脱离上下文理解本章，也不能孤立测试本章。

## V4.1 通用 Web 服务安全

本节处理一般 Web 服务安全考虑，因此也涵盖基本的 Web 服务卫生实践。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **4.1.1** | 验证每个带消息正文的 HTTP 响应都包含 Content-Type 头字段，且该字段与响应的实际内容匹配，并包含 charset 参数以按 IANA Media Types 指定安全字符编码（例如 UTF-8、ISO-8859-1），例如 "text/"、"/+xml" 和 "/xml"。 | 1 |
| **4.1.2** | 验证只有面向用户的端点（用于人工通过 Web 浏览器访问）会自动从 HTTP 重定向到 HTTPS，而其他服务或端点不实现透明重定向。这样可以避免客户端错误发送未加密 HTTP 请求，但由于请求被自动重定向到 HTTPS，敏感数据泄露未被发现的情况。 | 2 |
| **4.1.3** | 验证应用所使用且由中间层设置的任何 HTTP 头字段，例如负载均衡器、Web 代理或 BFF 服务设置的头字段，均不能被最终用户覆盖。此类头字段可能包括 X-Real-IP、X-Forwarded-* 或 X-User-ID。 | 2 |
| **4.1.4** | 验证只有应用或其 API 明确支持的 HTTP 方法（包括预检请求中的 OPTIONS）可以使用，未使用的方法会被阻止。 | 3 |
| **4.1.5** | 验证对于高度敏感或会经过多个系统的请求或交易，使用逐消息数字签名，在传输层保护之外提供额外保障。 | 3 |

## V4.2 HTTP 消息结构验证

本节说明应如何验证 HTTP 消息的结构和头字段，以防止请求走私、响应拆分、头注入，以及由过长 HTTP 消息导致的拒绝服务等攻击。

这些要求适用于一般 HTTP 消息处理和生成，但在不同 HTTP 版本之间转换 HTTP 消息时尤其重要。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **4.2.1** | 验证所有应用组件（包括负载均衡器、防火墙和应用服务器）使用适合 HTTP 版本的机制确定传入 HTTP 消息边界，以防止 HTTP 请求走私。在 HTTP/1.x 中，如果存在 Transfer-Encoding 头字段，则必须按照 RFC 2616 忽略 Content-Length 头。使用 HTTP/2 或 HTTP/3 时，如果存在 Content-Length 头字段，接收方必须确保它与 DATA 帧长度一致。 | 2 |
| **4.2.2** | 验证生成 HTTP 消息时，Content-Length 头字段不会与 HTTP 协议分帧所确定的内容长度冲突，以防止请求走私攻击。 | 3 |
| **4.2.3** | 验证应用不会发送也不会接受带有连接特定头字段（例如 Transfer-Encoding）的 HTTP/2 或 HTTP/3 消息，以防止响应拆分和头注入攻击。 | 3 |
| **4.2.4** | 验证应用只接受头字段和值不包含任何 CR（\r）、LF（\n）或 CRLF（\r\n）序列的 HTTP/2 和 HTTP/3 请求，以防止头注入攻击。 | 3 |
| **4.2.5** | 验证如果应用的后端或前端会构建并发送请求，则应采用验证、净化或其他机制，避免生成因过长而被接收组件拒绝的 URI（例如 API 调用 URI）或 HTTP 请求头字段（例如 Authorization 或 Cookie）。否则可能导致拒绝服务，例如发送包含过长 Cookie 头字段的请求后，服务器始终返回错误状态。 | 3 |

## V4.3 GraphQL

GraphQL 作为一种创建数据丰富客户端的方式越来越常见，这类客户端不需要与各种后端服务紧密耦合。本节覆盖 GraphQL 的安全考虑。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **4.3.1** | 验证使用查询允许列表、深度限制、数量限制或查询成本分析，防止昂贵的嵌套查询导致 GraphQL 或数据层表达式拒绝服务（DoS）。 | 2 |
| **4.3.2** | 验证生产环境中禁用 GraphQL 内省查询，除非该 GraphQL API 原本就是供其他方使用。 | 2 |

## V4.4 WebSocket

WebSocket 是一种通信协议，可在单个 TCP 连接上提供同时双向通信通道。它于 2011 年由 IETF 以 RFC 6455 标准化；虽然它被设计为通过 HTTP 端口 443 和 80 工作，但它不同于 HTTP。

本节提供关键安全要求，用于防止专门利用这种实时通信通道的通信安全和会话管理相关攻击。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **4.4.1** | 验证所有 WebSocket 连接都使用基于 TLS 的 WebSocket（WSS）。 | 1 |
| **4.4.2** | 验证在初始 HTTP WebSocket 握手期间，会根据应用允许的源列表检查 Origin 头字段。 | 2 |
| **4.4.3** | 验证如果无法使用应用的标准会话管理，则会为此使用专用令牌，并且这些令牌符合相关会话管理安全要求。 | 2 |
| **4.4.4** | 验证将现有 HTTPS 会话切换到 WebSocket 通道时，专用 WebSocket 会话管理令牌最初通过先前已认证的 HTTPS 会话获取或验证。 | 2 |

## 参考资料

更多信息，另见：

* [OWASP REST Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
* 来自 [graphql.org](https://graphql.org/learn/authorization/) 和 [Apollo](https://www.apollographql.com/docs/apollo-server/security/authentication/#authorization-methods) 的 GraphQL 授权资源。
* [OWASP Web Security Testing Guide: GraphQL Testing](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/12-API_Testing/01-Testing_GraphQL)
* [OWASP Web Security Testing Guide: Testing WebSockets](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets)
