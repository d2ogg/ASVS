# V3 Web 前端安全

## 控制目标

本类别聚焦于防范通过 Web 前端执行的攻击。这些要求不适用于机器到机器的解决方案。

## V3.1 Web 前端安全文档

本节概述应用文档中应明确规定的浏览器安全功能。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **3.1.1** | 验证应用文档说明了使用该应用的浏览器必须支持哪些预期安全功能（例如 HTTPS、HTTP 严格传输安全（HSTS）、内容安全策略（CSP）以及其他相关 HTTP 安全机制）。文档还必须定义当部分功能不可用时应用应如何表现（例如警告用户或阻止访问）。 | 3 |

## V3.2 非预期内容解释

在错误上下文中渲染内容或功能，可能导致恶意内容被执行或显示。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **3.2.1** | 验证已具备安全控制，防止浏览器在错误上下文中渲染 HTTP 响应中的内容或功能（例如直接请求 API、用户上传文件或其他资源时）。可能的控制包括：除非 HTTP 请求头字段（例如 Sec-Fetch-\*）表明当前上下文正确，否则不提供该内容；使用 Content-Security-Policy 头字段的 sandbox 指令；或在 Content-Disposition 头字段中使用 attachment 处置类型。 | 1 |
| **3.2.2** | 验证对于只应显示为文本而不应渲染为 HTML 的内容，使用安全渲染函数（例如 createTextNode 或 textContent）进行处理，以防止 HTML 或 JavaScript 等内容被意外执行。 | 1 |
| **3.2.3** | 验证应用在使用客户端 JavaScript 时，通过显式变量声明、严格类型检查、避免在 document 对象上存储全局变量，以及实现命名空间隔离来避免 DOM clobbering。 | 3 |

## V3.3 Cookie 设置

本节概述安全配置敏感 Cookie 的要求，以更高程度保证这些 Cookie 由应用自身创建，并防止其内容泄露或被不当修改。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **3.3.1** | 验证 Cookie 已设置 'Secure' 属性；如果 Cookie 名称未使用 '\__Host-' 前缀，则必须使用 '__Secure-' 前缀。 | 1 |
| **3.3.2** | 验证每个 Cookie 的 'SameSite' 属性值都根据 Cookie 用途设置，以降低用户界面重定向攻击和基于浏览器的请求伪造攻击暴露面，后者通常称为跨站请求伪造（CSRF）。 | 2 |
| **3.3.3** | 验证 Cookie 名称使用 '__Host-' 前缀，除非这些 Cookie 被明确设计为与其他主机共享。 | 2 |
| **3.3.4** | 验证如果某个 Cookie 的值不应被客户端脚本访问（例如会话令牌），则该 Cookie 必须设置 'HttpOnly' 属性，并且相同值（例如会话令牌）只能通过 'Set-Cookie' 头字段传输给客户端。 | 2 |
| **3.3.5** | 验证应用写入 Cookie 时，Cookie 名称和值的总长度不超过 4096 字节。过大的 Cookie 不会被浏览器存储，因此也不会随请求发送，这会阻止用户使用依赖该 Cookie 的应用功能。 | 3 |

## V3.4 浏览器安全机制头

本节说明应在 HTTP 响应上设置哪些安全头，以便浏览器在处理应用响应时启用安全功能和限制。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **3.4.1** | 验证所有响应都包含 Strict-Transport-Security 头字段，以强制执行 HTTP 严格传输安全（HSTS）策略。必须定义至少 1 年的最大有效期；对于 L2 及以上，策略还必须适用于所有子域名。 | 1 |
| **3.4.2** | 验证 Cross-Origin Resource Sharing（CORS）的 Access-Control-Allow-Origin 头字段由应用设置为固定值；如果使用 Origin HTTP 请求头字段值，则必须根据可信源允许列表进行验证。当需要使用 'Access-Control-Allow-Origin: *' 时，验证响应不包含任何敏感信息。 | 1 |
| **3.4.3** | 验证 HTTP 响应包含 Content-Security-Policy 响应头字段，其中定义指令以确保浏览器只加载和执行可信内容或资源，从而限制恶意 JavaScript 的执行。最低要求是使用包含 object-src 'none' 和 base-uri 'none' 指令的全局策略，并定义允许列表，或使用 nonce 或哈希。对于 L3 应用，必须为每个响应定义带有 nonce 或哈希的策略。 | 2 |
| **3.4.4** | 验证所有 HTTP 响应都包含 'X-Content-Type-Options: nosniff' 头字段。它指示浏览器不要对给定响应使用内容嗅探和 MIME 类型猜测，并要求响应的 Content-Type 头字段值与目标资源匹配。例如，只有当响应的 Content-Type 为 'text/css' 时，对样式资源的请求响应才会被接受。这也会启用浏览器的 Cross-Origin Read Blocking（CORB）功能。 | 2 |
| **3.4.5** | 验证应用设置来源信息策略（referrer policy），防止技术敏感数据通过 'Referer' HTTP 请求头字段泄露给第三方服务。可以通过 Referrer-Policy HTTP 响应头字段或 HTML 元素属性实现。敏感数据可能包括 URL 中的路径和查询数据；对于内部非公开应用，还可能包括主机名。 | 2 |
| **3.4.6** | 验证 Web 应用对每个 HTTP 响应使用 Content-Security-Policy 头字段的 frame-ancestors 指令，确保默认情况下不能被嵌入，并且只有在必要时才允许嵌入特定资源。注意，X-Frame-Options 头字段虽然仍被浏览器支持，但已经过时，不应依赖它。 | 2 |
| **3.4.7** | 验证 Content-Security-Policy 头字段指定了报告违规的位置。 | 3 |
| **3.4.8** | 验证所有会发起文档渲染的 HTTP 响应（例如 Content-Type 为 text/html 的响应）都按需要包含带 same-origin 指令或 same-origin-allow-popups 指令的 Cross‑Origin‑Opener‑Policy 头字段。这可以防止滥用对 Window 对象共享访问的攻击，例如 tabnabbing 和 frame counting。 | 3 |

## V3.5 浏览器源隔离

在服务端接受对敏感功能的请求时，应用需要确保该请求由应用自身或可信方发起，而不是由攻击者伪造。

此处的敏感功能可以包括接受已认证和未认证用户的表单提交（例如认证请求）、改变状态的操作，或消耗大量资源的功能（例如数据导出）。

这里的关键保护包括同源策略这类浏览器安全策略（针对 JavaScript），以及 Cookie 的 SameSite 逻辑。另一个常见保护是 CORS 预检机制。该机制对于设计为可从不同源调用的端点至关重要，但对于并非设计为可从不同源调用的端点，它也可以作为有用的请求伪造预防机制。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **3.5.1** | 验证如果应用不依赖 CORS 预检机制来防止不允许的跨源请求使用敏感功能，则会验证这些请求，确保它们来自应用自身。可以通过使用并验证防伪令牌，或要求额外的 HTTP 头字段且这些字段不属于 CORS 安全列表请求头字段来实现。这用于防御基于浏览器的请求伪造攻击，通常称为跨站请求伪造（CSRF）。 | 1 |
| **3.5.2** | 验证如果应用依赖 CORS 预检机制来防止不允许的跨源使用敏感功能，则无法通过不会触发 CORS 预检请求的请求调用该功能。这可能需要检查 'Origin' 和 'Content-Type' 请求头字段的值，或使用不属于 CORS 安全列表头字段的额外头字段。 | 1 |
| **3.5.3** | 验证访问敏感功能的 HTTP 请求使用合适的 HTTP 方法，例如 POST、PUT、PATCH 或 DELETE，而不是 HTTP 规范定义为“安全”的方法，例如 HEAD、OPTIONS 或 GET。或者，可以严格验证 Sec-Fetch-* 请求头字段，确保请求并非来自不合适的跨源调用、导航请求，或资源加载（例如图片源）等非预期来源。 | 1 |
| **3.5.4** | 验证不同应用托管在不同主机名上，以利用同源策略提供的限制，包括一个源加载的文档或脚本如何与另一个源的资源交互，以及基于主机名的 Cookie 限制。 | 2 |
| **3.5.5** | 验证通过 postMessage 接口收到的消息，如果消息来源不可信，或消息语法无效，会被丢弃。 | 2 |
| **3.5.6** | 验证应用任何位置都未启用 JSONP 功能，以避免跨站脚本包含（XSSI）攻击。 | 3 |
| **3.5.7** | 验证需要授权的数据不会包含在脚本资源响应（例如 JavaScript 文件）中，以防止跨站脚本包含（XSSI）攻击。 | 3 |
| **3.5.8** | 验证只有在预期情况下，认证资源（例如图片、视频、脚本和其他文档）才可以代表用户被加载或嵌入。可以通过严格验证 Sec-Fetch-* HTTP 请求头字段，确保请求并非来自不合适的跨源调用；也可以设置限制性的 Cross-Origin-Resource-Policy HTTP 响应头字段，指示浏览器阻止返回内容。 | 3 |

## V3.6 外部资源完整性

本节为在第三方站点上安全托管内容提供指导。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **3.6.1** | 验证客户端资产，例如 JavaScript 库、CSS 或 Web 字体，只有在资源是静态且有版本标识，并且使用子资源完整性（SRI）验证资产完整性时，才会托管在外部（例如内容分发网络）。如果无法做到这一点，应针对每个资源记录安全决策来说明理由。 | 3 |

## V3.7 其他浏览器安全考虑

本节包含客户端浏览器安全所需的其他各类安全控制和现代浏览器安全功能。

| # | 描述 | 级别 |
| :---: | :--- | :---: |
| **3.7.1** | 验证应用只使用仍受支持且被认为安全的客户端技术。不符合此要求的技术示例包括 NSAPI 插件、Flash、Shockwave、ActiveX、Silverlight、NACL 或客户端 Java 小程序。 | 2 |
| **3.7.2** | 验证应用只有在目标出现在允许列表中时，才会自动将用户重定向到不同主机名或域名（且该主机名或域名不受应用控制）。 | 2 |
| **3.7.3** | 验证当用户被重定向到应用控制范围外的 URL 时，应用会显示通知，并提供取消导航的选项。 | 3 |
| **3.7.4** | 验证应用的顶级域名（例如 site.tld）已加入 HTTP 严格传输安全（HSTS）公共预加载列表。这确保主流浏览器直接内置该应用使用 TLS 的要求，而不是只依赖 Strict-Transport-Security 响应头字段。 | 3 |
| **3.7.5** | 验证如果用于访问应用的浏览器不支持预期安全功能，应用会按文档描述的方式表现（例如警告用户或阻止访问）。 | 3 |

## 参考资料

更多信息，另见：

* [Set-Cookie __Host- prefix details](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#cookie_prefixes)
* [OWASP Content Security Policy Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html)
* [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
* [OWASP Cross-Site Request Forgery Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
* [HSTS Browser Preload List submission form](https://hstspreload.org/)
* [OWASP DOM Clobbering Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_Clobbering_Prevention_Cheat_Sheet.html)
