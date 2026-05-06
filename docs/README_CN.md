![Logo](../docs/logo.png)

# Whois4J

<br>

<p align="center">
  <a href="../README.md"><img src="https://img.shields.io/badge/Doc.-English-lightgrey?style=flat-square&logo=read-the-docs&logoColor=white" alt="English" /></a>
  <a href="./README_CN.md"><img src="https://img.shields.io/badge/文档-简体中文-e2ca8a?style=flat-square&logo=read-the-docs&logoColor=white" alt="简体中文" /></a>
</p>

<div align="center">
  <a href="../LICENSE"><img src="https://img.shields.io/badge/License-Proprietary-4eb1ba" alt="License"></a>
  <a href="https://www.oracle.com/java"><img src="https://img.shields.io/badge/Java-8%2f11%2f17+-b07219" alt="Java Support"></a>
  <a href="https://spring.io/projects/spring-boot"><img src="https://img.shields.io/badge/Spring%20Boot-2.x%2f3.x-6db33f" alt="Spring Boot"></a>
  <a href="https://central.sonatype.com/search?q=g:io.github.tinolab.whois4j"><img src="https://img.shields.io/maven-central/v/io.github.tinolab.whois4j/whois4j-parent?label=Maven%20Central" alt="Maven Central"></a>
</div>

<br>

**Whois4J** 是一个现代化、高性能、全功能的 Java WHOIS/RDAP 查询与解析工具库。它通过独创的双协议并行调度机制和深度文本解析引擎，为 Java 生态提供极其准确、结构化的域名及网络资产信息检索能力。

## 🚀 核心特性

- **双协议并行调度**: 同时发起传统 WHOIS (TCP 43) 和现代 RDAP (HTTP/JSON) 请求，利用 `CompletableFuture` 实现非阻塞并发，并执行智能数据合并，确保信息的完整性。
- **智能时间归一化**: 针对 WHOIS 报文中千奇百怪的时间格式（如 `Oct-2023`, `2023.10.27`, `2023-10-27 10:00:00` 等），内置了高容错性的解析引擎，确保最终能输出统一的 ISO 8601 标准时间字符串。
- **全球化 IP/ASN 引导**: 严格遵循 IANA 官方的 RDAP 引导规范，动态加载 IPv4/IPv6 及 ASN 的服务映射表，确保对全球各区域（ARIN, RIPE, APNIC, LACNIC, AFRINIC）的网络资产都能进行精准的路由查询，彻底告别硬编码接口。
- **多维度价格参考**: 自动集成第三方域名行情接口（如 Nazhumi），提供新注册、续费、转入的实时价格参考及币种转换，并支持自动识别高价溢价资产。
- **注册商与名称服务器 (NS) 品牌识别**: 内置庞大的品牌数据库，支持自动识别全球数百家主流注册商（如 GoDaddy, Namecheap）及名称服务器提供商（如 Cloudflare, AWS Route 53），并提供对应的品牌图标路径与主题色。
- **高性能服务端缓存**: 原生集成 Redis 缓存机制，支持可配置的过期时间（TTL）与缓存键前缀，并自动下发 `Cache-Control` 响应头（支持 `stale-while-revalidate`）控制客户端及 CDN 缓存行为，大幅提升重复查询的响应速度并降低权威服务器压力。
- **IDN 国际化域名支持**: 原生支持中文字符等非 ASCII 域名的自动转码（Punycode）查询，支持如 `测试.com` 或 `google.中国` 等域名。
- **递归路由追溯**: 内置智能路由追踪逻辑。能够自动从 IANA 根服务器识别顶级域所属的 Registry Server，并自动重定向至具体的 Registrar 服务器。
- **全资产支持**: 不仅支持域名查询，还完整支持 **IPv4/IPv6 CIDR** 段和 **ASN** 的信息检索。
- **RDAP 深度映射**: 深度解析 RDAP 复杂的 JSON 嵌套结构，支持对 `vCard` 数组的深度剥离及 `events` 时间轴的精确解析。
- **EPP 状态字典**: 完整内置 ICANN 标准的 EPP 状态库，提供状态含义、分类（OK/Grace/Redemption等）以及专业的 UI 颜色展示建议。
- **工业级日志标准**: 基于 **SLF4J** 接口构建，可无缝集成至 Logback、Log4j2 等主流日志框架。

## 🏗️ 模块架构

项目采用 Maven 多模块结构，确保核心逻辑与框架适配完全解耦：

- `whois4j-core`: **核心逻辑库**。纯 Java 实现，包含协议通讯、解析引擎、路由字典、品牌识别、IDN 支持。
- `whois4j-spring-boot-starter`: **Spring Boot 2.x 适配器**。支持 JDK 8+，兼容传统的 `spring.factories` 自动装配。
- `whois4j-spring-boot-3-starter`: **Spring Boot 3.x 适配器**。支持 JDK 17+，采用新的 `AutoConfiguration.imports` 规范。

## 📦 安装

### 1. Spring Boot 项目 (推荐)

根据您的 Spring Boot 版本选择对应的 Starter：

```xml
<!-- Spring Boot 3.x / Java 17+ -->
<dependency>
    <groupId>io.github.tinolab.whois4j</groupId>
    <artifactId>whois4j-spring-boot-3-starter</artifactId>
    <version>1.0.0</version>
</dependency>
```

```xml
<!-- Spring Boot 2.x / Java 8+ -->
<dependency>
    <groupId>io.github.tinolab.whois4j</groupId>
    <artifactId>whois4j-spring-boot-starter</artifactId>
    <version>1.0.0</version>
</dependency>
```

### 2. 标准 Java 项目

仅引入核心逻辑库：

```xml
<!-- Whois4J Core -->
<dependency>
    <groupId>io.github.tinolab.whois4j</groupId>
    <artifactId>whois4j-core</artifactId>
    <version>1.0.0</version>
</dependency>
```

## ⚙️ 配置详解

`Whois4J` 遵循“约定大于配置”的原则。在 Spring Boot 环境中，您可以通过 `application.yml` 进行如下配置（所有项均为可选，系统将自动应用默认值）：

```yaml
# ====================================================================
# Whois4J 全局配置
# ====================================================================
whois4j:
  # [查询基础配置]
  timeout: 15000         # 全局查询超时时间 (单位：毫秒)，默认为 15000ms
  max-follow: 2          # WHOIS 查询的最大追溯层数 (1-2)，默认为 2
  
  # [网络代理配置]
  # 建议在内网环境或受限网络下配置，用于访问海外 TCP 43 端口及 RDAP 接口
  proxy:
    host: 127.0.0.1      # SOCKS5 代理服务器地址
    port: 1080           # SOCKS5 代理服务器端口

  # [增强功能凭据]
  # 配置后可激活 data.mozDomainAuthority 等 SEO 权重的自动抓取
  moz:
    access-id: ""        # Moz Access ID
    secret-key: ""       # Moz Secret Key

  # [服务端缓存配置]
  # 开启后将使用 Redis 缓存查询结果，需项目中已配置 spring-data-redis
  cache:
    enabled: true        # 是否启用缓存，默认 false
    prefix: "whois:"     # 缓存键前缀
    ttl: 3600            # 缓存过期时间 (单位：秒)

  # [自动 API 接口配置]
  # 开启后将自动注册一个标准的 REST API 查询接口
  api:
    enabled: true        # 是否启用自动接口，默认 false
```

### 💡 配置优先级说明

系统在运行时会根据以下严谨的优先级顺序合并配置，确保灵活性的同时提供可靠的默认行为：

#### 1. 超时与追溯层数
1.  **最高优先级**：代码中通过 `Whois4J.timeout(ms)` 或 `Whois4J.maxFollow(n)` 显式设置的值。
2.  **中等优先级**：`application.yml` 配置文件中的 `whois4j.timeout` 或 `whois4j.max-follow` 属性。
3.  **最低优先级**：系统默认值（超时 15000ms，追溯 2 层）。

#### 2. 代理配置
1.  **最高优先级**：代码中通过 `Whois4J.proxy(host, port)` 显式设置的代理。
2.  **中等优先级**：`application.yml` 配置文件中的 `whois4j.proxy` 属性。
3.  **最低优先级**：无代理直连模式（默认）。
    *注意：代码层面的代理配置将完全覆盖 yml 中的全局设置，适用于针对特定请求使用专用通道的场景。*

#### 3. Moz 凭据
1.  **最高优先级**：代码中通过 `Whois4J.moz(id, key)` 显式注入的凭据。
2.  **次高优先级**：`application.yml` 配置文件中的 `whois4j.moz` 属性。
3.  **第三优先级**：操作系统环境变量 `MOZ_ACCESS_ID` 与 `MOZ_SECRET_KEY`。
4.  **默认行为**：若以上来源均未提供有效凭据，相关 SEO 字段将默认返回 `-1`。

## 💻 使用指南

### 1. 极简一键查询 (外观模式)
支持极其直观的流式语法，无需繁琐的初始化：

```java
import io.github.tinolab.whois4j.Whois4J;
import io.github.tinolab.whois4j.core.model.WhoisResponse;

// [方式 A] 最简查询：直接使用默认或 yml 配置
WhoisResponse res = Whois4J.lookup("google.com");

// [方式 B] 快捷代理查询：一行代码搞定
WhoisResponse resProxy = Whois4J.proxy("127.0.0.1", 1080).lookup("测试.com");

// [方式 C] 完整流式链：自由组合多种参数
WhoisResponse resFull = Whois4J
    .timeout(5000)
    .maxFollow(1) // 仅追溯一层，不跟随注册商重定向
    .proxy("127.0.0.1", 1080)
    .moz("myAccessId", "mySecretKey")
    .lookup("example.ai");
```

### 2. Spring Boot 自动注入 (标准模式)
在 Spring 环境中，**推荐直接注入 `WhoisService`**。该实例是单例的，会自动加载并应用 `yml` 中的全局设置，包括 **Redis 自动缓存** 逻辑。

> **💡 设计建议**：注入模式适用于 95% 的标准业务场景，它确保了全系统查询行为的确定性与稳定性。如果您需要针对某个特定请求临时微调参数（如临时更换代理），请使用前文提到的 `Whois4J` 流式调用模式。

```java
@Autowired
private WhoisService whoisService;

public void demo(String target) {
    
    // 内部封装了高性能并行查询、数据增强及 Redis 缓存逻辑
    WhoisResponse response = whoisService.lookup(target);
    
    if (response.isCached()) {
        System.out.println("数据来自本地缓存");
    }
}
```

### 3. 开箱即用的 Web API (自动 API 模式)
如果您开启了 `whois4j.api.enabled: true`，系统会自动注册以下端点：

- **端点路径**：`/api/lookup`
- **请求方式**：`GET`
- **请求参数**：`query` (域名、IP 或 ASN)
- **特性**：
    - 自动关联 Redis 缓存。
    - 自动下发 `Cache-Control` 响应头。
    - 失败时自动返回标准的 HTTP 错误状态码。

## 🛠️ 数据模型详解

### 1. WhoisResponse (响应包装对象)
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `status` | `boolean` | **业务成功标志**。`true` 表示成功获取并解析了有效数据。 |
| `time` | `double` | **查询总耗时**。单位为秒，精确到毫秒。若命中缓存则返回 `0.0`。 |
| `source` | `String` | **主数据源**。取值为 `rdap` (结构化) 或 `whois` (文本解析)。 |
| `cached` | `boolean` | **缓存标记**。`true` 表示本次数据来自服务端 Redis 缓存。 |
| `error` | `String` | **错误信息**。失败时返回原因（如 `No match`, `Timeout` 等）。 |
| `data` | `WhoisAnalyzeResult` | **核心业务数据**。包含所有解析后的属性。 |

### 2. WhoisAnalyzeResult (解析结果标准对象)

#### 🌐 基础注册信息
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `domain` | `String` | **标准化资产名称**。归一化后的域名、IP 或 ASN。 |
| `registrar` | `String` | **注册商名称**。例如 `GoDaddy.com, LLC` |
| `registrarURL` | `String` | **注册商官网**。通常从 RDAP 数据中提取。 |
| `ianaId` | `String` | **IANA 编号**。注册商唯一识别码（N/A 表示未知）。 |
| `whoisServer` | `String` | **权威源**。本次查询所使用的权威服务器地址。 |
| `dnssec` | `String` | **DNSSEC 状态**。通常返回 `signedDelegation` 或 `unsigned`。 |

#### 🏢 品牌识别信息
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `registrarIcon` | `String` | **注册商图标标识**。可用于前端图标路径匹配。 |
| `registrarColor` | `String` | **注册商主题色**。例如 `#1BDBDB`。 |
| `nsBrand` | `String` | **名称服务器品牌**。例如 `Cloudflare` 或 `AWS`。 |
| `nsIcon` | `String` | **NS 品牌图标标识**。 |
| `nsColor` | `String` | **NS 品牌主题色**。 |

#### 📅 时间节点
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `creationDate` | `String` | **注册日期**。标准化为 ISO 8601 格式。 |
| `updatedDate` | `String` | **最后更新日期**。标准化为 ISO 8601 格式。 |
| `expirationDate` | `String` | **到期日期**。标准化为 ISO 8601 格式。 |
| `domainAge` | `Integer` | **域名年龄**。自动计算的已注册年数。 |
| `remainingDays` | `Integer` | **剩余天数**。距离域名到期还有多少天。 |

#### 🛡️ 状态与服务器
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `status` | `List<DomainStatus>` | **EPP 状态列表**。包含状态码原文及解释链接。详见下文子模型说明。 |
| `nameServers` | `List<String>` | **DNS 服务器列表**。清洗后的名称服务器。 |

#### 👤 注册人信息
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `registrantOrganization` | `String` | 注册组织/公司名称。 |
| `registrantProvince` | `String` | 注册人所在省份/城市。 |
| `registrantCountry` | `String` | 注册人所属国家。 |
| `registrantPhone` | `String` | 注册人联系电话。 |
| `registrantEmail` | `String` | 注册人联系邮箱。 |

#### 🏷️ SEO 指标与价格 (增强)
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `mozDomainAuthority` | `int` | **DA (域名权重)**。0-100 分。 |
| `mozPageAuthority` | `int` | **PA (页面权重)**。0-100 分。 |
| `mozSpamScore` | `int` | **垃圾邮件得分**。0-100 分。 |
| `registerPrice` | `DomainPricing` | **新注册价格参考**。详见下文子模型说明。 |
| `renewPrice` | `DomainPricing` | **续费价格参考**。 |
| `transferPrice` | `DomainPricing` | **转入价格参考**。 |

#### 🛰️ 网络字段 (IP/ASN 查询特有)
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `cidr` | `String` | 无类别域间路由（如 1.1.1.0/24）。 |
| `netRange` | `String` | 起始 IP 范围（如 1.1.1.0 - 1.1.1.255）。 |
| `inetNum` | `String` | 起始 IPv4 地址。 |
| `inet6Num` | `String` | 起始 IPv6 地址。 |
| `originAS` | `String` | 自治系统编号（如 AS13335）。 |
| `netName` | `String` | 网络名称（ISP 定义）。 |
| `netType` | `String` | 网络类型（如 Direct Allocation）。 |

#### 📄 原始报文
| 属性 | 类型 | 说明 |
| :--- | :--- | :--- |
| `rawWhoisContent` | `String` | **WHOIS 原始文本**。来自 TCP 43 端口的完整原始响应。 |
| `rawRdapContent` | `String` | **RDAP 原始 JSON**。来自 HTTP 接口的完整结构化响应。 |

### 📦 嵌套子模型说明

#### DomainStatus (状态详情)
- `status`: 原始 EPP 状态码（如 `clientTransferProhibited`）。
- `url`: 指向 ICANN 官方解释该状态的权威链接。

#### DomainPricing (定价详情)
- `registrar`: 价格来源注册商 ID。
- `registrarname`: 注册商显示名称。
- `registrarweb`: 注册商官网链接。
- `newPrice` / `renewPrice` / `transferPrice`: 价格数值（可能是 `Double` 或字符串 `"n/a"`）。
- `currency`: 币种简写（如 `USD`, `CNY`）。
- `updatedtime`: 价格数据最后更新时间。
- `premium`: `boolean`。是否为溢价/高价域名。
- `externalLink`: 该价格在第三方平台的实时来源详情页链接。

## 💡 原始报文与解析设计哲学

在使用 `Whois4J` 时，解析结果（`data`）与原始报文（`rawWhoisContent`）之间的表现差异源于本库核心的 **“数据保真”** 与 **“确定性解析”** 设计原则。

### 1. 物理原始数据 vs. 合成虚拟数据
这是理解差异的关键。目前行业内存在两种完全不同的展示策略：
- **物理原始数据**：`Whois4J` 坚持 **“非物理原始、不回显”** 的工程底线。`rawWhoisContent` 字段仅存放从物理 Socket 链路中抓取到的真实原始字节流，不进行任何模拟美化或内容拼接。
- **合成虚拟报文**：许多 Web 查询工具在无法获取原始报文时，会利用已有的 RDAP 数据进行“逆向合成”并带有 `NOTICE` 标记。这本质上是 UI 层的视觉补偿，并非服务器返回的真实报文。

### 2. 非结构化数据的冲突处理原则
在处理非结构化的 WHOIS 文本报文时，若针对同一字段返回了多组值（例如某些注册局会同时列出简繁体域名、或多组不同格式的标识），本库遵循 **“末尾覆盖”** 策略：
- **确定性行为**：解析引擎在循环匹配过程中，将保留报文中最末端出现的有效行，以确保输出结构的唯一性与逻辑的确定性。
- **全量审计**：若业务上需要获取报文中提及的所有变体信息，建议以 `rawWhoisContent` 中的原始文本作为最终审计依据。

### 3. 为什么有些查询的原始报文为空？
当 `source` 为 `rdap` 且 `rawWhoisContent` 为空时，通常由以下技术原因导致：
- **并行竞争机制**：本库同时发起双协议请求。若 RDAP 路径率先返回了高质量数据，而 WHOIS 链路因网络波动或限流在超时阈值内（15s）未响应，系统将优先交付业务数据，不再无限期挂起等待。
- **协议重心迁移**：许多现代顶级域（如 `.app`, `.dev`）已将 RDAP 作为首选协议，其传统的 WHOIS 服务器可能响应极慢甚至已停止服务。

### 4. 数据完整性保障
请放心，**原始报文的缺失并不代表数据的缺失**。即使 `rawWhoisContent` 为空，结构化字段（如 `registrar`, `expirationDate` 等）依然是完整的，这些数据是通过高度可靠的 RDAP 权威源获取并自动映射的。

### 5. 开发者集成策略建议
为了在 UI 层达到最佳展示效果，建议集成方采取以下方案：
- **语义层**：直接绑定 `data` 对象中的属性，这是经过归一化处理的最准确数据。
- **表现层**：将 `rawWhoisContent` 作为“原始证据”供专业用户查阅。
- **补偿层**：若原始报文为空，可根据 `data` 中的字段自行编写展示模板进行视觉补白。

## 🛡️ 核心鲁棒性设计

- **路由自动修正**: 自动识别并修正部分注册局返回的非标服务器地址。
- **错误精准识别**: 内置强大的模式识别逻辑，能够精准区分“域名未注册”与“网络查询异常”。
- **多级结果融合**: 智能合并来自不同源的数据片段，确保最终输出的是全网最完整的信息快照。

## 📜 版权声明

本项目为闭源软件，所有权利保留。未经许可，禁止任何形式的传播、修改或商业使用。

## 🤝 贡献与反馈

如果您发现某个特定后缀的解析存在偏差，欢迎提交 Issue。我们致力于维护 Java 生态中最强大的域名解析引擎。

**Whois4J - 为 Java 开发者打造的专业级资产探测引擎。**