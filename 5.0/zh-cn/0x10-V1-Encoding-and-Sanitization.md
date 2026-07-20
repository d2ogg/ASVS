# V1 编码和净化

## 控制目标

本章处理 Web 应用中最常见的一类安全弱点：不安全地处理不可信数据。这类弱点可能导致多种技术漏洞，其根源在于不可信数据会按照相关解释器的语法规则被解释。

对于现代 Web 应用，最佳做法始终是使用更安全的 API，例如参数化查询、自动转义或模板框架。否则，谨慎执行输出编码、转义或净化就会成为应用安全的关键。

输入验证是一种纵深防御机制，用来防止意外或危险内容。不过，由于它的主要目的是确保传入内容符合功能和业务预期，因此相关要求位于“验证和业务逻辑”章节。

## V1.1 编码和净化架构

以下各节给出了针对特定语法或解释器的要求，用于安全处理不可信内容，避免产生安全漏洞。本节规定这些处理的执行顺序和位置，并要求数据在存储时保持原始状态，而不是以编码或转义后的形式（例如 HTML 编码）存储，以避免双重编码问题。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.1.1** | 验证仅将输入解码或反转义为规范形式一次；仅在预期接收相应形式的编码数据时才进行解码；并且该操作在进一步处理输入之前执行，例如不得在输入验证或净化之后执行。 | 2 |
| **1.1.2** | 验证输出编码和转义在目标解释器使用数据前的最后一步执行，或由解释器自身执行。 | 2 |

## V1.2 注入预防

在潜在危险上下文附近执行输出编码或转义，对任何应用的安全都至关重要。通常，输出编码和转义不会持久化保存，而是用于让输出在相应解释器中立即安全使用。过早执行这些处理，可能导致内容格式错误，或让编码、转义失效。

很多情况下，软件库会提供自动完成这些处理的安全或更安全函数，但仍必须确保这些函数适用于当前上下文。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.2.1** | 验证 HTTP 响应、HTML 文档或 XML 文档的输出编码适用于所需上下文，例如为 HTML 元素、HTML 属性、HTML 注释、CSS 或 HTTP 头字段编码相关字符，以避免改变消息或文档结构。 | 1 |
| **1.2.2** | 验证动态构建 URL 时，不可信数据会按其上下文进行编码（例如，对查询参数或路径参数使用 URL 编码或 base64url 编码）。确保只允许安全的 URL 协议（例如禁止 javascript: 或 data:）。 | 1 |
| **1.2.3** | 验证动态构建 JavaScript 内容（包括 JSON）时使用输出编码或转义，以避免改变消息或文档结构（防止 JavaScript 和 JSON 注入）。 | 1 |
| **1.2.4** | 验证数据选择或数据库查询（例如 SQL、HQL、NoSQL、Cypher）使用参数化查询、ORM、实体框架，或以其他方式防范 SQL 注入和其他数据库注入攻击。编写存储过程时也适用此要求。 | 1 |
| **1.2.5** | 验证应用能够防范操作系统命令注入，并且在调用操作系统命令时采用参数化机制，或进行符合上下文的命令行输出编码。 | 1 |
| **1.2.6** | 验证应用能够防范 LDAP 注入漏洞，或已经实现用于防止 LDAP 注入的特定安全控制。 | 2 |
| **1.2.7** | 验证应用通过查询参数化或预编译查询防范 XPath 注入攻击。 | 2 |
| **1.2.8** | 验证 LaTeX 处理器已安全配置（例如不使用 "--shell-escape" 标志），并使用命令允许列表防止 LaTeX 注入攻击。 | 2 |
| **1.2.9** | 验证应用会转义正则表达式中的特殊字符（通常使用反斜杠），防止它们被误解释为元字符。 | 2 |
| **1.2.10** | 验证应用能够防范 CSV 和公式注入。导出 CSV 内容时，应用必须遵循 RFC 4180 第 2.6 和 2.7 节定义的转义规则。此外，导出为 CSV 或其他电子表格格式（例如 XLS、XLSX 或 ODF）时，如果特殊字符（包括 '='、'+'、'-'、'@'、'\t'（制表符）和 '\0'（空字符））出现在字段值的第一个字符位置，必须用单引号进行转义。 | 3 |

注意：使用参数化查询或转义 SQL 并不总是足够。表名、列名（包括 "ORDER BY" 中的列名）等查询组成部分无法转义。把经过转义的用户提供数据放入这些字段，会导致查询失败或 SQL 注入。

## V1.3 净化

在不安全上下文中使用不可信内容时，理想的防护方式是使用符合上下文的编码或转义。这种方式可以保持不安全内容的语义不变，同时让它在特定上下文中安全使用，上一节对此已有更详细说明。

如果无法做到这一点，就需要进行净化，移除潜在危险字符或内容。在某些情况下，这可能会改变输入的语义，但出于安全原因，可能没有其他选择。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.3.1** | 验证来自 WYSIWYG 编辑器或类似来源的所有不可信 HTML 输入，都使用知名且安全的 HTML 净化库或框架功能进行净化。 | 1 |
| **1.3.2** | 验证应用避免使用 eval() 或其他动态代码执行功能，例如 Spring Expression Language（SpEL）。如果没有替代方案，任何被包含的用户输入都必须在执行前进行净化。 | 1 |
| **1.3.3** | 验证传递到潜在危险上下文的数据会预先净化，以强制执行安全措施，例如只允许对该上下文安全的字符，并截断过长输入。 | 2 |
| **1.3.4** | 验证用户提供的可脚本化可缩放矢量图（SVG）内容经过验证或净化，仅包含对应用安全的标签和属性（例如绘图相关内容），例如不得包含脚本和 foreignObject。 | 2 |
| **1.3.5** | 验证应用会净化或禁用用户提供的可脚本化内容或表达式模板语言内容，例如 Markdown、CSS 或 XSL 样式表、BBCode 或类似内容。 | 2 |
| **1.3.6** | 验证应用能够防范服务端请求伪造（SSRF）攻击：在使用不可信数据调用其他服务前，先按协议、域名、路径和端口允许列表验证这些数据，并净化潜在危险字符。 | 2 |
| **1.3.7** | 验证应用通过禁止基于不可信输入构建模板来防范模板注入攻击。如果没有替代方案，在模板创建期间动态包含的任何不可信输入都必须经过净化或严格验证。 | 2 |
| **1.3.8** | 验证应用在将不可信输入用于 Java Naming and Directory Interface（JNDI）查询前会进行适当净化，并且 JNDI 已安全配置，以防止 JNDI 注入攻击。 | 2 |
| **1.3.9** | 验证应用在将内容发送到 memcache 前会进行净化，以防止注入攻击。 | 2 |
| **1.3.10** | 验证可能在使用时被解析为意外或恶意内容的格式字符串，会在处理前经过净化。 | 2 |
| **1.3.11** | 验证应用在把用户输入传递给邮件系统前会进行净化，以防范 SMTP 或 IMAP 注入。 | 2 |
| **1.3.12** | 验证正则表达式不包含会导致指数级回溯的元素，并对不可信输入进行净化，以缓解 ReDoS 或失控正则表达式攻击。 | 3 |

## V1.4 内存、字符串和非托管代码

以下要求处理不安全内存使用带来的风险，这通常适用于使用系统语言或非托管代码的应用。

在某些情况下，可以通过设置编译器标志来实现这些要求，例如启用缓冲区溢出保护和警告，包括栈随机化和数据执行保护，并在发现不安全的指针、内存、格式字符串、整数或字符串操作时让构建失败。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.4.1** | 验证应用使用内存安全的字符串操作、更安全的内存复制和指针运算，以检测或防止栈、缓冲区或堆溢出。 | 2 |
| **1.4.2** | 验证使用符号、范围和输入验证技术来防止整数溢出。 | 2 |
| **1.4.3** | 验证动态分配的内存和资源会被释放，并且指向已释放内存的引用或指针会被移除或置为空，以防止悬空指针和释放后使用漏洞。 | 2 |

## V1.5 安全反序列化

将数据从存储或传输表示转换为实际应用对象（反序列化），历史上曾导致多种代码注入漏洞。为避免这类问题，必须谨慎且安全地执行这一过程。

尤其需要注意的是，某些反序列化方法已被编程语言或框架文档明确标识为不安全，并且在处理不可信数据时无法变得安全。对于正在使用的每一种机制，都应认真进行尽职审查。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.5.1** | 验证应用将 XML 解析器配置为限制性配置，并禁用解析外部实体等不安全功能，以防止 XML 外部实体（XXE）攻击。 | 1 |
| **1.5.2** | 验证对不可信数据进行反序列化时，会强制执行安全输入处理，例如使用对象类型允许列表，或限制客户端定义的对象类型，以防范反序列化攻击。不得将明确被定义为不安全的反序列化机制用于不可信输入。 | 2 |
| **1.5.3** | 验证应用中针对同一数据类型使用的不同解析器（例如 JSON 解析器、XML 解析器、URL 解析器）以一致方式解析，并使用相同字符编码机制，以避免 JSON 互操作性漏洞，或在远程文件包含（RFI）和服务端请求伪造（SSRF）攻击中利用不同 URI 或文件解析行为等问题。 | 3 |

## 参考资料

更多信息，另见：

* [OWASP LDAP Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LDAP_Injection_Prevention_Cheat_Sheet.html)
* [OWASP Cross Site Scripting Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
* [OWASP DOM Based Cross Site Scripting Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)
* [OWASP XML External Entity Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)
* [OWASP Web Security Testing Guide: Client-Side Testing](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/11-Client-side_Testing)
* [OWASP Java Encoding Project](https://owasp.org/owasp-java-encoder/)
* [DOMPurify - Client-side HTML Sanitization Library](https://github.com/cure53/DOMPurify)
* [RFC4180 - Common Format and MIME Type for Comma-Separated Values (CSV) Files](https://datatracker.ietf.org/doc/html/rfc4180#section-2)

关于反序列化或解析问题的更多信息，请参见：

* [OWASP Deserialization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Deserialization_Cheat_Sheet.html)
* [An Exploration of JSON Interoperability Vulnerabilities](https://bishopfox.com/blog/json-interoperability-vulnerabilities)
* [Orange Tsai - A New Era of SSRF Exploiting URL Parser In Trending Programming Languages](https://www.blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf)
