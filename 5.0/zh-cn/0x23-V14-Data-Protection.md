# V14 数据保护

## 控制目标

应用无法掌控所有使用方式和用户行为，因此应实施控制，限制对客户端设备上敏感数据的未授权访问。

本章包含与定义哪些数据需要保护、应如何保护，以及需要实现的具体机制或应避免的陷阱相关的要求。

数据保护的另一个考虑是批量提取、修改或过度使用。每个系统的要求可能差异很大，因此判断什么是“异常”必须结合威胁模型和业务风险。从 ASVS 角度看，检测这些问题由“安全日志和错误处理”章节处理，设置限制由“验证和业务逻辑”章节处理。

## V14.1 数据保护文档

能够保护数据的一个关键前提，是对哪些数据应视为敏感数据进行分类。敏感性可能分为多个等级；对于每个等级，保护该等级数据所需的控制也会不同。

有多种隐私法规和法律会影响应用必须如何存储、使用和传输敏感个人信息。本节不再试图重复这些数据保护或隐私法律，而是聚焦于保护敏感数据的关键技术考虑。请查阅当地法律法规，并按需咨询合格的隐私专家或律师。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **14.1.1** | 验证应用创建和处理的所有敏感数据都已被识别，并分类到保护等级中。这包括仅经过编码因而容易解码的数据，例如 Base64 字符串或 JWT 内部的明文载荷。保护等级需要考虑应用必须遵守的任何数据保护和隐私法规与标准。 | 2 |
| **14.1.2** | 验证每个敏感数据保护等级都有一组成文的保护要求。其中必须包括但不限于：通用加密、完整性验证、保留期限、数据记录到日志的方式、日志中敏感数据的访问控制、数据库级加密、采用的隐私保护和隐私增强技术，以及其他保密性要求。 | 2 |

## V14.2 通用数据保护

本节包含与数据保护相关的各类实践要求。多数要求针对特定问题，例如非预期数据泄露，但也包括一条通用要求：根据每个数据项所需保护等级实现保护控制。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **14.2.1** | 验证敏感数据只会在 HTTP 消息正文或头字段中发送给服务器，并且 URL 和查询字符串不包含敏感信息，例如 API 密钥或会话令牌。 | 1 |
| **14.2.2** | 验证应用防止敏感数据缓存在服务器组件中，例如负载均衡器和应用缓存；或者确保数据在使用后被安全清除。 | 2 |
| **14.2.3** | 验证已定义的敏感数据不会发送给不可信方（例如用户跟踪器），以防止数据在应用控制范围外被不必要收集。 | 2 |
| **14.2.4** | 验证围绕敏感数据的控制，包括加密、完整性验证、保留、数据如何记录到日志、日志中敏感数据的访问控制、隐私和隐私增强技术，均按具体数据保护等级文档中的定义实现。 | 2 |
| **14.2.5** | 验证缓存机制配置为只缓存具有该资源预期内容类型，且不包含敏感动态内容的响应。访问不存在的文件时，Web 服务器应返回 404 或 302 响应，而不是返回另一个有效文件。这应能防止 Web Cache Deception 攻击。 | 3 |
| **14.2.6** | 验证应用只返回其功能所需的最少敏感数据。例如，只返回信用卡号的部分数字，而不是完整号码。如果需要完整数据，除非用户明确查看，否则应在用户界面中进行遮蔽。 | 3 |
| **14.2.7** | 验证敏感信息受数据保留分类规则约束，确保过时或不再需要的数据会被自动删除、按既定计划删除，或在具体情况需要时删除。 | 3 |
| **14.2.8** | 验证除非用户同意存储，否则会从用户提交文件的元数据中移除敏感信息。 | 3 |

## V14.3 客户端数据保护

本节包含防止数据在应用客户端或用户代理侧以特定方式泄露的要求。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **14.3.1** | 验证客户端或会话终止后，会从客户端存储（例如浏览器 DOM）中清除已认证数据。'Clear-Site-Data' HTTP 响应头字段可能有助于实现这一点，但如果会话终止时服务器连接不可用，客户端也应能够自行清理。 | 1 |
| **14.3.2** | 验证应用设置足够的反缓存 HTTP 响应头字段（即 Cache-Control: no-store），使敏感数据不会缓存在浏览器中。 | 2 |
| **14.3.3** | 验证存储在浏览器存储中的数据（例如 localStorage、sessionStorage、IndexedDB 或 Cookie）不包含敏感数据，唯一例外是会话令牌。 | 2 |

## 参考资料

更多信息，另见：

* [Consider using the Security Headers website to check security and anti-caching header fields](https://securityheaders.com/)
* [Documentation about anti-caching headers by Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)
* [OWASP Secure Headers project](https://owasp.org/www-project-secure-headers/)
* [OWASP Privacy Risks Project](https://owasp.org/www-project-top-10-privacy-risks/)
* [OWASP User Privacy Protection Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/User_Privacy_Protection_Cheat_Sheet.html)
* [Australian Privacy Principle 11 - Security of personal information](https://www.oaic.gov.au/privacy/australian-privacy-principles/australian-privacy-principles-guidelines/chapter-11-app-11-security-of-personal-information)
* [European Union General Data Protection Regulation (GDPR) overview](https://www.edps.europa.eu/data-protection_en)
* [European Union Data Protection Supervisor - Internet Privacy Engineering Network](https://www.edps.europa.eu/data-protection/ipen-internet-privacy-engineering-network_en)
* [Information on the "Clear-Site-Data" header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Clear-Site-Data)
* [White paper on Web Cache Deception](https://www.blackhat.com/docs/us-17/wednesday/us-17-Gil-Web-Cache-Deception-Attack-wp.pdf)
