# 附录 A：术语表

* **绝对最大会话生命周期（Absolute Maximum Session Lifetime）** – NIST 也称其为“总体超时”。它指用户认证后，无论是否与应用交互，会话最多可以保持活动的时间。这是会话过期的一部分。
* **允许列表（Allowlist）** – 被允许的数据或操作列表，例如用于输入验证的一组允许字符。
* **防伪令牌（Anti-forgery token）** – 一种机制，通过在请求中传递一个或多个令牌，并由应用服务器验证这些令牌，确保请求来自预期端点。
* **应用安全（Application Security）** – 应用层安全聚焦于分析构成开放系统互连参考模型（OSI 模型）应用层的组件，而不是底层操作系统或连接的网络等内容。
* **应用安全验证（Application Security Verification）** – 根据 OWASP ASVS 对应用进行的技术评估。
* **应用安全验证报告（Application Security Verification Report）** – 由验证方针对特定应用生成的报告，记录总体结果和支撑分析。
* **认证（Authentication）** – 验证应用用户所声称身份的过程。
* **自动化验证（Automated Verification）** – 使用自动化工具（动态分析工具、静态分析工具，或两者结合），通过漏洞特征发现问题。
* **黑盒测试（Black box testing）** – 一种软件测试方法，在不了解应用内部结构或工作方式的情况下检查应用功能。
* **通用弱点枚举（Common Weakness Enumeration，CWE）** – 由社区开发的常见软件安全弱点列表。它作为通用语言、软件安全工具的衡量标尺，以及弱点识别、缓解和预防工作的基线。
* **组件（Component）** – 自包含的代码单元，带有相关磁盘和网络接口，并与其他组件通信。
* **凭据服务提供方（Credential Service Provider，CSP）** – 也称身份提供方（IdP）。一种用户数据来源，可被其他应用用作认证来源。
* **跨站脚本包含（Cross-Site Script Inclusion，XSSI）** - 跨站脚本（XSS）攻击的一种变体，Web 应用从外部资源获取恶意代码，并把该代码作为自身内容的一部分包含进来。
* **跨站脚本（Cross-Site Scripting，XSS）** – 通常出现在 Web 应用中的安全漏洞，允许向内容中注入客户端脚本。
* **密码学模块（Cryptographic module）** – 实现密码学算法和/或生成密码学密钥的硬件、软件和/或固件。
* **密码学安全伪随机数生成器（Cryptographically secure pseudo-random number generator，CSPRNG）** - 具备适合密码学用途属性的伪随机数生成器，也称密码随机数生成器（CRNG）。
* **数据报传输层安全（Datagram Transport Layer Security，DTLS）** – 在网络连接上提供通信安全的密码协议。它基于 TLS 协议，但适配用于保护面向数据报的协议（通常基于 UDP）。DTLS 1.3 定义于 RFC 9147。
* **用于为安全实时传输协议建立密钥的 DTLS 扩展（DTLS-SRTP）** – 使用 DTLS 握手为 SRTP 会话建立密钥材料的机制。定义于 RFC 5764。
* **设计验证（Design Verification）** – 对应用安全架构进行的技术评估。
* **动态应用安全测试（Dynamic Application Security Testing，DAST）** – 这类技术旨在检测应用运行状态中表明存在安全漏洞的条件。
* **动态验证（Dynamic Verification）** – 在应用执行期间，使用基于漏洞特征的自动化工具发现问题。
* **Fast IDentity Online（FIDO）** – 一组认证标准，允许使用多种不同认证方法，包括生物识别、可信平台模块（TPM）、USB 安全令牌等。
* **硬件安全模块（Hardware Security Module，HSM）** – 以受保护方式存储密码学密钥和其他秘密的硬件组件。
* **Hibernate 查询语言（Hibernate Query Language，HQL）** – Hibernate ORM 库使用的一种查询语言，外观与 SQL 类似。
* **HTTP 严格传输安全（HTTP Strict Transport Security，HSTS）** – 一种策略，指示浏览器只在使用 TLS 且提供有效证书时连接返回该头的域名。它通过 Strict-Transport-Security 响应头字段启用。
* **超文本传输协议（HyperText Transfer Protocol，HTTP）** – 用于分布式、协作式、超媒体信息系统的应用协议，是万维网数据通信的基础。
* **基于 SSL/TLS 的 HTTP（HTTPS）** – 通过使用传输层安全（TLS）加密来保护 HTTP 通信的方法。
* **身份提供方（Identity Provider，IdP）** – 在 NIST 参考资料中也称凭据服务提供方（CSP）。为其他应用提供认证来源的实体。
* **不活动超时（Inactivity Timeout）** – 在用户没有与应用交互的情况下，会话可以保持活动的时间长度。这是会话过期的一部分。
* **输入验证（Input Validation）** – 对不可信用户输入进行规范化和验证。
* **JSON Web Token（JWT）** – RFC 7519 定义的一种 JSON 数据对象标准，由说明如何验证对象的头部、包含一组声明的正文，以及包含数字签名的签名部分组成；该签名可用于验证正文内容。它是一种自包含令牌。
* **本地文件包含（Local File Inclusion，LFI）** - 一种利用应用中有漏洞的文件包含流程的攻击，会导致包含服务器上已经存在的本地文件。
* **恶意代码（Malicious Code）** – 在应用开发期间，在应用所有者不知情的情况下引入应用的代码，会绕过应用预期安全策略。它不同于病毒或蠕虫等恶意软件！
* **恶意软件（Malware）** – 在运行时，在应用用户或管理员不知情的情况下引入应用的可执行代码。
* **消息认证码（Message authentication code，MAC）** - 由 MAC 生成算法计算出的数据密码校验值，用于保证数据完整性和真实性。
* **多因素认证（Multi-factor authentication，MFA）** – 包含两个或更多单一因素的认证。
* **双向 TLS（Mutual TLS，mTLS）** – 见 TLS 客户端认证。
* **对象关系映射（Object-relational Mapping，ORM）** – 一种系统，用于让应用通过与其兼容的对象模型引用和查询关系型或表格型数据库。
* **一次性密码（One-time Password，OTP）** – 为单次使用而唯一生成的密码。
* **Open Worldwide Application Security Project（OWASP）** – OWASP 是一个全球性、免费且开放的社区，专注于提升应用软件安全。其使命是让应用安全“可见”，使个人和组织能够对应用安全风险作出知情决策。见：[https://www.owasp.org/](https://www.owasp.org/)。
* **基于密码的密钥派生函数 2（Password-Based Key Derivation Function 2，PBKDF2）** – 一种特殊的单向算法，用于从输入文本（例如密码）和额外的随机盐值生成强密码学密钥。如果存储生成值而不是原始密码，可增加离线破解密码的难度。
* **公钥基础设施（Public Key Infrastructure，PKI）** – 将公钥与相应实体身份绑定的体系。绑定通过证书颁发机构（CA）的注册和证书签发过程建立。
* **公共交换电话网络（Public Switched Telephone Network，PSTN）** – 包含固定电话和移动电话的传统电话网络。
* **实时传输协议（Real-time Transport Protocol，RTP）和实时传输控制协议（RTCP）** – 两个配合使用、用于传输多媒体流的协议。WebRTC 协议栈使用它们。定义于 RFC 3550。
* **引用令牌（Reference Token）** – 一种作为指针或标识符的令牌，指向存储在服务器上的状态或元数据，有时也称随机令牌或不透明令牌。不同于在令牌自身嵌入部分相关数据的自包含令牌，引用令牌不包含内在信息，而是依赖服务器提供上下文。引用令牌本身会是会话标识符，或包含会话标识符。
* **依赖方（Relying Party，RP）** – 通常指依赖用户已通过独立认证提供方完成认证的应用。该应用依赖认证提供方提供的某种令牌或一组签名断言，来相信用户确实是其所声称的身份。
* **远程文件包含（Remote File Inclusion，RFI）** - 一种利用应用中有漏洞的包含流程的攻击，会导致包含远程文件。
* **可缩放矢量图形（Scalable Vector Graphics，SVG）** – 一种基于 XML 的标记语言，用于描述二维矢量图形。
* **安全实时传输协议（SRTP）和安全实时传输控制协议（SRTCP）** – RTP 和 RTCP 协议的一个配置文件，支持消息加密、认证和完整性保护。定义于 RFC 3711。
* **安全架构（Security Architecture）** – 对应用设计的抽象，识别并描述安全控制在何处以及如何使用，也识别并描述用户数据和应用数据的位置与敏感性。
* **安全断言标记语言（Security Assertion Markup Language，SAML）** – 一种基于在身份提供方和依赖方之间传递签名断言（通常是 XML 对象）实现单点登录认证的开放标准。
* **安全配置（Security Configuration）** – 影响安全控制如何使用的应用运行时配置。
* **安全控制（Security Control）** – 执行安全检查（例如授权检查），或被调用后产生安全效果（例如生成审计记录）的功能或组件。
* **安全信息和事件管理（Security information and event management，SIEM）** - 通过收集和分析组织 IT 基础设施内各来源的安全相关数据，实现威胁检测、合规和安全事件管理的系统。
* **自包含令牌（Self-Contained Token）** – 一种封装一个或多个属性的令牌，不依赖服务端状态或其他外部存储。这类令牌确保其所含属性的真实性和完整性，支持系统间安全的“无状态”信息交换。自包含令牌通常使用数字签名或消息认证码（MAC）等密码技术来确保数据真实性、完整性，并在某些情况下确保保密性。常见示例包括 SAML 断言和 JWT。
* **服务端请求伪造（Server-side Request Forgery，SSRF）** – 一种滥用服务器端功能读取或更新内部资源的攻击。攻击者提供或修改 URL，服务器上运行的代码会读取该 URL 或向其提交数据。
* **会话描述协议（Session Description Protocol，SDP）** – 用于建立多媒体会话的消息格式（例如 WebRTC 中使用）。定义于 RFC 4566。
* **会话标识符（Session Identifier）或会话 ID（Session ID）** – 标识后端存储的有状态会话的密钥。它会作为“引用令牌”或在“引用令牌”内部，在客户端和服务端之间传输。
* **会话令牌（Session Token）** – 本标准中的统称，用于指代无状态会话机制（使用自包含令牌）或有状态会话机制（使用引用令牌）中使用的令牌或值。
* **NAT 会话穿越工具（Session Traversal Utilities for NAT，STUN）** – 一种用于协助 NAT 穿越以建立点对点通信的协议。定义于 RFC 3489。
* **单因素认证器（Single-factor authenticator）** – 用于检查用户是否已认证的机制。它应是你知道的东西（记忆秘密、密码、口令短语、PIN）、你是谁（生物识别、指纹、面部扫描），或你拥有的东西（OTP 令牌、智能卡等密码设备）。
* **单点登录认证（Single Sign-on Authentication，SSO）** – 指用户登录一个应用后，无需重新认证就自动登录其他应用。例如，登录 Google 后，用户会自动登录 YouTube、Google Docs 和 Gmail 等其他 Google 服务。
* **软件物料清单（Software bill of materials，SBOM）** - 构建或组装软件应用所需所有组件、模块、库、框架和其他资源的结构化、完整列表。
* **软件成分分析（Software Composition Analysis，SCA）** – 一组用于分析应用组成、依赖、库和包的技术，以发现正在使用的特定组件版本中的安全漏洞。不要将其与源代码分析混淆，后者现在通常称为 SAST。
* **软件开发生命周期（Software development lifecycle，SDLC）** – 软件从初始需求到部署和维护的逐步开发过程。
* **SQL 注入（SQL Injection，SQLi）** – 一种用于攻击数据驱动应用的代码注入技术，将恶意 SQL 语句插入入口点。
* **有状态会话机制（Stateful Session Mechanism）** – 在有状态会话机制中，应用在后端保留会话状态，该状态通常对应一个会话令牌；该令牌使用密码学安全伪随机数生成器（CSPRNG）生成，并签发给最终用户。
* **无状态会话机制（Stateless Session Mechanism）** – 无状态会话机制会使用传递给客户端的自包含令牌，其中包含会话信息，而这些信息不一定存储在接收并验证该令牌的服务中。现实中，为了执行所需安全控制，服务仍需要访问某些会话信息（例如 JWT 撤销列表）。
* **静态应用安全测试（Static application security testing，SAST）** – 一组用于分析应用源代码、字节码和二进制文件的技术，以发现表明安全漏洞的编码和设计条件。SAST 解决方案在应用非运行状态下从“内部向外”分析应用。
* **威胁建模（Threat Modeling）** – 一种技术，通过逐步完善安全架构来识别威胁行为者、安全区域、安全控制以及重要技术和业务资产。
* **检查时到使用时（Time-of-check to time-of-use，TOCTOU）** – 指应用在使用资源前检查资源状态，但在检查和使用之间资源状态可能发生变化。这会使检查结果失效，并因状态不一致导致应用执行无效操作。
* **基于时间的一次性密码（Time based One-time Passwords，TOTPs）** - 一种生成 OTP 的方法，当前时间作为算法的一部分参与生成密码。
* **TLS 客户端认证（TLS client authentication）**，也称 **双向 TLS（Mutual TLS，mTLS）** – 在标准 TLS 连接中，客户端可以使用服务器提供的证书验证服务器身份。使用 TLS 客户端认证时，客户端也使用自己的私钥和证书，让服务器也能验证客户端身份。
* **传输层安全（Transport Layer Security，TLS）** – 通过网络连接提供通信安全的密码协议。
* **Traversal Using Relays around NAT（TURN）** – STUN 协议的扩展，当无法建立直接点对点连接时，使用 TURN 服务器作为中继。定义于 RFC 8656。
* **可信执行环境（Trusted execution environment，TEE）** - 一种隔离处理环境，可在不依赖系统其他部分的情况下安全执行应用。
* **可信平台模块（Trusted Platform Module，TPM）** – 一种 HSM，通常附加在主板等较大硬件组件上，并作为该系统的“信任根”。
* **可信服务层（Trusted Service Layer）** – 任何可信控制执行点，例如微服务、无服务器 API、服务端、具备安全启动的客户端设备上的可信 API、合作伙伴或外部 API 等。可信意味着无需担心不可信用户能够绕过或跳过该层或该层实现的控制。
* **统一资源标识符（Uniform Resource Identifier，URI）** - 标识资源的唯一字符串，例如网页、邮件地址或位置。
* **统一资源定位符（Uniform Resource Locator，URL）** – 指定互联网资源位置的字符串。
* **通用唯一标识符（Universally Unique Identifier，UUID）** – 软件中用作标识符的唯一参考编号。
* **验证方（Verifier）** – 根据 OWASP ASVS 要求审查应用的个人或团队。
* **Web 实时通信（Web Real-Time Communication，WebRTC）** – 一套协议栈及相关 Web API，用于在 Web 应用中传输多媒体流，通常用于电话会议场景。它基于 SRTP、SRTCP、DTLS、SDP 和 STUN/TURN。
* **基于 TLS 的 WebSocket（WebSocket over TLS，WSS）** – 通过在 TLS 协议之上承载 WebSocket 来保护 WebSocket 通信的做法。
* **所见即所得（What You See Is What You Get，WYSIWYG）** – 一种富内容编辑器，显示内容渲染后的实际外观，而不是显示用于控制渲染的代码。
* **X.509 证书（X.509 Certificate）** – X.509 证书是一种数字证书，使用被广泛接受的国际 X.509 公钥基础设施（PKI）标准，验证公钥属于证书中包含的用户、计算机或服务身份。
* **XML 外部实体（XML eXternal Entity，XXE）** – 一种 XML 实体，可通过声明的系统标识符访问本地或远程内容。这可能导致多种注入攻击。
