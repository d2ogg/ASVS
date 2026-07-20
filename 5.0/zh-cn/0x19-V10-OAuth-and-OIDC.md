# V10 OAuth 和 OIDC

## 控制目标

OAuth2（本章中称为 OAuth）是用于委派授权的行业标准框架。例如，使用 OAuth 时，只要用户授权客户端应用这样做，客户端应用就可以代表用户获得对 API（服务器资源）的访问权限。

OAuth 本身并不是为用户认证设计的。OpenID Connect（OIDC）框架在 OAuth 之上增加了用户身份层，从而扩展了 OAuth。OIDC 支持标准化用户信息、单点登录（SSO）和会话管理等功能。由于 OIDC 是 OAuth 的扩展，本章中的 OAuth 要求同样适用于 OIDC。

OAuth 定义了以下角色：

* OAuth 客户端是尝试获取服务器资源访问权限的应用（例如使用已签发的访问令牌调用 API）。OAuth 客户端通常是服务端应用。
    * 机密客户端是能够对其用于向授权服务器认证自身的凭据保密的客户端。
    * 公共客户端无法对用于向授权服务器认证自身的凭据保密。因此，它不会认证自身（例如使用 'client_id' 和 'client_secret' 参数），而只是标识自身（使用 'client_id' 参数）。
* OAuth 资源服务器（RS）是向 OAuth 客户端暴露资源的服务器 API。
* OAuth 授权服务器（AS）是向 OAuth 客户端签发访问令牌的服务器应用。这些访问令牌允许 OAuth 客户端代表最终用户，或代表 OAuth 客户端自身，访问 RS 资源。AS 通常是独立应用，但在合适情况下也可以集成到合适的 RS 中。
* 资源所有者（RO）是最终用户，他们授权 OAuth 客户端代表自己获取对资源服务器上托管资源的有限访问权限。资源所有者通过与授权服务器交互来同意这种委派授权。

OIDC 定义了以下角色：

* 依赖方（RP）是通过 OpenID Provider 请求最终用户认证的客户端应用。它承担 OAuth 客户端角色。
* OpenID Provider（OP）是能够认证最终用户并向 RP 提供 OIDC 声明的 OAuth AS。OP 可以是身份提供方（IdP），但在联合场景中，OP 和最终用户进行认证的身份提供方可能是不同的服务器应用。

OAuth 和 OIDC 最初是为第三方应用设计的。如今，它们也经常被第一方应用使用。不过，在第一方场景中使用时，例如用于认证和会话管理，协议会增加一些复杂性，也可能引入新的安全挑战。

OAuth 和 OIDC 可用于多种类型的应用，但 ASVS 以及本章要求的重点是 Web 应用和 API。

由于 OAuth 和 OIDC 可以视为构建在 Web 技术之上的逻辑，其他章节中的通用要求始终适用，本章不能脱离上下文理解。

本章处理 OAuth2 和 OIDC 的当前最佳实践，并与 <https://oauth.net/2/> 和 <https://openid.net/developers/specs/> 中的规范保持一致。即便 RFC 被认为已经成熟，它们也会经常更新。因此，应用本章要求时，与最新版本保持一致非常重要。更多细节见参考资料章节。

鉴于该领域的复杂性，要构建安全的 OAuth 或 OIDC 解决方案，使用知名且符合行业标准的授权服务器并采用推荐的安全配置至关重要。

本章使用的术语与 OAuth RFC 和 OIDC 规范一致，但请注意，OIDC 术语只用于 OIDC 特定要求；其他情况下使用 OAuth 术语。

在 OAuth 和 OIDC 语境中，本章中的“令牌”指：

* 访问令牌，只应由 RS 消费，可以是通过内省验证的引用令牌，也可以是使用某些密钥材料验证的自包含令牌。
* 刷新令牌，只应由签发该令牌的授权服务器消费。
* OIDC ID Token，只应由触发授权流程的客户端消费。

本章中某些要求的风险等级取决于客户端是机密客户端，还是被视为公共客户端。由于使用强客户端认证可以缓解许多攻击向量，当 L1 应用使用机密客户端时，少数要求可以放宽。

## V10.1 通用 OAuth 和 OIDC 安全

本节覆盖适用于所有使用 OAuth 或 OIDC 的应用的通用架构要求。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **10.1.1** | 验证令牌只会发送给确实需要它们的组件。例如，对于基于浏览器的 JavaScript 应用使用 BFF（backend-for-frontend）模式时，访问令牌和刷新令牌应只允许后端访问。 | 2 |
| **10.1.2** | 验证客户端仅在授权服务器返回的值（例如授权码或 ID Token）源自同一用户代理会话中发起的同一交易时，才接受这些值。这要求客户端生成的秘密值（例如授权码交换证明密钥（PKCE）的 'code_verifier'、'state' 或 OIDC 'nonce'）不可猜测、仅用于该交易，并安全绑定到客户端及发起该交易的用户代理会话。 | 2 |

## V10.2 OAuth 客户端

这些要求详细说明 OAuth 客户端应用的职责。客户端可以是 Web 服务器后端（通常作为 Backend For Frontend，BFF）、后端服务集成，或前端单页应用（SPA，也称基于浏览器的应用）。

一般而言，后端客户端被视为机密客户端，前端客户端被视为公共客户端。不过，在最终用户设备上运行的原生应用，如果使用 OAuth 动态客户端注册，也可以被视为机密客户端。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **10.2.1** | 验证如果使用授权码流程，OAuth 客户端具备防护，能够防止触发令牌请求的基于浏览器的请求伪造攻击（通常称为跨站请求伪造，CSRF），方式可以是使用授权码交换证明密钥（PKCE）功能，或检查授权请求中发送的 'state' 参数。 | 2 |
| **10.2.2** | 验证如果 OAuth 客户端可以与多个授权服务器交互，它具备防范混淆攻击的能力。例如，可以要求授权服务器返回 'iss' 参数值，并在授权响应和令牌响应中验证该值。 | 2 |
| **10.2.3** | 验证 OAuth 客户端在向授权服务器发出的请求中，只请求所需的范围（或其他授权参数）。 | 3 |

## V10.3 OAuth 资源服务器

在 ASVS 和本章语境中，资源服务器是一个 API。为提供安全访问，资源服务器必须：

* 根据令牌格式和相关协议规范验证访问令牌，例如 JWT 验证或 OAuth 令牌内省。
* 如果令牌有效，则基于访问令牌中的信息和已授予权限执行授权决策。例如，资源服务器需要验证客户端（代表 RO 行事）是否被授权访问所请求的资源。

因此，这里列出的要求是 OAuth 或 OIDC 特定要求，应在令牌验证之后、基于令牌信息执行授权之前进行。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **10.3.1** | 验证资源服务器只接受预期用于该服务的访问令牌（受众）。受众可以包含在结构化访问令牌中（例如 JWT 中的 'aud' 声明），也可以使用令牌内省端点检查。 | 2 |
| **10.3.2** | 验证资源服务器基于访问令牌中定义委派授权的声明来执行授权决策。如果存在 'sub'、'scope' 和 'authorization_details' 等声明，它们必须参与决策。 | 2 |
| **10.3.3** | 验证如果访问控制决策需要从访问令牌（JWT 或相关令牌内省响应）中识别唯一用户，资源服务器会使用不能被重新分配给其他用户的声明识别该用户。通常，这意味着使用 'iss' 和 'sub' 声明的组合。 | 2 |
| **10.3.4** | 验证如果资源服务器要求特定的认证强度、认证方法或认证时效性，则会确认提交的访问令牌满足这些约束。例如，可以分别使用 OIDC 的 'acr'、'amr' 和 'auth_time' 声明（如存在）。 | 2 |
| **10.3.5** | 验证资源服务器通过要求发送方约束访问令牌，防止被盗访问令牌被使用或访问令牌被未授权方重放，方式可以是 OAuth 2 Mutual TLS 或 OAuth 2 Demonstration of Proof of Possession（DPoP）。 | 3 |

## V10.4 OAuth 授权服务器

这些要求详细说明 OAuth 授权服务器的职责，包括 OpenID Provider。

对于客户端认证，在满足 [RFC 8705](https://datatracker.ietf.org/doc/html/rfc8705) [第 2.2 节](https://datatracker.ietf.org/doc/html/rfc8705#name-self-signed-certificate-mut)要求前提下，允许使用 'self_signed_tls_client_auth' 方法。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **10.4.1** | 验证授权服务器基于预注册 URI 的客户端特定允许列表，并使用精确字符串比较来验证重定向 URI。 | 1 |
| **10.4.2** | 验证如果授权服务器在授权响应中返回授权码，该授权码只能用于一次令牌请求。对于第二个使用已被用于签发访问令牌的授权码的有效请求，授权服务器必须拒绝令牌请求，并撤销与该授权码相关的任何已签发令牌。 | 1 |
| **10.4.3** | 验证授权码生命周期较短。L1 和 L2 应用的最长生命周期可为 10 分钟，L3 应用最长为 1 分钟。 | 1 |
| **10.4.4** | 验证对于给定客户端，授权服务器只允许使用该客户端需要使用的授权授予类型。注意，不应再使用 'token'（隐式流程）和 'password'（资源所有者密码凭据流程）授予。 | 1 |
| **10.4.5** | 验证授权服务器会缓解公共客户端的刷新令牌重放攻击，最好使用发送方约束刷新令牌，即 Demonstrating Proof of Possession（DPoP）或使用双向 TLS（mTLS）的证书绑定访问令牌。对于 L1 和 L2 应用，可以使用刷新令牌轮换。如果使用刷新令牌轮换，授权服务器必须在使用后使刷新令牌失效；如果提供了已使用且已失效的刷新令牌，必须撤销该授权下的所有刷新令牌。 | 1 |
| **10.4.6** | 验证如果使用授权码授予，授权服务器会通过要求授权码交换证明密钥（PKCE）来缓解授权码截获攻击。对于授权请求，授权服务器必须要求有效的 'code_challenge' 值，并且不得接受 'code_challenge_method' 值为 'plain'。对于令牌请求，必须要求验证 'code_verifier' 参数。 | 2 |
| **10.4.7** | 验证如果授权服务器支持未经认证的动态客户端注册，它会缓解恶意客户端应用风险。它必须验证客户端元数据，例如任何已注册 URI，确保用户同意，并在处理不可信客户端应用的授权请求前警告用户。 | 2 |
| **10.4.8** | 验证刷新令牌具有绝对过期时间，即便应用了滑动刷新令牌过期也同样如此。 | 2 |
| **10.4.9** | 验证授权用户可以通过授权服务器用户界面撤销刷新令牌和引用访问令牌，以缓解恶意客户端或被盗令牌风险。 | 2 |
| **10.4.10** | 验证机密客户端会在客户端到授权服务器的后通道请求中进行认证，例如令牌请求、推送授权请求（PAR）和令牌撤销请求。 | 2 |
| **10.4.11** | 验证授权服务器配置只向 OAuth 客户端分配所需范围。 | 2 |
| **10.4.12** | 验证对于给定客户端，授权服务器只允许使用该客户端需要使用的 'response_mode' 值。例如，由授权服务器根据预期值验证该值，或使用推送授权请求（PAR）或 JWT 保护的授权请求（JAR）。 | 3 |
| **10.4.13** | 验证授权授予类型 'code' 始终与推送授权请求（PAR）一起使用。 | 3 |
| **10.4.14** | 验证授权服务器只签发发送方约束（Proof-of-Possession）访问令牌，方式可以是使用双向 TLS（mTLS）的证书绑定访问令牌，或 DPoP 绑定访问令牌（Demonstration of Proof of Possession）。 | 3 |
| **10.4.15** | 验证对于服务端客户端（不在最终用户设备上执行的客户端），授权服务器确保 'authorization_details' 参数值来自客户端后端，并且未被用户篡改。例如，可以要求使用推送授权请求（PAR）或 JWT 保护的授权请求（JAR）。 | 3 |
| **10.4.16** | 验证客户端是机密客户端，并且授权服务器要求使用强客户端认证方法（基于公钥密码学且能够抵抗重放攻击），例如双向 TLS（'tls_client_auth'、'self_signed_tls_client_auth'）或私钥 JWT（'private_key_jwt'）。 | 3 |

## V10.5 OIDC 客户端

由于 OIDC 依赖方充当 OAuth 客户端，“OAuth 客户端”小节中的要求同样适用。

注意，“认证”章节中的“使用身份提供方进行认证”小节也包含相关通用要求。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **10.5.1** | 验证客户端（作为依赖方）会缓解 ID Token 重放攻击。例如，确保 ID Token 中的 'nonce' 声明与发送给 OpenID Provider 的认证请求中的 'nonce' 值匹配（在 OAuth2 中，该请求称为发送给授权服务器的授权请求）。 | 2 |
| **10.5.2** | 验证客户端从 ID Token 声明中唯一识别用户，通常使用不能被重新分配给其他用户（在某个身份提供方范围内）的 'sub' 声明。 | 2 |
| **10.5.3** | 验证客户端会拒绝恶意授权服务器试图通过授权服务器元数据冒充另一个授权服务器。若授权服务器元数据中的签发方 URL 与客户端预配置的预期签发方 URL 不完全匹配，客户端必须拒绝该授权服务器元数据。 | 2 |
| **10.5.4** | 验证客户端会通过检查令牌中的 'aud' 声明是否等于该客户端的 'client_id' 值，来确认 ID Token 预期用于该客户端（受众）。 | 2 |
| **10.5.5** | 验证使用 OIDC 后通道注销时，依赖方会缓解强制注销导致的拒绝服务，以及注销流程中的跨 JWT 混淆。客户端必须验证注销令牌类型正确且值为 'logout+jwt'，包含具有正确成员名称的 'event' 声明，并且不包含 'nonce' 声明。注意，也建议设置较短过期时间（例如 2 分钟）。 | 2 |

## V10.6 OpenID Provider

由于 OpenID Provider 充当 OAuth 授权服务器，“OAuth 授权服务器”小节中的要求同样适用。

注意，如果使用 ID Token 流程（而不是授权码流程），不会签发访问令牌，许多 OAuth AS 要求并不适用。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **10.6.1** | 验证 OpenID Provider 只允许 response mode 的值为 'code'、'ciba'、'id_token' 或 'id_token code'。注意，'code' 优先于 'id_token code'（OIDC 混合流程），且不得使用 'token'（任何隐式流程）。 | 2 |
| **10.6.2** | 验证 OpenID Provider 会缓解强制注销导致的拒绝服务。方式可以是从最终用户处获得明确确认，或在存在注销请求（由依赖方发起）时验证其中的参数，例如 'id_token_hint'。 | 2 |

## V10.7 同意管理

这些要求覆盖授权服务器对用户同意的验证。缺少适当用户同意验证时，恶意行为者可能通过欺骗或社会工程代表用户获取权限。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **10.7.1** | 验证授权服务器确保用户同意每个授权请求。如果无法保证客户端身份，授权服务器必须始终明确提示用户同意。 | 2 |
| **10.7.2** | 验证当授权服务器提示用户同意时，会清楚且充分地展示用户正在同意什么。适用时，这应包括所请求授权的性质（通常基于 scope、资源服务器、Rich Authorization Requests（RAR）授权详情）、被授权应用的身份，以及这些授权的生命周期。 | 2 |
| **10.7.3** | 验证用户可以查看、修改和撤销自己通过授权服务器授予的同意。 | 2 |

## 参考资料

关于 OAuth 的更多信息，请参见：

* [oauth.net](https://oauth.net/)
* [OWASP OAuth 2.0 Protocol Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/OAuth2_Cheat_Sheet.html)

ASVS 中 OAuth 相关要求使用了以下已发布和草案状态的 RFC：

* [RFC6749 The OAuth 2.0 Authorization Framework](https://datatracker.ietf.org/doc/html/rfc6749)
* [RFC6750 The OAuth 2.0 Authorization Framework: Bearer Token Usage](https://datatracker.ietf.org/doc/html/rfc6750)
* [RFC6819 OAuth 2.0 Threat Model and Security Considerations](https://datatracker.ietf.org/doc/html/rfc6819)
* [RFC7636 Proof Key for Code Exchange by OAuth Public Clients](https://datatracker.ietf.org/doc/html/rfc7636)
* [RFC7591 OAuth 2.0 Dynamic Client Registration Protocol](https://datatracker.ietf.org/doc/html/rfc7591)
* [RFC8628 OAuth 2.0 Device Authorization Grant](https://datatracker.ietf.org/doc/html/rfc8628)
* [RFC8707 Resource Indicators for OAuth 2.0](https://datatracker.ietf.org/doc/html/rfc8707)
* [RFC9068 JSON Web Token (JWT) Profile for OAuth 2.0 Access Tokens](https://datatracker.ietf.org/doc/html/rfc9068)
* [RFC9126 OAuth 2.0 Pushed Authorization Requests](https://datatracker.ietf.org/doc/html/rfc9126)
* [RFC9207 OAuth 2.0 Authorization Server Issuer Identification](https://datatracker.ietf.org/doc/html/rfc9207)
* [RFC9396 OAuth 2.0 Rich Authorization Requests](https://datatracker.ietf.org/doc/html/rfc9396)
* [RFC9449 OAuth 2.0 Demonstrating Proof of Possession (DPoP)](https://datatracker.ietf.org/doc/html/rfc9449)
* [RFC9700 Best Current Practice for OAuth 2.0 Security](https://datatracker.ietf.org/doc/html/rfc9700)
* [draft OAuth 2.0 for Browser-Based Applications](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-browser-based-apps)<!-- recheck on release -->
* [draft The OAuth 2.1 Authorization Framework](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-12)<!-- recheck on release -->

关于 OpenID Connect 的更多信息，请参见：

* [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html)
* [FAPI 2.0 Security Profile](https://openid.net/specs/fapi-security-profile-2_0-final.html)
