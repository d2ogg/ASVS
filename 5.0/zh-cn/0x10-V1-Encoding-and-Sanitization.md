# V1 编码和净化

## 控制目标

本章讨论 Web 应用中最常见的一类安全弱点：未能安全处理不可信数据。这类问题会引发多种技术漏洞，根本原因是解释器可能按照自身的语法规则，把不可信数据当成指令或其他特殊内容来解析。

现代 Web 应用应优先使用更安全的 API，例如参数化查询、自动转义机制或模板框架。如果无法使用这些机制，就必须谨慎地对输出进行编码、转义或净化，才能保证应用安全。

输入验证是一种纵深防御机制，用来防止意外或危险内容。不过，由于它的主要目的是确保传入内容符合功能和业务预期，因此相关要求位于“验证和业务逻辑”章节。

## V1.1 编码和净化架构

以下各节针对不同语法和解释器，说明如何安全处理不可信内容。本节则规定这些处理应在何时、何处执行。数据在存储时应保持原始形式，不应存储为已经编码或转义的形式（例如经过 HTML 编码的文本），否则容易发生重复编码。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.1.1** | 验证输入最多只经过一次解码或反转义，并转换为规范形式。只有在预期输入本身采用了相应编码时，才可以进行解码。解码必须先于其他输入处理；例如，不得先验证或净化输入，再进行解码。 | 2 |
| **1.1.2** | 验证输出编码或转义是在数据交给目标解释器之前的最后一个处理步骤，或者由解释器自行完成。 | 2 |

## V1.2 注入预防

要保证应用安全，必须在数据进入潜在危险的上下文之前，尽可能晚地进行输出编码或转义。通常不应长期保存编码或转义后的结果，而应在数据即将交给相应解释器时再处理。处理得过早，既可能破坏内容格式，也可能使编码或转义失去防护作用。

许多软件库都提供能够自动完成这些处理的安全函数。即便如此，仍须确认所用函数适合当前的输出上下文。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.2.1** | 验证 HTTP 响应、HTML 文档或 XML 文档采用了符合输出上下文的编码。例如，根据数据所在位置，对 HTML 元素、HTML 属性、HTML 注释、CSS 或 HTTP 头字段中的特殊字符进行编码，防止数据改变消息或文档的结构。 | 1 |
| **1.2.2** | 验证动态构建 URL 时，根据数据所在位置对不可信数据进行编码。例如，对查询参数或路径参数采用 URL 编码或 base64url 编码。同时，只允许使用安全的 URL 协议，不得允许 `javascript:`、`data:` 等危险协议。 | 1 |
| **1.2.3** | 验证动态生成 JavaScript 内容（包括 JSON）时，对输出进行适当的编码或转义，防止不可信数据改变消息或文档结构，从而避免 JavaScript 注入和 JSON 注入。 | 1 |
| **1.2.4** | 验证数据检索或数据库查询（例如 SQL、HQL、NoSQL、Cypher）采用参数化查询、ORM、实体框架或其他有效措施，以防范 SQL 注入及其他数据库注入攻击。编写存储过程时也必须满足此要求。 | 1 |
| **1.2.5** | 验证应用能够防范操作系统命令注入。调用操作系统命令时，应使用能够区分命令与参数的参数化接口；如果无法使用，则应根据命令行上下文对输出进行编码。 | 1 |
| **1.2.6** | 验证应用已经采取适当的安全控制，能够防范 LDAP 注入。 | 2 |
| **1.2.7** | 验证应用使用参数化查询或预编译查询来防范 XPath 注入。 | 2 |
| **1.2.8** | 验证 LaTeX 处理器采用了安全配置，例如不启用 `--shell-escape`；同时使用命令允许列表来防范 LaTeX 注入。 | 2 |
| **1.2.9** | 验证插入正则表达式的数据，其特殊字符已经过转义（通常使用反斜杠），不会被解释为正则表达式元字符。 | 2 |
| **1.2.10** | 验证应用能够防范 CSV 和公式注入。导出 CSV 内容时，应用必须遵循 RFC 4180 第 2.6 和 2.7 节定义的转义规则。此外，导出为 CSV 或其他电子表格格式（例如 XLS、XLSX 或 ODF）时，如果特殊字符（包括 '='、'+'、'-'、'@'、'\t'（制表符）和 '\0'（空字符））出现在字段值的第一个字符位置，必须用单引号进行转义。 | 3 |

注意：参数化查询或 SQL 转义并不能解决所有问题。表名、列名（包括 `ORDER BY` 子句中的列名）等查询结构无法通过转义来保证安全。如果把用户提供的值用于这些位置，即使经过转义，也可能造成查询失败或 SQL 注入。

## V1.3 净化

需要在危险上下文中使用不可信内容时，首选方法是根据上下文进行编码或转义。这样既能保留原内容的含义，又能保证它在该上下文中安全使用。上一节对此作了详细说明。

如果无法做到这一点，就需要进行净化，移除潜在危险字符或内容。在某些情况下，这可能会改变输入的语义，但出于安全原因，可能没有其他选择。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.3.1** | 验证所有来自 WYSIWYG 编辑器或类似来源的不可信 HTML，都经过知名且安全的 HTML 净化库或框架功能处理。 | 1 |
| **1.3.2** | 验证应用没有使用 `eval()`、Spring Expression Language（SpEL）等动态代码执行功能。如果确实没有替代方案，则必须先净化其中包含的所有用户输入，再执行动态代码。 | 1 |
| **1.3.3** | 验证数据进入潜在危险的上下文之前已经过净化，并且符合该上下文的安全限制。例如，只允许安全字符，并截断过长的输入。 | 2 |
| **1.3.4** | 验证用户提供的可脚本化 SVG 内容经过验证或净化，只保留对应用安全的标签和属性（例如绘图相关内容），不得包含脚本、`foreignObject` 等危险内容。 | 2 |
| **1.3.5** | 验证应用对用户提供的可脚本化内容或表达式模板内容进行净化，或者彻底禁用这类内容，例如 Markdown、CSS、XSL 样式表、BBCode 等。 | 2 |
| **1.3.6** | 验证应用能够防范服务端请求伪造（SSRF）攻击：在使用不可信数据调用其他服务前，先按协议、域名、路径和端口允许列表验证这些数据，并净化潜在危险字符。 | 2 |
| **1.3.7** | 验证应用禁止使用不可信输入来构建模板，以防范模板注入。如果没有其他可行方案，则必须对模板创建过程中动态插入的所有不可信输入进行净化或严格验证。 | 2 |
| **1.3.8** | 验证不可信输入在用于 Java Naming and Directory Interface（JNDI）查询之前已经过适当净化，并且 JNDI 本身采用了安全配置，以防范 JNDI 注入。 | 2 |
| **1.3.9** | 验证内容发送到 memcache 之前已经过净化，以防范注入攻击。 | 2 |
| **1.3.10** | 验证格式字符串在处理之前已经过净化，避免其中的内容在使用时被解析成非预期指令或恶意内容。 | 2 |
| **1.3.11** | 验证用户输入在传递给邮件系统之前已经过净化，以防范 SMTP 或 IMAP 注入。 | 2 |
| **1.3.12** | 验证正则表达式中没有可能引发指数级回溯的结构，并对不可信输入进行净化，以降低 ReDoS 或失控正则表达式攻击的风险。 | 3 |

## V1.4 内存、字符串和非托管代码

以下要求处理不安全内存使用带来的风险，这通常适用于使用系统语言或非托管代码的应用。

部分要求可以通过编译器选项来落实。例如，启用缓冲区溢出保护、栈随机化、数据执行保护和相关警告；一旦发现不安全的指针、内存、格式字符串、整数或字符串操作，就让构建失败。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.4.1** | 验证应用采用内存安全的字符串操作、安全的内存复制方法和指针运算，以检测或防止栈溢出、缓冲区溢出和堆溢出。 | 2 |
| **1.4.2** | 验证应用通过符号检查、范围检查和输入验证来防止整数溢出。 | 2 |
| **1.4.3** | 验证动态分配的内存和资源在使用完毕后得到释放；同时删除或置空指向已释放内存的引用和指针，以防止悬空指针和释放后使用漏洞。 | 2 |

## V1.5 安全反序列化

反序列化是把存储或传输形式的数据转换为应用对象的过程。这个过程曾引发多种代码注入漏洞，因此必须谨慎、安全地处理。

尤其要注意，有些反序列化方法已被编程语言或框架的官方文档明确标记为不安全，而且无论如何配置，都不能安全地处理不可信数据。应逐一审查应用采用的反序列化机制，确认其安全性。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **1.5.1** | 验证 XML 解析器采用严格的安全配置，并禁用外部实体解析等危险功能，以防范 XML 外部实体（XXE）攻击。 | 1 |
| **1.5.2** | 验证应用在反序列化不可信数据时，强制采用安全的输入处理方式，例如设置对象类型允许列表，或限制客户端能够指定的对象类型。不得使用已经明确认定为不安全的反序列化机制来处理不可信输入。 | 2 |
| **1.5.3** | 验证应用使用多个解析器处理同一类数据时（例如使用不同的 JSON、XML 或 URL 解析器），各解析器采用一致的解析规则和字符编码。这样可以避免 JSON 互操作性漏洞，也可以防止攻击者利用不同解析器对 URI 或文件的解释差异实施远程文件包含（RFI）或服务端请求伪造（SSRF）攻击。 | 3 |

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
