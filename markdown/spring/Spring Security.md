## Spring security

> VERSON:5.7.9

### 概述

Spring Security 是一个提供身份验证、授权和防御常见攻击的框架。它为保护命令式和反应式应用程序提供了一流的支持，是保护基于 Spring 的应用程序的事实标准。 有关功能的完整列表，请参阅参考资料中的“功能”部分。

如果您已准备好开始保护应用程序，请参阅 servlet 和响应式入门部分。这些部分将引导您创建第一个 Spring Security 应用程序。 如果您想了解 Spring Security 的工作原理，可以参考架构部分。 如果您有任何疑问，有一个很棒的社区很乐意为您提供帮助！

#### 先决条件

Spring Security 需要 Java 8 或更高版本的运行时环境。
由于 Spring Security 旨在以独立的方式运行，因此您无需在 Java 运行时环境中放置任何特殊配置文件。特别是，您无需配置特殊的 Java 身份验证和授权服务 (JAAS) 策略文件或将 Spring Security 放入常见的类路径位置。 同样，如果您使用 EJB 容器或 Servlet 容器，则无需在任何地方放置任何特殊配置文件，也无需将 Spring Security 包含在服务器类加载器中。
所有必需的文件都包含在您的应用程序中。 这种设计提供了最大的部署时间灵活性，因为您可以将目标工件（无论是 JAR、WAR 还是 EAR）从一个系统复制到另一个系统，并且它会立即运行。

### 社区

#### 获取帮助

- 阅读本文档。
- 尝试我们的众多[sample applications](https://docs.spring.io/spring-security/reference/5.7/samples.html#samples)之一。
- 使用 spring-security 标签在 https://stackoverflow.com 上提问。
- 在 https://github.com/spring-projects/spring-security/issues 上报告错误和增强请求

#### 参与其中

欢迎您参与 Spring Security 项目。贡献的方式有很多，包括回答 Stack Overflow 上的问题、编写新代码、改进现有代码、协助编写文档、开发示例或教程、报告错误或只是提出建议。有关更多信息，请参阅我们的贡献文档。

#### 源码

您可以在 GitHub 上找到 Spring Security 的源代码：https://github.com/spring-projects/spring-security/

#### 许可证

Spring Security 是根据 [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0.html) 许可发布的开源软件。

#### 社交媒体

您可以在 Twitter 上关注 @SpringSecurity 和 Spring Security 团队，了解最新动态。您还可以关注 @SpringCentral，了解整个 Spring 产品组合的最新动态。

### 特性

Spring Security 为身份验证、授权和防御常见漏洞提供了全面的支持。它还提供与其他库的集成以简化其使用。

[认证](https://docs.spring.io/spring-security/reference/5.7/features/authentication/index.html)
[防范漏洞](https://docs.spring.io/spring-security/reference/5.7/features/exploits/index.html)
[集成](https://docs.spring.io/spring-security/reference/5.7/features/integrations/index.html)

#### 认证

Spring Security 为身份验证提供全面支持。身份验证是我们验证尝试访问特定资源的人的身份的方式。验证用户身份的一种常见方法是要求用户输入用户名和密码。一旦执行身份验证，我们就知道身份并可以执行授权。 Spring Security 为验证用户身份提供内置支持。本节专门介绍适用于 Servlet 和 WebFlux 环境的通用身份验证支持。有关每个堆栈支持的详细信息，请参阅有关 Servlet 和 WebFlux 身份验证的部分。

##### 密码存储

Spring Security 的 PasswordEncoder 接口用于对密码进行单向转换，以便安全地存储密码。鉴于 PasswordEncoder 是单向转换，因此当密码转换需要双向转换时（即存储用于向数据库进行身份验证的凭据），它并不适用。通常，PasswordEncoder 用于存储需要在身份验证时与用户提供的密码进行比较的密码。

###### Password Storage History

多年来，存储密码的标准机制一直在演变。起初，密码以纯文本形式存储。人们认为密码是安全的，因为数据存储中保存了访问密码所需的凭据。然而，恶意用户能够找到方法，利用 SQL 注入等攻击获取大量用户名和密码的“数据转储”。随着越来越多的用户凭据被公开，安全专家意识到我们需要采取更多措施来保护用户的密码。

（哈希 -> 彩虹表撞库 -> 加盐哈希 -> 工作因子）
然后，开发人员被鼓励在将密码通过单向哈希（例如 SHA-256）后再存储它们。当用户尝试进行身份验证时，哈希密码将与他们输入的密码的哈希值进行比较。这意味着系统只需要存储密码的单向哈希值。如果发生泄露，则只会暴露密码的单向哈希值。由于哈希是单向的，并且从计算上很难猜测给定哈希值的密码，因此不值得花费精力去找出系统中的每个密码。为了击败这个新系统，恶意用户决定创建称为彩虹表的查找表。他们不必每次都猜测每个密码，而是计算一次密码并将其存储在查找表中。

为了降低彩虹表的有效性，我们鼓励开发人员使用加盐密码。它不会只使用密码作为哈希函数的输入，而是为每个用户的密码生成随机字节（称为盐）。盐和用户密码将通过哈希函数运行，从而产生唯一的哈希值。盐将以明文形式与用户密码一起存储。然后，当用户尝试进行身份验证时，哈希密码将与存储的盐和他们输入的密码的哈希值进行比较。唯一的盐意味着彩虹表不再有效，因为每个盐和密码组合的哈希值都不同。

在现代，我们意识到加密哈希（如 SHA-256）不再安全。原因是，借助现代硬件，我们可以每秒执行数十亿次哈希计算。这意味着我们可以轻松破解每个密码。

现在鼓励开发人员利用自适应单向函数来存储密码。使用自适应单向函数验证密码会占用大量资源（即 CPU、内存等）。自适应单向函数允许配置“工作因子”，该因子会随着硬件的改进而增加。建议将“工作因子”调整为在您的系统上验证密码大约需要 1 秒钟。这种权衡是让攻击者难以破解密码，但成本不会太高，从而给您自己的系统带来过重的负担。Spring Security 试图为“工作因子”提供一个良好的起点，但鼓励用户为自己的系统定制“工作因子”，因为性能会因系统而异。应使用的自适应单向函数示例包括 bcrypt、PBKDF2、scrypt 和 argon2。

由于自适应单向函数是故意占用大量资源的，因此对每个请求验证用户名和密码将显著降低应用程序的性能。Spring Security（或任何其他库）无法加快密码验证速度，因为安全性是通过使验证资源密集型而获得的。鼓励用户将长期凭证（即用户名和密码）换成短期凭证（即会话、OAuth 令牌等）。可以快速验证短期凭证而不会损失任何安全性。

###### DelegatingPasswordEncoder

在 Spring Security 5.0 之前，默认的 PasswordEncoder 是 NoOpPasswordEncoder，它需要纯文本密码。根据密码历史记录部分，您可能认为默认的 PasswordEncoder 现在类似于 BCryptPasswordEncoder。然而，这忽略了三个现实世界的问题：

- 许多应用程序使用无法轻松迁移的旧密码编码
- 密码存储的最佳实践将再次改变
- 作为框架，Spring Security 不能频繁做出重大更改

相反，Spring Security 引入了 DelegatingPasswordEncoder，它通过以下方式解决了所有问题：

- 确保使用当前密码存储建议对密码进行编码
- 允许验证现代和传统格式的密码
- 允许将来升级编码

您可以使用 PasswordEncoderFactories 轻松构造 DelegatingPasswordEncoder 的实例。

### 项目模块和依赖

即使您不使用 Maven，我们也建议您查阅 pom.xml 文件以了解第三方依赖项和版本。另一个好主意是检查示例应用程序中包含的库。

本节提供了 Spring Security 中的模块及其在运行的应用程序中运行所需的其他依赖项的参考。我们不包含仅在构建或测试 Spring Security 本身时使用的依赖项。我们也不包含外部依赖项所需的传递依赖项。

所需的 Spring 版本已在项目网站上列出，因此下面省略了 Spring 依赖项的具体版本。请注意，下面列出的某些“可选”依赖项可能仍是 Spring 应用程序中其他非安全功能所必需的。此外，如果大多数应用程序中都使用“可选”依赖项，则它们实际上可能不会在项目的 Maven POM 文件中被标记为“可选”。它们只是在某种意义上是“可选”的，除非您正在使用指定的功能，否则您不需要它们。

当一个模块依赖于另一个 Spring Security 模块时，它所依赖的模块的非可选依赖项也被视为必需的，不会单独列出。

#### Core — `spring-security-core.jar`

此模块包含核心身份验证和访问控制类和接口、远程支持和基本配置 API。任何使用 Spring Security 的应用程序都需要它。它支持独立应用程序、远程客户端、方法（服务层）安全性和 JDBC 用户配置。它包含以下顶级
- `org.springframework.security.core`
- `org.springframework.security.access`
- `org.springframework.security.authentication`
- `org.springframework.security.provisioning`

>此包依赖

| Dependency        | Version | Description                                                  |
| :---------------- | :------ | :----------------------------------------------------------- |
| ehcache           | 1.6.2   | Required if the Ehcache-based user cache implementation is used (optional). |
| spring-aop        |         | Method security is based on Spring AOP                       |
| spring-beans      |         | Required for Spring configuration                            |
| spring-expression |         | Required for expression-based method security (optional)     |
| spring-jdbc       |         | Required if using a database to store user data (optional).  |
| spring-tx         |         | Required if using a database to store user data (optional).  |
| aspectjrt         | 1.6.10  | Required if using AspectJ support (optional).                |
| jsr250-api        | 1.0     | Required if you are using JSR-250 method-security annotations (optional). |

#### Remoting — `spring-security-remoting.jar`
此模块提供与 Spring Remoting 的集成。除非您正在编写使用 Spring Remoting 的远程客户端，否则您不需要此功能。主包是 org.springframework.security.remoting。

> 此包依赖

| Dependency           | Version | Description                                           |
| :------------------- | :------ | :---------------------------------------------------- |
| spring-security-core |         |                                                       |
| spring-web           |         | Required for clients which use HTTP remoting support. |




#### Web — `spring-security-web.jar`
此模块包含过滤器和相关的网络安全基础架构代码。它包含任何具有 servlet API 依赖项的内容。如果您需要 Spring Security Web 身份验证服务和基于 URL 的访问控制，则需要它。主包是 org.springframework.security.web。

> 此包依赖

| Dependency           | Version | Description                                                  |
| :------------------- | :------ | :----------------------------------------------------------- |
| spring-security-core |         |                                                              |
| spring-web           |         | Spring web support classes are used extensively.             |
| spring-jdbc          |         | Required for JDBC-based persistent remember-me token repository (optional). |
| spring-tx            |         | Required by remember-me persistent token repository implementations (optional). |

#### Config —` spring-security-config.jar`
此模块包含安全命名空间解析代码和 Java 配置代码。如果您使用 Spring Security XML 命名空间进行配置或 Spring Security 的 Java 配置支持，则需要它。主包是 org.springframework.security.config。这些类均不适用于在应用程序中直接使用。

> 此包依赖

| Dependency             | Version | Description                                                  |
| :--------------------- | :------ | :----------------------------------------------------------- |
| spring-security-core   |         |                                                              |
| spring-security-web    |         | Required if you are using any web-related namespace configuration (optional). |
| spring-security-ldap   |         | Required if you are using the LDAP namespace options (optional). |
| spring-security-openid |         | Required if you are using OpenID authentication (optional).  |
| aspectjweaver          | 1.6.10  | Required if using the protect-pointcut namespace syntax (optional). |

#### LDAP — `spring-security-ldap.jar`

此模块提供 LDAP 身份验证和配置代码。如果您需要使用 LDAP 身份验证或管理 LDAP 用户条目，则需要此模块。顶级包是 org.springframework.security.ldap。

| Dependency                                                   | Version | Description                                                  |
| :----------------------------------------------------------- | :------ | :----------------------------------------------------------- |
| spring-security-core                                         |         |                                                              |
| spring-ldap-core                                             | 1.3.0   | LDAP support is based on Spring LDAP.                        |
| spring-tx                                                    |         | Data exception classes are required.                         |
| apache-ds [[1](https://docs.spring.io/spring-security/reference/5.7/modules.html#_footnotedef_1)] | 1.5.5   | Required if you are using an embedded LDAP server (optional). |
| shared-ldap                                                  | 0.9.15  | Required if you are using an embedded LDAP server (optional). |
| ldapsdk                                                      | 4.1     | Mozilla LdapSDK. Used for decoding LDAP password policy controls if you are using password-policy functionality with OpenLDAP, for example. |



### Servlet应用

Spring Security 通过使用标准 Servlet 过滤器与 Servlet 容器集成。这意味着它可以与在 Servlet 容器中运行的任何应用程序配合使用。更具体地说，您无需在基于 Servlet 的应用程序中采用 Spring 即可利用 Spring Security。

#### 章节摘要

- [Getting Started](https://docs.spring.io/spring-security/reference/5.7/servlet/getting-started.html)

- [Architecture](https://docs.spring.io/spring-security/reference/5.7/servlet/architecture.html)

- [Authentication](https://docs.spring.io/spring-security/reference/5.7/servlet/authentication/index.html)

- [Authorization](https://docs.spring.io/spring-security/reference/5.7/servlet/authorization/index.html)

- [OAuth2](https://docs.spring.io/spring-security/reference/5.7/servlet/oauth2/index.html)

- [SAML2](https://docs.spring.io/spring-security/reference/5.7/servlet/saml2/index.html)

- [Protection Against Exploits](https://docs.spring.io/spring-security/reference/5.7/servlet/exploits/index.html)

- [Integrations](https://docs.spring.io/spring-security/reference/5.7/servlet/integrations/index.html)
- [Configuration-Java](https://docs.spring.io/spring-security/reference/5.7/servlet/configuration/java.html)

- [Testing](https://docs.spring.io/spring-security/reference/5.7/servlet/test/index.html)

- [Appendix](https://docs.spring.io/spring-security/reference/5.7/servlet/appendix/index.html)

#### 快速开始

<span  style="color:green"> ` NOTE ` </span> 完整的应用程序可以在我们的示例存储库中找到。为方便起见，您可以点击此处下载最小的 Spring Boot + Spring Security 应用程序。

本节介绍如何使用 Spring Security 和 Spring Boot 的最低设置。















































