![Logo](./docs/logo.png)

# Whois4J

<br>

<p align="center">
  <a href="./README.md"><img src="https://img.shields.io/badge/Doc.-English-e2ca8a?style=flat-square&logo=read-the-docs&logoColor=white" alt="English" /></a>
  <a href="./docs/README_CN.md"><img src="https://img.shields.io/badge/文档-简体中文-lightgrey?style=flat-square&logo=read-the-docs&logoColor=white" alt="简体中文" /></a>
</p>

<div align="center">
  <a href="./LICENSE"><img src="https://img.shields.io/badge/License-Proprietary-4eb1ba" alt="License"></a>
  <a href="https://www.oracle.com/java"><img src="https://img.shields.io/badge/Java-8%2f11%2f17+-b07219" alt="Java Support"></a>
  <a href="https://spring.io/projects/spring-boot"><img src="https://img.shields.io/badge/Spring%20Boot-2.x%2f3.x-6db33f" alt="Spring Boot"></a>
  <a href="https://central.sonatype.com/search?q=g:io.github.tinolab.whois4j"><img src="https://img.shields.io/maven-central/v/io.github.tinolab.whois4j/whois4j-parent?label=Maven%20Central" alt="Maven Central"></a>
</div>

<br>

**Whois4J** is a modern, high-performance, full-featured Java WHOIS/RDAP query and parsing library. It provides extremely accurate, structured domain and network asset information retrieval capabilities for the Java ecosystem through an original dual-protocol parallel scheduling mechanism and a deep text parsing engine.

## 🚀 Core Features

- **Dual-Protocol Strategy**: Simultaneously initiates traditional WHOIS (TCP 43) and modern RDAP (HTTP/JSON) requests, utilizing `CompletableFuture` for non-blocking concurrency and performing intelligent data merging to ensure information integrity.
- **Robust Date Parsing**: Built-in high-fault-tolerant parsing engine for various date formats in WHOIS messages (e.g., `Oct-2023`, `2023.10.27`, `2023-10-27 10:00:00`), ensuring final output as unified ISO 8601 standard date strings.
- **Global RDAP Bootstrap**: Strictly follows IANA official RDAP bootstrap specifications, dynamically loading service mapping tables for IPv4/IPv6 and ASN to ensure precise routing queries for network assets in all global regions (ARIN, RIPE, APNIC, LACNIC, AFRINIC), completely eliminating hard-coded interfaces.
- **Multi-dimensional Pricing Reference**: Automatically integrates third-party domain market interfaces (e.g., Nazhumi) to provide real-time price references and currency conversion for new registration, renewal, and transfer, supporting automatic identification of high-priced premium assets.
- **Registrar and Name Server (NS) Branding**: Built-in massive brand database supporting automatic identification of hundreds of global mainstream registrars (e.g., GoDaddy, Namecheap) and nameserver providers (e.g., Cloudflare, AWS Route 53), providing corresponding brand icon paths and theme colors.
- **High-performance Server-side Caching**: Native integration of Redis caching mechanism, supporting configurable time-to-live (TTL) and cache key prefixes, and automatically issuing `Cache-Control` response headers (supporting `stale-while-revalidate`) to control client and CDN caching behavior, significantly improving response speed for repeated queries and reducing pressure on authoritative servers.
- **IDN Internationalized Domain Name Support**: Native support for automatic transcoding (Punycode) queries for non-ASCII domains such as Chinese characters, supporting domains like `测试.com` or `google.中国`.
- **Authoritative Referral Follow**: Built-in intelligent routing tracking logic. It can automatically identify the Registry Server of top-level domains from IANA root servers and automatically redirect to specific Registrar servers.
- **Full Asset Support**: Not only supports domain queries but also provides full information retrieval for **IPv4/IPv6 CIDR** blocks and **ASN**.
- **Deep RDAP Mapping**: Deeply parses complex RDAP nested JSON structures, supporting deep extraction of `vCard` arrays and precise parsing of `events` timelines.
- **EPP Status Dictionary**: Complete built-in ICANN standard EPP status library, providing status meanings, classifications (OK/Grace/Redemption, etc.), and professional UI color display suggestions.
- **Industrial Logging Standard**: Built on the **SLF4J** interface, seamlessly integrating with mainstream logging frameworks like Logback and Log4j2.

## 🏗️ Module Architecture

The project adopts a Maven multi-module structure to ensure complete decoupling of core logic and framework adapters:

- `whois4j-core`: **Core logic library**. Pure Java implementation, including protocol communication, parsing engine, routing dictionary, brand identification, and IDN support.
- `whois4j-spring-boot-starter`: **Spring Boot 2.x adapter**. Supports JDK 8+, compatible with traditional `spring.factories` auto-configuration.
- `whois4j-spring-boot-3-starter`: **Spring Boot 3.x adapter**. Supports JDK 17+, adopting the new `AutoConfiguration.imports` specification.

## 📦 Installation

### 1. Spring Boot Project (Recommended)

Choose the corresponding Starter based on your Spring Boot version:

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

### 2. Standard Java Project

Include only the core logic library:

```xml
<!-- Whois4J Core -->
<dependency>
    <groupId>io.github.tinolab.whois4j</groupId>
    <artifactId>whois4j-core</artifactId>
    <version>1.0.0</version>
</dependency>
```

## ⚙️ Configuration Details

`Whois4J` follows the principle of "Convention over Configuration." In a Spring Boot environment, you can configure it via `application.yml` (all items are optional; the system will automatically apply default values):

```yaml
# ====================================================================
# Whois4J Global Configuration
# ====================================================================
whois4j:
  # [Basic Query Configuration]
  timeout: 15000         # Global query timeout (in milliseconds), default is 15000ms
  max-follow: 2          # Maximum follow depth for WHOIS queries (1-2), default is 2
  
  # [Network Proxy Configuration]
  # Recommended for intranet or restricted network environments to access overseas TCP 43 and RDAP interfaces
  proxy:
    host: 127.0.0.1      # SOCKS5 proxy server address
    port: 1080           # SOCKS5 proxy server port

  # [Enhanced Feature Credentials]
  # Configured to activate automatic crawling of SEO weights like data.mozDomainAuthority
  moz:
    access-id: ""        # Moz Access ID
    secret-key: ""       # Moz Secret Key

  # [Server-side Cache Configuration]
  # When enabled, Redis will be used to cache query results; requires spring-data-redis in the project
  cache:
    enabled: true        # Whether to enable caching, default is false
    prefix: "whois:"     # Cache key prefix
    ttl: 3600            # Cache expiration time (in seconds)

  # [Automatic API Interface Configuration]
  # When enabled, a standard REST API query interface will be automatically registered
  api:
    enabled: true        # Whether to enable automatic interface, default is false
```

### 💡 Configuration Priority Description

The system merges configurations during runtime according to the following strict priority order, ensuring flexibility while providing reliable default behavior:

#### 1. Timeout & Follow
1.  **Highest Priority**: Values explicitly set in code via `Whois4J.timeout(ms)` or `Whois4J.maxFollow(n)`.
2.  **Medium Priority**: `whois4j.timeout` or `whois4j.max-follow` properties in the `application.yml` configuration file.
3.  **Lowest Priority**: System default values (timeout 15000ms, follow 2 layers).

#### 2. Proxy Configuration
1.  **Highest Priority**: Proxies explicitly set in code via `Whois4J.proxy(host, port)`.
2.  **Medium Priority**: `whois4j.proxy` properties in the `application.yml` configuration file.
3.  **Lowest Priority**: No-proxy direct connection mode (default).
    *Note: Code-level proxy configuration will completely override global settings in yml, suitable for scenarios using dedicated channels for specific requests.*

#### 3. Moz Credentials
1.  **Highest Priority**: Credentials explicitly injected in code via `Whois4J.moz(id, key)`.
2.  **Second Priority**: `whois4j.moz` properties in the `application.yml` configuration file.
3.  **Third Priority**: OS environment variables `MOZ_ACCESS_ID` and `MOZ_SECRET_KEY`.
4.  **Default Behavior**: If no valid credentials are provided from the above sources, related SEO fields will return `-1` by default.

## 💻 Usage Guide

### 1. Simple One-click Query (Facade Mode)
Supports extremely intuitive fluent syntax without tedious initialization:

```java
import io.github.tinolab.whois4j.Whois4J;
import io.github.tinolab.whois4j.core.model.WhoisResponse;

// [Option A] Simplest query: Directly use default or yml configuration
WhoisResponse res = Whois4J.lookup("google.com");

// [Option B] Quick proxy query: Done in one line
WhoisResponse resProxy = Whois4J.proxy("127.0.0.1", 1080).lookup("测试.com");

// [Option C] Full fluent chain: Freely combine multiple parameters
WhoisResponse resFull = Whois4J
    .timeout(5000)
    .maxFollow(1) // Only follow one layer, don't follow registrar redirects
    .proxy("127.0.0.1", 1080)
    .moz("myAccessId", "mySecretKey")
    .lookup("example.ai");
```

### 2. Spring Boot Auto-injection (Standard Mode)
In a Spring environment, **it is recommended to directly inject `WhoisService`**. This instance is a Singleton, automatically loading and applying global settings from `yml`, including **Redis auto-caching** logic.

> **💡 Design Suggestion**: The injection mode is suitable for 95% of standard business scenarios, ensuring determinism and stability of query behavior across the entire system. If you need to temporarily tweak parameters for a specific request (e.g., temporarily changing a proxy), please use the `Whois4J` fluent call mode mentioned earlier.

```java
@Autowired
private WhoisService whoisService;

public void demo(String target) {
    
    // Encapsulates high-performance parallel query, data enhancement, and Redis caching logic
    WhoisResponse response = whoisService.lookup(target);
    
    if (response.isCached()) {
        System.out.println("Data from local cache");
    }
}
```

### 3. Out-of-the-box Web API (Auto-API Mode)
If you have enabled `whois4j.api.enabled: true`, the system will automatically register the following endpoint:

- **Endpoint Path**: `/api/lookup`
- **Request Method**: `GET`
- **Request Parameter**: `query` (Domain, IP, or ASN)
- **Features**:
    - Automatically associated with Redis cache.
    - Automatically issues `Cache-Control` response headers.
    - Automatically returns standard HTTP error status codes on failure.

## 🛠️ Data Model Details

### 1. WhoisResponse (Response Wrapper Object)
| Property | Type | Description |
| :--- | :--- | :--- |
| `status` | `boolean` | **Business success flag**. `true` means valid data was successfully retrieved and parsed. |
| `time` | `double` | **Total query time**. In seconds, accurate to milliseconds. Returns `0.0` if cache hit. |
| `source` | `String` | **Main data source**. Value is `rdap` (structured) or `whois` (text parsing). |
| `cached` | `boolean` | **Cache flag**. `true` means data is from server-side Redis cache. |
| `error` | `String` | **Error message**. Returns reason on failure (e.g., `No match`, `Timeout`). |
| `data` | `WhoisAnalyzeResult` | **Core business data**. Contains all parsed properties. |

### 2. WhoisAnalyzeResult (Standard Object for Parsed Results)

#### 🌐 Basic Registration Information
| Property | Type | Description |
| :--- | :--- | :--- |
| `domain` | `String` | **Standardized asset name**. Normalized domain, IP, or ASN. |
| `registrar` | `String` | **Registrar name**. e.g., `GoDaddy.com, LLC` |
| `registrarURL` | `String` | **Registrar website**. Usually extracted from RDAP data. |
| `ianaId` | `String` | **IANA ID**. Registrar unique identifier (N/A means unknown). |
| `whoisServer` | `String` | **Authoritative source**. Authoritative server address used for this query. |
| `dnssec` | `String` | **DNSSEC status**. Usually returns `signedDelegation` or `unsigned`. |

#### 🏢 Branding Information
| Property | Type | Description |
| :--- | :--- | :--- |
| `registrarIcon` | `String` | **Registrar icon identifier**. Used for frontend icon path matching. |
| `registrarColor` | `String` | **Registrar theme color**. e.g., `#1BDBDB`. |
| `nsBrand` | `String` | **Name Server brand**. e.g., `Cloudflare` or `AWS`. |
| `nsIcon` | `String` | **NS brand icon identifier**. |
| `nsColor` | `String` | **NS brand theme color**. |

#### 📅 Dates
| Property | Type | Description |
| :--- | :--- | :--- |
| `creationDate` | `String` | **Creation date**. Standardized to ISO 8601 format. |
| `updatedDate` | `String` | **Last updated date**. Standardized to ISO 8601 format. |
| `expirationDate` | `String` | **Expiration date**. Standardized to ISO 8601 format. |
| `domainAge` | `Integer` | **Domain age**. Automatically calculated registered years. |
| `remainingDays` | `Integer` | **Remaining days**. Days until domain expiration. |

#### 🛡️ Status & Name Servers
| Property | Type | Description |
| :--- | :--- | :--- |
| `status` | `List<DomainStatus>` | **EPP status list**. Contains status code original text and explanation links. |
| `nameServers` | `List<String>` | **DNS server list**. Cleaned name servers. |

#### 👤 Registrant Information
| Property | Type | Description |
| :--- | :--- | :--- |
| `registrantOrganization` | `String` | Organization/Company name. |
| `registrantProvince` | `String` | Registrant province/city. |
| `registrantCountry` | `String` | Registrant country. |
| `registrantPhone` | `String` | Registrant phone number. |
| `registrantEmail` | `String` | Registrant email address. |

#### 🏷️ SEO Metrics & Pricing (Enhanced)
| Property | Type | Description |
| :--- | :--- | :--- |
| `mozDomainAuthority` | `int` | **DA (Domain Authority)**. 0-100 score. |
| `mozPageAuthority` | `int` | **PA (Page Authority)**. 0-100 score. |
| `mozSpamScore` | `int` | **Spam Score**. 0-100 score. |
| `registerPrice` | `DomainPricing` | **New registration price reference**. See nested model below. |
| `renewPrice` | `DomainPricing` | **Renewal price reference**. |
| `transferPrice` | `DomainPricing` | **Transfer price reference**. |

#### 🛰️ Network Info (Specific to IP/ASN Queries)
| Property | Type | Description |
| :--- | :--- | :--- |
| `cidr` | `String` | Classless Inter-Domain Routing (e.g., 1.1.1.0/24). |
| `netRange` | `String` | IP range (e.g., 1.1.1.0 - 1.1.1.255). |
| `inetNum` | `String` | Starting IPv4 address. |
| `inet6Num` | `String` | Starting IPv6 address. |
| `originAS` | `String` | Autonomous System Number (e.g., AS13335). |
| `netName` | `String` | Network name (defined by ISP). |
| `netType` | `String` | Network type (e.g., Direct Allocation). |

#### 📄 Raw Content
| Property | Type | Description |
| :--- | :--- | :--- |
| `rawWhoisContent` | `String` | **WHOIS Raw Text**. Full original response from TCP 43 port. |
| `rawRdapContent` | `String` | **RDAP Raw JSON**. Full structured response from HTTP interface. |

### 📦 Nested Sub-model Descriptions

#### DomainStatus (Status Details)
- `status`: Original EPP status code (e.g., `clientTransferProhibited`).
- `url`: Authoritative link to ICANN's official explanation of the status.

#### DomainPricing (Pricing Details)
- `registrar`: Price source registrar ID.
- `registrarname`: Registrar display name.
- `registrarweb`: Registrar website link.
- `newPrice` / `renewPrice` / `transferPrice`: Price value (can be `Double` or string `"n/a"`).
- `currency`: Currency abbreviation (e.g., `USD`, `CNY`).
- `updatedtime`: Last updated time of price data.
- `premium`: `boolean`. Whether it's a premium/high-priced domain.
- `externalLink`: Detailed source link for the price on third-party platforms.

## 💡 Raw Content and Parsing Design Philosophy

When using `Whois4J`, differences between parsing results (`data`) and raw content (`rawWhoisContent`) stem from the core design principles of **"Data Fidelity"** and **"Deterministic Parsing."**

### 1. Physical Raw Data vs. Synthesized Virtual Data
This is key to understanding the difference. Currently, two different display strategies exist in the industry:
- **Physical Raw Data**: `Whois4J` adheres to the engineering bottom line of **"No physical raw, no echo."** The `rawWhoisContent` field only stores the real original byte stream captured from physical socket links, without any simulated beautification or content splicing.
- **Synthesized Virtual Messages**: Many web query tools "reverse-synthesize" RDAP data into messages with a `NOTICE` tag when raw messages are unavailable. This is essentially visual compensation at the UI layer, not the actual message returned by the server.

### 2. Conflict Handling Principles for Unstructured Data
When processing unstructured WHOIS text messages, if multiple sets of values are returned for the same field (e.g., some registries list both Simplified and Traditional Chinese domains, or multiple sets of identifiers in different formats), this library follows a **"Last-Win"** strategy:
- **Deterministic Behavior**: During the loop matching process, the parsing engine retains the last valid line appearing in the message to ensure uniqueness and logical determinism of the output structure.
- **Full Audit**: If business requirements necessitate obtaining all variants mentioned in the message, it is recommended to use the original text in `rawWhoisContent` as the final audit basis.

### 3. Why is the raw content of some queries empty?
When `source` is `rdap` and `rawWhoisContent` is empty, it is usually caused by the following technical reasons:
- **Parallel Competition Mechanism**: This library initiates dual-protocol requests simultaneously. If the RDAP path returns high-quality data first, while the WHOIS link fails to respond within the timeout threshold (15s) due to network fluctuations or rate limiting, the system delivers business data first and no longer hangs indefinitely.
- **Protocol Shift**: Many modern TLDs (e.g., `.app`, `.dev`) have adopted RDAP as their primary protocol; their traditional WHOIS servers may respond extremely slowly or even stop service.

### 4. Data Integrity Guarantee
Rest assured, **the absence of raw content does not mean the absence of data**. Even if `rawWhoisContent` is empty, structured fields (such as `registrar`, `expirationDate`, etc.) are still complete, as these data are retrieved through highly reliable RDAP authoritative sources and automatically mapped.

### 5. Developer Integration Strategy Suggestions
For optimal display effects at the UI layer, it is recommended that integrators adopt the following solutions:
- **Semantic Layer**: Bind directly to properties in the `data` object; this is the most accurate normalized data.
- **Presentation Layer**: Use `rawWhoisContent` as "raw evidence" for professional users.
- **Compensation Layer**: If raw content is empty, you can write your own display templates based on fields in `data` for visual filling.

## 🛡️ Core Robustness Design

- **Route Auto-correction**: Automatically identifies and corrects non-standard server addresses returned by some registries.
- **Precise Error Identification**: Built-in powerful pattern recognition logic to accurately distinguish between "domain not registered" and "network query exception."
- **Multi-level Result Fusion**: Intelligently merges data fragments from different sources to ensure the final output is the most complete information snapshot on the entire network.

## 📜 Copyright Notice

This project is closed-source software, all rights reserved. Any form of dissemination, modification, or commercial use is prohibited without permission.

## 🤝 Contribution and Feedback

If you find any deviations in the parsing of a specific suffix, please submit an Issue. We are committed to maintaining the most powerful domain name parsing engine in the Java ecosystem.

**Whois4J - A professional-grade asset detection engine for Java developers.**