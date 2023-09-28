Internet Engineering Task Force (IETF) R. Fielding, Ed.
Request for Comments: 9110 Adobe
STD: 97 M. Nottingham, Ed.
Obsoletes: 2818, 7230, 7231, 7232, 7233, 7235, Fastly
7538, 7615, 7694 J. Reschke, Ed.
Updates: 3864 greenbytes
Category: Standards Track June 2022
ISSN: 2070-1721

Translated by [Lifthus](https://github.com/lifthus)

# HTTP Semantics

## 요약

Hypertext Transfer Protocol (HTTP)는 분산형, 협업형, 하이퍼텍스트 정보 시스템들을 위한 무상태성을 띄는 애플리케이션 레벨의 프로토콜이다. 이 문서는 HTTP의 전반적인 아키텍처를 기술하고, 공통적인 용어를 확립하며, 프로토콜의 모든 버전에서 공유되는 면들을 정의한다. 이 정의에는 핵심 프로토콜 요소들, 확장성 메커니즘, 그리고 "http"와 "https" Uniform Resource Identifier (URI) scheme이 포함된다.

이 문서는 RFC 3864를 갱신하고 RFC 2818, 7231, 7232,
7233, 7235, 7538, 7615, 7694 문서 전체와 7230 문서의 일부를 폐기한다.

### Status of This Memo

This is an Internet Standards Track document.

This document is a product of the Internet Engineering Task Force
(IETF). It represents the consensus of the IETF community. It has
received public review and has been approved for publication by the
Internet Engineering Steering Group (IESG). Further information on
Internet Standards is available in Section 2 of RFC 7841.

Information about the current status of this document, any errata,
and how to provide feedback on it may be obtained at
https://www.rfc-editor.org/info/rfc9110.

### Copyright Notice

Copyright (c) 2022 IETF Trust and the persons identified as the
document authors. All rights reserved.

This document is subject to BCP 78 and the IETF Trust's Legal
Provisions Relating to IETF Documents
(https://trustee.ietf.org/license-info) in effect on the date of
publication of this document. Please review these documents
carefully, as they describe your rights and restrictions with respect
to this document. Code Components extracted from this document must
include Revised BSD License text as described in Section 4.e of the
Trust Legal Provisions and are provided without warranty as described
in the Revised BSD License.

This document may contain material from IETF Documents or IETF
Contributions published or made publicly available before November
10, 2008. The person(s) controlling the copyright in some of this
material may not have granted the IETF Trust the right to allow
modifications of such material outside the IETF Standards Process.
Without obtaining an adequate license from the person(s) controlling
the copyright in such materials, this document may not be modified
outside the IETF Standards Process, and derivative works of it may
not be created outside the IETF Standards Process, except to format
it for publication as an RFC or to translate it into languages other
than English.

## 목차

[1. 소개](#1-소개)

- [1.1. 목적](#11-목적)
- [1.2. 역사와 발전](#12-역사와-발전)
- [1.3. 핵심 의미체계](#13-핵심-의미체계)
- [1.4. 이 문서에 의해 폐기된 사양들](#14-이-문서에-의해-폐기된-사양들)

[2. 준수 사항](#2-준수-사항)

- [2.1. 문법 표기](#21-문법-표기)
- [2.2. 요구사항 표기](#22-요구사항-표기)
- [2.3. 길이 요구사항](#23-길이-요구사항)
- [2.4. 에러 핸들링](#24-에러-핸들링)
- [2.5. 프로토콜 버전](#25-프로토콜-버전)

[3. 용어와 핵심 개념들](#3-용어와-핵심-개념들)

- [3.1. 리소스](#31-리소스)
- [3.2. 표현](#32-표현)
- [3.3. 연결, 클라이언트, 그리고 서버](#33-연결-클라이언트-그리고-서버)
- [3.4. 메시지](#34-메시지)
- [3.5. 유저 에이전트](#35-유저-에이전트)
- [3.6. 오리진 서버](#36-오리진-서버)
- [3.7. 중개자](#37-중개자)
- [3.8. 캐시](#38-캐시)
- [3.9. 메시지 교환 예시](#39-메시지-교환-예시)

[4. HTTP의 식별자들](#4-http의-식별자들)

- [4.1. URI 레퍼런스](#41-uri-레퍼런스)
- [4.2. HTTP와 연관된 URI 체계들](#42-http와-연관된-uri-체계들)
- - [4.2.1. http URI 체계](#421-http-uri-체계)
- - [4.2.2. https URI 체계](#422-https-uri-체계)
- - [4.2.3. http(s) 정규화와 비교](#423-https-정규화와-비교)
- - [4.2.4. http(s) URI에서의 userinfo 지원 중단](#424-https-uri에서의-userinfo-지원-중단)
- - [4.2.5. 프래그먼트 식별자들과 http(s) 레퍼런스들](#425-프래그먼트-식별자들과-https-레퍼런스들)
- [4.3. 권한 있는 접근](#43-권한-있는-접근)
- - [4.3.1. URI 오리진](#431-uri-오리진)
- - [4.3.2. http 오리진들](#432-http-오리진들)
- - [4.3.3. https 오리진들](#433-https-오리진들)
- - [4.3.4. https 인증서 검증](#434-https-인증서-검증)
- - [4.3.5. IP-ID 참조 신원](#435-ip-id-참조-신원)

[5. 필드](#5-필드)

- [5.1. 필드 이름](#51-필드-이름)
- [5.2. 필드 라인들과 조합된 필드 값](#52-필드-라인들과-조합된-필드-값)
- [5.3. 필드 순서](#53-필드-순서)
- [5.4. 필드 제한](#54-필드-제한)
- [5.5. 필드 값들](#55-필드-값들)
- [5.6. 필드 값들을 정의하기 위한 공통 규칙들](#56-필드-값들을-정의하기-위한-공통-규칙들)
- - [5.6.1. Lists (#rule ABNF Extension)](#561-lists-rule-abnf-extension)
- - - [5.6.1.1. 발신자 요구사항들](#5611-발신자-요구사항들)
- - - [5.6.1.2. 수신자 요구사항들](#5612-수신자-요구사항들)
- - [5.6.2. 토큰](#562-토큰)
- - [5.6.3. 공백](#563-공백)
- - [5.6.4. 인용된 문자열](#564-인용된-문자열)
- - [5.6.5. 코멘트](#565-코멘트)
- - [5.6.6. 파라미터](#566-파라미터)
- - [5.6.7. Date/Time 포맷](#567-datetime-포맷)

[6. 메시지 추상화](#6-메시지-추상화)

- [6.1 프레이밍과 완전성](#61-프레이밍과-완전성)
- [6.2 제어 데이터](#62-제어-데이터)
- [6.3 헤더 필드](#63-헤더-필드)
- [6.4 콘텐츠](#64-콘텐츠)
- - [6.4.1 콘텐츠 의미체계](#641-콘텐츠-의미체계)
- - [6.4.2 콘텐츠 식별](#642-콘텐츠-식별)
- [6.5 트레일러 필드](#65-트레일러-필드)
- - [6.5.1 트레일러 사용에 대한 제한](#651-트레일러-사용에-대한-제한)
- - [6.5.2 트레일러 필드 처리](#652-트레일러-필드-처리)
- [6.6 메시지 메타데이터](#66-메시지-메타데이터)
- - [6.6.1 Date](#661-date)
- - [6.6.2 Trailer](#662-trailer)

[7. HTTP 메시지 라우팅](#7-http-메시지-라우팅)

- [7.1 타겟 리소스 결정](#71-타겟-리소스-결정)
- [7.2 Host와 :authority](#72-host와-authority)
- [7.3 인바운드 요청 라우팅](#73-인바운드-요청-라우팅)
- - [7.3.1 캐시로](#731-캐시로)
- - [7.3.2 프록시로](#732-프록시로)
- - [7.3.3 오리진으로](#733-오리진으로)
- [7.4 잘못 도달한 요청 거부](#74-잘못-도달한-요청-거부)
- [7.5 응답 연관짓기](#75-응답-연관짓기)
- [7.6 메시지 포워딩](#76-메시지-포워딩)
- - [7.6.1 Connection](#761-connection)
- - [7.6.2 Max-Forwards](#762-max-forwards)
- - [7.6.3 Via](#763-via)
- [7.7 메시지 변환](#77-메시지-변환)
- [7.8 Upgrade](#78-upgrade)

[8. 표현 데이터와 메타데이터](#8-표현-데이터와-메타데이터)

- [8.1. 표현 데이터](#81-표현-데이터)
- [8.2. 표현 메타데이터](#82-표현-메타데이터)
- [8.3. Content-Type](#83-content-type)
- - [8.3.1. Media Type](#831-media-type)
- - [8.3.2. Charset](#832-charset)
- - [8.3.3. Multipart Types](#833-multipart-types)
- [8.4. Content-Encoding](#84-content-encoding)
- - [8.4.1. Content Codings](#841-content-codings)
- - - [8.4.1.1. Compress Coding](#8411-compress-coding)
- - - [8.4.1.2. Deflate Coding](#8412-deflate-coding)
- - - [8.4.1.3. Gzip Coding](#8413-gzip-coding)
- [8.5. Content-Language](#85-content-language)
- - [8.5.1. 언어 태그들](#851-언어-태그들)
- [8.6. Content-Length](#86-content-length)
- [8.7. Content-Location](#87-content-location)
- [8.8. Validator 필드들](#88-validator-필드들)
- - [8.8.1. Weak versus Strong]()
- - [8.8.2. Last-Modified]()
- - - [8.8.2.1. Generation]()
- - -[ 8.8.2.2. Comparison]()
- - [8.8.3. ETag]()
- - - [8.8.3.1. Generation]()
- - - [8.8.3.2. Comparison]()
- - - [8.8.3.3. Example: Entity Tags Varying on Content-Negotiated Resources]()

9. Methods
   9.1. Overview
   9.2. Common Method Properties
   9.2.1. Safe Methods
   9.2.2. Idempotent Methods
   9.2.3. Methods and Caching
   9.3. Method Definitions
   9.3.1. GET
   9.3.2. HEAD
   9.3.3. POST
   9.3.4. PUT
   9.3.5. DELETE
   9.3.6. CONNECT
   9.3.7. OPTIONS
   9.3.8. TRACE 10. Message Context
   10.1. Request Context Fields
   10.1.1. Expect
   10.1.2. From
   10.1.3. Referer
   10.1.4. TE
   10.1.5. User-Agent
   10.2. Response Context Fields
   10.2.1. Allow
   10.2.2. Location
   10.2.3. Retry-After
   10.2.4. Server 11. HTTP Authentication
   11.1. Authentication Scheme
   11.2. Authentication Parameters
   11.3. Challenge and Response
   11.4. Credentials
   11.5. Establishing a Protection Space (Realm)
   11.6. Authenticating Users to Origin Servers
   11.6.1. WWW-Authenticate
   11.6.2. Authorization
   11.6.3. Authentication-Info
   11.7. Authenticating Clients to Proxies
   11.7.1. Proxy-Authenticate
   11.7.2. Proxy-Authorization
   11.7.3. Proxy-Authentication-Info 12. Content Negotiation
   12.1. Proactive Negotiation
   12.2. Reactive Negotiation
   12.3. Request Content Negotiation
   12.4. Content Negotiation Field Features
   12.4.1. Absence
   12.4.2. Quality Values
   12.4.3. Wildcard Values
   12.5. Content Negotiation Fields
   12.5.1. Accept
   12.5.2. Accept-Charset
   12.5.3. Accept-Encoding
   12.5.4. Accept-Language
   12.5.5. Vary 13. Conditional Requests
   13.1. Preconditions
   13.1.1. If-Match
   13.1.2. If-None-Match
   13.1.3. If-Modified-Since
   13.1.4. If-Unmodified-Since
   13.1.5. If-Range
   13.2. Evaluation of Preconditions
   13.2.1. When to Evaluate
   13.2.2. Precedence of Preconditions 14. Range Requests
   14.1. Range Units
   14.1.1. Range Specifiers
   14.1.2. Byte Ranges
   14.2. Range
   14.3. Accept-Ranges
   14.4. Content-Range
   14.5. Partial PUT
   14.6. Media Type multipart/byteranges
   
[15. 상태 코드](#15-상태-코드)

   - [15.1. 상태 코드의 개요](#151-상태-코드의-개요)
   - [15.2. Informational 1xx](#152-Informational-1xx)
   - - [15.2.1. 100 Continue](#1521-100-Continue)
   - - [15.2.2. 101 Switching Protocols](#1522-101-Switching-Protocols)
   - [15.3. Successful 2xx](#153-Successful-2xx)
   - - [15.3.1. 200 OK](#1531-200-OK)
   - - [15.3.2. 201 Created](#1532-201-Created)
   - - [15.3.3. 202 Accepted](#1533-202-Accepted)
   - - [15.3.4. 203 Non-Authoritative Information ](#1534-203-Non-Authoritative-Information )
   - - [15.3.5. 204 No Content ](#1535-204-No-Content)
   - - [15.3.6. 205 Reset Content](#1536-205-Reset-Content)
   - - [15.3.7. 206 Partial Content](#1537-206-Partial-Content)
   - - - [15.3.7.1. Single Part](#15371-Single-Part)
   - - - [15.3.7.2. Multiple Parts](#15372-Multiple-Parts)
   - - - [15.3.7.3. Combining Parts](#15373-Combining-Parts)
   - [15.4. Redirection 3xx](#154-Redirection-3xx)
   - - [15.4.1. 300 Multiple Choices](#1541-300-Multiple-Choices)
   - - [15.4.2. 301 Moved Permanently](#1542-301-Moved-Permanently)
   - - [15.4.3. 302 Found](#1543-Found)
   - - [15.4.4. 303 See Other](#1544-303-See-Other)
   - - [15.4.5. 304 Not Modified](#1545-304-Not-Modified)
   - - [15.4.6. 305 Use Proxy](#1546-305-Use-Proxy)
   - - [15.4.7. 306 (Unused)](#1547-306-(Unused))
   - - [15.4.8. 307 Temporary Redirect](#1548-307-Temporary-Redirect)
   - - [15.4.9. 308 Permanent Redirect](#1549-308-Permanent-Redirect)
   - [15.5. Client Error 4xx](#155-Client-Error-4xx)
   - - [15.5.1. 400 Bad Request](#1551-400-Bad-Request)
   - - [15.5.2. 401 Unauthorized](#1552-401-Unauthorized)
   - - [15.5.3. 402 Payment Required](#1553-402-Payment-Required)
   - - [15.5.4. 403 Forbidden](#1554-403-Forbidden)
   - - [15.5.5. 404 Not Found](#1555-404-Not-Found)
   - - [15.5.6. 405 Method Not Allowed](#1556-405-Method-Not-Allowed)
   - - [15.5.7. 406 Not Acceptable](#1557-406-Not-Acceptable)
   - - [15.5.8. 407 Proxy Authentication Required](#1558-407-Proxy-Authentication-Required)
   - - [15.5.9. 408 Request Timeout](#1559-408-Request-Timeout)
   - - [15.5.10. 409 Conflict](#15510-409-Conflict)
   - - [15.5.11. 410 Gone](#15511-410-Gone)
   - - [15.5.12. 411 Length Required](#15512-411-Length-Required)
   - - [15.5.13. 412 Precondition Failed](#15513-412-Precondition-Failed)
   - - [15.5.14. 413 Content Too Large](#15514-413-Content-Too-Large)
   - - [15.5.15. 414 URI Too Long](#15515-414-URI-Too-Long)
   - - [15.5.16. 415 Unsupported Media Type](#15516-415-Unsupported-Media-Type)
   - - [15.5.17. 416 Range Not Satisfiable](#15517-416-Range-Not-Satisfiable)
   - - [15.5.18. 417 Expectation Failed](#15518-417-Expectation-Failed)
   - - [15.5.19. 418 (Unused)](#15519-418-(Unused))
   - - [15.5.20. 421 Misdirected Request](#15520-421-Misdirected-Request)
   - - [15.5.21. 422 Unprocessable Content](#15521-422-Unprocessable-Content)
   - - [15.5.22. 426 Upgrade Required](#15522-426-Upgrade-Required)
   - [15.6. Server Error 5xx](#156-Server-Error-5xx)
   - - [15.6.1. 500 Internal Server Error](#1561-500-Internal-Server-Error)
   - - [15.6.2. 501 Not Implemented](#1562-501-Not-Implemented)
   - - [15.6.3. 502 Bad Gateway](#1563-502-Bad-Gateway)
   - - [15.6.4. 503 Service Unavailable](#1564-503-Service-Unavailable)
   - - [15.6.5. 504 Gateway Timeout](#1565-504-Gateway-Timeout)
   - - [15.6.6. 505 HTTP Version Not Supported](#1566-505-HTTP-Version-Not-Supported)
   
   16. Extending HTTP
   16.1. Method Extensibility
   16.1.1. Method Registry
   16.1.2. Considerations for New Methods
   16.2. Status Code Extensibility
   16.2.1. Status Code Registry
   16.2.2. Considerations for New Status Codes
   16.3. Field Extensibility
   16.3.1. Field Name Registry
   16.3.2. Considerations for New Fields
   16.3.2.1. Considerations for New Field Names
   16.3.2.2. Considerations for New Field Values
   16.4. Authentication Scheme Extensibility
   16.4.1. Authentication Scheme Registry
   16.4.2. Considerations for New Authentication Schemes
   16.5. Range Unit Extensibility
   16.5.1. Range Unit Registry
   16.5.2. Considerations for New Range Units
   16.6. Content Coding Extensibility
   16.6.1. Content Coding Registry
   16.6.2. Considerations for New Content Codings
   16.7. Upgrade Token Registry 17. Security Considerations
   17.1. Establishing Authority
   17.2. Risks of Intermediaries
   17.3. Attacks Based on File and Path Names
   17.4. Attacks Based on Command, Code, or Query Injection
   17.5. Attacks via Protocol Element Length
   17.6. Attacks Using Shared-Dictionary Compression
   17.7. Disclosure of Personal Information
   17.8. Privacy of Server Log Information
   17.9. Disclosure of Sensitive Information in URIs
   17.10. Application Handling of Field Names
   17.11. Disclosure of Fragment after Redirects
   17.12. Disclosure of Product Information
   17.13. Browser Fingerprinting
   17.14. Validator Retention
   17.15. Denial-of-Service Attacks Using Range
   17.16. Authentication Considerations
   17.16.1. Confidentiality of Credentials
   17.16.2. Credentials and Idle Clients
   17.16.3. Protection Spaces
   17.16.4. Additional Response Fields 18. IANA Considerations
   18.1. URI Scheme Registration
   18.2. Method Registration
   18.3. Status Code Registration
   18.4. Field Name Registration
   18.5. Authentication Scheme Registration
   18.6. Content Coding Registration
   18.7. Range Unit Registration
   18.8. Media Type Registration
   18.9. Port Registration
   18.10. Upgrade Token Registration

[19. 레퍼런스](#19-레퍼런스)

19.1. Normative References
19.2. Informative References
Appendix A. Collected ABNF
Appendix B. Changes from Previous RFCs

[B.1. RFC 2818로부터의 변화](#B1-RFC-2818로-부터의-변화)
[B.2. RFC 7230로부터의 변화](#B2-RFC-7230로-부터의-변화)
[B.3. RFC 7231로부터의 변화](#B3-RFC-7231로-부터의-변화)
[B.4. RFC 7232로부터의 변화](#B4-RFC-7232로-부터의-변화)
[B.5. RFC 7233로부터의 변화](#B5-RFC-7233로-부터의-변화)
[B.6. RFC 7235로부터의 변화](#B6-RFC-7235로-부터의-변화)
[B.7. RFC 7538로부터의 변화](#B7-RFC-7538로-부터의-변화)
[B.8. RFC 7615로부터의 변화](#B8-RFC-7615로-부터의-변화)
[B.9. RFC 7694로부터의 변화](#B9-RFC-7694로-부터의-변화)
Acknowledgements
Index
Authors' Addresses

## 1. 소개

### 1.1. 목적

Hypertext Transfer Protocol (HTTP)는 네트워크 기반 하이퍼텍스트 정보 시스템과 유연하게 상호작용할 수 있도록 일반적인 인터페이스, 확장 가능한 의미체계, 자기설명적 메시지를 가지는 무상태성, 애플리케이션-레벨, 요청/응답 프로토콜들 중 하나다.

HTTP는 균일한 인터페이스를 제시함으로써 제공되는 리소스의 타입과 관계 없는 클라이언트에게 서비스가 정확히 어떻게 구현됐는가를 숨긴다. 비슷하게, 서버는 각 클라이언트의 목적을 알 필요가 없다: 하나의 요청은 특정 타입의 클라이언트나 미리 결정된 애플리케이션이 진행되는 단계의 순서 같은 것과 관계없이 각각 별도로 고려될 수 있다. 이는 다른 많은 상황들에서 효율적으로 사용될 수 있는 범용적인 구현을 가능하게 하며, 상호작용의 복잡도를 줄이고, 시간이 지남에 따라 (각 엔드포인트가) 독립적으로 발전할 수 있도록 한다.

HTTP는 또한 중개 프로토콜로 사용될 수 있도록 설계됐는데, 이 경우 프록시나 게이트웨이는 HTTP가 아닌 정보 체계를 더 일반적인 인터페이스로 변환할 수 있다.

이 유연성의 한 결과로써 이 프로토콜은 인터페이스 뒤에서 무슨 일이 일어나냐를 가지고는 정의될 수 없게 됐다. 대신에, 우리가 할 수 있는 것은 통신의 문법, 수신된 통신의 의도, 그리고 수신하는 쪽의 예상되는 행동을 정의하는 것으로 제한된다. 각 통신이 별도로 고려된다면, 통신의 성공적인 어떤 행동은 서버가 제공하는 관측 가능한 인터페이스의 변화에 반영되어야 한다. 그러나, 한번에 여러 클라이언트들이 병렬적으로 행동하게될 수 있고 또 교차적인 목적을 띌 수 있기 때문에, 우리는 그러한 인터페이스의 변화가 단일 응답의 범위를 넘어서까지 관측 가능하도록 요구할 수는 없다.

### 1.2. 역사와 발전

HTTP는 1990년에 등장한 이후로 World Wide Web에서 주요한 정보 전송 프로토콜로 사용되고 있다. 처음에는 단 하나의 메소드 (GET)로 주어진 경로명에 의해 식별되며 해당 경로에 있을 것을 것으로 추정되는 하이퍼텍스트 문서의 전송을 요청하는 짧은 지연시간의 요청을 위한 단순한 메커니즘으로 시작됐다. 웹이 성장하면서, HTTP는 요청과 응답을 메시지에 포함시키고, MIME 같은 미디어 타입을 사용해 임의의 데이터 포맷을 전송하고, 중개자를 통해 요청을 라우팅할 수 있도록 확장됐다. 이러한 프로토콜들은 결국 HTTP/0.9와 HTTP/1.0으로 정의됐다. ([RFC1945](https://datatracker.ietf.org/doc/html/rfc1945) 참조)

HTTP/1.1은 원래의 텍스트 기반 메시징 구문과의 호환성을 유지하고, 상호운용성과 황장성, 인터넷에서의 견고함은 더 강화시키면서 위 프로토콜의 기능들을 다듬도록 설계됐다. 여기에는 고정된, 그리고 동적인 (어떤 단위로 묶인) 콘텐츠 데이터를 길이에 기반해 구분하는 것, 콘텐츠 협상을 위한 일관적인 프레임워크, 조건부 요청을 위한 불투명한 유효성 검사, 더 나은 캐시 일관성을 위한 캐시 제어, 부분 갱신을 위한 범위 요청, 그리고 기본적으로 지속되는 연결이 포함된다. HTTP/1.1은 1995년에 처음 소개되어 1997년에 표준 트랙(Standards Track)으로 발표됐고[[RFC2068](https://datatracker.ietf.org/doc/html/rfc2068)], 1999년에 개정됐으며[[RFC2616](https://datatracker.ietf.org/doc/html/rfc2616)], 2014년에 재개정됐다([[RFC7230](https://datatracker.ietf.org/doc/html/rfc7230)] 에서 [[RFC7235](https://datatracker.ietf.org/doc/html/rfc7235)])

HTTP/2([RFC7540](https://datatracker.ietf.org/doc/html/rfc7540))에서는 충분한 필드 압축 및 서버 푸시 기능과 함께 동시에 발생하는 HTTP 메시지들을 교환하기 위해 기존의 TLS와 TCP 프로토콜 위에 다중화된 세션 레이어가 도입됐다. HTTP/3([RFC9114](https://datatracker.ietf.org/doc/html/rfc9114))는 다중화된 보안 전송을 위해 TCP 대신 UDP 기반의 QUIC을 사용함으로써 동시 메시지들의 더욱 큰 독립성을 제공한다.

HTTP의 세가지 모든 major 버전들은 이 문서에서 정의된 의미체계에 의존한다. 이들은 사용하는 상황에 따라 각각 이점과 한계점이 있기 때문에 서로를 완전히 폐기하지는 않는다. 이들의 구현에 대해서는 각각의 특정한 상황에 따라 가장 적합한 전송 방식과 메시징 구문이 선택되기를 기대된다.

이 HTTP에 대한 개정은 각 major 프로토콜 버전들이 같은 핵심적인 의미체계를 공유하며 독립적으로 진행하도록 하기 위해 의미체계 정의 (이 문서)와 캐싱([RFC7234](https://datatracker.ietf.org/doc/html/rfc7234))을 현재 HTTP/1.1 메시징 구문([RFC7231](https://datatracker.ietf.org/doc/html/rfc7231))에서 분리한다.

### 1.3. 핵심 의미체계

HTTP는 -- 리소스의 타입, 성질이나 구현과 관계 없이 -- 리소스와의 상호작용을 위한 통일적인 인터페이스를 제공하고(3.1절), 이는 표현을 조작하거나 전송하는 메시지를 보내는 것으로 이루어진다(3.2절).

각 메시지는 요청 아니면 응답이다. 클라이언트는 의도를 전달하는 요청 메시지를 구성하고, 그 메시지들을 식별된 오리진 서버로 라우팅한다. 서버는 요청을 기다리며, 수신한 각 메시지를 파싱하고, 메시지의 의미체계를 식별된 타겟 리소스와 관련해 해석하며, 하나 이상의 응답 메시지로 해당 요청에 응답한다. 클라이언트는 의도대로 잘 수행됐는지 보기 위해 수신한 응답 메시지들을 검사하고, 수신한 상태 코드와 콘텐츠에 기반해 다음에 무엇을 할지 결정한다.

HTTP 의미체계는 각 요청 메소드에 정의된 의도(9절), 요청 헤더 필드에 기술될 수 있는 의미체계의 확장, 응답을 기술하는 상태 코드(15절), 그리고 응답 필드에 주어질 수 있는 다른 제어 데이터와 리소스 메타데이터를 포함한다.

의미체계는 또한 수신자가 어떻게 콘텐츠를 해석하도록 의도됐는지를 설명하는 표현 메타데이터, 콘텐츠 선택에 영향을 줄 수 있는 요청 헤더 필드, 그리고 총칭하여 "content negotiatoin"이라고 불리는 다양한 선택 알고리즘들을 포함한다(12절).

### 1.4. 이 문서에 의해 폐기된 사양들

<center>

| Title                                                                         | Reference                                                | See                               |
| ----------------------------------------------------------------------------- | -------------------------------------------------------- | --------------------------------- |
| HTTP Over TLS                                                                 | [RFC2818](https://datatracker.ietf.org/doc/html/rfc2818) | [B.1](#B1-RFC-2818로-부터의-변화) |
| HTTP/1.1 Message Syntax and Routing [*]                                       | [RFC7230](https://datatracker.ietf.org/doc/html/rfc7230) | [B.2](#B1-RFC-7230로-부터의-변화) |
| HTTP/1.1 Semantics and Content                                                | [RFC7231](https://datatracker.ietf.org/doc/html/rfc7231) | [B.3](#B1-RFC-7231로-부터의-변화) |
| HTTP/1.1 Conditional Requests                                                 | [RFC7232](https://datatracker.ietf.org/doc/html/rfc7232) | [B.4](#B1-RFC-7232로-부터의-변화) |
| HTTP/1.1 Range Requests                                                       | [RFC7233](https://datatracker.ietf.org/doc/html/rfc7233) | [B.5](#B1-RFC-7233로-부터의-변화) |
| HTTP/1.1 Authentication                                                       | [RFC7235](https://datatracker.ietf.org/doc/html/rfc7235) | [B.6](#B1-RFC-7235로-부터의-변화) |
| HTTP Status Code 308 (Permanent Redirect)                                     | [RFC7538](https://datatracker.ietf.org/doc/html/rfc7538) | [B.7](#B1-RFC-7538로-부터의-변화) |
| HTTP Authentication-Info and Proxy-Authentication-Info Response Header Fields | [RFC7615](https://datatracker.ietf.org/doc/html/rfc7615) | [B.8](#B8-RFC-7615로-부터의-변화) |
| HTTP Client-Initiated Content-Encoding                                        | [RFC7694](https://datatracker.ietf.org/doc/html/rfc7694) | [B.9](#B9-RFC-7694로-부터의-변화) |

Table 1

</center>

이 문서는 오직 HTTP/1.1 메시징 구문 및 연결 관리와 독립적인 RFC7230의 일부만을 폐기한다; RFC7230의 나머지 부분은 "[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)"에 의해 폐기된다.

## 2. 준수사항

### 2.1. 문법 표기

이 사양은 [RFC5234](https://www.rfc-editor.org/rfc/rfc5234)의 Augmented Backus-Naur Form (ABNF) 표기법을 사용하며, [RFC7405](https://datatracker.ietf.org/doc/html/rfc7405)에 정의된 문자열의 대소문자 구분을 위한 표기법을 적용한다.

또한 5.6.1절에 정의된 "#" 연산자를 이용해 간결하게 콤마로 구분되는 리스트를 정의할 수 있도록 하는 리스트 확장도 사용한다("/\*" 연산자가 반복을 나타내는 것과 비슷하게). 부록 A는 표준 ABNF 표기법으로 확장된 모든 리스트 연산자의 문법 모음을 보여준다.

관행적으로, "obs-"로 시작하는 ABNF 규칙명들은 역사와 관련된 이유로 나타나는 폐기된 문법 규칙들을 나타낸다.

다음의 핵심 규칙들은 [RFC5234 부록 B.1](https://www.rfc-editor.org/rfc/rfc5234#appendix-B.1)에 정의된 대로 참조하여 포함한다: ALPHA (문자), CR (캐리지 리턴), CRLF (CR LF), CTL (컨트롤), DIGIT (십진 0-9), DQUOTE (큰 따옴표), HEXDIG (십육진 0-9/A-F/a-f), HTAB (수평 탭), LF (라인 피드), OCTET (임의의 8비트 길이 데이터), SP (스페이스), 그리고 VCHAR (임의의 가시적인 US-ASCII 문자).

5.6절은 필드 값들을 위한 몇몇 일반적인 구문 구성요소를 정의한다.

이 사양은 [RFC6365](https://www.rfc-editor.org/rfc/rfc6365)의 정의를 따라 "문자", "문자 인코딩 체계", "문자 집합", 그리고 "프로토콜 요소"라는 용어들을 사용한다.

### 2.2. 요구사항 표기

원본 영어 문서의 키워드 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL"은 보여진 바와 같이 모두 대문자로 나타날 때 [BCP 14](https://www.rfc-editor.org/info/bcp14) [[RFC2119](https://datatracker.ietf.org/doc/html/rfc2119)] [[RFC8174](https://datatracker.ietf.org/doc/html/rfc8174)]에 기술된 대로 해석돼야 한다.

**이 사양은 HTTP 통신의 참여자의 역할에 따른 준수 사항**을 위함이다. 따라서, 요구사항들은 어떤 행동이 제약되냐에 따라 발신자, 수신자, 클라이언트, 서버, 유저 에이전트(브라우저), 중개자, 오리진 서버, 프록시, 게이트웨이, 혹은 캐시에 적용된다. 추가 요구사항은 구현, 리소스 소유자에, 그리고 단일 통신의 범위를 넘어서 새 프로토콜 요소를 등록하는 경우에 적용된다.

요구사항이 수신된 요소를 다운스트림으로 포워드하는 구현사항이 아니고 오직 프로토콜 요소를 만드는 구현사항인 경우에는 "생성한다"라는 동사를 "보낸다" 대신 사용한다.

구현사항이 HTTP에서 하는 역할들과 관련한 요구사항들 모두 준수한다면 해당 구현은 사양을 준수하는 것으로 간주된다.

발신자는 절대(MUST NOT) 해당하는 ABNF 규칙에 의해 정의된 문법에 맞지 않는 프로토콜 요소를 생성하면 안된다. 주어진 메시지에서, 발신자는 절대(MUST NOT) 다른 역할의 통신 참여자들만이 생성할 수 있도록 허용된 프로토콜 요소나 구문 대안을 생성해서는 안된다 (즉, 해당 메시지에 대해 발신자가 가지지 않은 역할).

HTTP를 준수한다는 것은 해당 프로토콜의 특정 메시징 문법을 사용 중에 따르는 것은 물론 보내진 프로토콜 요소들의 의미체계를 따르는 것도 포함한다. 예를 들어, HTTP/1.1을 준수한다고 주장하지만 HTTP/1.1 수신자들에게 요구되는 기능들을 인식하지 못하는 클라이언트는 해당 주장에 따라 응답을 조정하는 서버와 상호운영되지 못할 것이다. 콘텐츠 협상과 유저 선택 확장 같은 유저의 선택을 반영하는 기능들은 프로토콜 스트림을 넘어 애플리케이션 행동에 영향을 줄 수 있다; 유저의 선택을 부정확하게 따르는 프로토콜 요소를 보내는 것은 유저를 혼란스럽게 하고 선택을 억제할 것이다.

구현이 의미체계를 준수하지 못할 경우, 해당 구현의 메시지를 받은 수신자는 그에 따라 그들의 행동을 조절하기 위해 결국 해결법을 찾으려할 것이다. 만약 해결법의 적용이 잘못된 구현사항에만 제한돼있으면 수신자는 아마(MAY) 그런 해결법을 프로토콜을 준수하면서 사용할 수 있을 것이다. 예컨대, 서버는 종종 유저 에이전트(브라우저) 필드 값의 일부를 스캔하고, 유저 에이전트도 종종 서버의 필드 값을 스캔하는데, 이는 알려진 버그나 잘못 선택된 기본값에 대해 자신들의 행동을 조절하기 위함이다.

### 2.3. 길이 요구사항

수신자는 웬만하면(SHOULD) 수신한 프로토콜 요소가 해당하는 ABNF 문법을 준수하고 합리적인 버퍼 사이즈를 맞출 것이라는 최소한의 기대만 가지고 방어적으로 파싱해야 한다.

HTTP에 속하는 많은 프로토콜 요소들의 적절한 길이라 하면, 배포 컨텍스트나 구현 목적에 따라 넓은 범위에서 다양할 것이기 때문에 프로토콜 요소들에 특정된 길이 제한은 없다. 이리하여, 발신자와 수신자 사이의 상호운영성은 각 프로토콜 요소의 길이가 어느정도면 합리적인지에 대한 공유되는 기대치에 의존한다. 게다가, 일부 프로토콜 요소들에 대해 합리적인 길이라고 생각돼 왔던 것은 HTTP를 사용해온 지난 30년간 계속 바뀌어 왔고 앞으로도 계속 바뀔 것이다.

최소한, 수신자는 반드시(MUST) 프로토콜 요소들을 자신이 다른 메시지에서 같은 프로토콜로 생성하는 값들의 길이만큼은 파싱하고 처리할 수 있어야 한다. 예를 들어, 자신의 리소스에 대해 아주 긴 URI 참조를 발행하는 오리진 서버는 해당 참조를 타겟 URI로 받았을 때 그걸 파싱하고 처리할 수 있어야겠다.

수신된 많은 프로토콜 요소들은 오직 식별하고 다운스트림으로 포워딩하기 위해 필요한 정도까지만 파싱된다. 예를 들어, 중개자들은 수신한 필드를 필드 명과 필드 값 컴포넌트로 파싱하고, 거기서 필드 값을 더 파싱하지 않고 필드를 포워딩할 수 있을 것이다.

### 2.4. 에러 핸들링

수신자는 (경험이나 설정을 통해) 발신자가 그 의미체계에서 내포하는 것들을 잘못 구현하고 있다고 판단하지 않은 이상에는 반드시(MUST) 수신한 프로토콜 요소를 이 문서의 확장과 이 문서에 정의된 의미체계에 따라 해석해야 한다. 예를 들어, 오리진 서버는 User-Agent 헤더가 특정 콘텐츠 코딩 수신을 실패한다고 알려진 특정한 구현 버전을 가리키고 있을 때 수신된 Accept-Encoding 헤더 필드의 내용을 무시할 수 있을 것이다.

별도의 언급이 없는한, 수신자는 아마(MAY) 사용 가능한 프로토콜 요소를 잘못된 구조로부터 복원하는 것을 시도할 수 있을 것이다. HTTP는 보안에 직접적인 영향을 줄 때를 제외하고는 특정한 에러 핸들링 메커니즘을 정의하지 않는다. 예를 들어, 웹 브라우저는 Location 헤더 필드가 ABNF에 따라 파싱되지 않는 응답을 투명하게 복원하길 원할 수 있는 반면, 시스템 제어 클라이언트는 에러 복원은 어떠한 형태든지 위험하다고 여길 수 있을 것이다.

9.2.2절에 기술된대로, 일부 요청들은 기반이 되는 연결의 실패 상황에서 클라이언트에 의해 자동으로 재시도될 수 있다.

### 2.5. 프로토콜 버전

HTTP 버전 넘버는 "."(마침표 혹은 소수점)으로 구분되는 두 십진수로 구성된다. 첫번째 숫자(메이저 버전)는 메시징 구문을 가리키는 반면, 두번째 숫자(마이너 버전)은 발신자가 따르는(이후 통신에서 이해할 수 있는) 메이저 버전에서 가장 높은 마이너 버전을 가리킨다.

HTTP 핵심 의미체계가 프로토콜 버전에 따라 바뀌지는 않지만, "통신 선로 상에서" 그 표현은 바뀔 수 있고, 따라서 HTTP 버전 넘버는 연결 형식에 호환되지 않는 변경이 생길 때 바뀐다. 추가로, HTTP는 정의된 확장 포인트들(16절)을 이용해 프로토콜 버전을 바꾸지 않으면서 점진적이고, 이전 버전과 호환 가능한 프로토콜의 변화를 허용한다.

프로토콜 버전 넘버 전체는 발신자가 그 버전에 해당하는 사양에 명시된 요구 사항들을 준수하겠다는 것을 나타낸다. 예를 들어, "HTTP/1.1"은 이 문서, "[RFC7234 HTTP Caching](https://www.rfc-editor.org/rfc/rfc7234)", 그리고 "[RFC7231 HTTP/1.1](https://datatracker.ietf.org/doc/html/rfc7231)"이 조합된 사양에 의해 정의된다.

HTTP의 메이저 버전 넘버는 호환되지 않는 메시지 구문이 도입됐을 때 올려진다. 마이너 넘버는 프로토콜에 대한 변경이 메시지 의미체계를 추가하거나 발신자의 추가적인 기능을 암시할 때 올려진다.

마이너 버전은 발신자가 프로토콜의 이전 버전과 호환되는 일부만 사용한다고 해도 발신자의 통신 기능들을 알리게 되는데, 이는 수신자가 응답(서버에 의한)이나 이후의 요청(클라이언트에 의한)에서 더 발전된 기능들이 사용될 수 있음을 알게 한다.

HTTP의 메이저 버전이 어떤 마이너 버전도 정의하고 있지 않을 때, 마이너 버전은 "0"으로 암시된다. "0"은 마이너 버전 식별자가 필요한 요소들 내에서 해당 프로토콜을 언급할 때 사용된다.

## 3. 용어와 핵심 개념들

HTTP는 World Wide Web(WWW) 아키텍처를 위해 만들어졌고 시간이 지남에 따라 전 세계 하이퍼텍스트 시스템의 확장성에 대한 요구를 지원하기 위해 계속 발전해왔다. 이 아키텍처의 많은 부분은 HTTP를 정의하는데 사용된 용어들에 반영되어 있다.

### 3.1. 리소스

HTTP 요청의 타겟은 "리소스"라고 불린다. HTTP는 리소스의 성질을 제한하지 않는다; 단지 리소스와의 상호작용에 사용될 수 있는 인터페이스를 정의한다. 대부분의 리소스들은 Uniform Resource Identifier(URI)에 의해 정의되며, 이에 대해 4절에서 설명한다.

HTTP의 설계 목적 중 하나는 리소스의 식별을 리소스 의미체계로부터 분리하는 것인데, 이는 요청 메소드(9절)와 몇몇 요청 수정 헤더 필드에 요청 의미체계를 부여함으로써 가능해진다. 리소스는 요청을 요청의 메소드의 의미체계와 일치하지 않는 방식으로 다룰 수 없다. 예를 들어, 리소스의 URI는 안전하지 않은 의미체계를 내포할 수도 있지만, 클라이언트는 리소스가 안전한 메소드(9.2.1절)의 요청을 처리할 때는 안전하지 않은 행동을 피할 것으로 기대할 수 있다.

HTTP는 타겟 리소스(7.1절)와 리소스들 간의 관계를 나타내기 위해 Uniform Resource Identifier([RFC3986](https://datatracker.ietf.org/doc/html/rfc3986)) 표준에 의존한다.

### 3.2. 표현

"표현"은 주어진 리소스의 과거나 현재의 상태, 혹은 바라는 상태를 프로토콜을 통해 손쉽게 통신할 수 있는 형태로 반영하게 돼있는 정보다. 표현은 표현 메타데이터의 집합과 잠재적으로 제한되지 않은 표현 데이터 스트림으로 구성된다 (8절).

HTTP는 통신을 리소스를 전송하는 것 자체가 아니라 전송 가능한 리소스 상태의 표현에 대한 것으로 정의함으로써 균일한 인터페이스 뒤로 "정보 은닉"할 수 있도록 한다. 이는 잠재적으로 메시지가 생성된 시점의 리소스를 나타내는 정보를 제공하면서, "광안리 해변의 현재 날씨" 같은 일시적인 기능들을 포함해, URI에 의해 식별되는 리소스가 무엇이든 될 수 있게 한다[[REST](https://roy.gbiv.com/pubs/dissertation/rest_arch_style.htm)].

균일한 인터페이스라는 것은 통하여 어떤 것을 관찰할 수 있고 그 어떤 것에 대해 오로지 독립적인 반대편 사람과 메시지를 교환하는 통신 방식으로만 무언가 행할 수 있는 창문과 비슷하다. 이 통신에서 현재 혹은 바라는 상태를 나타내기("대신하기") 위해서는 공유된 추상화가 필요하다. 표현이 하이퍼텍스트라면, 리소스 상태의 표현과 수신자의 향후 상호작용을 안내하는 처리 지침 둘 다 제공할 수 있다.

타겟 리소스는 각각이 리소스의 현재 상태를 반영하도록 돼있는 여러 표현들을 제공 받거나, 생성할 수 있을 것이다. 보통은 콘텐츠 협상(섹션 12)에 기반한 한 알고리즘이, 그 표현들 중 요청에 대해 가장 적합한 하나를 선택하는 데 사용될 것이다. 이 "선택된 표현"은 조건부 요청(13절)을 평가하고 GET에 대해 200(OK), 206(Partial Content), 304(Not Modified) 응답 콘텐츠를 구성하기 위한 데이터와 메타데이터를 제공한다(9.3.1절).

### 3.3. 연결, 클라이언트, 그리고 서버

HTTP는 신뢰할 수 있는 전송 혹은 세션 계층의 "연결" 위에서 작동하는 클라이언트/서버 프로토콜이다.

HTTP "클라이언트"라 함은 하나 이상의 HTTP 요청을 보내기 위한 목적으로 서버와의 연결을 수립하는 프로그램이다. HTTP "서버"라 함은 HTTP 응답을 보냄으로써 HTTP 요청을 처리해주기 위해 연결을 수락하는 프로그램이다.

클라이언트와 서버라는 용어는 오로지 이 프로그램들이 특정 연결에 대해서 수행하는 역할 그 자체만을 나타낸다. 즉, 같은 프로그램이 어떤 연결에서는 클라이언트로, 다른 곳에서는 또 서버로 동작할 수도 있다.

HTTP는 무상태성 프로토콜로 정의되는데, 이는 각 메시지의 의미체계가 독립적으로 해석될 수 있으며, 연결들과 그 위의 메시지들 간의 관계가 그 메시지들의 해석에 어떠한 영향도 주지 않음을 의미한다. 예를 들어, CONNECT 요청(9.3.6절)이나 Upgrade 헤더 필드(7.8절)를 가진 요청은 연결의 첫번째 메시지에서뿐만 아니라 언제든지 일어날 수 있다. 많은 구현들은 프록시된 연결을 재사용하거나 동적으로 여러 서버들에 걸쳐 요청들을 분산하기 위해서 HTTP의 무상태성 설계에 의존한다.

결과적으로, 서버는 연결이 안전한 동시에 특정 유저 에이전트에게만 고유한 것이 보장되지 않는한 절대(MUST NOT) 같은 연결 상의 두 요청이 같은 유저 에이전트로부터 왔다고 가정해서는 안된다. 일부 비표준 HTTP 확장들([RFC4599](https://datatracker.ietf.org/doc/html/rfc4559) 같은)은 이러한 요구사항들을 위반하는 것으로 알려져있고, 덕분에 보안 문제와 상호운영성 문제를 야기한다.

### 3.4. 메시지

HTTP는 한 연결에 걸쳐 "메시지들"을 교환하기 위한 무상태성 요청/응답 프로토콜이다. "발신자"와 "수신자"라는 용어는 각각 어떤 형태로든 주어진 메시지를 보내거나 받는 구현을 말한다.

클라이언트는 메소드(9절) 그리고 요청 타겟(7.1절)과 함께 "요청" 메시지의 형태로 서버에게 요청들을 보낸다. 요청은 또한 요청 수정자들, 클라이언트 정보, 그리고 표현 메타데이를 위한 헤더 필드들(6.3절), 메소드에 따라 처리하도록 의도된 콘텐츠(6.4절), 그리고 콘텐츠를 전송하는 동안 수집된 정보를 전달하기 위한 트레일러 필드(6.5절)을 포함할 수도 있다.

서버는 각각 상태 코드(15절)를 포함하는, 하나 이상의 "응답" 메시지들을 보냄으로써 클라이언트의 요청에 응답한다. 응답은 또한 서버 정보, 리소스 메타데이터, 그리고 표현 메타데이터를 위한 헤더 필드들, 상태 코드에 따라 해석될 콘텐츠, 그리고 콘텐츠를 전송하는 동안 수집된 정보를 전달하기 위한 트레일러 필드들을 포함할 수도 있다.

### 3.5. 유저 에이전트

"유저 에이전트"라는 용어는 어느 것이든 요청을 시작하는 다양한 클라이언트 프로그램들 중 하나를 가리킨다.

가장 친숙한 형태의 유저 에이전트는 범용 웹 브라우저이지만, 이는 오직 작은 일부다. 다른 일반적인 유저 에이전트들로는 스파이더(웹 탐색 로봇), 커맨드-라인 도구, 전광판, 가전제품, 저울, 전구, 펌웨어 업데이트 스크립트, 모바일 앱, 그리고 다양한 형태와 크기의 통신 기기들이 포함된다.

유저 에이전트라는 것이 꼭 요청 시점에 그 소프트웨어 에이전트와 직접적으로 상호작용하는 인간 사용자가 있다는 것을 의미하지는 않는다. 많은 경우에서, 유저 에이전트는 백그라운드에서 실행되고 나중의 점검을 위해 그 결과들을 저장하도록(혹은 그 결과들 중 흥미롭거나 에러와 관련된 부분만 저장하도록) 설치되거나 설정된다. 예를 들어 스파이더는, 일반적으로 시작 URI가 주어지고 나서 웹을 하이퍼텍스트 그래프로써 크롤링하는 동안 특정한 행동을 따르도록 설정된다.

많은 유저 에이전트들은 유저와 상호작용하며 제안을 하거나 보안 혹은 프라이버시 문제들에 관해 적절한 경고를 제공하지 못하거나, 혹은 하지 않는다. 이 사양서에서 유저에게 에러를 보고할 것을 요구하는 극히 일부의 경우에서, 그러한 보고를 오직 에러 콘솔이나 로그 파일에서만 확인할 수 있도록 하는 것도 허용된다. 마찬가지로, 자동화된 행동을 진행하기 전에 유저에게 확인을 받아야 한다는 요구사항들은 고급 설정 선택, 런타임 옵션, 혹은 안전하지 않은 행동에 대한 간단한 회피를 통해 만족시킬 수 있다; 확인이라는 것은 이미 유저가 그 선택을 결정했다면 어떠한 특정 유저 인터페이스나 정상 프로세싱의 인터럽트를 의미하지 않는다.

### 3.6. 오리진 서버

"오리진 서버"라는 용어는 주어진 타겟 리소스에 대해 인가된 응답을 발생시킬 수 있는 프로그램을 말한다.

가장 익숙한 형태의 오리진 서버는 대형 퍼블릭 웹사이트들이다. 그러나, 유저 에이전트가 브라우저와 동일시 되는 것 처럼, 모든 오리진 서버들이 같다고 오해하기 쉽다. 일반적인 오리진 서버들은 또한 가정 자동화 유닛, 설정 가능한 네트워킹 컴포넌트, 사무실 기계, 자율 로봇, 뉴스 피드, 교통 카메라, 실시간 광고 선택기, 그리고 비디오-온-디맨드 플랫폼들을 포함한다.

대부분의 HTTP 요청은 URI로 식별되는 어떤 리소스의 표현에 대한 검색 요청(GET)으로 이루어진다. 가장 단순한 경우에, 이는 유저 에이전트(UA)와 오리진 서버(O) 사이 단 하나의 양방향 연결(===)을 통해 이루어질 수 있다.

            요청   >
       UA ======================================= O
                                   <   응답

                                  Figure 1

### 3.7. 중개자

HTTP는 요청을 충족시키기 위해 연결 체인에 걸쳐 중개자들을 개입시키는 것을 허용한다. 대표적인 세 갖; HTTP "중개자"가 있다: 프록시, 게이트웨이, 그리고 터널. 일부 케이스들에서는, 단 하나의 중개자가 각 요청의 성질에 따라 행동을 스위칭하면서 오리진 서버, 프록시, 게이트웨이나 터널의 역할을 할 수도 있다.

            >             >             >             >
       UA =========== A =========== B =========== C =========== O
                  <             <             <             <

                                  Figure 2

위의 그림은 유저 에이전트와 오리진 서버 간의 세 중개자들(A, B, 그리고 C)을 보여준다. 전체 체인을 지나가는 한 요청이나 응답은 별개인 네 개의 연결들을 통과할 것이다. 일부 HTTP 통신 옵션들은 오직 가장 가깝고, 터널이 아닌 이웃에, 오직 체인의 엔드포인트들에, 혹은 체인의 모든 연결들에 적용될 수 있다. 위 다이어그램은 선형이지만, 각 참가자들은 동시에 여러 통신들에 참여할 수 있다. 예를 들어, B는 A의 요구를 처리하는 동시에 A가 아닌 다른 많은 클라이언트로부터 요청을 수신하고 있을 수도 있고, 그와 동시에 / 혹은 그와 별개로 C가 아닌 다른 서버로 요청들을 포워딩하고 있을 수도 있다. 마찬가지로, 나중의 요청들은 연결의 다른 경로를 통해 전송될 수 있는데, 이는 보통 로드 밸런싱을 위한 동적 설정에 기반한다.

"업스트림"과 "다운스트림"이라는 용어는 메시지 흐름과 관련한 방향에 관한 요구사항을 기술하는데 사용된다: 모든 메시지는 업스트림에서 다운스트림으로 흐른다. "인바운드"와 "아웃바운드"라는 용어는 요청 루트에 관련한 방향에 관한 요구사항을 기술하는데 사용된다: 인바운드는 "오리진 서버를 향해", 반면 아웃바운드는 "유저 에이전트를 향해"를 뜻한다.

"프록시"는 보통 로컬 설정 규칙들에 의해 클라이언트에게 선택된, 절대 URI의 어떤 타입(들)에 대한 요청을 수신하거나 그러한 요청들을 만족시키기 위해 HTTP 인터페이스로 변환하는 메시지-포워딩 에이전트다. "http" URI들에 대한 프록시 요청 같은 변환은 최소한의 수준인 반면, 다른 요청들은 완전히 다른 애플리케이션 레벨 프로토콜로 왔다 갔다하는 수준일 수도 있다. 프록시는 종종 보안 서비스, 어노테이션 서비스, 혹은 공유 캐시를 위해 한 조직의 HTTP 요청들을 공통의 중개자를 통해 그룹화하는데 사용된다. 어떤 프록시들은 7.7절에 설명된대로, 선택된 메시지들이나 콘텐츠가 포워딩되는 동안 변환을 적용하도록 설계되어 있다.

"게이트웨이"("리버스 프록시"라고도 알려진)는 아웃바운드 연결에 대한 오리진 서버 처럼 작동하지만 요청들을 수신해서 다른 서버나 서버들로 인바운드 포워딩하는 중개자의 일종이다. 게이트웨이들은 종종 "액셀러레이터" 캐싱을 통해 서버 성능을 향상하기 위해서, 그리고 여러 머신들에 걸친 HTTP 서비스들의 파티셔닝이나 로드 밸런싱을 가능하게 하기 위해서 레거시나 신뢰할 수 없는 정보 서비스들을 캡슐화하는데 사용된다.

오리진 서버에 적용되는 모든 HTTP 요구사항들은 게이트웨이의 아웃바운드 통신에도 마찬가지로 적용된다. 게이트웨이는 이 사양서를 벗어나는 HTTP에 대한 사설 확장을 포함해, 원하는 어떤 프로토콜로든 인바운드 서버들과 통신한다. 그러나, 써드-파티 HTTP 서버들과 상호운용하길 바라는 HTTP-to-HTTP 게이트웨이는 게이트웨이의 인바운드 연결에서 유저 에이전트 요구사항을 준수할 필요가 있다.

"터널"은 두 연결 사이에서 메시지를 변경하거나 해석하지 않고 그저 중개하는 역할을 수행한다. 일단 활성화되면, 터널은 비록 HTTP 요청에 의해 초기화되었다고 해도 HTTP 통신의 참가자로 간주되지 않는다. 터널은 중개되던 양쪽 연결이 모두 닫히면 사라진다. 터널은 공유된 방화벽 프록시를 통해 비밀 통신울 수립하는데 Transport Layer Security(TLS, [RFC8446](https://datatracker.ietf.org/doc/html/rfc8446))가 사용될 때 처럼, 중개자를 통해 가상 연결을 확장하는데 사용된다.

위에 나오는 중개자의 범주들은 오직 HTTP 통신의 참가자들만 고려한다. 메시지 발신자에 대한 지식이나 허가 없이 HTTP 트래픽을 필터링하거나 리다이렉션하는, 네트워크 프로토콜 스택의 더 낮은 계층에서 작동하는 중개자들 또한 존재한다. 네트워크 중개자들은 경로상의 공격자와 구분할 수 없는데(프로토콜 레벨에서), 종종 의도치 않게 HTTP 의미체계를 위반함으로써 보안 결함이나 상호운용 문제들 야기하기도 한다.

예를 들어, "인터셉션 프록시"[[RFC3040](https://datatracker.ietf.org/doc/html/rfc3040)](흔히 "투명 프록시"[[RFC1919](https://datatracker.ietf.org/doc/html/rfc1919)]라고 알려져 있기도 한)는 클라이언트에게 선택된 것이 아니기 때문에 HTTP 프록시와는 다르다. 대신에, 인터셉션 프록시는 밖으로 나가는 TCP 80 포트 패킷들(그리고 가끔 이 외의 일반적인 포트 트래픽)을 필터링하거나 리다이렉트 한다. 인터셉션 프록시들은 비-로컬 인터넷 서비스 사용을 허용하기 전에 계정 구독을 강제하기 위해, 그리고 회사 방화벽과 함께 네트워크 사용 정책을 강제하기 위한 수단으로써, 흔히 퍼블릭 네트워크 액세스 포인트에서 찾을 수 있다.

### 3.8. 캐시

"캐시"는 이전 응답 메시지들의 로컬 저장소이고 그 메시지 저장소, 검색, 그리고 삭제를 제어하는 서브시스템이다. 캐시는 향후, 같은 요청들에서 응답 시간과 네트워크 대역폭 소모를 줄이기 위해 캐시가능한 응답들을 저장한다. 터널로 작동하는 동안은 캐시가 사용될 수 없지만, 어느 클라이언트나 서버든 아마(MAY) 캐시를 도입할 수 있을 것이다.

캐시의 효과는 체인 상의 참가자들 중 하나가 어떤 요청에 대해 적용가능한 응답을 캐시해놓았다면 요청/응답 체인이 짧아지는 것이다. 다음은 B가 UA나 A가 캐시하지 않은 요청에 대한 O(C를 통한)로부터의 이전 응답의 캐시 복사본을 가지고 있는 경우의 결과 체인을 묘사한다.

          >             >
     UA =========== A =========== B - - - - - - C - - - - - - O
                    <             <

                              Figure 3

응답은 후속 요청들에 답변하기 위한 용도로 응답 메시지의 복사본이 캐시에 저장되는 것이 허용될 때 "캐시 가능"하다. 응답이 캐시 가능하더라도, 그 캐시된 응답이 특정 요청에 대해 사용될 때에 대한 클라이언트나 오리진 서버의 추가적인 제약이 있을 수 있다. 캐시 동작과 캐시 응답들을 위한 HTTP 사양은 [RFC7234](https://datatracker.ietf.org/doc/html/rfc7234)에 정의돼 있다.

World Wide Web과 여러 대형 조직들 내부에 걸쳐 배치된 아주 다양한 아키텍처와 설정의 캐시들이 존재한다. 여기에는 대역폭을 절약하고 지연시간을 줄이기 위한 프록시 캐시들의 국가 단위 계층들, 인기있는 사이트들의 지역별의 그리고 세계적인 분산을 최적화하기 위해 게이트웨이 캐싱을 사용하는 content delivery network들, 캐시 엔트리들을 브로드캐스트 혹은 멀티캐스트하는 협업 시스템들, 오프라인 혹은 고지연시간 환경에서 사용하기 위해 미리 페치된 캐시 엔트리들의 아카이브들, 등등이 포함된다.

### 3.9. 메시지 교환 예시

다음의 예시는 "http://www.example.com/hellot.txt" URI에 대한 GET 요청(9.3.1절)을 위한 전형적인 HTTP/1.1 메시지 교환을 묘사한다:

클라이언트 요청:

     GET /hello.txt HTTP/1.1
     User-Agent: curl/7.64.1
     Host: www.example.com
     Accept-Language: en, mi

서버 응답:

     HTTP/1.1 200 OK
     Date: Mon, 27 Jul 2009 12:28:53 GMT
     Server: Apache
     Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
     ETag: "34aa387-d-1568eb00"
     Accept-Ranges: bytes
     Content-Length: 51
     Vary: Accept-Encoding
     Content-Type: text/plain

     Hello World! My content includes a trailing CRLF.

## 4. HTTP의 식별자들

Uniform Resource Identifiers(URI, [RFC3986](https://datatracker.ietf.org/doc/html/rfc3986))는 HTTP 전반에 걸쳐 리소스를 식별하는 수단으로 사용된다 (3.1절).

### 4.1 URI 레퍼런스

URI 레퍼런스는 요청들을 타게팅하는데, 리다이렉트를 나타내는데, 그리고 관계들을 정의하는데 사용된다.

"URI-레퍼런스", "절대-URI", "상대-파트", "권한", "포트", "호스트", "path-abempty", "세그먼트", 그리고 "쿼리"의 정의들은 일반 URI 구문으로부터 채택됐다. "절대-경로" 규칙은 비어있지 않은 경로 구성요소를 포함할 수 있는 프로토콜 요소들을 위해 정의됐다. (이 규칙은 RFC 3986의 path-abempty 규칙과 살짝 다른데, 그 규칙은 빈 경로를 허용하고, "//"로 시작하는 경로를 허용하지 않는 절대 경로 규칙도 허용한다.) "부분 URI" 규칙은 상대 경로를 포함할 수 있지만 프레그먼트 구성요소는 포함할 수 없는 프로토콜 요소들을 위해 정의됐다.

     URI-reference = <URI-레퍼런스, RFC 3976 4.1절 참조.>
     absolute-URI  = <절대-URI, RFC 3976 4.3절 참조>
     relative-part = <상대-경로, RFC 3976 4.2절 참조>
     authority     = <권한, RFC 3976 3.2절 참조>
     uri-host      = <호스트, RFC 3976 3.2.2절 참조>
     port          = <포트, RFC 3976 3.2.3절 참조>
     path-abempty  = <path-abempty, RFC 3976 3.3절 참조>
     segment       = <세그먼트, RFC 3976 3.3절 참조>
     query         = <쿼리, RFC 3976 3.4절 참조>

     absolute-path = 1*( "/" segment )
     partial-URI   = relative-part [ "?" query ]

URI 레퍼런스를 허용하는 HTTP의 각 프로토콜 요소는 자체적인 ABNF 프로덕션 안에서 엘리먼트가 레퍼런스(URI-레퍼런스)의 어떤 형태라도 허용하는지, 절대적 형태(절대-URI)의 URI만 허용하는지, 경로와 선택적 쿼리 구성요소들(partial-URI)만 허용하는지, 혹은 위의 것들의 어떤 조합을 허용하는지 나타낼 것이다. 만약 그렇지 않으면, URI 레퍼런스들은 타겟 URI에 대해 상대적으로 파싱된다(7.1절).

모든 발신자들과 수신자들을, 최소한, 프로토콜 요소들에서 8000 옥텟 이상의 길이를 지원하도록 권장된다(RECOMMENDED). 이는 어떤 구조들과 on-wire 표현들(예를 들어, HTTP/1.1의 요청 라인)이 어떤 경우들에서는 반드시 더 클 것이라는 의미를 내포한다.

### 4.2. HTTP와 연관된 URI 체계들

IANA maintains the registry of URI Schemes [BCP35] at
<https://www.iana.org/assignments/uri-schemes/>. Although requests
might target any URI scheme, the following schemes are inherent to
HTTP servers:

IANA는 <<https://www.iana.org/assignments/uri-schemes/>>에서 URI 체계 등록[[BCP35](https://www.rfc-editor.org/info/bcp35)]을 유지 관리한다. 비록 어느 URI 체계든 요청이 목표로 삼을 수 있지만, 다음의 체계들은 HTTP 서버에게는 내재적이다.

     +============+====================================+=========+
     | URI Scheme | Description | Section |
     +============+====================================+=========+
     | http | Hypertext Transfer Protocol | 4.2.1 |
     +------------+------------------------------------+---------+
     | https | Hypertext Transfer Protocol Secure | 4.2.2 |
     +------------+------------------------------------+---------+

                              Table 2

"http"나 "https" URI의 존재가 연결을 기다리고 있는 식별된 오리진에 항상 HTTP 서버가 있다는 것을 의미하지는 않는다는 것을 명심하라. 누구든 URI를 만들 수 있으며, 서버의 존재유무와 서버가 현재 해당 식별자를 리소스로 매핑하고 있는지 여부와는 상관없다. 등록된 이름과 IP 주소들의 위임된 성질은 HTTP 서버의 존재와 관계없이 연합된 네임스페이스를 만들어낸다.

#### 4.2.1. http URI 체계

"http" URI 체계는 이로써 주어진 포트에서 TCP([RFC9293, TCP](https://www.rfc-editor.org/rfc/rfc9293)) 연결을 대기하는 잠재적인 HTTP 오리진 서버가 관리하는 계층적 네임스페이스 내에서 식별자를 만들어내기 위해 정의된다.

     http-URI = "http" "://" authority path-abempty [ "?" query ]

"http" URI의 오리진 서버는 권한 요소에 의해 식별되는데, 이것은 호스트 식별자([[URI](https://www.rfc-editor.org/info/rfc3986)], 3.2.2절)와 선택적 포트 넘버([[URI](https://www.rfc-editor.org/info/rfc3986)], 3.2.3절)를 포함한다. 만약 포트 하위구성요소가 비었거나 주어지지 않으면, TCP 80 포트(WWW 서비스들에 예약된 것)가 기본이다. 오리진은 4.3.2절에 정의된 대로, 식별된 리소스를 목표로 하는 요청들에 대해 누가 정식으로 응답할 권리를 가지는지 결정한다.

발신자는 절대(MUST NOT) 호스트 식별자 부분이 비어있는 "http" URI를 생성해서는 안된다. 그런 URI 레퍼런스를 처리하는 수신자는 반드시(MUST) 무효로 거부해야 한다.

계층적 경로 구성요소와 선택적 쿼리 구성요소는 해당 오리진 서버의 네임스페이스 내에서 타겟 리소스를 식별한다.

#### 4.2.2. https URI 체계

"https" URI 체계는 이로써 주어진 포트에서 TCP 연결을 대기하고 있으면서 HTTP 통신의 보안을 설정하는 TLS([[TLS13](https://www.rfc-editor.org/info/rfc8446)) 연결을 수립할 수 있는 잠재적인 오리진 서버에 의해 관리되는 계층적 네임스페이스 내에서 식별자를 만들어내기 위해 정의된다. 이 문맥에서, "보안을 설정"한다는 말은 구체적으로 서버가 식별된 권한을 대신하여 작동하도록 인증됐고 해당 서버의 모든 HTTP 통신이 비밀성과 클라이언트 서버 양쪽에서 받아들일 수 있는 무결성 보호를 가진다는 말이다.

     https-URI = "https" "://" authority path-abempty [ "?" query ]

"https" URI의 오리진 서버는 호스트 식별자([[URI](https://www.rfc-editor.org/info/rfc3986)], 3.2.2절)와 선택적 포트 넘버([[URI](https://www.rfc-editor.org/info/rfc3986)], 3.2.3절)를 포함하는, 권한 요소에 의해 식별된다. 만약 포트 하위 요소가 비었거나 주어지지 않으면, TCP 443 포트(HTTP over TLS를 위해 예약된 포트)가 기본이다. 오리진은 4.3.3절에 정의된대로, 식별된 리소스를 목표로 하는 요청들에 대해 누가 정식으로 응답할 권리를 가지는지 결정한다.

발신자는 절대(MUST NOT) 호스트 식별자 부분이 빈 "https" URI를 생성해서는 안된다. 그러한 URI 레퍼런스를 처리하는 수신자는 반드시(MUST) 무효로 거부해야 한다.

계층적 경로 요소와 선택적 쿼리 요소는 해당 오리진 서버의 네임스페이스 내에서 타겟 리소스를 식별한다.

클라이언트는 반드시(MUST) "https" 리소스에 대한 자신의 HTTP 요청들에 보안이 설정됐다는 것, 통신되기 전에, 그 요청들에 대해 오직 보안 설정된 응답들만 받아들일 것이라는 것을 확실히 해야한다. 어떤 암호학 매커니즘이 클라이언트와 서버에게 받아들여지는가는 보통 둘 사이에 협상되고 시간이 지남에 따라 변할 수 있음에 주의하라.

"https" 체계를 통해 가용 가능한 리소스들은 "http" 체계와 공유하는 아이덴티티를 가지지 않는다. 그들은 분리된 네임스페이스들을 가진 별개의 오리진들이다. 그러나, 쿠키 프로토콜[[COOKIE](https://www.rfc-editor.org/info/rfc6265)]과 같이 같은 호스트를 가진 모든 오리진들에게 적용되는 것으로 정의된 HTTP 확장들은, 한 서비스에 의해 설정된 정보가 호스트 도메인들의 매칭 그룹 내의 다른 서비스들과의 통신에 영향을 주는 것을 허용한다. 그러한 확장들은 보안 설정된 연결이 실수로 안전하지 않은 콘텍스트에서 교환될 때 정보를 얻어가는 것을 방지하기 위해 매우 주의하여 설계되야 한다.

#### 4.2.3. http(s) 정규화와 비교

"http"나 "https" 체계의 URI들은 [[URI](https://www.rfc-editor.org/info/rfc3986)]의 6절에 정의된 방법들에 따라, 상술한 각 체계에 대한 기본값들을 이용해 정규화되고 비교된다.

HTTP는 동등함을 결정하기 위해 특별한 메소드를 사용하는 것을 요구하지 않는다. 예를 들어, 캐시 키는, 문법 기반 정규화 후나, 체계 기반 정규화 후에, 단순한 문자열로써 비교될 수 있을 것이다.

"http"와 "https" URI들의 체계 기반 정규화 ([[URI](https://www.rfc-editor.org/info/rfc3986)]의 6.2.3절)는 다음의 추가적인 규칙들을 포함한다:

- 만약 포트가 해당 체계의 기본 포트와 일치한다면, 정규적 형태는 포트 하위 요소를 생략하는 것이다.

- OPTIONS 요청의 타겟으로 사용되지 않을 때, 빈 경로 구성요소는 절대 경로 "/"와 동등하고, 그래서 정규적 형태는 대신 "/" 경로를 제공하는 것이다.

- 체계와 호스트는 대소문자를 구분하지 않고 보통 소문자로 제공된다; 모든 다른 구성요소들은 대소문자를 구분하여 비교한다.

- "예약된" 문자 집합에 속하지 않는 다른 문자들은 그들의 퍼센트 인코딩된 8비트 옥텟들과 동등하다: 정규적 형태는 그들을 인코딩하지 않는 것이다([[URI](https://www.rfc-editor.org/info/rfc3986)]의 2.1절과 2.2절 참조).

예를 들어, 다음의 세 URI들은 동등하다:

      http://example.com:80/~smith/home.html
      http://EXAMPLE.com/%7Esmith/home.html
      http://EXAMPLE.com:/%7esmith/home.html

정규화 후에 (어떤 방법이든) 동등한 두 HTTP URI는 같은 리소스를 식별한다고 가정할 수 있고, 어떤 HTTP 구성요소든 아마 (MAY) 정규화를 수행할 수 있을 것이다. 결과적으로, 구분되는 리소스들은 웬만해서는(SHOULD NOT) 정규화 후에([[URI](https://www.rfc-editor.org/info/rfc3986)])의 6.2절에 정의된 어떤 방법을 사용하든) 동등한 URI들을 통해 식별되지 않도록 해야 한다.

#### 4.2.4. http(s) URI에서의 userinfo 지원 중단

권한을 위한 URI 일반 구문은 URI에 유저 인증 정보를 포함하기 위해 유저 정보 하위 구성요소([[URI](https://www.rfc-editor.org/info/rfc3986)], 3.2.1절) 또한 포함한다. 해당 하위 구성요소에서, "user:password" 형식의 사용은 더 이상 지원되지 않는다.

일부 구현들은 커맨드 호출 옵션, 설정 파일, 혹은 북마크 리스트 내에서 처럼, 유저 정보 구성 요소를, 이로 인해 유저 아이디와 비밀번호가 노출될 수 있다해도 내부적인 인증 정보의 설정을 위해 사용한다.

발신자는 "http"나 "https" URI 레퍼런스가 타겟 URI로서의 메시지나 필드 값 내에서 생성될 때에는 절대(MUST NOT) 유저 정보 하위 구성요소(그리고 그것의 "@" 구분자)를 생성해서는 안된다.

신뢰할 수 없는 소스로부터 수신한 "http"나 "https" URI 레퍼런스를 사용하기 전에, 수신자는 웬만하면(SHOULD) 유저 정보를 파싱하고 그 존재를 에러로 취급해야 한다; 그것은 피싱 공격을 위해 권한을 흐려놨을 가능성이 크다.

### 4.2.5. 프래그먼트 식별자들과 http(s) 레퍼런스들

프래그먼트 식별자들은, [[URI](https://www.rfc-editor.org/info/rfc3986)] 3.5절에 정의된대로, URI 체계와 관계 없이, 2차 리소스를 간접 식별하는 것을 허용한다. URI를 참조하는 일부 프로토콜 요소들은 프래그먼트의 포함을 허용하는 반면, 다른 것들은 허용하지 않는다. 그것들은 프래그먼트가 허용되는 요소들을 위한 ABNF 규칙의 사용으로 구분된다; 그게 아니면, 프래그먼트들을 제외하는 특정 룰이 사용된다.

- 주의: 프래그먼트 식별자 구성요소는 URI 체계 정의의 일부가 아니며( [URI] 4.3절 참조), 이리하여 위의 "http"와 "https" 체계를 위한 ABNF 정의에 나타나지 않는다.

### 4.3. 권한 있는 접근

권한 있는 접근이라 함은, 식별된 리소스에 대한 접근을 위해, 클라이언트가 권한 있다고 믿을 수 있는(리소스 오너에게 제어되는) 방식으로의 주어진 식별자에 대한 역참조를 가리킨다. 접근이 허가됐는지 여부를 결정하는 프로세스는 URI 체계에 의해 정의되고 일반적인 구문이 사용될 때 종종 권한 구성요소 같은, URI 구성요소들 내의 데이터를 이용한다. 그러나, 권한 있는 접근은 식별된 메커니즘으로 제한되지는 않는다.

4.3.1절은 그런 용도에 대해 도움이 되도록 오리진 개념을 정의하고, 이어지는 하위 절들은 통신 상대가 오리진을 대표하는 권한을 가졌는지를 확인하는 방법에 대해 설명한다.

권한 확인과 관련한 보안 고려사항들에 관해서는 17.1절을 참조하라.

### 4.3.1. URI 오리진

주어지는 URI에 대한 "오리진"은 사용 체계와 호스트를 소문자로 정규화하고 포트 번호 앞의 0들을 제거하고난 후의 체계, 호스트, 그리고 포트의 삼중 결합으로 이루어진다. 만약 포트가 URI에서 제거됐다면, 사용 체계에 대한 기본 포트가 사용된다. 예를 들어, 다음 URI는

     https://Example.Com/happy.js

다음 오리진을 가질 것이다.

     { "https", "example.com", "443" }

또한 포트가 항상 표시되는 정규화된 URI 접두사로서 기술될 수 있다.

     https://example.com:443

각 오리진은 자신만의 네임스페이스를 정의하고 그 네임스페이스 내의 식별자들이 리소스들로 어떻게 매핑되냐를 제어한다. 차례 차례, 시간이 지남에도 일관적으로, 오리진이 유효한 요청들에 응답하는 방식은, 유저들 URI와 연관지을 의미체계를 결정하고, 해당 의미체계의 유용성은 궁극적으로 이러한 매커니즘들을 유저가 이후에 참조하고 접근할 리소스로 변형시킨다.

두 오리진은 그들이 사용하는 체계, 호스트, 또는 포트가 다르면 구분된다. 구별되는 두 오리진이 같은 개체에 의해 제어되는게 확실할 때 조차, 오리진들 아래의 두 네임스페이스들은 해당 오리진에 대해 권한있는 서버에 의해 명시적으로 별칭이 지정되지 않은 한 구별된다.

Origin is also used within HTML and related Web protocols, beyond the
scope of this document, as described in [RFC6454].

오리진은 또한, 이 문서의 범위를 넘어, [[RFC6454](https://datatracker.ietf.org/doc/html/rfc6454)]에 서술된대로, HTML 그리고 그와 관련된 웹 프로토콜에서도 사용된다.

### 4.3.2. http 오리진들

HTTP가 전송 프로토콜과는 별개이긴 하지만, "http" 체계(4.2.1절)는 권한 요소 내에서 식별된 모든 종류의 호스트의 지정된 포트에서 TCP 연결을 대기하고 있는 오리진 서버를 제어하고 있다면 그게 누구든 상관없이 권한과 연관짓는데 특화돼있다. 이는 권한에 관해 매우 취약한 의식이라할 수 있는데, 이것이 on-path 공격자에게서 안전하지 않을지 모르는 클라이언트 지정 이름 결정 메커니즘과 통신에 의존하기 때문이다. 그럼에도 불구하고, 이는 신뢰되는 환경 내의 일관적인 결정을 위해 "http" 식별자들을 한 오리진 서버에 바인딩하기에는 충분한 최소치다.

만약 호스트 식별자가 IP 주소로 제공된다면, 오리진 서버는 (만약 있다면) 해당 IP 주소의 지정된 TCP 포트의 수신대기자다. 만약 호스트가 등록된 이름이라면, 등록된 이름은, DNS 같은, 적절한 오리진 서버의 주소를 찾기 위해 이름 결정 서비스와 함께 사용되기 위한 간접 식별자다.

"http" URI가 지정된 리소스로의 접근을 요청하는 용도로 사용될 때, 클라이언트는 아마(MAY) 호스트 식별자를 IP 주소로 결정하여, 해당 주소의 지정된 포트로 TCP 연결을 수립하고, 해당 연결을 통해 클랑이언트의 타겟 URI와 매치되는 요청 타겟을 담은 HTTP 요청 메시지를 보내며 접근을 시도할 수도 있을 것이다(7.1절).

만약 서버가 15절에 서술된대로, 그러한 요청에 non-interim HTTP 응답 메시지로 응답한다면, 해당 응답은 클라이언트의 요청에 대해 권위있는 답변으로 간주된다.

그러나, 위 내용이 권위있는 대답을 얻기위한 유일한 방법은 아니고, 권위있는 응답이 항상 필수인 것을 의미하지는 않는다는 것을 명심하라 ([[CACHING](https://www.rfc-editor.org/info/rfc9111)] 참조). 예를 들어, Alt-Svc 헤더 필드 [[ALTSVC](https://www.rfc-editor.org/info/rfc7838)]는 오리진 서버가 마찬가지로 해당 오리진에 대해 권위있는 다른 서비스들을 식별하도록 허용한다. "http"로 식별된 자원들에 대한 접근은 또한 이 문서의 범위를 벗어나는 프로토콜들에 의해서도 제공된다.

### 4.3.3. https 오리진들

"https" 체계(4.2.2절)는 권한을 서버가 식별된 오리진에 대해 클라이언트가 신뢰할 수 있는 것으로 간주하는 인증서에 해당하는 개인 키를 사용할 수 있는가에 기반해 연관짓는다. 클라이언트는 한 인증서를 신뢰가능하다고 간주하기 위해 보통, 미리 준비되거나 설정된 트러스트 앵커로부터 전달되는, 트러스트 체인에 의존한다(4.3.4절).

HTTP/1.1 이하 버전에서는, 클라이언트는 오직 성공적으로 수립된 보안 연결 상에서 통신하고 있을 때만 서버, 구체적으로는 해당 URI 오리진의 호스트에게 권한을 부여한다. 연결 수립과 인증서 검증은 권한의 증거로 사용된다.

HTTP/2와 HTTP/3에서는, 클라이언트는 URI 오리진의 호스트가 서버의 인증서에 나타나는 호스트들 중 어느 하나라도 매치되고 클라이언트가 해당 URI에 대한 해당 호스트에 대해 연결을 열 수 있겠다고 믿는다면 성공적으로 수립된 보안 연결 상에서 통신할 때 권한을 서버에게 부여한다. 실용적으로는, 클라이언트는 해당 오리진의 호스트가 수립된 연결과 같은 서버 IP 주소를 포함하고 있는지 확인하기 위해 DNS 쿼리를 할 것이다. 이 규약은 오리진 서버가 동등한 ORIGIN 프레임을 보냄으로써 제거될 수 있다[[RFC8336](https://datatracker.ietf.org/doc/html/rfc8336)].

요청 타겟의 호스트와 포트 값은 각 HTTP 요청 내에서 전달되고, 오리진을 식별하며 이를 같은 서버에 의해 통제되고 있을 수도 있는 다른 네임스페이스들로부터 구분한다(7.2절). 오리진 인증서의 개인 키 상의 통제와 함께 제공되는 어떠한 서비스든 그들이 해당 "https" 네임스페이스들을 관리하는데 있어 동등한 책임을 갖는 것 또는 최소한 잘못 전달된 것으로 나타나는 요청들을 거부할 준비가 되어있는 것을 확실히 하는 것은 오리진의 책임이다(7.4절).

오리진 서버는 처리할 권한을 가지고 있음에도 특정 타겟 URI들에 대한 요청들은 처리하고 싶지 않을 수도 있다. 예를 들어, 호스트가 다른 포트들에서 별도의 서비스들을 구동할 때(443과 8000 같이), 오리진 서버에서 타겟 URI를 확인하는 것은 필수고(연결에 보안 설정이 된 경우에도) 이는 네트워크 공격자가 한 포트에 대한 연결들을 다른 포트에서 수신되도록 할 수도 있기 때문이다. 타겟 URI 체크의 실패는 그러한 공격자가 한 타겟 URI에 대한 응답("https://example.com/foo"에 대한 응답을)을 겉보기에는 권한을 가진 다른 포트로부터의 응답("https://example.com:8000/foo"의 응답으로)과 교체해버릴 수 있도록 한다.

"https" 체계는 권한을 연관짓기 위해 TCP 그리고 연결된 포트 번호에 의존하지 않는다는 것을 명심하라, 이는 이 둘이 모두 보안 통신의 외부에 있고 그리하여 결정적인 것으로 신뢰될 수 없기 때문이다. 이렇게 해서, HTTP 통신은 TCP를 사용하지 않는 프로토콜들을 포함해, 어떠한 채널이든, 4.2.2절에 정의된대로, 보안 설정이 됐다면 이루어질 수 있다.

"https" URI가 지정된 리소스에 대한 접근을 요청하는 용도로 사용될 때, 클라이언트는 아마(MAY) 호스트 식별자를 IP 주소로 결정하여, 지정된 포트의 해당 주소로 TCP 연결을 수립하고, 성공적으로 비밀성과 무결성 보호를 제공하는 TLS over TCP 연결을 초기화함으로써 end-to-end 연결에 보안을 설정 후, 그 연결 상으로 클라이언트의 타겟 URI와 매치되는 요청 타겟을 포함하는 HTTP 요청 메시지를 보내며 접근을 시도할 수 있을 것이다(7.1절).

만약 서버가, 15절에 정의된대로, 그러한 요청에 non-interim HTTP 응답 메시지로 응답한다면, 해당 응답은 해당 클라이언트의 요청에 대해 권한 있는 응답으로 간주된다.

그러나, 위의 내용이 권한 있는 응답을 얻거나, 권한 있는 응답이 항상 필수라는 것을 의미하지 않음을 명심하라([[CACHING](https://www.rfc-editor.org/info/rfc9111)] 참조).

### 4.3.4. https 인증서 검증

한 URI를 역참조하기 위해 보안 연결을 수립하려면, 클라이언트는 반드시(MUST) 서비스의 신원이 해당 URI의 오리진 서버와 매치되는지를 확인해야 한다. 인증서 검증은 on-path 공격자나 이름 결정 시스템을 제어하는 공격자가 서버로 가장하는 것을 방지하기 위해 행해진다. 이 프로세스는 클라이언트가 트러스트 앵커들의 집합으로 구성될 것을 요구한다.

일반적으로, 클라이언트는 반드시(MUST) [[RFC6125](https://datatracker.ietf.org/doc/html/rfc6125)]의 6절에 정의된 검증 프로세스를 사용해 서비스의 신원을 검증해야 한다. 클라이언트는 반드시(MUST) 서비스의 호스트로부터 참조 신원을 구성해야 한다: 만약 호스트가 IP 주소 그대로라면(4.3.5절), 참조 신원은 IP-ID이고, 그렇지 않다면 호스트는 이름이고 참조 신원은 DNS-ID이다.

CN-ID 타입의 참조 신원을 절대(MUST NOT) 클라이언트에 의해 사용되어서는 안된다. [[RFC6125](https://datatracker.ietf.org/doc/html/rfc6125)]의 6.2.1절에 나와있는대로, CN-ID 타입의 참조 신원은 더 오래된 클라이언트들에 의해 사용될지 모른다.

클라이언트는 특별히 대체된 형태의 서버 신원 검증을 받도록 설정될 수도 있다. 예를 들어, 클라이언트는 서비스가 타겟 URI의 오리진에 매치되는 대신 다른 형태의 특정한 인증서(혹은 외부적으로 정의된 참조 신원과 매칭되는 인증서)를 제시할 것이라는 기대와 함께, 주소와 호스트명이 동적인 서버에 연결하려 할 수도 있다.

특별한 경우들에서, 클라이언트가 간단히 서버의 신원을 무시해버리는게 적절할 수도 있지만, 이는 연결을 액티브 어택에 노출시킨다는 사실이 반드시 이해되어야 한다.

만약 인증서가 타겟 URI의 오리진에 대해 유효하지 않다면, 유저 에이전트는 반드시(MUST) 더 진행하기 전에 유저에게 확인을 받거나(3.5절 차조) bad certificate 에러와 함께 연결을 종료해야 한다. 자동화된 클라이언트들은 반드시(MUST) 적절한 감사 로그(가용하다면)에 에러 로그를 남겨하고 웬만하면(SHOULD) 해당 연결을 종료해야한다(bad certificate 에러와 함께). 자동화된 클라이언트들은 아마(MAY) 이 체크를 비활성화하는 설정을 제공할 수도 있지만, 반드시(MUST) 이를 활성화하는 설정을 제공해야 한다.

### 4.3.5. IP-ID 참조 신원

"https" URI의 "host" 필드에 IP 주소를 그대로 사용해 식별되는 서버는 IP-ID 타입의 참조 신원을 가진다. IP 버전 4 주소는 "IPv4address" ABNF 규칙을 사용하고, IP 버전 6 주소는 "IP-literal" 프로덕션을 "IPv6address" 옵션과 함께 사용한다; [[URI](https://www.rfc-editor.org/info/rfc3986)]의 3.2.2절을 참조하라. IP-ID의 참조 신원은 IP 주소의 디코딩된 바이트들을 포함한다.

IP 버전 4 주소는 4 옥텟이고, IP 버전 6의 주소는 16 옥텟이다. IP-ID의 사용은 어떠한 다른 IP 버전들에 대해서도 정의되어 있지 않다. 인증서 subjectAltname 확장의 iPAddress choice는 명시적으로 IP 버전을 포함하지 않고 그래서 버전들을 구분하기 위해 주소의 길이에 의존한다; [[RFC5280](https://datatracker.ietf.org/doc/html/rfc5280)]의 4.2.1.6절을 참조하라.

IP-ID 타입의 참조 신원은 주소가 인증서의 subjectAltName 확장의 iPAddress 값과 동일하면 매치된다.

## 5. 필드

HTTP는 미리 등록된 키 네임스페이스와 함께 확장 가능한 이름/값 쌍 형태의 데이터를 제공하기 위해 "필드"를 사용한다. 필드는 메시지의 헤더와 트레일러 섹션 내에서 보내지고 수신된다(6절).

### 5.1. 필드 이름

필드 이름은 그 이름에 의해 정의되는 의미를 갖도록 해당하는 필드 값을 라벨링한다. 예를 들어, Date 헤더 필드는 6.6.1절에 그것이 나타나는 메시지의 발생 시각 타임스탬프를 포함한다고 정의돼있다.

     field-name     = token

필드 이름들은 대소문자를 구분하지 않고 "Hypertext Transfer Protocol (HTTP) Field Name Registry" 내에 등록되어 있어야 한다; 16.3.1절을 참조하라.

필드의 해석은 같은 HTTP 버전의 마이너 버전 사이에서는 바뀌지 않지만, 그러한 필드가 없는 경우 수신자의 기본 행동은 바뀔 수 있다. 따로 명시되지 않는 한, 필드들은 모든 HTTP 버전들에 대해 정의된다. 특히, Host와 Connection 필드는 HTTP/1.1을 준수한다고 알리는지 여부와 관계없이 모든 HTTP 구현체들에 의해 인식돼야 한다.

만약 정의된 의미가 그들을 인식하지 못한 수신자들에 의해 안전하게 무시될 수 있도록 되어있다면 새로운 필드들은 프로토콜 버전을 수정하지 않고도 도입될 수 있다; 16.3절 참조.

필드 이름이 Connection 헤더 필드에 나열되어 있거나(7.6.1절) 프록시가 그런 필드들을 특별히 블락하도록, 그렇지 않으면 변형하도록 설정되어 있지 않다면 프록시는 반드시(MUST) 인식되지 않은 헤더 필드들을 포워딩해야 한다. 다른 수신자들은 웬만하면(SHOULD) 인식되지 않은 헤더와 트레일러 필드들을 무시하는게 좋다. 이 요구사항을 준수하는 것은 HTTP의 기능이 배포된 중개자들을 업데이트 하거나 삭제할 필요 없이 확장되는 것을 허용한다.

### 5.2. 필드 라인들과 조합된 필드 값

필드 섹션들은 아무 개수의 "필드 라인"들로 구성되고, 각각은 해당 필드를 식별하는 "필드 이름"(5.1절 참조), 그리고 필드의 해당 인스턴스에 대한 데이터를 전달하는 "필드 라인 값"과 함께 한다.

필드 이름이 한 섹션에서 오직 한번만 등장할 때, 해당 필드를 위한 조합된 "필드 값"은 해당하는 필드 라인 값으로 이루어진다. 필드 이름이 한 섹션 내에서 반복될 때는, 그것의 조합된 필드 값은 섹션 내의 해당하는 필드 라인 값들로 이루어지며, 순서에 따라 연결되고, 각 필드 라인 값은 콤마로 구분된다.

예를 들어, 다음 섹션은:

     Example-Field: Foo, Bar
     Example-Field: Baz

두 필드 라인을 포함하며, 둘 다 필드 이름이 "Example-Field"이다. 첫번째 필드 라인은 필드 라인 값으로 "Foo, Bar"를 가진 반면, 두번째 필드 라인 값은 "Baz"다. "Example-Field"의 필드 값은 리스트 "Foo, Bar, Baz"가 된다.

### 5.3. 필드 순서

수신자는 아마(MAY), 메시지의 의미를 바꾸는 일 없이, 후속 필드 라인 값을 첫번째 필드 라인 값에 순서대로 덧붙이고, 콤마(",")와 선택적공백(OWS, 5.6.3절에 정의)에 의해 분리되도록 하여 같은 필드 이름을 가진 필드 섹션 내의 여러 필드 라인들을 하나의 필드라인으로 조합하려 할 수도 있을 것이다. 일관성을 위해, 콤마 SP를 사용하라.

수신한 같은 이름의 필드 라인들의 순서는 이리하여 필드 값 해석에 아주 중요하다; 프록시는 절대(MUST NOT) 이 필드 라인 값들의 순서를 메시지를 포워딩할 때 변경해서는 안된다.

이는, 아래의 잘 알려진 예외는 별개로, 발신자는 해당 필드의 정의에서 다중 필드 라인 값이 콤마로 구분되는 리스트로 재조합되도록 허용되지 않는다면(즉, 최소 하나의 필드 정의의 대안이 5.6.1절에 정의된 #(values)의 ABNF룰과 같은, 콤마로 구분되는 리스트를 허용하지 않으면), 절대(MUST NOT) 메시지 내에서 같은 이름의 여러 필드 라인들을 생성하거나 같은 이름이 이미 존재할 때 그 이름의 필드 라인을 덧붙여서는 안됨을 의미한다.

_Note:_ 실무적으로, "Set-Cookie" 헤더 필드 ([[COOKIE](https://www.rfc-editor.org/info/rfc6265)]) 종종 응답 메시지에서 여러 필드 라인에거쳐 나타나고, 리스트 구문을 사용하지 않고, 위의 같은 필드 이름의 여러 필드 라인들에 대한 요구사항들을 위반한다. 그것이 하나의 필드 값으로 조합될 수 없기 때문에, 수신자들은 "Set-Cookie"를 필드들을 처리하며 특별한 케이스로 다뤄야 한다. (자세한 건 [[Kri2001](http://arxiv.org/abs/cs.SE/0105018)의 부록 A.2.3을 참조하라)

한 섹션에서 수신한 다른 이름을 가진 필드 라인들의 순서는 그리 중요하지 않다. 그러나, 구현체들이 가능한 빨리 메시지를 핸들링할지 말지 결정할 수 있도록, 요청의 Host나 응답의 Date 같은, 추가적인 제어 데이터를 포함하는 헤더 필드들을 먼저 보내는 것이 좋은 관행이다.

서버는 전체 요청 헤더 섹션을 수신할 때 까지 절대(MUST NOT) 요청을 타겟 리소스에 적용해서는 안되는데, 이는 나중의 헤더 필드 라인들이 조건부이거나, 인증 자격증명이거나, 혹은 고의로 요청을 처리하는데 영향을 줄 수 있는 중복 헤더 필드들을 오도한 것일 수 있기 때문이다.

### 5.4. 필드 제한

HTTP 자체는 2절에 정의된대로, 필드 라인, 필드 값의 길이, 혹은 헤더나 트레일러 섹션 전체의 길이에 대해 미리 정의된 제한을 두지 않는다. 개별적인 길이들에 대한 다양한 애드 혹 제한들은 실무에서 찾아볼 수 있는데, 종종 특정 필드의 의미체계에 의존한다.

처리하기에 기대보다 큰 헤더 필드 라인, 필드 값, 혹은 필드들의 집합을 수신한 서버는 반드시(MUST) 적절한 4xx(클라이언트 에러) 상태 코드로 응답해야 한다. 그런 헤더 필드들을 무시하는 행위는 요청 스머글링 공격에 대한 서버의 취약성을 증가시킬 것이다([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)] 11.2절).

클라이언트는 아마(MAY) 클라이언트가 처리하길 바라는 것보다 큰 필드 라인들을 수신할 경우 필드 의미체계가 버려진 값(들)을 메시지 프레이밍이나 응답 의미체계를 바꾸지 않고 안전하게 무시할 수 있다면 버리거나 자를 수 있을 것이다.

### 5.5. 필드 값들

HTTP 필드 값들은 해당 필드의 문법 규칙에 의해 정의된 형태의 문자들의 시퀀스로 구성된다. 각 필드의 문법 규칙은 보통 ABNF([[RFC5234](https://datatracker.ietf.org/doc/html/rfc5234)])를 사용해 정의된다.

     field-value    = *field-content
     field-content  = field-vchar
                      [ 1*( SP / HTAB / field-vchar ) field-vchar ]
     field-vchar    = VCHAR / obs-text
     obs-text       = %x80-FF

필드 값은 선행하거나 후행하는 공백을 포함하지 않는다. 특정 HTTP 버전이 그러한 공백이 메시지에 나타나도록 허용할 때는, 필드 파싱 구현체는 반드시(MUST) 필드 값을 평가하기 전에 그러한 공백을 제외해야 한다.

필드 값들은 보통 [US-ASCII] 문자들의 범위로 제약된다. 더 큰 범위의 문자들을 필요로 하는 필드들은 [[RFC8187](https://www.rfc-editor.org/info/rfc8187)]에 정의된 것과 같이, 인코딩을 활용할 수 있다. 역사적으로, HTTP는 [ISO-8859-1] 캐릭터셋의 텍스트를 필드 콘텐츠로 허용했고, 다른 캐릭터셋들은 오직 [[RFC2047](ttps://www.rfc-editor.org/info/rfc2047)] 인코딩의 사용을 통해서만 지원했다. 새롭게 정의된 필드들을 위한 사양들은 웬만하면(SHOULD) 그것들의 값들을 가시적인 US-ASCII 옥텟(VCHAR), SP, 그리고 HTAB으로 제한해야 한다. 수신자는 웬만하면(SHOULD) 필드 콘텐츠의 다른 허용된 옥텟들(즉, obs-text)을 불투명한 데이터로 다뤄야 한다.

CR, LF, 혹은 NUL 문자들을 포함하는 필드 값들은 유효하지 않고 위험한데, 구현체들이 그 문자들을 다양한 방식으로 파싱하고 해석할 수 있기 때문이다; 필드 값 내에 CR, LF, 혹은 NUL을 수신한 자는 반드시(MUST) 해당 메시지에 대한 추가적인 처리나 포워딩을 하기 전에 메시지를 거부하거나 그 문자들 각각을 SP로 대체해야 한다. 다른 CTL 문자들을 포함하는 필드 값들 또한 유효하지 않다; 그러나, 수신자들은 아마(MAY) 그것들이 안전한 콘텍스트 내에서 등장할 때에는 견고성을 위해 그러한 문자들을 유지할 수도 있을 것이다(예를 들어, 어떠한 다운스트림 HTTP 파서에 의해서도 처리되지 않을 애플리케이션 특화 인용 문자열).

필드 값으로 오직 하나의 단독 멤버만을 허용하는 필드들은 "싱글톤 필드"라고 불린다.

필드 값으로 여러 멤버들을 허용하는 필드들은 "리스트-기반 필드"라고 불린다. 5.6.1절의 리스트 연산자 확장은 여러 멤버들을 포함할 수 있는 필드 값들을 정의하기 위한 공통적인 표기법으로 사용된다.

콤마(",")는 멤버들 간의 구분자로 쓰이기 때문에, 그것들이 멤버 내의 데이터로 허용되는 경우에는 주의하여 다뤄져야 한다. 이는 리스트-기반과 싱글톤 필드 양쪽 모두에 해당되는데, 이는 싱글톤 필드가 잘못되어 여러 멤버들과 함께 보내질 수 있기 때문이고 그러한 에러를 탐지하는 것은 상호운영성을 향상시킨다. HTTP-date나 URI-reference 요소 내에서와 같이, 멤버 내에 콤마가 포함될 것으로 예상되는 필드들은 잠재적인 리스트 구분자로부터 데이터 내의 어떤 콤마든 구분하기 위해 해당 요소 주변에서 구분자와 함께 정의돼야 한다.

예를 들어, 텍스트로 된 날짜와 URI(둘 다 콤마를 포함할 수도 있는)는 리스트-기반 필드 값들 내에서 다음과 같이 안전하게 운반될 수 있다:

     Example-URIs: "http://example.com/a.html,foo",
     "http://without-a-comma.example.com/"
     Example-Dates: "Sat, 04 May 1996", "Wed, 14 Sep 2005"

쌍따옴표 구분자는 거의 항상 quoted-string 프로덕션(5.6.4절)과 함께 사용된다는 것을 주목하라; 쌍따옴표들 내에서 다른 구문을 사용하는 것은 불필요한 혼동을 일으킬 가능성이 높다.

많은 필드들(8.3절에 정의된, Content-Type 같은)은 파라미터 값으로 따옴표 없는 경우(token) 그리고 따옴표 있는 경우(quoted-string)의 구문을 허용하는 파라미터들을 위해 공통적인 구문을 사용한다(5.6.6절). 공통적인 구문의 사용은 수신자들로 하여금 기존의 파서 구성 요소들을 재사용할 수 있도록 한다. 두 형태 모두 허용할 때는, 파라미터 값의 의미는 token으로 받았든 quoted string으로 받았든 동일해야 한다.

_Note:_ 필드 값 구문을 정의하기 위해, 이 사양서는 해당 필드의 값에 허용된 문법을 정의하기 위해 해당 필드 이름을 따른 ABNF 룰을 사용한다(해당 값이 기반 메시지 구문에서 추출되고 여러 인스턴스들이 list로 조합된 후).

### 5.6. 필드 값들을 정의하기 위한 공통 규칙들

#### 5.6.1. Lists (#rule ABNF Extension)

[[RFC5234](https://www.rfc-editor.org/info/rfc5234)]의 ABNF 규칙들에대한 #rule 확장은 몇몇 리스트-기반 필드 값들의 정의들에서 가독성을 향상시키기 위해 사용된다.

"#"이라는 구문은, "\*"와 비슷하게, 요소들의 콤마로 구분된 리스트들을 정의하기 위해 정의됐다. 전체 형태는 최소 <n>과 최대 <m> 개의, 각각이 하나의 콤마(",") 그리고 선택적 공백(5.6.3절에 정의된, OWS)에 의해 구분되는 요소들을 나타내는 "<n>#<m>element" 가 된다.

##### 5.6.1.1. 발신자 요구사항들

리스트 구문을 사용하는 어떠한 프로덕션에서든, 발신자는 절대(MUST NOT) 빈 리스트 요소들을 생성해서는 안된다. 다시 말해, 발신자는 다음 구문을 만족하는 리스트들을 생성해야만 한다:

     1#element => element *( OWS "," OWS element )

그리고:

     #element => [ 1#element ]

그리고 n >= 1 과 m > 1의 경우:

     <n>#<m>element => element <n-1>*<m-1>( OWS "," OWS element )

부록 A는 리스트 구문이 확장된 후의 발신자들을 위한 ABNF 모음을 보여준다.

##### 5.6.1.2. 수신자 요구사항들

빈 요소들은 존재하는 요소들의 수를 세는데 포함되지 않는다. 수신자는 반드시(MUST) 합리적인 수의 빈 리스트 요소들을 파싱하고 무시해야 한다: 발신자들이 값들을 합치는 흔한 실수들을 처리하는 것으로 충분하지만, 그것들이 denial-of-service 메커니즘으로 사용될 수 있을 정도로 많아서는 안된다. 다시 말해, 수신자는 반드시(MUST) 다음 구문을 만족하는 리스트들을 받아들어야 한다:

     #element => [ element ] *( OWS "," OWS [ element ] )

빈 리스트 요소들의 잠재적인 존재 가능성 때문에, RFC 5234 ABNF는 리스트 요소들의 카디널리티를 강제할 수 없고, 결과적으로 모든 케이스들은 카디널리티가 지정되지 않은 것처럼 매핑된다는 것을 명심하라.

예를 들어, 주어진 이러한 ABNF 프로덕션들이 있다:

     example-list      = 1#example-list-elmt
     example-list-elmt = token ; see Section 5.6.2

Then the following are valid values for example-list (not including
the double quotes, which are present for delimitation only):

또 다음은 example-list에 대해 유효한 값들이다(쌍따옴표들은 오로지 구분을 위해서 존재하니까 제외하고):

     "foo,bar"
     "foo ,bar,"
     "foo , ,bar,charlie"

반대로, 다음 값들은, 최소 하나의 비어이씾 않은 요소가 example-list 프로덕션에 의해 요구되기 때문에, 유효하지 않다:

     ""
     ","
     ",   ,"

#### 5.6.2. 토큰

토큰은 공백이나 구분자를 포함하지 않는 짧은 텍스트 형식의 식별자를 가리킨다.

     token          = 1*tchar

     tchar          = "!" / "#" / "$" / "%" / "&" / "'" / "*"
                    / "+" / "-" / "." / "^" / "_" / "`" / "|" / "~"
                    / DIGIT / ALPHA
                    ; any VCHAR, except delimiters

많은 HTTP 필드 값들은 공백이나 특정 구분 문자들에 의해 구분되는, 공통적인 구문 구성 요소들을 사용해 정의된다. 구분자들은 US-ASCII의 가시적 문자들의 집합에서 토큰 내에 허용되지 않는 것들(DQUOTE 그리고 "(),/:;<=>?@[\]{}:) 중 선택된다.

#### 5.6.3. 공백

이 사양서는 ㅅ헤 가지 규칙을 사용해 선형 공백의 사용을 나타낸다: OWS(선택적 공백), RWS(필수 공백), 그리고 BWS("나쁜" 공백).

OWS 규칙은 0개 이상의 선형 공백 옥텟이 나타날지 모르는 곳에서 사용된다. 선택적 공백이 가독성 향상을 위해 선호되는 프로토콜 요소들에 대해서, 발신자는 웬만하면(SHOULD) 선택적 공백을 단일 SP로 생성해야 한다; 그게 아니면, 발신자는 내부의 메시지 필터링 중에 유효하지 않거나 원하지 않는 프로토콜 요소들을 덮어쓸 필요가 있을 때를 제외하고는 웬만해서는(SHOULD NOT) 선택적 공백을 생성해서는 안된다.

RWS 규칙은 하나 이상의 선형 공백 옥텟이 필드 토콘들을 구분하기 위해 요구될 때 사용된다. 발신자는 웬만하면(SHOULD) RWS를 단일 SP로 생성해야 한다.

OWS와 RWS는 단일 SP로써 동일한 의미를 가진다. OWS나 RWS로 정의된 것으로 알려진 콘텐츠는 아마(MAY) 어느 것이든 해석되기 전이나 메시지를 다운스트림으로 포워딩 하기 전에 단일 SP로 대체될 수 있을 것이다.

BWS 규칙은 구문이 선택적 공백을 오직 역사적 이유에서만 허용할 때 사용된다. 발신자는 절대(MUST NOT) 메시지에 BWS를 생성해서는 안된다. 수신자는 해당 프로토콜 요소를 해석하기 전 반드시(MUST) 그러한 나쁜 공백을 파싱하고 삭제해야 한다.

BWS는 의미가 없다. BWS로 정의된 것으로 알려진 어떤 콘텐츠든 아마(MAY) 해석되거나 메시지를 다운스트림으로 포워딩 하기 전 삭제될 수 있을 것이다.

     OWS            = *( SP / HTAB )
                    ; optional whitespace
     RWS            = 1*( SP / HTAB )
                    ; required whitespace
     BWS            = OWS
                    ; "bad" whitespace

#### 5.6.4. 인용된 문자열

한 텍스트 문자열이 인용 부홀호 묶여 있으면 그것은 단일 값으로 파싱된다.

     quoted-string  = DQUOTE *( qdtext / quoted-pair ) DQUOTE
     qdtext         = HTAB / SP / %x21 / %x23-5B / %x5D-7E / obs-text

역슬래시 옥텟("\")은 quoted-string과 comment 구조 내에서 단일-옥텟 인용 메커니즘으로 사용될 수 있다. quoted-string 값을 처리하는 수신자들은 반드시(MUST) quoted-pair가 역슬래시를 따르는 옥텟으로 대체된 것 처럼 처리해야 한다.

     quoted-pair    = "\" ( HTAB / SP / VCHAR / obs-text )

발신자는 해당 문자열 내에 있는 DQUOTE와 역슬래시 옥텟들을 인용해야 하는 경우를 제외하고는 웬만해서는(SHOULD NOT) quoted-string 내에 quoted-pair를 생성해서는 안된다. 발신자는 코멘트 내에 있는 괄호 ["(" 와 ")"]와 역슬래시 옥텟들을 인용해야 하는 경우를 제외하고는 웬만해서는(SHOULD NOT) 코멘트 내에 quoted-pair를 생성해서는 안된다.

#### 5.6.5. 코멘트

코멘트는 일부 HTTP 필드에 코멘트 텍스트를 괄호로 감싸므로써 포함될 수 있다. 코멘트들은 "comment"를 그 필드 값 정의로 포함하고 있는 필드들에만 허용된다.

     comment        = "(" *( ctext / quoted-pair / comment ) ")"
     ctext          = HTAB / SP / %x21-27 / %x2A-5B / %x5D-7E / obs-text

#### 5.6.6. 파라미터

파라미터는 이름/값 쌍의 인스턴스들이다; 그것들은 종종 필드 값들에서 아이템에 보조 정보를 추가하기 위한 공통 구문으로 사용된다. 각 파라미터는 직전의 세미콜론에 의해 구분된다.

     parameters      = *( OWS ";" OWS [ parameter ] )
     parameter       = parameter-name "=" parameter-value
     parameter-name  = token
     parameter-value = ( token / quoted-string )

파라미터 이름은 대소문자를 구분하지 않는다. 파라미터 값은 파라미터 이름의 의미에 따라, 대소문자를 구분할 수도 안 할 수도 있다. 파라미터 그리고 일부 동등한 형태에 대한 예시는 미디어 타입(8.3.1절)과 Accept 헤더 필드(12.5.1절)에서 볼 수 있다.

토큰 프로덕션과 매치되는 한 파라미터 값은 토큰으로써 혹은 quoted-string 내에서 전송될 수 있다. 인용된 것과 안된 값들은 동등하다.

_Note:_ 파라미터는 "=" 문자 주변에 공백("나쁜" 공백 조차)을 허용하지 않는다.

#### 5.6.7. Date/Time 포맷

1995년 이전에는, 타임스탬프를 전달하기 위해 서버들에 의해 사용되던 세 가지 다른 형태들이 존재했었다. 옛 구현들과의 호환성을 위해, 세 가지 모두 여기 정의돼 있다. 선호되는 형태는 Internet Message Format[[RFC5322](https://www.rfc-editor.org/info/rfc5322)]에서 사용된 날짜와 시간에 관한 사양의 고정-길이와 싱글-존 서브셋이다.

     HTTP-date    = IMF-fixdate / obs-date

선호되는 형태의 예시

     Sun, 06 Nov 1994 08:49:37 GMT    ; IMF-fixdate

나머지 두 구식 형태의 예시

     Sunday, 06-Nov-94 08:49:37 GMT   ; obsolete RFC 850 format
     Sun Nov  6 08:49:37 1994         ; ANSI C's asctime() format

HTTP 필드의 타임스탬프 값을 파싱하는 수신자는 반드시(MUST) 세 가지 모든 HTTP-date 포맷들을 받아들여야 한다. 발신자가 HTTP-data로 정의된 타임스탬프를 하나 이상 포함하는 필드를 생성할 때, 발신자는 반드시(MUST) IMF-fixadate 형태로 그 타임스탬프들을 생성해야 한다.

HTTP-data 값은 시간을 Coordinated Universal Time(UTC)의 인스턴스로 표현한다. 처음 두 가지 포맷들은 UTC를 Greenwich Mean Time의 세-글자 약자, "GMT"로 표현하고, 이는 UTC라는 이름의 전신이다; asctime 포맷의 값들은 UTC 형태로 가정된다.

"clock"은 현재 순간에 대한 합리적인 UTC 근사를 제공할 수 있는 구현체를 말한다. clock 구현체는 NTP([[RFC5905](https://www.rfc-editor.org/rfc/rfc5905.html)]), 혹은 어떤 유사한 프로토콜을 사용해, UTC와 동기화되어야 한다.

선호되는 형태:

     IMF-fixdate  = day-name "," SP date1 SP time-of-day SP GMT
     ; fixed length/zone/capitalization subset of the format
     ; see Section 3.3 of [RFC5322]

     day-name     = %s"Mon" / %s"Tue" / %s"Wed"
                  / %s"Thu" / %s"Fri" / %s"Sat" / %s"Sun"

     date1        = day SP month SP year
                  ; e.g., 02 Jun 1982

     day          = 2DIGIT
     month        = %s"Jan" / %s"Feb" / %s"Mar" / %s"Apr"
                  / %s"May" / %s"Jun" / %s"Jul" / %s"Aug"
                  / %s"Sep" / %s"Oct" / %s"Nov" / %s"Dec"
     year         = 4DIGIT

     GMT          = %s"GMT"

     time-of-day  = hour ":" minute ":" second
                  ; 00:00:00 - 23:59:60 (leap second)

     hour         = 2DIGIT
     minute       = 2DIGIT
     second       = 2DIGIT

폐기된 형태:

     obs-date     = rfc850-date / asctime-date

     rfc850-date  = day-name-l "," SP date2 SP time-of-day SP GMT
     date2        = day "-" month "-" 2DIGIT
                  ; e.g., 02-Jun-82

     day-name-l   = %s"Monday" / %s"Tuesday" / %s"Wednesday"
                  / %s"Thursday" / %s"Friday" / %s"Saturday"
                  / %s"Sunday"

     asctime-date = day-name SP date3 SP time-of-day SP year
     date3        = month SP ( 2DIGIT / ( SP 1DIGIT ))
                  ; e.g., Jun  2

HTTP-date는 대소문자를 구분한다. [[CACHING](https://www.rfc-editor.org/info/rfc9111)]의 4.2절은 캐시 수신자들을 위해 이를 완화한다는 것을 주목하라.

발신자는 절대(MUST NOT) 구문의 SP와 같이 명시적으로 포함된 것을 넘어서 HTTP-data에 추가적인 공백을 생성해서는 안된다. day-name, day, month, year과 time-of-day의 의미는 Internet Message Format 구조들을 위해 각각에 대응되는 이름들로 정의된 것들([[RFC5322](https://www.rfc-editor.org/info/rfc5322)], 3.3절)과 동일하다.

rfc850-date 포맷, 즉 숫자 두 개로 연도를 표현하는 형태의 타임스탬프 값의 수신자들은, 반드시(MUST) 50년 보다 미래를 가리키는 것으로 보이는 타임스탬프를 가장 최근 과거 연도 중 마지막 두 수가 같은 연도를 표현하는 것으로 해석해야 한다.

타임스탬프 값의 수신자들은 필드 정의에 의해 딱히 제한된 게 아니라면 타임스탬프를 파싱하는데 있어 강력하게 하도록 권장된다. 예를 들어, 메시지들은 종종 Internet Message Format에 의해 정의된 어떤 날짜와 시간 사양이든 생설할 수도 있는 HTTP가 아닌 소스로 부터 HTTP를 통해 포워딩된다.

_Note:_ 타임스탬프 포맷들을 위한 HTTP 요구사항들은 그것들이 프로토콜 스트림 내에서 사용될 때만 적용된다. 구현체들까지 사용자에게 보여주거나, 요청 로깅 등등을 위해 그러한 포맷들을 사용할 필요는 없다.

## 6. 메시지 추상화

HTTP의 각 메이저 버전은 메시지 통신을 위해 자체적인 구문을 정의한다. 이 절은 HTTP 메시지들의 특징, 공통 구조, 그리고 의미를 전달하기 위한 수용능력에 대한 일반화에 기반한 HTTP 메시지의 추상 데이터 타입을 정의한다. 이 추상화는 HTTP 버전과 관계없이 발신자들과 수신자들에 대한 요구사항을 정의하기 위해 사용되며, 그렇게해서 한 메시지는 자체적인 의미를 바꿀 필요 없이 다른 버전들을 통해서도 전달될 수 있게 된다.

"메시지"는 다음의 것들로 구성된다:

- 메시지를 설명하고 라우팅하기 위한 제어 데이터,

- 해당 제어 데이터를 확장하고 발신자, 메시지, 콘텐츠, 혹은 콘텍스트에 관한 추가적인 정보를 전달하기 위한 이름/값 쌍의 헤더 룩업 테이블,

- 잠재적으로 무한한 콘텐츠 스트림, 그리고

- 콘텐츠를 보내는 동안 얻어진 정보를 전달하기 위한 이름 값 쌍의 트레일러 룩업 테이블.

프레이밍과 제어 데이터가 처음 전송되고, 그 뒤를 헤더 테이블을 위한 필드들을 포함하는 헤더 섹션이 뒤따른다. 메시지가 콘텐츠를 포함할 때는, 해당 콘텐츠는 헤더 섹션 이후에 보내진다. 잠재적으로 트레일러 테이블을 위한 필드들을 포함하는 트레일러 섹션이 뒤따를 수 있다.

메시지들은 하나의 스트림으로써 처리되기를 기대되는데, 여기서 스트림과 스트림의 계속되는 처리의 목적은 읽어지는 동안 드러난다. 이리하여, 제어 데이터는 수신자가 즉시 알 필요가 있는 것을 기술하고, 헤더 필드들은 콘텐츠를 수신하기 전에 알 필요가 있는 것을 기술하며, 콘텐츠(존재할 때)는 아마 수신자가 메시지 의미를 충족시키기 위해 원하거나 필요로 하는 것을 포함할 것이며, 그리고 트레일러 필드들은 콘텐츠를 보내기 전에는 알 수 없었던 선택적인 메타데이터를 제공한다.

메시지들은 "자기-설명적"이도록 의도된다: 수신자가 메시지에 대해 알아야 할 모든 것은 발신자의 현재 애플리케이션 상태(이전 메시지들에 의해 형성된)에 대한 이해를 요구하지 않으면서도, 전송 중에 압축되거나 생략된 부분들을 디코딩하거나 재구성하고 난 후, 메시지 그 자체를 보는 것만으로 결정될 수 있다. 그러나, 클라이언트는 반드시(MUST) 해당하는 응답을 파싱, 해석, 혹은 캐싱할 때 요청에 대한 지식을 유지해야 한다. 예를 들어, HEAD 메소드에 대한 응답들은 GET에 대한 응답의 시작인 것 처럼 보이지만 같은 방식으로 파싱될 수는 없다.

이 메시지 추상화는 일부 버전들에서는 존재하지 않을지 모르는 많은 HTTP 버전들에 걸친 일반화라는 것을 명심하라. 예를 들어, 트레일러들은 콘텐츠를 따르는 트레일러 섹션으로써 HTTP/1.1 chunked transfer coding 내에서 도입되었다. 그것과 동등한 기능은 HTTP/2와 HTTP/3에서 각 스트림을 종료하는 헤더 블락 내에 존재한다.

### 6.1. 프레이밍과 완전성

메시지 프레이밍은 어떻게 각 메시지들이 시작하고 끝나는지를 나타내고, 각 메시지가 같은 연결 상의 다른 메시지들이나 노이즈로 부터 구분될 수 있도록 한다. HTTP의 각 메이저 버전들은 자체적인 프레이밍 메커니즘을 정의한다.

HTTP/0.9와 HTTP/1.0의 이른 배포들은 응답을 끝내기 위해 기반이 되는 연결을 중단하는 방식을 사용했다. 하위 호환성을 위해, 이러한 암묵적인 프레이밍은 HTTP/1.1에서도 허용됐다. 그러나, 암묵적인 프레이밍은 연결이 일찍 닫히는 경우에 불완전한 응답을 구분하는데 실패할 수 있다. 그러한 이유롸ㅡ 거의 모든 현대 구현체들은 길이가 제한된 메시지 데이터의 시퀀스의 형태로 명시적인 프레이밍을 사용한다.

한 메시지는 해당 프레이밍에 의해 지정되는 모든 옥텟들이 가용할 때 "완전하다"고 간주된다. 주목할 것은, 명시적 프레이밍이 사용되지 않을 때, 기반 연결의 종료로 인해 끝나는 응답 메시지는 해당 메시지가 불완전한 응답과 구분되지 않을 수 있더라도, 전송 계층 에러가 해당 메시지가 완전하지 않다는 것을 나타내지 않는 한 완전한 것으로 간주된다는 것이다.

### 6.2. 제어 데이터

메시지는 스스로의 주된 목적을 기술하는 제어 데이터로 시작한다. 요청 메시지 제어 데이타는 요청 메소드(9절), 요청 타겟(7,1절), 그리고 프로토콜 버전(2.5절)을 포함한다. 응답 메시지 제어 데이터는 상태 코드(15절), 선택적 reason phrase, 그리고 프로토콜 버전을 포함한다.

HTTP/1.1([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)])과 그 이전 버전에서, 제어 데이터는 메시지의 첫번째 라인으로 보내진다. HTTP/2([[HTTP/2](https://www.rfc-editor.org/info/rfc9113)])와 HTTP/3([[HTTP/3](https://www.rfc-editor.org/info/rfc9114)])에서, 제어 데이터는 예약된 이름 접두사(예를 들어, ":authority")와 함께 슈도-헤더 필드들로 보내진다.

모든 HTTP 메시지는 프로토콜 버전을 가진다. 사용되고 있는 버전에 따라, 이는 메시지 내에서 명시적으로 식별될 수도 있고 메시지가 수신되는 연결에 의해 추론될 수도 있다. 수신자들은 해당 버전 정보를 제한사항이나 해당 발신자와의 추후 통신에 대한 잠재성을 결정하는데 사용한다.

메시지가 중개자에 의해 포워딩됐을 때, 프로토콜 버전은 중개자에 의해 사용된 버전을 반영하도록 업데이트 된다. Via 헤더 필드(7.6.3절)은 포워드된 메시지 내에서 업스트림 프로토콜 정보를 전달하기 위해 사용된다.

클라이언트는 웬만하면(SHOULD) 클라이언트가 준수하면서, 알고 있다면 서버가 제공하는 가장 높은 버전보다 메이저 버전이 높지 않은 것 중 가장 높은 버전과 동등한 요청 버전을 보내야 한다. 클라이언트는 절대(MUST NOT) 준수하지 않는 버전을 보내서는 안된다.

클라이언트는 오직 서버가 더 높은 요청 버전을 부적합하게 처리하는지 최소한 한번은 정상적인 요청을 해보고 응답 상태 코드나 헤더필드(예를 들어, Server)로 부터 결정한 후에, 서버가 HTTP 사양을 부적합하게 구현한 것을 알게 된다면, 아마(MAY) 더 낮은 요청 버전을 보낼 수 있을 것이다.

서버는 웬만하면(SHOULD) 서버가 준수하는 요청에서 수신한 것 이하의 메이저 버전들 중 가장 높은 버전과 동등한 응답 버전을 보내야 한다. 서버는 절대(MUST NOT) 준수하지 않는 버전을 보내서는 안된다. 서버는 원한다면 505(HTTP Version Not Supported) 응답을 보내서, 어떤 이유에서든, 클라이언트의 메이저 프로토콜 버전에 대한 서비스를 거부할 수 있다.

메이저 버전은 구현됐고 마이너 버전은 구현한 것보다 더 높은 메시지를 수신하는 수신자는 웬만하면(SHOULD) 해당 메시지를 수신자가 준수하는 해당 메이저 버전 내에서 가장 높은 마이너 버전인 것 처럼 처리해야 한다. 수신자는 더 높은 마이너 버전의 메시지가, 더 높은 버전에 대한 지원을 아직 나타내지 않았을 때 보내진 경우, 같은 메이저 버전의 어떤 구현체든 해당 메시작 안전하게 처리되기에 충분히 하위 호환성을 가진다고 가정할 수 있다.

### 6.3. 헤더 필드

콘텐츠보다 빨리 발신되거나 수신된 필드들(5절)은 "헤더 필드"라고 불린다(혹은 구어체 적으로 그냥 "헤더").

메시지의 "헤더 섹션"은 헤더 필드 라인들의 시퀀스로 구성된다. 각 헤더 필드는 메시지 의미를 수정하거나 확장할 수도, 발신자에 대해 기술할 수도, 콘텐츠를 정의할 수도, 혹은 추가적인 콘텍스트를 제공할 수도 있다.

_Note:_ 우리는 오직 헤더 섹션에서 보내지는 것이 허용됐을 때만 명명된 필드들을 특별히 "헤더 필드"라고 부른다.

### 6.4. 콘텐츠

HTTP 메시지는 메시지 "콘텐츠"로 종종 완전한 혹은 부분적인 표현을 전송한다: 메시지 프레이밍에 의해 묘사된대로, 헤더 섹션 이후에 보내지는 옥텟들의 스트림.

콘텐츠에 대한 이 추상적인 정의는 메시지 프레이밍으로부터 추출된 후의 데이터를 반영한다. 예를 들어, HTTP/1.1 메시지 바디([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 6절)는 chunked transfer coding -- 데이터 청크들의 시퀀스, 길이가 0인 하나의 청크, 그리고 트레일러 섹션 -- 으로 인코딩된 데이터 스트림으로 구성될 수 있는 반면 같은 메시지의 콘텐츠는 오직 transfer coding이 디코딩되고 난 후의 데이터 스트림만 포함한다; 청크 길이, 청크 프레이밍 구문, 혹은 트레일러 필드를 포함하지 않는다(6.5절).

_Note:_ 일부 필드 이름들은 "Content-"라는 접두사를 가진다. 이는 비공식적인 관행이다; 이 필드들 중 일부가, 위에 정의된 것 처럼 메시지의 콘텐츠를 가리키는 반면, 이외의 것들은 선택된 표현(3.2절)으로 범위가 지정된다. 명확히 하기 위해 각 필드의 정의를 참고해라.

#### 6.4.1. 콘텐츠 의미체계

요청에 있는 콘텐츠의 목적은 메소드 의미 체계에 의해 정의된다(9절).

예를 들어, PUT 요청의 콘텐츠에 있는 표현(9.3.4절)은 요청이 적용되고 난 후에 기대되는 타겟 리소스의 상태를 나타내며, 반면에 POST 요청의 콘텐츠에 있는 표현(9.3.3절)은 타겟 리소스에 의해 처리될 정보를 나타낸다.

응답에서, 콘텐츠의 목적은 요청 메소드, 응답 상태 코드(15절), 그리고 콘텐츠를 기술하는 응답 필드들에 의해 정의된다. 예를 들어, GET(9.3.1절)에 대한 200(OK) 응답의 콘텐츠는, 메시지 발생 날짜에 관측된대로, 타겟 리소스의 현재 상태를 나타내며, 반면에 같은 상태 코드의 콘텐츠지만 POST에 대한 응답이면 그 처리에 대한 결과나 처리가 적용된 후 타겟 리소스의 새로운 상태를 나타낼 수도 있다.

GET에 대한 206(Partial Content) 응답의 콘텐츠는 선택된 표현의 단일 부분 혹은 해당 표현의 여러 부분들을 포함하고 있는 여러 부분의 메시지 바디를 포함하고, 이에 관해서는 15.3.7절에서 설명한다.

에러 상태 코드의 응답 메시지들은 보통 에러 컨디션을 나타내는 콘텐츠를 포함하여, 콘텐츠가 에러의 상태와 해결을 위해 거치도록 제안되는 단계들을 설명하도록 한다.

HEAD 요청 메소드 (9.3.2)절에 대한 응답은 절대 콘텐츠를 포함하지 않는다; 관련된 응답 헤더 필드들은 요청 메소드가 GET이었다면 가졌을 값들만을 나타낸다.

CONNECT 요청 메소드(9.3.6절)에 대한 2xx(Successful) 응답들은 콘텐츠를 갖는 대신에 연결을 터널 모드로 전환한다.

모든 1xx(Informational), 204(No Content), 그리고 304(Not Modified) 응답들은 콘텐츠를 포함하지 않는다.

모든 다른 응답들은 콘텐츠를 포함하지만, 그 콘텐츠의 길이가 0일 수도 있다.

#### 6.4.2. 콘텐츠 식별

완전한 혹은 부분적인 표현이 메시지로 전달될 때, 종종 특정된 표현에 해당하는 리소스에 대한 식별자를, 발신자가 제공하거나, 혹은 수신자가 결정하도록 기대된다. 예를 들어, "현재 날씨 리포트"에 관한 리소스에 대해 GET 요청을 하고 있는 한 클라이언트는 반환된 콘텐츠에 특정된 식별자를 원할 수 있다(예를 들어, 20210720T1711의 광안리 해변 날씨 리포트"). 이는 시간에 따라 표현이 변할 것으로 예상되는 리소스들로부터 콘텐츠를 공유하거나 북마크하는 데 유용할 수 있다.

요청 메시지에 관해:

- 만약 요청이 Content-Location 헤더 필드를 가진다면, 발신자는 콘텐츠가 Content-Location 필드 값에 의해 식별되는 리소스에 대한 표현임을 단언한다. 그러나, 그러한 단언은 다른 수단들(이 사양에서 정의되지 않은)을 통해 검증될 수 없다면 신뢰할 수 없다. 해당 정보는 여전히 revision history links에 대해 유용할 수 있다.

- HTTP에 의해 콘텐츠가 식별되지 않으면, 콘텐츠 자체에 더 구체적인 식별자가 제공될 수 있다.

응답 메시지에 관해서, 매치되는 첫 룰이 발견될 때 까지 다음 룰들이 순서대로 적용된다.

1. 만약 요청 메소드가 HEAD거나 응답 상태 코드가 204(No Content) 혹은 304(Not Modified)라면, 응답에 콘텐츠는 없다.

2. 만약 요청 메소드가 GET이고 응답 상태 코드가 200(OK)이면, 콘텐츠는 타겟 리소스에 대한 표현이다(7.1절).

3. 만약 요청 메소드가 GET이고 응답 상태코드가 203(Non-Authoritative Information)이면, 콘텐츠는 중개자에 의해 제공된대로 수정되거나 강화된 타겟 리소스의 표현이다.

4. 만약 요청 메소드가 GET이고 상태코드가 206(Partial Content)이면, 콘텐츠는 타겟 리소스의 하나 이상의 부분들이다.

5. 만약 응답에 Content-Location 헤더 필드가 존재하고 그 필드 값이 타겟 URI와 같은 URI에 대한 참조라면, 콘텐츠는 타겟 리소스에 대한 표현이다.

6. 만약 응답에 Content-Location 헤더 필드가 존재하고 그 필드 값이 타겟 URI와 다른 URI를 참조하고 있다면, 발신자는 콘텐츠가 Content-Location 필드 값에 의해 식별되는 리소스의 표현임을 단언한다. 그러나, 이러한 단언은 다른 수단들(이 사양에서 정의되지 않은)에 의해 검증되지 않으면 신뢰할 수 없다

7. HTTP에 의해 콘텐츠가 식별되지 않으면, 콘텐츠 자체에 더 구체적인 식별자가 제공되고 있을 수 있다.

### 6.5. 트레일러 필드

"트레일러 섹션" 내에 위치한 필드들(5절)은 "트레일러 필드"라고 불린다(혹은 그냥 구어체로, "트레일러"). 트레일러 필드는 메시지 무결성 체크, 디지털 시그니처, 전달 지표, 혹은 포스트-처리 상태 정보를 제공하는데 유용할 수 있다.

트레일러 필드들은 헤더 섹션 처리가 완료된 시점에 알려진 메시지 의미 체계와 모순되는 것을 피하기 위해 헤더 섹션의 필드들과 별도로 처리되고 저장되어야 한다. 특정 헤더 필드들의 존재 혹은 부재는 트레일러들이 수신되기 전에 전체 메시지에 대한 라우팅이나 처리에 관한 선택들에 영향을 미칠 수도 있다; 그러한 선택들은 나중에 트레일러 필드들을 발견하더라도 없던 일로 만들 수는 없다.

#### 6.5.1. 트레일러 사용에 대한 제한

트레일러 섹션은 오로지 사용되고 있는 HTTP 버전이 지원할 때와 프레이밍 메커니즘에 의해 명시적으로 허용될 때만 가능하다. 예를 들어, HTTP/1.1의 chunked transfer coding은 트레일러 섹션이 콘텐츠 다음에 보내지는 것을 허용한다([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 7.1.2절).

많은 필드들은 헤더 섹션 밖에서는 처리될 수 없는데 이는 그것들에 대한 평가를 콘텐츠 수신 이전에 필요로 하기 때문이고, 메시지 프레이밍, 라우팅, 인증, 요청 수정자, 응답 제어, 혹은 콘텐츠 포맷과 같은 것들을 기술하는 필드들이 그러하다. 발신자는 어떤 헤더 필드 이름의 정의가 해당 필드가 트레일러에 실려 보내지는 것을 허용하는지 알고 있지 않는 한 절대(MUST NOT) 트레일러 필드를 생성해서는 안된다.

트레일러 필드들은 한 프로토콜 버전에서 다른 프로토콜 버전으로 메시지들을 포워딩하는 중개자 입장에서 처리하기 어려울 수 있다. 만약 전체 메시지가 전송 중에 버퍼될 수 있다면, 어떤 중개자들은 포워딩 하기 전 트레일러 필드들을 헤더 섹션에 병합할 수 있다(적절히). 그러나, 대부분의 경우들에서, 트레일러들은 그냥 간단히 버려진다. 수신자는 스스로 해당하는 헤더 필드 정의를 이해하고 있으면서 그 정의가 명시적으로 트레일러 필드 값들이 안전하게 병합되는 방식을 허용하고 정의하고 있지 않는 한 절대(MUST NOT) 트레일러 필드를 헤더 섹션으로 병합해서는 안된다.

요청의 TE 헤더 필드(10.1.4절)에서 "trailers"라는 키워드의 존재는 클라이언트가 자신을 포함한 어떤 다른 다운스트림 클라이언트들을 대신하여, 기꺼이 트레일러 필드들을 받아들일 것임을 나타낸다. 이는 중개자들로부터의 요청들에 대해, 모든 다운스트림 클라이언트들이 포워드된 응답의 트레일러 필드들을 받아들일 것임을 암시한다. "trailers" 키워드의 존재가 클라이언트들이 응답의 어떠한 특정 트레일러 필드를 처리할 것임을 의미하지는 않는다는 것을 명심하라; 오직 트레일러 섹션(들)이 어떠한 클라이언트들에 의해서도 버려지지 않음을 의미할 뿐이다.

이렇게 트레일러 필드들이 전송 중에 버려질 가능성 때문에, 서버는 웬만해서는(SHOULD NOT) 유저 에이전트가 반드시 수신해야 한다고 믿어지는 트레일러 필드들은 생성하지 않아야 한다.

#### 6.5.2. 트레일러 필드 처리

"Trailer" 헤더 필드(6.6.2절)은 트레일러 섹션을 통해 보내질 가능성이 있는 필드들을 나타내기 위해 보내질 수 있는데, 이는 수신자들이 콘텐츠를 처리하기 전 그것들을 수신할 준비를 할 수 있도록 한다. 예를 들어, 이는 필드 이름이 콘텐츠가 수신되는대로 동적 체크섬을 계산하고 트레일러 필드 값을 받는 즉시 확인해야함을 나타낼 때 유용할 수 있다.

헤더 필드 처럼, 같은 이름의 트레일러 필드들은 수신된 순서대로 처리된다; 같은 이름의 여러 트레일러 필드 라인들은 여러 값 멤버들을 리스트 형태로 붙이는 것과 동등한 의미를 가진다. 한 메시지에서 두 번 이상 생성될 수 있는 트레일러 필드들은 각 멤버 값들이 수신된 필드 라인 당 오직 한번만 처리된다고 하더라도 반드시(MUST) 리스트-기반 필드로 정의되어야 한다.

한 메시지의 끝에서, 수신자는 아마(MAY) 수신된 트레일러 필드들의 집합을 이름/값 쌍들의 데이터 스트럭쳐로 다룰 수 있을 것이며, 이는 헤더 필드와 비슷하다(그러나 별개다). 추가적인 처리에 대한 기대는, 만약 있다면, 트레일러에 사용되도록 의도된 필드의 필드 명세 내에서 정의될 수 있다.

### 6.6. 메시지 메타데이터

메시지 스스로를 기술하는 필드들은, 메시지가 언제 어떻게 생성됐는지와 같이, 요청과 응답 양쪽에서 모두 나타날 수 있다.

#### 6.6.1. Date

"Date" 헤더 필드는 메시지가 생성된 날짜와 시간을 나타내며, [[RFC5322](https://www.rfc-editor.org/info/rfc5322)]의 3.6.1절에 정의된 Origination Date Field(origin-date)와 같은 의미 체계를 공유한다. 필드 값은 5.6.7절에서 정의된 것과 같이, HTTP-date이다.

     Date = HTTP-date

예시는 다음과 같다

Date: Tue, 15 Nov 1994 08:12:31 GMT

Date 헤더 필드를 생성하는 발신자는 웬만하면(SHOULD) 그 필드 값을 메시지 생성 시점의 날짜와 시간에 최대한 근사하도록 해야 한다. 이론적으로는, 날짜는 메시지 콘텐츠를 생성하기 바로 직전의 순간을 나타내야 한다. 실무에서는 발신자는 메시지 생성 중 어느 시점에서든 날짜 값을 생성할 수 있다.

clock을 가진 오리진 서버는(5.6.7절에 정의된대로) 반드시(MUST) 모든 2xx(Successful), 3xx(Redirection), 그리고 4xx(Client Error) 응답들에 대해 Date 헤더 필드를 생성해야 하고, 아마(MAY) 1xx(Informational) 그리고 5xx(Server Error) 응답에서도 Date 헤더 필드를 생성할 수 있을 것이다.

clock을 가지지 않은 오리진 서버는 절대(MUST NOT) Date 헤더 필드를 생성해서는 안된다.

Date 헤더 필드가 없는 응답 메시지를 받는 clock을 가진 수신자는 메시지가 캐시되거나 다운스트름으로 포워드된다면 반드시(MUST) 그것을 수신한 시간을 기록하고 상응하는 Date 헤더 필드를 메시지의 헤더 섹션에 추가해야 한다.

clock을 가진 수신자가 유효하지 않은 Date 헤더 필드를 가진 응답을 수신한다면 아마(MAY) 그 값을 응답을 수신한 시간으로 교체할 수 있을 것이다.

유저 에이전트는 아마(MAY) 요청에 Date 헤더 필드를 포함해 발신할 수 있겠지만, 그것이 서버에게 유용한 정보를 제공할 것이라고 믿어지는 경우가 아닌 이상 보통 그렇게 하지는 않을 것이다. 예를 들어, HTTP의 커스텀 애플리케이션은 서버가 유저 에이전트와 서버 clock의 차이에 기반해 유저의 요청에 대한 해석을 조정할 것으로 기대되는 경우 Date를 전달할 수 있을 것이다.

#### 6.6.2. Trailer

"Trailer" 헤더 필드는 발신자가 메시지 내의 트레일러 필드들로 보낼 것으로 예상되는 필드 이름들의 리스트를 제공한다. 이는 수신자가 콘텐츠의 처리를 시작하기 전에 지정된 메타데이터들을 수신할 준비를 할 수 있도록 한다.

     Trailer = #field-name

예를 들어, 발신자는 시그니처가 콘텐츠가 스트림되면서 계산될 것임을 나타내면서 마지막 시그니처를 트레일러 필드로 제공할 수 있을 것이다. 이는 수신자가 콘텐츠를 수신하는대로 같은 체크를 즉석에서 수행할 수 있도록 한다.

하나 이상의 트레일러 필드들을 메시지에 생성하려고 의도하는 발신자는 웬만하면(SHOULD) 해당 메시지 헤더 필드 섹션에 어떤 필드가 트레일러에 나타날 수 있는지를 지정하기 위해 Trailer 헤더 필드를 생성해야 한ㄷ가.

만약 전송중에 어떤 중개자가 트레일러 섹션을 버렸다면, 비록 Trailer 필드가 그 발신자가 항상 명명된 필드들을 따라 보낼 것 것이라고 보장하지는 않지만, 어떤 메타데이터가 손실됐는지에 대한 힌트는 제공할 수 있다.

## 7. HTTP 메시지 라우팅

HTTP 요청 메시지 라우팅은 타겟 리소스, 클라이언트의 프록시 설정, 그리고 인바운드 연결에 대한 수립 혹은 재사용에 따라 각 클라이언트에 의해 결정된다. 상응하는 응답 라우팅은 같은 연결 체인을 따라 클라이언트로 돌아온다.

### 7.1. 타겟 리소스 결정

비록 HTTP가 넓은 범위 여러 형태의 애플리케이션들에서 사용되기는 하지만, 대부분의 클라이언트들은 범용 웹브라우저들과 똑같은 리소스 식별 메커니즘과 설정 기술들에 의존한다. 클라이언트의 설정에 통신 옵션들이 하드-코딩되어 있을 때 조차, 우리는 그것들의 조합된 효과를 URI 참조(4.1절)로 생각할 수 있다.

URI 레퍼런스는 "타겟 URI"를 획득하기 위해 그것의 절대 형태로 결정된다. 타겟 URI는 레퍼런스의 fragement component를, 만약 있다면, 제외하는데 이는 fragement 식별자들이 클라이언트 측 처리를 위해 예약되어 있기 때문이다 ([[URI](https://www.rfc-editor.org/info/rfc3986)], 3.5절)

"타겟 리소스"에 대해 어떤 행동을 수행하기 위해, 클라이언트는 요청 메시지를 수신자들이 같은 리소스를 식별할 수 있도록 그것의 파싱된 타겟 URI의 컴포넌트들을 충분히 포함하여 보낸다. 역사적인 이유들로, 통칭 "요청 타겟"이라고 불리는, 파싱된 타겟 URI 컴포넌트들은, 메시지 제어 데이터와 Host 헤더 필드 내에 실려 보내진다(7.2절).

요청 타겟 컴포넌트들이 메소드에 특정되는 형태로 되어 있는 두 가지 일반적이지 않은 경우가 있다.

- CONNECT(9.3.6절)에서, 요청 타겟은 호스트 이름과 터널 목적지의 포트 번호이고, 콜론으로 구분되어 있다.

- OPTIONS(9.3.7절)에서는, 요청 타겟이 단일의 별표 문자("\*")가 될 수 있다.

자세한 건 각각의 메소드 정의들을 참고하라. 이 형태들은 절대(MUST NOT) 다른 메소들에서는 사용되서는 안된다.

클라이언트 요청을 수신했을 때, 서버는 그들의 로컬 설정과 수신된 연결 컨텍스에 따라 수신된 컴포넌트들로부터 타겟 URI를 재구성한다. 이 재구성은 각 메이저 프로토콜 버전에 따라 특정되어 있다. 예를 들어, [[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 3.3절은 서버가 어떻게 HTTP/1.1 요청의 타겟 URI를 결정하는지 정의한다.

_Note:_ 이전의 사양들은 재구성된 타겟 URI를, "effective request URI"라는 별개의 개념으로 정의했었다.

### 7.2. Host와 :authority

"Host" 헤더 필드는 요청에서 타겟 URI에 대한 호스트와 포트 정보를 제공하며, 오리진 서버가 여러 호스트 이름들에 대해 요청을 받아 서비스를 하고 있을 때 리소스들 중 하나를 구별할 수 있도록 한다.

HTTP/2[[HTTP/2](https://www.rfc-editor.org/info/rfc9113)]와 HTTP/3[[HTTP/3](https://www.rfc-editor.org/info/rfc9114)]에서는, Host 헤더 필드가, 어떤 경우에서는, 요청의 제어 데이터의 ":authority"라는 슈도-헤더 필드에 의해 대체되기도 한다.

     Host = uri-host [ ":" port ] ; 4절

타겟 URI의 authority 정보는 요청을 다루는데 있어 핵심적이다. 유저 에이전트는 해당하는 정보를 ":authority" 슈도-헤더 필드로 보내지 않는 한 반드시(MUST) Host 헤더 필드를 요청에 생성해야 한다. Host를 보내는 유저 에이전트는 웬만하면(SHOULD) 그것을 요청 헤더 섹션의 첫번째 필드로 설정해야 한다.

예를 들어, <http://www.example.org/pub/WWW/>에 대한 오리진 서버로 GET 요청을 보내면 다음과 같이 시작할 것이다:

     GET /pub/WWW/ HTTP/1.1
     Host: www.example.org

호스트와 포트 정보는 애플리케이션-레벨 라우팅 매커니즘의 일환으로 동작하기 때문에, 이는 공유 캐시를 오염시키거나 요청을 의도치 않은 서버로 보내려는 말웨어들에게 자주 타겟이 된다. 예컨대 처음에 가로채진 연결이 해당 호스트에 대해 유효한 IP 주소를 타게팅하고 있는지 확인 과정을 거치지 않은 채, 내부 서버로 요청을 리다이렉트하기 위해, 혹은 공유 캐시의 캐시 키로 사용하기 위해서 호스트와 포트 정보에 의존하는 인터셉션 프록시들에게 있어서 특히 취약하다.

### 7.3. 인바운드 요청 라우팅

일단 타겟 URI와 그것의 오리진이 결정되면, 클라이언트는 네트워크 요청이 원하는 의미를 달성하는데 필수적인지를 결정하고, 만약 그렇다면, 해당 요청이 어느 쪽으로 향할지를 결정한다.

#### 7.3.1. 캐시로

만약 클라이언트가 캐시[[CACHING](https://www.rfc-editor.org/info/rfc9111)]를 가지고 있고 그것에 의해 요청이 만족될 수 있는 경우에, 요청은 보통 캐시로 먼저 향한다.

#### 7.3.2. 프록시로

만약 요청이 캐시에 의해 만족되지 않는다면, 전형적인 클라이언트는 해당 요청을 만족시키기 위해 프록시를 이용할지 결정하기 위해 자신의 설정을 확인할 것이다. 프록시 설정은 구현에 따라 다르지만, 종종 URI 접두사 매칭, 선택적 권한 매칭, 혹은 둘 다를 기반으로 하고, 프록시 그 자체는 "http" 혹은 "https" URI에 의해 식별된다.

어떤 "http" 혹은 "https" 프록시가 적용 가능한 경우, 클라이언트는 해당 프록시에 대해 연결을 수립(혹은 재사용)하고는 클라이언트의 타겟 URI와 매치되는 요청 타겟을 포함하는 HTTP 요청 메시지를 보냄으로써 인바운에 연결한다.

#### 7.3.3. 오리진으로

어떠한 프록시도 적용할 수 없다면, 전형적인 클라이언트는 식별된 리소스에 대한 접근을 획득하기 위해 핸들러 루틴 (해당 타겟 URI 체계에 특정된)을 호출할 것이다. 그것이 어떻게 완수되는지는 타겟 URI 체계에 의존하며 그와 관련된 사양에 의해 정의된다.

4.3.2절에서는 식별된 오리진 서버에 대해 인바운드 연결을 수립(혹은 재사용)하고는 클라이언트의 타겟 URI와 매치되는 요청 타겟을 포함하는 HTTP 요청 메시지를 보냄으로써 "http" 리소스에 대한 접근을 획득하는 방식을 정의한다.

4.3.3절에서는 식별된 오리진에 대해 권한있는 오리진 서버에 안전한 인바운드 연결을 수립(혹은 재사용)하고는 클라이언트의 타겟 URI에 매치되는 요청 타겟을 포함하는 HTTP 요청 메시지를 보냄으로써 "https" 리소스에 대한 접근을 획득하는 방식을 정의한다.

### 7.4. 잘못 도달한 요청 거부

일단 서버가 한 요청을 수신하고 그것의 타겟 URI를 결정할만큼 충분히 파싱했으면, 이제 서버는 스스로 요청을 처리할지, 다른 서버로 포워드할지, 클라이언트를 다른 리소스로 리다이렉트할지, 에러로 응답할지, 혹은 연결을 끊어버릴지를 결정하게 된다. 이 결정은 요청 혹은 연결 컨텍스트와 관련된 어느 것에 의해서든 영향을 받을 수 있지만, 특히 서버가 해당 타겟 URI에 대한 요청들을 처리하도록 설정됐는지와 해당 요청의 연결 컨텍스트가 적절한지에 직접적으로 영향 받는다.

예를 들어, 요청은, 고의로 혹은 우발적으로, 오도될 수도 있는데, 수신된 Host 헤더 필드 내의 정보가 연결의 호스트나 포스트와 다르게 된다. 만약 연결이 신뢰할 수 있는 게이트웨이에서 오는 것이라면, 그러한 불일치는 예측될 수도 있다; 그렇지 않으면, 그것은 보안 필터들을 우회하려는, 서버가 공개되지 않은 콘텐츠를 전달하도록 속이려는, 혹은 캐시를 오염시키려는 시도를 나타내는 것일 수 있다. 메시지 라우팅에 관한 보안 고려사항들에 관해서는 17절을 참고하라.

연결이 신뢰할 수 있는 게이트웨이로부터 오는 게 아니라면, 오리진 서버는 요청이 타겟 URI에 대해서 체계에 특정된 요구 사항들 중 어떠한 것이라도 만족되지 않는다면 반드시(MUST) 해당 요청을 거부해야 한다. 특히나, "https" 리소스에 대한 요청은 그것이 4.2.2절에 정의된 대로, 해당 타겟 URI의 오리진에 대해 유효한 인증서를 통해 보안이 확보된 연결을 통해 수신된 것이 아니라면 반드시(MUST) 거부되어야 한다.

응답의 421(Misdirected Request) 상태 코드는 오리진 서버가 해당하는 요청이 오도된 것으로 보이기 때문에 거부했다는 것을 나타낸다(15.5.20절).

### 7.5. 응답 연관짓기

한 연결은 여러 요청/응답 교환에 걸쳐 사용될 수 있다. 요청과 응답 메시지들을 연관시키기 위한 메커니즘은 버전에 따라 다르다; HTTP의 일부 버전들은 메시지들의 암시적인 순서를 사용하는 반면, 다른 것들은 명시적인 식별자를 사용한다.

모든 응답들은, 상태 코드와 관계없이(중간 응답을 포함하여) 요청을 수신한 후 언제든지 보내질 수 있고, 요청이 아직 완전하지 않을 때조차 가능하다. 어떤 한 응답은 상응하는 요청이 끝나기도 전에 완료될 수 있다(6.1절). 마찬가지로, 클라이언트들은 응답에 대해 어떠한 특정된 시간만큼 기다리도록 기대되지 않는다. 클라이언트들은(중개자들을 포함해) 응답이 합리적인 수준의 기간 내에 수신되지 않으면 그냥 요청을 버릴 수도 있다.

클라이언트는 응답을 수신할 때 여전히 관련된 요청을 보내고 있더라도 반대 상황에 대한 명시적인 표시가 없는 한 웬만하면(SHOULD) 해당 요청을 계속 보내야 한다(예를 들어, [[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 9.5절과 [[HTTP/2](https://www.rfc-editor.org/info/rfc9113)]의 6.4절을 참고하라).

### 7.6. 메시지 포워딩

3.7절에 기술된대로, 중개자들은 HTTP 요청과 응답들을 처리하는데 있어 다양한 역할들을 수행할 수 있다. 어떤 중개자들은 성능과 가용성을 향상시키기 위해 사용된다. 어떤 것들은 접근 제어 혹은 콘텐츠 필터링을 위해 사용된다. HTTP 스트림은 pipe-and-filter 아키텍처와 비슷한 특성들을 가지기 때문에, 본질적으로 중개자에게는 스트림의 어느 쪽으로든 향상시키는데(혹은 간섭하는데) 정해진 정도 같은 것이 없다.

중개자들은 프로토콜 요소들이 인식되지 않을 때조차 메시지들을 포워딩하도록 기대되는데(예를 들어, 새로운 메소드, 상태 코드, 혹은 필드 이름들) 이렇게 하는 것이 다운스트림 수신자들에 대한 확장성을 보호하기 때문이다.

터널로써 작동하지 않는 중개자는 반드시(MUST) 7.6.1절에 지정된대로, Connection 헤더 필드를 구현해야 하고, 들어오는 연결에 대해서만 의도된 필드들은 포워딩에서 제외해야 한다.

한 중개자는 무한 요청 루프에 대해 보호되지 않은 경우에는 절대(MUST NOT) 스스로에게 메시지를 포워딩해서는 안된다. 일반적으로, 중개자는 자기 서버 이름들을, 어떠한 별칭들, 로컬 변형들, 혹은 리터럴 IP 주소들을 포함하여 인식하고 있어야 하고, 그러한 요청들에 대해 직접적으로 응답해야 한다.

하나의 HTTP 메시지는 incremental processing이나 다운스트림 포워딩을 위해 하나의 스트림으로 파싱될 수 있다. 하지만, 발신자들과 수신자들은 부분 메시지들의 incremental delivery에 의존할 수 없다, 이는 일부 구현체들이 네트워크 효율성, 보안 체크, 혹은 콘텐츠 변형을 위해 메시지 포워딩을 버퍼링하거나 지연시킬 것이기 때문이다.

#### 7.6.1. Connection

"Connection" 헤더 필드는 발신자가 현재 연결에 대해 바라는 제어 옵션들을 나열할 수 있도록 한다.

     Connection        = #connection-option
     connection-option = token

연결 옵션들은 대소문자를 구분하지 않는다.

Connection 말고 다른 필드가 현재 연결을 위해서 혹은 대해서 제어 정보를 공급하기 위해 사용될 때는, 발신자든 반드시(MUST) Connection 헤더 필드 내에 상응하는 필드 이름을 나열해야 한다. HTTP 일부 버전들은 그러한 정보를 위해 필드들을 사용하는 것을 금지하는데, 따라서 Connection 필드가 허용되지 않음을 명심하라.

중개자들은 반드시(MUST) 메시지가 포워딩되기 전에, 수신된 Connection 헤더 필드를 파싱해야하고, 해당 필드의 connection-option들에 대해, connection-opion과 같은 이름의 헤더나 트레일러 필드(들)을 메시지로부터 제거하고는, Connection 헤더 필드 자체까지 제거해야 한다(혹은 포워딩되는 메시지에 대해 중개자 자신의 제어 옵션들로 대체하거나).

이리하여, Connection 헤더 필드는 직후의 수신자("hop-by-hop")만을 위해 의도된 필드들을 체인 상의 모든 수신자들("end-to-end")을 위해 의도된 필드들로 부터 구분하는 선언적인 방법을 제공하며, 메시지가 자가-설명적일 수 있도록 하고 차후의 연결별 확장들이 오래된 중개자들에 의해 무지성으로 포워딩될 것이라는 공포 없이 배포될 수 있도록 한다.

나아가서, 중개자들은 웬만하면(SHOULD) 포워딩하기 전에 제거를 요구하는 것으로 알려진 필드들을, 그 필드들의 의미를 적용하고 나서, 제거하거나 대체해야 하며, 그것들이 connection-option으로 나타나든 말든 상관 없다. 여기에는 다음 것들이 포함되지만 이에 국한되지는 않는다:

- Proxy-Connection (Appendix C.2.2 of [[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)])

- Keep-Alive (Section 19.7.1 of [[RFC2068](https://www.rfc-editor.org/info/rfc2068)])

- TE (Section 10.1.4)

- Transfer-Encoding (Section 6.1 of [[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)])

- Upgrade (Section 7.8)

발신자는 절대(MUST NOT) 콘텐츠의 모든 수신자들을 위해 의도된 필드에 상응하는 연결 옵션을 보내서는 안된다. 예를 들어, Cache-Control은 연결 옵션으로 절대 적합하지 않다([[CACHING](https://www.rfc-editor.org/info/rfc9111)]의 5.2절).

연결 옵션들이 항상 메시지에 존재하는 필드에 대응하지는 않는데, 이는 연결 옵션과 관련된 파라미터가 없을 경우 연결별 필드가 필요하지 않을 수 있기 때문이다. 반대로, 대응되는 연결 옵션 없이 수신된 연결별 필드는 보통 그 필드가 중개자에 의해 부적합하게 포워딩됐고 수신자에 의해 무시되어야 함을 나타낸다.

필드와 대응되지 않는 새로운 연결 옵션을 정의하려 할 때, 사양 작성자들은 대응되는 필드 이름을 어쨌든 나중의 충돌을 피하기 위해 예약해야 한다. 그러한 예약된 필드 이름들은 "Hypertext Transfer Protocol (HTTP) Field Name Registry"(16.3.1절)에 등록된다.

#### 7.6.2. Max-Forwards

"Max-Forwards" 헤더 필드는 TRACE(9.3.8절)와 OPTIONS(9.3.7절) 요청 메소드와 함께 요청이 프록시들에 의해 포워드되는 횟수를 제한하는 메커니즘을 제공한다. 이는 클라이언트가 체인 중간에서 실패하거나 루핑하는 것으로 보이는 요청을 추적하려할 때 유용할 수 있다.

     Max-Forwards = 1*DIGIT

Max-Forwards 값은 해당 메시지가 포워드될 수 있는 남은 횟수를 나타내는 십진 정수이다.

한 Max-Forwards 헤더 필드를 포함하는 TRACE나 OPTIONS 요청을 수신한 각 중개자는 반드시(MUST) 해당 요청을 포워딩하기 전에 그 값을 확인하고 업데이트 해야 한다. 만약 수신된 값이 0이라면, 중개자는 절대(MUST NOT) 해당 요청을 포워딩해서는 안된다; 대신에, 중개자는 반드시(MUST) 최종 수신자로서 응답해야 한다. 만약 수신한 Max-Forwards 값이 0보다 크다면, 중개자는 포워딩된 메시지에 반드시(MUST) a) 수신된 값에서 1이 감소된 값 혹은 b) 수신자가 지원하는 최대 Max-Forwards 값 둘 중 더 작은 값으로 갱신된 Max-Forwards 필드를 생성해야 한다.

수신자는 아마(MAY) 이외의 요청 메소드들과 함께 수신된 Max-Forwards 헤더 필드는 무시할 수 있을 것이다.

#### 7.6.3. Via

"Via" 헤더 필드는 유저 에이전트와 서버 사이(요청들에서) 혹은 오리진서버와 클라이언트 사이(응답들에서)에 중간 프로토콜들과 수신자들이 존재한다는 것을 나타내는데, email의 "Received" 헤더 필드([[RFC5322](https://www.rfc-editor.org/info/rfc5322)]의 3.6.7절)와 비슷하다. Via는 메시지 포워딩을 추적하고, 요청 루프를 회피하고, 요청/응답 체인에 거친 발신자들의 프로토콜 수용 능력을 식별하는데 사용될 수 있다.

     Via = #( received-protocol RWS received-by [ RWS comment ] )

     received-protocol = [ protocol-name "/" ] protocol-version
                       ; 7.8절 참조
     received-by       = pseudonym [ ":" port ]
     pseudonym         = token

Via 필드 값의 각 멤버는 메시지를 포워드한 프록시나 게이트웨이를 나타낸다. 각 중개자는 메시지를 어떻게 수신했는지에 대한 자신의 정보를 덧붙여, 마지막 결과는 포워드한 수신자들의 순서에 따라 나열된다.

프록시는 반드시(MUST) 포워드하는 각 메시지에서 , 아래에 기술된대로, 적합한 Via 헤더 필드를 보내야 한다. HTTP-to-HTTP 게이트웨이는 반드시(MUST) 각 인바운드 요청 메시지들에 적합한 Via 헤더 필드를 보내야 하고 아마(MAY) 포워드되는 응답 메시지들에 Via 헤더를 보낼 수 있을 것이다.

각 중개자들에 대해, received-protocol은 메시지의 업스트림 발신자에 의해 사용된 프로토콜과 프로토콜 버전을 나타낸다. 이리하여, Via 필드 값은 요청/응답 체인의 알려진 프로토콜 수용 능력을 기록하게 되고 이는 다운스트림 수신자들이 계속 볼 수 있게 된다; 이는 어떤 하위-비호환 기능들이 응답, 혹은 나중 요청에서, 사용하기에 안전한지 결정할 때 유용할 수 있고, 2.5절에 관련해서 기술되어 있다. 간결함을 위해, protocol-name은 수신 프로토콜이 HTTP일 때는 생략된다.

received-by 부분은 보통 이후에 메시지를 포워드한 수신 서버 혹은 클라이언트의 호스트와 선택적인 포트 넘버를 나타낸다. 그러나, 실제 호스트가 민감한 정보로 간주된다면, 발신자는 아마(MAY) 그것을 pseudonym으로 교체할 수 있을 것이다. 만약 포트가 제공되지 않으면, 수신자는 아마(MAY), 만약 있다면, received-protocle의 기본 포트에서 수신됐다는 의미로 해석할 수 있을 것이다.

발신자는 아마(MAY) 각 수신자의 소프트웨어를 식별하기 위해 주석들을 생성할 수 있을 것이고, User-Agent와 Server 헤더 필드와 유사하다. 그러나, Via의 주석은 선택 사항이고, 수신자는 아마(MAY) 메시지를 포워드하기 전에 그것들을 제거할 수도 있을 것이다.

예를 들어, HTTP/1.0 유저 에이전트로부터 코드 네임 "fred"의 내부 프록시로 한 요청 메시지가 전달될 수 있는데, 그 프록시는 메시지를 p.example.net의 공개 프록시로 포워드하기 위해 HTTP/1.1을 사용하며, 공개 프록시는 요청을 www.example.com의 오리진 서버로 포워드하며 요청을 완료한다고 하자. www.example.com에 의해 수신된 해당 요청은 다음의 Via 헤더 필드를 가지고 있을 것이다.

     Via: 1.0 fred, 1.1 p.example.net

네트워크 방화벽을 통한 포탈로써 사용되는 중개자는 명시적으로 그리 하라 되어있지 않은 한 웬만해서는(SHOULD NOT) 방화벽 구역 내의 호스트들의 이름과 포트를 포워딩해서는 안된다. 그렇게 하라고 되어 있지 않으면, 그러한 중개자는 웬만하면(SHOULD) 방화벽 뒤의 어떠한 호스트든 각 received-by 호스트를 그 호스트를 위한 적절한 pseudonym으로 교체해 줘야 한다.

중개자는 Via 헤더 필드 리스트 멤버 부분열의 항목들이 같은 received-protocol 값들을 가지고 있다면 아마(MAY) 하나의 멤버로 결합할 수 있을 것이다. 예를 들어,

     Via: 1.0 ricky, 1.1 ethel, 1.1 fred, 1.0 lucy

는 다음과 같이 줄일 수 있다

     Via: 1.0 ricky, 1.1 mertz, 1.0 lucy

발신자는 여러 리스트 멤버들이 모두 같은 조직 통제 아래에 있고 호스트들이 이미 pseudonyms로 교체되지 않은 한 웬만해서는(SHOULD NOT) 그것들을 결합해서는 안된다. 발신자는 절대(MUST NOT) 다른 received-protocol 값들을 가진 멤버들을 결합해서는 안된다.

### 7.7. 메시지 변환

어떤 중개자들은 메시지들과 그 콘텐츠를 변형하기 위한 기능들을 포함한다. 프록시는 아마, 예를 들어, 캐시 공간을 절약하거나 느린 링크 상의 트래픽을 감소시키기 위해 이미지 포맷들 간에 전환할 수 있을 것이다. 그러나, 이러한 변형들이, 의료 영상 처리나 과학 데이터 분석 같이 중요한 애플리케이션들을 위해 의도된 콘텐츠에 대해 적용될 때는, 작동상의 문제들이 발생할 수 있을 것이고, 특히 수신된 콘텐츠가 원본과 동일하다는 것을 보장하기 위해 무결성 체크나 디지털 시그니처들이 사용될 때 그러하다.

HTTP-to-HTTP 프록시는 의미론적으로 유의미한 방식으로 메시지들을 수정하도록 설계 혹은 설정된 경우 "transforming proxy"라고 불린다(즉, 원본 발신자에게나 잠재적으로는 다운스트림 수신자들에게 의미있을 방법을 통해 메시지를 변화시키는, 일반 HTTP 처리에서 요구되는 것을 넘어서는, 그러한 수정들). 예를 들어, transforming proxy는 공유된 어노테이션 서버(응답들이 로컬 어노테이션 데이터베이스에 대한 참조들을 포함하도록 수정하는)로서 작동할 수 있고, 말웨어 필터, 포맷 트랜스코더, 혹은 프라이버시 필터로 작동할 수 있다. 이러한 변형들은 해당 프록시를 선택한 어느 클라이언트(혹은 클라이언트 조직)든 그런 동작을 바랐다고 간주된다.

만약 프록시가 호스트 이름이 완전히 갖춰지지 않은 도메인 이름인 타겟 URI를 수신한다면, 프록시는 아마(MAY) 해당 요청을 포워딩할 때 수신한 호스트 네임에 자기 자신의 도메인을 추가할 수 있을 것이다. 프록시는 타겟 URI가 완전히 갖춰진 도메인 이름을 포함하고 있다면 절대(MUST NOT) 호스트 이름을 변경해서는 안된다.

프록시는 포워딩 프로토콜에 의해 요구되는 경우를 제외하고는 절대(MUST NOT) 수신한 타겟 URI의 "absolute-path"와 "query" 파트들을 수정해서는 안된다. 예를 들어, HTTP/1.1을 통해 오리진 서버로 요청을 포워딩하는 프록시는 빈 경로를 "/"([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 3.2.1절) 혹은 "\*"([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 3.2.4절)로 대체할 것이고, 이는 요청 메서드에 따른다.

프록시는 절대(MUST NOT) no-transform 캐시 지시자를 포함하는 응답 메시지들의 콘텐츠(6.4절)를 변형해서는 안된다([[CACHING](https://www.rfc-editor.org/info/rfc9111)]의 5.2.2.6절). 다만 콘텐츠를 변경하지 않는 메시지 변형들에는 적용되지 않음을 주의하고, transfer coding들의 추가나 제거가 그러한 변형이다([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 7절).

프록시는 아마 (MAY) no-transform 캐시 지시자를 포함하지 않는 메시지의 콘텐츠는 변형할 수 있을 것이다. 200(OK) 응답의 콘텐츠를 변형하는 프록시는 응답 상태 코드를 203(Non-Authoritative Information)로 수정하여 다운스트림 수신자들에게 변형이 적용됐음을 알릴 수 있다(15.3.4절).

프록시는 필드의 정의가 구체적으로 수정을 허용하거나 수정이 프라이버시나 보안을 위해 필수적이라고 간주되는게 아닌 한 웬만해서는(SHOULD NOT) 통신 체인의 엔드포인트들, 리소스 상태, 혹은 선택된 표현(콘텐츠를 제외한)에 대한 정보를 제공하는 헤더 필드들을 수정해서는 안된다.

### 7.8. Upgrade

"Upgrade" 헤더 필드는 같은 연결 상에서 HTTP/1.1으로 부터 다른 어떤 프로토콜로 전환하기 위한 간단한 메커니즘을 제공하도록 의도되어 있다.

클라이언트는 아마(MAY) 요청의 Upgrade 헤더 필드에 프로토콜 이름들의 리스트를 포함해 보내 서버가 하나 이상의 지정된 프로토콜들로 스위칭하도록 제안할 수 있고, 그 우선순위는 앞에 나열될 수록 높으며, 서버는 최종 응답을 보내기 전에 스위칭할 수 있다. 서버는 아마(MAY) 수신한 Upgrade 헤더 필드를 해당 연결에서 기존 프로토콜을 사용하는 것을 계속하길 바라면 그냥 무시할 수 있을 것이다. Upgrade는 확실한 프로토콜 변경을 요구하는 수단이 아니다.

     Upgrade          = #protocol

     protocol         = protocol-name ["/" protocol-version]
     protocol-name    = token
     protocol-version = token

프로토콜 이름들의 대소문자를 선호하는대로 등록하면 되지만, 수신자들은 웬만하면(SHOULD) 각 protocol-name을 지원하는 프로토콜들로 매칭할 때 대소문자를 구분하지 않고 비교해야 한다.

101(Switching Protocols) 응답을 보내는 서버는 반드시(MUST) Upgrade 헤더 필드를 보내어 해당 연결이 스위칭하는 새로운 프로토콜(들)을 나타내야 한다; 만약 여러 프로토콜 레이어들이 스위칭된다면, 발신자는 반드시(MUST) 해당 프로토콜들을 레이어가 올라가는 순으로 나열해야 한다. 발신자는 절대 (MUST NOT) 클라이언트가 해당하는 요청의 Upgrade 헤더 필드에 나타내지 않은 프로토콜로 스위칭해서는 안된다. 서버는 아마(MAY) 클라이언트가 지정한 우선순위를 무시할 수 있을 것이고 새로운 프로토콜(들)을 다른 요소들, 요청의 성격이나 현재 서버의 부하 같은 것에 따라 선택할 수 있을 것이다.

426(Upgrade Required) 응답을 보내는 서버는 반드시(MUST) Upgrade 헤더 필드를 보내 수용 가능한 프로토콜들을 나타내야 하고, 이는 우선순위가 높은 것 부터 나열한다.

서버는 아마(MAY) 다른 아무 응답에서 Upgrade 헤더 필드를 보내 자신이 나열된 프로토콜들로의 업그레이딩을 지원한다고 알릴 수 있을 것이며, 이는 우선순위가 높은 순서고, 차후의 요청에서 적절한 때에 가능하다.

다음은 클라이언트에 의해 보내진 가상의 예시다:

     GET /hello HTTP/1.1
     Host: www.example.com
     Connection: upgrade
     Upgrade: websocket, IRC/6.9, RTA/x11

프로토콜이 바뀌고 나서의 애플리케이션-레벨 통신의 능력과 성격은 새로이 선택된 프로토콜(들)에 완전히 의존한다. 그러나, 101(Switching Protocols) 응답을 보낸 직후, 서버는 원래 요청을 서버가 새로운 프로토콜 내에서 원래 요청과 동등한 것을 받은 것 처럼 원래 요청에 대해 응답을 계속해야 한다(즉, 서버는 프로토콜이 바뀌고 나서도 아직 만족시켜야할 미해결 요청을 가지고 있고, 해당 요청을 반복할 필요 없이 이리 하도록 기대된다).

예를 들어, 만약 Upgrade 헤더 필드가 GET 요청에서 수신되고 서버는 프로토콜을 스위칭하기로 결정한다면, 먼저 서버는 101(Switching Protocols) 메시지를 HTTP/1.1로 응답하고나서 즉시 타겟 리소스의 GET에 대한 응답에 대해 새로운 프로토콜의 동등한 것으로 그 응답을 따른다. 이는 연결이 추가적인 라운드 트립에 의한 지연 비용 없이 HTTP와 같은 의미체계의 프로토콜들로 업그레이드될 수 있도록 한다. 서버는 수신한 메시지 의미체계가 새로운 프로토콜에 의해 지켜질 수 있는게 아니라면 절대(MUST NOT) 프로토콜들을 스위칭해서는 안된다; OPTIONS 요청은 아무 프로토콜들에게나 지켜질 수 있다.

다음은 위의 가상 요청에 대한 예시 응답이다:

     HTTP/1.1 101 Switching Protocols
     Connection: upgrade
     Upgrade: websocket

     [... 데이터 스트림은 "GET /hello" 요청에 대해 적절한 응답과 함께(새로운 프로토콜에 의해 정의된대로) websocket으로 스위칭 ...]

Upgrade의 발신자는 또한 "Upgrade" 연결 옵션을 Connection 헤더 필드(7.6.1절)에 포함해 보내 중개자들이 이 필드를 포워딩하지 않도록 알려야 한다. Upgrade 헤더 필드를 HTTP/1.0 요청에서 수신하는 서버는 반드시(MUST) 해당 Upgrade 필드를 무시해야 한다.

클라이언트는 업그레이드된 프로토콜을 해당 연결에서 사용하는 것을 요청 메시지를 완전히 보내기 전에는 시작할 수 없다(즉, 클라이언트는 메시지를 한창 보내고 있는 프로토콜을 변경할 수 없다). 만약 서버가 Upgrade와 Expect 헤더 필드를 동시에 "100-continue" expectation(10.1.1절)과 함께 수신한다면, 서버는 101(Switching Protocols) 응답을 보내기 전에 반드시(MUST) 100(Continue) 응답을 보내야 한다.

Upgrade 헤더 필드는 오직 기존 연결의 최상부에서 프로토콜을 전환하는데에만 적용된다; 기반 연결 (트랜스포트) 프로토콜 스위칭할 수 없고, 기존 통신을 다른 연결로 스위칭할 수도 없다. 그러한 목적들을 위해서는, 3xx(Redirection) 응답(15.4절)을 사용하는 편이 더 적절하다.

이 사양은 프로토콜 이름 "HTTP"를, 2.5절의 HTTP 버전 룰들과 이 사양의 차후 업데이트에 의해 정의되는대로 오직 Hypertext Transfer Protocol의 일종에 의한 사용을 위해 정의한다. 추가적인 프로토콜 이름들은 16.7절에 정의된 등록 절차를 이용해 등록되어야 한다.

## 8. 표현 데이터와 메타데이터

### 8.1. 표현 데이터

HTTP 메시지와 연관된 표현 데이터는 메시지의 콘텐츠로 제공되거나 메시지 의미체계와 타겟 URI에 의해 참조된다. 표현 데이터는 표현 메타데이터 헤더 필드들에 의해 정의된 포맷과 인코딩으로 되어 있다.

표현 데이터의 데이터 타입은 Content-Type과 Content-Encoding 헤더 필드들을 통해 결정된다. 이것들은 2-계층의, 순서대로된 인코딩 모델을 정의한다.

     representation-data := Content-Encoding( Content-Type( data ) )

### 8.2. 표현 메타데이터

표현 헤더 필드들은 표현에 대한 메타데이터를 제공한다. 메시지가 콘텐츠를 포함할 때, 표현 헤더 필드들은 해당 데이터를 어떻게 해석할지를 기술한다. HEAD 요청에 대한 응답에서, 표현 헤더 필드들은 만약 동일한 요청이 GET이라면 콘텐츠에 포함되어 있을 표현 데이터를 기술한다.

### 8.3. Content-Type

"Content-Type" 헤더 필드는 연관된 표현의 미디어 타입을 나타낸다: 표현이라함은 메시지 콘텐츠에 포함된 것 혹은, 메시지 의미체계에 의해 결정되어 선택된 표현. 지정된 미디어 타입은 Content-Encoding에 의해 지정된 콘텐츠 코딩들이 모두 디코딩된 후, 수신된 메시지 의미체계의 범위 내에서, 데이터 포맷과 해당 데이터가 수신자에게 어떻게 처리되기를 의도된 것인지를 정의한다.

     Content-Type = media-type

미디어 타입은 8.3.1절에 정의되어 있다. 필드 예시는

     Content-Type: text/html; charset=ISO-8859-4

콘텐츠를 포함하는 메시지를 생성하는 발신자는 포함된 표현의 의도된 미디어 타입이 발신자에게도 알려지지 않은 것이 아니라면 웬만하면(SHOULD) Content-Type 헤더 필드를 메시지에 생성해야 한다. 만약 Content-Type 헤더 필드가 존재하지 않는다면, 수신자는 아마(MAY) "application/octet-stream"([[RFC2046](https://www.rfc-editor.org/info/rfc2046)], 4.5.1절)의 미디어 타입을 가정하거나 그 타입을 결정하기 위해 조사할 수 있을 것이다.

실무적으로, 리소스 오너들이 항상 주어진 표현에 대해 올바른 Content-Type을 제공하도록 그들의 오리진 서버를 적절히 설정하지는 않는다. 어떤 유저 에이전트들은 콘텐츠를 조사하고, 특정 케이스들에서는, 수신된 타입을 오버라이드한다(예를 들어, [[Sniffing](https://mimesniff.spec.whatwg.org)] 참조). 이 "MIME sniffing"은 데이터에 관한 잘못된 결론들로 이끄는 위험을 초래하며, 유저에게 추가적인 보안 리스크들을 노출시킬 수 있다(예를 들어, "privilege escalation", "권한 확대"). 게다가, 다른 별도의 미디어 타입들이 종종 공통의 데이터 포맷을 공유하며, 데이터가 어떻게 처리되도록 의도됐는지에 관해서만 다른 경우도 있는데, 데이터만 검사해서는 서로 구분할 수 없다. 스니핑이 구현됐을 때, 구현자들은 유저가 이를 무력화할 수 있는 수단을 제공하도록 권장된다.

Content-Type이 싱글톤 필드로 정의되어 있긴 하지만, 가끔 부적절하게 여러 번 생성되기도 하며, 리스트로 나타나는 조합된 필드 값에 이르게 된다. 수신자들은 종종 이 에러를 문법적으로 유효한 리스트의 마지막 멤버를 사용함으로써 대처하기도 하는데, 다른 구현들이 다른 에러 핸들링 행동들을 가질 때 잠재적인 상호운용성과 보안 이슈들로 이어지게 된다.

#### 8.3.1. Media Type

HTTP는 Content-Type(8.3절)과 Accept(12.5.1절) 헤더 필드들에서 미디어 타입들[[RFC2046](https://www.rfc-editor.org/info/rfc2046)]을 사용하여 개방되고 확장 가능한 데이터 타이핑과 타입 협상을 제공한다. 미디어 타입들은 데이터 포맷과 다양한 프로세싱 모델들 둘 다를 정의 한다: 프로세싱 모델이라함은 메시지의 콘텍스에 따라 데이터를 어떻게 처리할지를 말함.

     media-type = type "/" subtype parameters
     type       = token
     subtype    = token

tupe과 subtype 토큰들은 대소문자를 구별하지 않는다.

type/subtype은 아마(MAY) 세미콜론으로 구분되는 이름/값 쌍의 파라미터들(5.6.6절)로 이어질 수 있을 것이다. 파라미터의 존재 혹은 부재는 미디어 타입의 처리에 있어 의미가 있을 수도 있으며, 이는 미디어 타입 레지스트리 내의 정의에 따른다. 파라미터 값들은 대소문자를 구분 할 수도 있고 안할 수도 있는데 이는 파라미터 이름의 의미체계에 따른다.

For example, the following media types are equivalent in describing HTML text data encoded in the UTF-8 character encoding scheme, but
the first is preferred for consistency (the "charset" parameter value
is defined as being case-insensitive in [RFC2046], Section 4.1.2):

예를 들어, 다음 미디어 타입들은 UTF-8 문자 인코딩 체계로 인코딩된 HTML 텍스트 데이터를 설명하는데 있어 동등하지만, 일관성을 위해 첫번째 것이 선호된다("charset" 파라미터 값은 대소문자를 구별하지 않는 것으로 [[RFC2046](https://www.rfc-editor.org/info/rfc2046)], 4.1.2절에 정의되어 있다.):

     text/html;charset=utf-8
     Text/HTML;Charset="utf-8"
     text/html; charset="utf-8"
     text/html;charset=UTF-8

미디어 타입들은 [[BCP13](https://www.rfc-editor.org/info/bcp13)]에 정의된 절차에 따라 IANA에 등록되어야 한다.

#### 8.3.2. Charset

HTTP는 텍스트 표현의 문자 인코딩 체계([[RFC6365](https://www.rfc-editor.org/info/rfc6365)], 2절)를 나타내거나 협상하기 위해 "charset" 이름들을 사용한다. 이 문서에 의해 정의되는 필드들에서, charset 이름들은 파라미터로(Content-Type), 혹은, Accept-Encoding에서, 플레인 토큰 형태로 나타난다. 두 경우 모두, charset 이름들은 대소문자를 구별하지 않는다.

Charset 이름들은 [[RFC2978](https://www.rfc-editor.org/info/rfc2978)]의 2절에 정의된 절차들에 따라 IANA "Character Sets" 레지스트리(<https://www.iana.org/assignments/character-sets>)에 등록되어야 한다.

_Note:_ 이론적으로, charset 이름들은 [[RFC2978](https://www.rfc-editor.org/info/rfc2978)]의 2.3절에 정의된 "mime-charset" ABNF 룰에 의해 정의 된다([[Err1912](https://www.rfc-editor.org/errata/eid1912)]에 정정된대로). 해당 규칙은 "token"에 포함되지 않은 두 문자("{"와 "}")를 허용하지만, 이 문서가 작성되는 시점에 등록된 어떠한 charset도 중괄호를 포함하지 않는다([[Err5433](https://www.rfc-editor.org/errata/eid5433)] 참조).

#### 8.3.3. Multipart Types

MIME은 많은 "multipart" 타입들을 제공한다 -- 즉 단일 메시지 바디 내에서 하나 이상의 표현들의 캡슐화들. 모든 멀티파트 타입들은 공통의 구문을 공유하며, 이는 [[RFC2046](https://www.rfc-editor.org/info/rfc2046)]의 5.1.1절에 정의되어 있고, 미디어 타입 값의 일부로 바운더리 파라미터를 포함한다. 메시지 바디는 그 자체로 프로토콜 요소다; 발신자는 반드시(MUST) 바디 파트들 간의 줄바꿈을 나타내기 위해 오로지 CRLF만 생성해야 한다.

HTTP 메시지 프레이밍은 멀티파트 바운더리를 메시지 바디 길이를 나타내는 용도로 사용하지 않지만, 콘텐츠를 생성하거나 처리하는 구현체들에 의해 그리 사용될 수 있다. 예를 들어, "multipart/form-data" 타입은 종종 요청의 폼 데이터를 전달하기 위해 사용되는데, [[RFC7578](https://www.rfc-editor.org/info/rfc7578)]에 해당 내용이 정의되어 있고, "multipart/byteranges" 타입은 이 사양에 의해 일부 206(Partial Content) 응답들에서 사용되도록 정의되어 있다(15.3.7절 참조).

### 8.4. Content-Encoding

"Content-Encoding" 헤더 필드는, 미디어 타입에 내재된 것을 넘어, 표현에 대해 어떤 콘텐츠 코딩들이 적용됐는지를 나타내어, Content-Type 헤더 필드에 의해 참조된 미디어 타입에서 데이터를 획득하기 위해 어떤 디코딩 매커니즘을 사용해야만 하는지를 나타나게 된다. Content-Encoding은 표현의 데이터가 그것의 기반이되는 미디어 타입의 정체성을 잃지 않으면서 압축되도록 하는데 주로 사용된다.

     Content-Encoding = #content-coding

그 사용 예는

     Content-Encoding: gzip

만약 하나 이상의 인코딩들이 표현에 대해 적용됐다면, 해당 인코딩들을 적용한 발신자는 반드시(MUST) Content-Encoding 헤더 필드를 생성하여 콘텐츠 코딩들의 그것들이 적용된 순서대로 나열하여야 한다. "identity"라고 명명된 코딩은 Accept-Encoding에서의 특수한 역할을 위해 예약됐기 때문에 웬만해서는(SHOULD NOT) 포함하면 안된다는 것에 주의하라.

인코딩 파라미터들에 대한 추가적인 정보는 이 사양에서 정의되지 않은 다른 헤더 필드들에 의해 제공될 수 있다.

Transfer-Encoding([[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 6.1절)과 달리, Content-Encoding에 나열된 코딩들은 표현의 특성이다; 표현은 코딩 형태로 정의되고, 모든 다른 표현에 대한 메타데이터는 해당 메타데이터 정의에 나타나있지 않은 이상 코딩 형태에 관한 것이다. 전형적으로, 표현은 오직 렌더링이나 유사한 사용에 바로 앞서 디코딩된다.

만약 미디어 타입이 고유한 인코딩을 포함한다면, 항상 압축되는 데이터 포맷 처럼, 해당 인코딩은 Content-Encoding에 재언급되지 않을 것이고 그것이 콘텐츠 코딩들 중 하나와 같은 알고리즘인 것으로 나타날지라도 그렇다. 이러한 콘텐츠 코딩은 오직, 어떤 괴상한 이유에서, 표현을 형성하기 위해 인코딩이 두번째 적용되면 나열될 것이다. 마찬가지로, 오리진 서버는 같은 데이터를 코딩이 Content-Type 아니면 Content-Encoding의 부분으로 정의됐는지 여부만 다른 여러 표현들로 발행할 수도 있는데, 이는 일부 유저 에이전트들이 그들이 각 응답을 처리하며 다르게 행동할 수 있기 때문이다(예를 들어, 자동 압축 해제와 콘텐츠 렌더링 대신에 "다른 이름으로 저장" 대화상자 열기).

오리진 서버는 아마(MAY) 요청 메시지의 표현이 받아들일 수 없는 콘텐츠 코딩으로 되어 있다면 415(Unsupported Media Type) 상태 코드로 응답할 수 있을 것이다.

#### 8.4.1. Content Codings

콘텐츠 코딩 값들은 표현에 대해 적용되어 있는 혹은 적용될 수 있는 인코딩 변환을 나타낸다. 콘텐츠 코딩들은 주로 한 표현이 압축되도록 그게 아니면 정보의 손실 없이 그 기반이되는 미디어 타입의 정체성을 잃지 않으면서 유용하게 변형하도록 허용하기 위해 사용된다. 자주, 표현은 코딩된 형태로 저장되고, 직접 전송되며, 그리고 오직 마지막 수신자에 의해서만 디코딩된다.

     content-coding   = token

모든 콘텐츠 코딩들은 대소문자를 구별하지 않고 "HTTP Content Coding Registry" 내에, 16.6절에 정의된대로 등록되어야 한다.

콘텐츠-코딩 값들은 Accept-Encoding(12.5.3절)과 Content-Encoding(8.4절) 헤더 필드들에서 사용된다.

##### 8.4.1.1. Compress Coding

"compress" 코딩은 adaptive Lempel-Ziv-Welch(LZW) 코딩[[Welch](https://ieeexplore.ieee.org/document/1659158/)이고 UNIX 파일 압축 프로그램 "compress"에 의해 흔히 생성된다. 수신자는 웬만하면(SHOULD) "x-compress"를 "compress"와 동등한 것으로 간주해야 한다

##### 8.4.1.2. Deflate Coding

"deflate" 코딩은 "zlib" 데이터 포맷[[RFC1950](https://www.rfc-editor.org/info/rfc1950)]이고 Lampel-Ziv(LZ77) 압축 알고리즘과 Huffman coding의 조합을 사용하는 "deflate" 압축된 데이터스트림[[RFC1951](https://www.rfc-editor.org/info/rfc1951)]을 포함한다.

_Note:_ 일부 준수하지 않는 구현체들은 "deflate" 압축된 데이터를 zlib wrapper 없이 보내기도 한다.

##### 8.4.1.3. Gzip Coding

"gzip" 코딩은 32-bit Cyclic Redundancy Check(CRC)를 사용하는 LZ77 코딩이며 흔히 gzip 파일 압축 프로그램[[RFC1952](https://www.rfc-editor.org/info/rfc1952)]에 의해 생성된다. 수신자는 웬만하면(SHOULD) "x-gzip"을 "gzip"과 동등하게 여겨야 한다.

### 8.5. Content-Language

"Content-Language" 헤더 필드는 표현의 의도된 청중들의 자연 언어(들)을 기술한다. 다만 이것이 표현 내에 사용된 모든 언어들과 동등하지는 않다는 것을 명심하라.

     Content-Language = #language-tag

언어 태그들은 8.5.1절에 정의되어 있다. Content-Language의 주된 목적은 유저 자신들이 선호하는 언어에 따라 표현들을 식별하고 구분할 수 있도록 하기 위함이다. 이리하여, 콘텐츠가 오로지 덴마크어를 구사할 수 있는 청중들을 위해 의도된 것이라면, 적절한 필드는 바로

Content-Language: da

만약 Content-Language가 지정되지 않았다면, 기본적으로 콘텐츠가 모든 언어의 청중들을 위해 의도된 것으로 된다. 이는 발신자가 콘텐츠가 어떠한 자연 언어들에 대해서도 특정되지 않음을 의미할 수도 있고, 어떤 언어가 의도된 것인지 모른다는 것을 의미할 수도 있다.

여러 청중들을 대상으로 한 콘텐츠를 위해서는 아마(MAY) 여러 언어들이 나열될 수도 있을 것이다. 예를 들어, 마오리 언어 원본과 영어 버전들이 동시에 제공되는, "Treaty of Waitangi"의 인도는, 다음과 같이 요구될 것이다

     Content-Language: mi, en

그러나, 그저 표현 내에 여러 언어들이 나타나는 것이 다국어를 구사하는 청중을 대상으로 함을 의미하지는 않는다. "A First Lesson in Latin"과 같은, 분명히 영어를 구사하는 청중들이 사용하도록 의도된, 초급 언어 입문서가 그 예시가 될 것이다. 이 경우, Content-Language는 적절히 "en"만을 포함해야 한다.

Content-Language MAY be applied to any media type -- it is not
limited to textual documents.

Content-Language는 아마(MAY) 어떠한 미디어 타입에 대해서도 적용될 수 있을 것이다 -- 텍스트 문서들로만 제한되어 있지 않다.

#### 8.5.1. 언어 태그들

언어 태그는, [[RFC5646](https://www.rfc-editor.org/info/rfc5646)]에 정의된 대로, 말해지거나, 써지거나, 혹은 그렇지 않으면 인간들에 의해 다른 인간들에게 정보 통신을 위해 전달된 자연 언어를 식별한다. 컴퓨터 언어들은 명시적으로 제외된다.

HTTP는 Accent-Language와 Content-Language 헤더 필드들 내에서 언어 태그들을 사용한다. Accept-Language는 12.5.4절에 정의된 더 넓은 language-range production을 사용하는 반면, Content-Language는 아래에 정의된 language-tag production을 사용한다.

     language-tag = <Language-Tag, [RFC5646], 2.1절 참조>

언어 태그는 각각이 하이픈 문자("-", %x2D)에 의해 구별되는, 하나 이상의 대소문자를 구별하지 않는 서브태그들의 시퀀스다. 대부분의 경우, 언어 태그는 관련된 넓은 언어군을 식별하는 주요 언어 서브태그(예를 들어, "en" = English)로 구성되며, 선택적으로 언어의 범위를 세밀히 조정하거나 좁히는 서브태그들의 시리즈가 뒤따른다(예를 들어, "en-CA" = 캐나다에서 사용되는 영어의 변형). 언어 태그 내에 공백은 허용되지 않는다. 예시 태그들은 다음과 같다:

     fr, en-US, es-419, az-Arab, x-pig-latin, man-Nkoo-GN

더 자세한 정보를 위해서는 [[RFC5646](https://www.rfc-editor.org/info/rfc5646)]를 참조하라.

### 8.6. Content-Length

"Content-Length" 헤더 필드는 연관된 표현의 데이터 길이를 옥텟들의 비음수 10진수로 나타낸다. 표현을 콘텐츠로 전송할 때, Content-Length는 특히 포함된 데이터의 양을 나타내어 프레이밍을 구분하는데 사용할 수 있도록 한다(예를 들어, [[HTTP/1.1](https://www.rfc-editor.org/info/rfc9112)]의 6.2절). 다른 경우들에서, Content-Length는 선택된 표현의 현재 길이를 나타내며, 수신자들이 전송 시간을 추정하거나 이전에 저장된 표현들과 비교하는데 사용할 수 있다.

     Content-Length = 1*DIGIT

예시는

     Content-Length: 3495

유저 에이전트는 메소드가 동봉된 콘텐츠에 대한 의미를 정의하고 Transfer-Encoding을 따로 보내지 않을 때의 요청에는 웬만하면(SHOULD) Content-Length를 보내야 한다. 예를 들어, 유저 에이전트는 보통 값이 0일 때 조차(빈 콘텐츠를 나타내며) POST 요청에서는 Content-Length를 보낸다. 유저 에이전트는 요청 메시지가 콘텐츠를 포함하지 않고 메소드 의미체계가 그러한 데이터를 기대하지 않을 때는 웬만해서는(SHOULD NOT) Content-Length를 보내지 않아야 한다.

서버는 아마(MAY) HEAD 요청에 대한 응답에서 Content-Length 헤더 필드를 보낼 수 있을 것이다.(9.3.2절); 서버는 같은 요청이 GET 메소드를 사용했다면 응답의 콘텐츠에 보내졌을 옥텟들의 십진수로된 수와 그 필드 값이 동일하지 않은 한 그러한 응답에 Content-Length를 절대(MUST NOT) 보내서는 안된다.

서버는 아마(MAY) 조건부 GET 요청에 대한 304 (Not Modified) 응답에 Content-Length 헤더 필드를 보낼 수 있을 것이다(15.4.5절); 서버는 같은 요청에 대한 200 (OK) 응답의 콘텐츠에 보내졌을 옥텟들의 십진수로된 수와 그 필드 값이 동일하지 않은 한 그러한 응답에 절대(MUST NOT) Content-Length를 보내서는 안된다.

서버는 1xx(Informational) 혹은 204(No Content) 상태 코드의 어떠한 응답에도 절대(MUST NOT) Content-Length 헤더 필드를 보내서는 안된다. 서버는 CONNECT 요청에 대한 어떠한 2xx(Successful) 응답에도 Content-Length 헤더필드를 절대(MUST NOT) 보내서는 안된다.

위에 정의된 경우들 외에, Transfer-Encoding이 부재한 경우, 오리진 서버는 웬만하면(SHOULD) 콘텐츠 사이즈가 완전한 헤더 섹션을 보내기 전에 알려져 있을 때라면 Content-Length 헤더 필드를 보내야 한다. 이는 다운스트림 수신자들이 전송 진행 상황을 측정할 수 있게 하고, 언제 수신된 메시지가 완료될지 알 수 있게 하고, 그리고 잠재적으로 추가적인 요청들에 대해 해당 연결을 재사용할 수 있게 한다.

Content-Length 필드 값이 0 이상이라면 어떠한 값이든 유효하다. 콘텐츠 길이에 대해 미리 정의된 제한 같은 것은 없기 때문에, 수신자는 반드시(MUST) 잠재적으로 큰 십진수 숫자들을 예측하고 정수 변환 오버플로우나 정수 변환에 의한 정확도 손실로 인한 파싱 에러들을 방지해야 한다(17.5절).

HTTP/1.1에서 Content-Length는 메시지 경계 결정을 위해 사용되기 때문에, 그 필드 값은 메시지가 다운스트림 수신자들에게 어떻게 파싱될지에 영향을 미치고 이는 당장의 연결이 HTTP/1.1이 아닐 때 조차도 그러하다. 만약 메시지가 다운스트림 중개자에 의해 포워드된다면, 수신된 메시지 프레이밍과 불일치한 Content-Length 필드 값은 요청 스머글링이나 응답 스플리팅으로 인한 보안 실패를 초래할 수 있다.

결론적으로, 발신자는 절대(MUST NOT) 잘못된 것으로 알려진 Content-Length 헤더 필드 값을 가진 메시지를 포워드해서는 안된다.

마찬가지로, 발신자는 위의 ABNF와 매치되지 않는 Content-Length 헤더 필드를 가진 메시지를 절대(MUST NOT) 포워드해서는 안되며, 다만 하나의 예외는: 콤마로 구분되는 반복되는 같은 십진수 값의 리스트(예를 들어, "Content-Length: 42, 42")로 구성된 Content-Length 헤더 필드 값의 수신자는 아마(MAY) 해당 메시지를 유효하지 않은 것으로 거부하거나 그 유효하지 않은 필드 값을 해당 십진수 값의 단일 인스턴스로 대체할 수 있을 것이며, 이는 업스트림 메시지 프로세서에 의해 중복이 생성되거나 결합된 것을 나타낼 가능성이 높기 때문이다.

### 8.7. Content-Location

"Content-Location" 헤더 필드는 해당 메시지 콘텐츠의 표현에 해당하는 특정한 자원을 위한 식별자로 사용될 수 있는 URI를 참조한다. 다시 말해, 만약 누군가 이 메시지가 생성된 시점에 해당 URI에 대해 GET 요청을 수행한다면, 그 200(OK) 응답은 기존 메시지의 콘텐츠로 동봉되어 있는 표현과 동일한 것을 포함할 것이다.

     Content-Location = absolute-URI / partial-URI

필드 값은 absolute-URI 혹은 partial-URI다. 후자의 경우(4절), 참조된 URI는 타겟 URI에 대해 상대적이다([[URI](https://www.rfc-editor.org/info/rfc3986)], 5절).

Content-Location 값은 타겟 URI에 대한 대체가 아니다(7.1절). 표현 메타데이터다. [[RFC2557](https://www.rfc-editor.org/info/rfc2557)]의 4절에 MIME body parts를 위해 정의된 같은 이름의 헤더 필드와 같은 구문과 의미체계를 가지고 있다. 그러나, HTTP 메시지에서 나타나는 그 모습은 HTTP 수신자들에 대해 약간 특별한 암시들을 갖는다.

만약 Content-Length가 2xx(Successful) 응답 메시지에 포함되고 그 값이(절대 형태로 전환된 후) 타겟 URI와 같은 URI를 참조한다면, 수신자는 아마(MAY) 그 콘텐츠가 메시지 발생 날짜에 의해 나타내진 시점의 해당 리소스에 대한 현재 표현이라고 간주할 수 있을 것이다. GET(9.3.1절) 혹은 HEAD(9.3.2절) 요청에 대해서는, 이것이 Content-Location이 서버에 의해 제공되지 않을 때의 기본 의미체계와 같다. PUT(9.3.4절)이나 POST(9.3.3절) 같은 상태-변화 요청에 대해서는, 서버의 응답이 해당 리소스의 새로운 표현을 포함한다는 것을 암시하여, 오직 행동에 대해 보고하기만 하는 표현들로부터 그것을 구분한다("It worked!" 같은). 이는 상태를 작성하는 애플리케이션들이 추가적인 GET 요청에 대한 필요 없이 그들의 로컬 카피들을 업데이트하도록 한다.

만약 Content-Length가 2xx(Successful) 응답 메시지에 포함되며 그 필드 값이 타겟 URI와 다른 URI를 참조한다면, 오리진 서버는 해당 URI가 동봉된 표현에 해당하는 다른 리소스를 위한 식별자라고 주장하는 것이다. 그러한 주장은 오직 두 식별자들 모두 HTTP를 통해 프로그램적으로 결정될 수 없는, 같은 리소스 소유자를 공유할 때만 신뢰할 수 있다.

- GET이나 HEAD 요청에 대해, 이는 타겟 URI가 콘텐츠 협상 대상이 되는 리소스를 가리키고 있고 Content-Location 필드 값은 선택된 표현에 대해 더 구체적인 식별자라는 것을 나타낸다.

- 상태-변화 메소드에 대한 201(Created) 응답에 대해, Location 필드 값과 동일한 Content-Location 필드 값은 이 콘텐츠가 새로 생성된 리소스의 현재 표현임을 나타낸다.

- 그렇지 않으면, 그러한 Content-Location은 이 콘텐츠가 요청된 행동의 상태를 보고하는 표현이라는 것과 그 보고가 주어진 URI에서(차후의 GET 접근에서) 가용하다는 것을 나타낸다. 예를 들어, POST 요청을 통해 만들어진 구매 트랜잭션은 200(OK) 응답의 콘텐츠로 영수증 문서를 포함할 수 있을 것이다; Content-Location 필드 값은 차후에 그와 같은 영수증의 복사본을 얻기 위한 식별자를 제공한다.

요청 메시지에 Content-Location을 보내는 유저 에이전트는 그 값이 유저 에이전트가 동봉된 표현의 콘텐츠를 원래 얻었던 곳을 가리킨다고 말하는 것이다(유저에 의해 어떠한 수정들이 이루어지기 전에). 다시 말해, 유저 에이전트는 원래 표현의 소스에게 백 링크를 제공하는 것이다.

요청 메시지에 Content-Location 필드를 수신하는 오리진 서버는 반드시(MUST) 그 정보를 표현의 일부로서 그대로 저장되는 메타데이터로 보다 일시적인 요청 콘텍스트로 다뤄야한다. 오리진 서버는, 소스 링크들이나 버저닝 메타데이터 내에서 처럼 아마(MAY) 해당 콘텍스트를 요청을 처리하는 것을 가이드하기 위해서나 다른 용도로 저장하기 위해서 사용할 수 있을 것이다. 그러나, 오리진 서버는 절대 (MUST NOT) 그러한 콘텍스트 정보를 요청 의미체계를 변경하기 위해 사용해서는 안된다.

예를 들어, 클라이언트가 협상된 리소스에 PUT 요청을 하고 오리진 서버가 해당 PUT을 수용한다면(리디렉션 없이), 해당 리소스의 새로운 상태는 해당 PUT에서 공급된 햔 표현과 일관적일 것으로 기대된다; Content-Location은 협상된 표현들 중 오직 하나만 업데이트 하기 위한 콘텐츠 역선택 식별자의 형태로 사용될 수 없댜. 만약 유저 에이전트가 후자의 의미체계를 원했다면, PUT을 해당 Content-Location URI에 직접 적용했을 것이다.

### 8.8. Validator 필드들

리소스 메타데이터는 조건부 요청을 만들기 위해 전제 조건(13.12절) 내에서 사용될 수 있다면 "validator"라고 불린다. Validator 필드들은 선택된 표현에 대한 현재 validator를 전달한다(3.2절).

안전한 요청들에 대한 응답들에서, validator 필드들은 오리진 서버가 응답을 핸들링하는 동안 선택한 표현을 설명한다. 메소드와 상태 코드 의미 체계에 따라, 주어진 응답에 대해 선택된 표현이 필연적으로 응답 콘텐츠로 동봉된 표현과 동일하진 않으니, 이를 명심하라.

상태-변화 요청에 대한 성공적인 응답에서, validator 필드들은 요청을 처리한 결과로서 이전에 선택된 표현을 대체한 새로운 표현을 설명한다.

예를 들어, 201(Created) 응답의 ETag 필드는 새로이 생성된 리소스의 표현의 엔티티 태그를 전달하여, 그 엔티티 태그가 나중의 조건부 요청들에서 "lost update" 문제를 예방하기 위한 validator로 쓰이도록 한다.

이 사양서는 리소스 상태를 관측하고 전제 조건을 테스트하는데 흔히 사용되는 두 형태의 메타데이터를 정의 한다: 수정 날짜들(8.8.2절)과 불투명 엔티티 태그들(8.8.3절). 리소스 상태를 반영하는 추가적인 메타데이터는 HTTP의 다양한 확장들에 의해 정의되어 있으며, Web Distributed Authoring and Versioning[[WEBDAV](https://www.rfc-editor.org/info/rfc4918)] 같은 것이 있고, 이 사양의 범위를 넘어선다.

#### 8.8.1. Weak versus Strong

Validators come in two flavors: strong or weak. Weak validators are
easy to generate but are far less useful for comparisons. Strong
validators are ideal for comparisons but can be very difficult (and
occasionally impossible) to generate efficiently. Rather than impose
that all forms of resource adhere to the same strength of validator,
HTTP exposes the type of validator in use and imposes restrictions on
when weak validators can be used as preconditions.

A "strong validator" is representation metadata that changes value
whenever a change occurs to the representation data that would be
observable in the content of a 200 (OK) response to GET.

A strong validator might change for reasons other than a change to
the representation data, such as when a semantically significant part
of the representation metadata is changed (e.g., Content-Type), but
it is in the best interests of the origin server to only change the
value when it is necessary to invalidate the stored responses held by
remote caches and authoring tools.

Cache entries might persist for arbitrarily long periods, regardless
of expiration times. Thus, a cache might attempt to validate an
entry using a validator that it obtained in the distant past. A
strong validator is unique across all versions of all representations
associated with a particular resource over time. However, there is
no implication of uniqueness across representations of different
resources (i.e., the same strong validator might be in use for
representations of multiple resources at the same time and does not
imply that those representations are equivalent).

There are a variety of strong validators used in practice. The best
are based on strict revision control, wherein each change to a
representation always results in a unique node name and revision
identifier being assigned before the representation is made
accessible to GET. A collision-resistant hash function applied to
the representation data is also sufficient if the data is available
prior to the response header fields being sent and the digest does
not need to be recalculated every time a validation request is
received. However, if a resource has distinct representations that
differ only in their metadata, such as might occur with content
negotiation over media types that happen to share the same data
format, then the origin server needs to incorporate additional
information in the validator to distinguish those representations.

In contrast, a "weak validator" is representation metadata that might
not change for every change to the representation data. This
weakness might be due to limitations in how the value is calculated
(e.g., clock resolution), an inability to ensure uniqueness for all
possible representations of the resource, or a desire of the resource
owner to group representations by some self-determined set of
equivalency rather than unique sequences of data.

An origin server SHOULD change a weak entity tag whenever it
considers prior representations to be unacceptable as a substitute
for the current representation. In other words, a weak entity tag
ought to change whenever the origin server wants caches to invalidate
old responses.

For example, the representation of a weather report that changes in
content every second, based on dynamic measurements, might be grouped
into sets of equivalent representations (from the origin server's
perspective) with the same weak validator in order to allow cached
representations to be valid for a reasonable period of time (perhaps
adjusted dynamically based on server load or weather quality).
Likewise, a representation's modification time, if defined with only
one-second resolution, might be a weak validator if it is possible
for the representation to be modified twice during a single second
and retrieved between those modifications.

Likewise, a validator is weak if it is shared by two or more
representations of a given resource at the same time, unless those
representations have identical representation data. For example, if
the origin server sends the same validator for a representation with
a gzip content coding applied as it does for a representation with no
content coding, then that validator is weak. However, two
simultaneous representations might share the same strong validator if
they differ only in the representation metadata, such as when two
different media types are available for the same representation data.

Strong validators are usable for all conditional requests, including
cache validation, partial content ranges, and "lost update"
avoidance. Weak validators are only usable when the client does not
require exact equality with previously obtained representation data,
such as when validating a cache entry or limiting a web traversal to
recent changes.

#### 8.8.2. Last-Modified

The "Last-Modified" header field in a response provides a timestamp
indicating the date and time at which the origin server believes the
selected representation was last modified, as determined at the
conclusion of handling the request.

     Last-Modified = HTTP-date

An example of its use is

Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT

##### 8.8.2.1. Generation

An origin server SHOULD send Last-Modified for any selected
representation for which a last modification date can be reasonably
and consistently determined, since its use in conditional requests
and evaluating cache freshness ([CACHING]) can substantially reduce
unnecessary transfers and significantly improve service availability
and scalability.

A representation is typically the sum of many parts behind the
resource interface. The last-modified time would usually be the most
recent time that any of those parts were changed. How that value is
determined for any given resource is an implementation detail beyond
the scope of this specification.

An origin server SHOULD obtain the Last-Modified value of the
representation as close as possible to the time that it generates the
Date field value for its response. This allows a recipient to make
an accurate assessment of the representation's modification time,
especially if the representation changes near the time that the
response is generated.

An origin server with a clock (as defined in Section 5.6.7) MUST NOT
generate a Last-Modified date that is later than the server's time of
message origination (Date, Section 6.6.1). If the last modification
time is derived from implementation-specific metadata that evaluates
to some time in the future, according to the origin server's clock,
then the origin server MUST replace that value with the message
origination date. This prevents a future modification date from
having an adverse impact on cache validation.

An origin server without a clock MUST NOT generate a Last-Modified
date for a response unless that date value was assigned to the
resource by some other system (presumably one with a clock).

##### 8.8.2.2. Comparison

A Last-Modified time, when used as a validator in a request, is
implicitly weak unless it is possible to deduce that it is strong,
using the following rules:

- The validator is being compared by an origin server to the actual
  current validator for the representation and,

- That origin server reliably knows that the associated
  representation did not change twice during the second covered by
  the presented validator;

or

- The validator is about to be used by a client in an
  If-Modified-Since, If-Unmodified-Since, or If-Range header field,
  because the client has a cache entry for the associated
  representation, and

- That cache entry includes a Date value which is at least one
  second after the Last-Modified value and the client has reason to
  believe that they were generated by the same clock or that there
  is enough difference between the Last-Modified and Date values to
  make clock synchronization issues unlikely;

or

- The validator is being compared by an intermediate cache to the
  validator stored in its cache entry for the representation, and

- That cache entry includes a Date value which is at least one
  second after the Last-Modified value and the cache has reason to
  believe that they were generated by the same clock or that there
  is enough difference between the Last-Modified and Date values to
  make clock synchronization issues unlikely.

This method relies on the fact that if two different responses were
sent by the origin server during the same second, but both had the
same Last-Modified time, then at least one of those responses would
have a Date value equal to its Last-Modified time.

#### 8.8.3. ETag

The "ETag" field in a response provides the current entity tag for
the selected representation, as determined at the conclusion of
handling the request. An entity tag is an opaque validator for
differentiating between multiple representations of the same
resource, regardless of whether those multiple representations are
due to resource state changes over time, content negotiation
resulting in multiple representations being valid at the same time,
or both. An entity tag consists of an opaque quoted string, possibly
prefixed by a weakness indicator.

     ETag       = entity-tag

     entity-tag = [ weak ] opaque-tag
     weak       = %s"W/"
     opaque-tag = DQUOTE *etagc DQUOTE
     etagc      = %x21 / %x23-7E / obs-text
                ; VCHAR except double quotes, plus obs-text

      |  *Note:* Previously, opaque-tag was defined to be a quoted-
      |  string ([RFC2616], Section 3.11); thus, some recipients might
      |  perform backslash unescaping.  Servers therefore ought to avoid
      |  backslash characters in entity tags.

An entity tag can be more reliable for validation than a modification
date in situations where it is inconvenient to store modification
dates, where the one-second resolution of HTTP-date values is not
sufficient, or where modification dates are not consistently
maintained.

Examples:

ETag: "xyzzy"
ETag: W/"xyzzy"
ETag: ""

An entity tag can be either a weak or strong validator, with strong
being the default. If an origin server provides an entity tag for a
representation and the generation of that entity tag does not satisfy
all of the characteristics of a strong validator (Section 8.8.1),
then the origin server MUST mark the entity tag as weak by prefixing
its opaque value with "W/" (case-sensitive).

A sender MAY send the ETag field in a trailer section (see
Section 6.5). However, since trailers are often ignored, it is
preferable to send ETag as a header field unless the entity tag is
generated while sending the content.

##### 8.8.3.1. Generation

The principle behind entity tags is that only the service author
knows the implementation of a resource well enough to select the most
accurate and efficient validation mechanism for that resource, and
that any such mechanism can be mapped to a simple sequence of octets
for easy comparison. Since the value is opaque, there is no need for
the client to be aware of how each entity tag is constructed.

For example, a resource that has implementation-specific versioning
applied to all changes might use an internal revision number, perhaps
combined with a variance identifier for content negotiation, to
accurately differentiate between representations. Other
implementations might use a collision-resistant hash of
representation content, a combination of various file attributes, or
a modification timestamp that has sub-second resolution.

An origin server SHOULD send an ETag for any selected representation
for which detection of changes can be reasonably and consistently
determined, since the entity tag's use in conditional requests and
evaluating cache freshness ([CACHING]) can substantially reduce
unnecessary transfers and significantly improve service availability,
scalability, and reliability.

##### 8.8.3.2. Comparison

There are two entity tag comparison functions, depending on whether
or not the comparison context allows the use of weak validators:

"Strong comparison": two entity tags are equivalent if both are not
weak and their opaque-tags match character-by-character.

"Weak comparison": two entity tags are equivalent if their opaque-
tags match character-by-character, regardless of either or both
being tagged as "weak".

The example below shows the results for a set of entity tag pairs and
both the weak and strong comparison function results:

+========+========+===================+=================+
| ETag 1 | ETag 2 | Strong Comparison | Weak Comparison |
+========+========+===================+=================+
| W/"1" | W/"1" | no match | match |
+--------+--------+-------------------+-----------------+
| W/"1" | W/"2" | no match | no match |
+--------+--------+-------------------+-----------------+
| W/"1" | "1" | no match | match |
+--------+--------+-------------------+-----------------+
| "1" | "1" | match | match |
+--------+--------+-------------------+-----------------+

                            Table 3

##### 8.8.3.3. Example: Entity Tags Varying on Content-Negotiated Resources

Consider a resource that is subject to content negotiation
(Section 12), and where the representations sent in response to a GET
request vary based on the Accept-Encoding request header field
(Section 12.5.3):

> > Request:

GET /index HTTP/1.1
Host: www.example.com
Accept-Encoding: gzip

In this case, the response might or might not use the gzip content
coding. If it does not, the response might look like:

> > Response:

HTTP/1.1 200 OK
Date: Fri, 26 Mar 2010 00:05:00 GMT
ETag: "123-a"
Content-Length: 70
Vary: Accept-Encoding
Content-Type: text/plain

Hello World!
Hello World!
Hello World!
Hello World!
Hello World!

An alternative representation that does use gzip content coding would
be:

> > Response:

HTTP/1.1 200 OK
Date: Fri, 26 Mar 2010 00:05:00 GMT
ETag: "123-b"
Content-Length: 43
Vary: Accept-Encoding
Content-Type: text/plain
Content-Encoding: gzip

...binary data...

      |  *Note:* Content codings are a property of the representation
      |  data, so a strong entity tag for a content-encoded
      |  representation has to be distinct from the entity tag of an
      |  unencoded representation to prevent potential conflicts during
      |  cache updates and range requests.  In contrast, transfer
      |  codings (Section 7 of [HTTP/1.1]) apply only during message
      |  transfer and do not result in distinct entity tags.

## 9. Methods

### 9.1. Overview

The request method token is the primary source of request semantics;
it indicates the purpose for which the client has made this request
and what is expected by the client as a successful result.

The request method's semantics might be further specialized by the
semantics of some header fields when present in a request if those
additional semantics do not conflict with the method. For example, a
client can send conditional request header fields (Section 13.1) to
make the requested action conditional on the current state of the
target resource.

HTTP is designed to be usable as an interface to distributed object
systems. The request method invokes an action to be applied to a
target resource in much the same way that a remote method invocation
can be sent to an identified object.

     method = token

The method token is case-sensitive because it might be used as a
gateway to object-based systems with case-sensitive method names. By
convention, standardized methods are defined in all-uppercase US-
ASCII letters.

Unlike distributed objects, the standardized request methods in HTTP
are not resource-specific, since uniform interfaces provide for
better visibility and reuse in network-based systems [REST]. Once
defined, a standardized method ought to have the same semantics when
applied to any resource, though each resource determines for itself
whether those semantics are implemented or allowed.

This specification defines a number of standardized methods that are
commonly used in HTTP, as outlined by the following table.

+=========+============================================+=========+
| Method | Description | Section |
| Name | | |
+=========+============================================+=========+
| GET | Transfer a current representation of the | 9.3.1 |
| | target resource. | |
+---------+--------------------------------------------+---------+
| HEAD | Same as GET, but do not transfer the | 9.3.2 |
| | response content. | |
+---------+--------------------------------------------+---------+
| POST | Perform resource-specific processing on | 9.3.3 |
| | the request content. | |
+---------+--------------------------------------------+---------+
| PUT | Replace all current representations of the | 9.3.4 |
| | target resource with the request content. | |
+---------+--------------------------------------------+---------+
| DELETE | Remove all current representations of the | 9.3.5 |
| | target resource. | |
+---------+--------------------------------------------+---------+
| CONNECT | Establish a tunnel to the server | 9.3.6 |
| | identified by the target resource. | |
+---------+--------------------------------------------+---------+
| OPTIONS | Describe the communication options for the | 9.3.7 |
| | target resource. | |
+---------+--------------------------------------------+---------+
| TRACE | Perform a message loop-back test along the | 9.3.8 |
| | path to the target resource. | |
+---------+--------------------------------------------+---------+

                                Table 4

All general-purpose servers MUST support the methods GET and HEAD.
All other methods are OPTIONAL.

The set of methods allowed by a target resource can be listed in an
Allow header field (Section 10.2.1). However, the set of allowed
methods can change dynamically. An origin server that receives a
request method that is unrecognized or not implemented SHOULD respond
with the 501 (Not Implemented) status code. An origin server that
receives a request method that is recognized and implemented, but not
allowed for the target resource, SHOULD respond with the 405 (Method
Not Allowed) status code.

Additional methods, outside the scope of this specification, have
been specified for use in HTTP. All such methods ought to be
registered within the "Hypertext Transfer Protocol (HTTP) Method
Registry", as described in Section 16.1.

9.2. Common Method Properties

9.2.1. Safe Methods

Request methods are considered "safe" if their defined semantics are
essentially read-only; i.e., the client does not request, and does
not expect, any state change on the origin server as a result of
applying a safe method to a target resource. Likewise, reasonable
use of a safe method is not expected to cause any harm, loss of
property, or unusual burden on the origin server.

This definition of safe methods does not prevent an implementation
from including behavior that is potentially harmful, that is not
entirely read-only, or that causes side effects while invoking a safe
method. What is important, however, is that the client did not
request that additional behavior and cannot be held accountable for
it. For example, most servers append request information to access
log files at the completion of every response, regardless of the
method, and that is considered safe even though the log storage might
become full and cause the server to fail. Likewise, a safe request
initiated by selecting an advertisement on the Web will often have
the side effect of charging an advertising account.

Of the request methods defined by this specification, the GET, HEAD,
OPTIONS, and TRACE methods are defined to be safe.

The purpose of distinguishing between safe and unsafe methods is to
allow automated retrieval processes (spiders) and cache performance
optimization (pre-fetching) to work without fear of causing harm. In
addition, it allows a user agent to apply appropriate constraints on
the automated use of unsafe methods when processing potentially
untrusted content.

A user agent SHOULD distinguish between safe and unsafe methods when
presenting potential actions to a user, such that the user can be
made aware of an unsafe action before it is requested.

When a resource is constructed such that parameters within the target
URI have the effect of selecting an action, it is the resource
owner's responsibility to ensure that the action is consistent with
the request method semantics. For example, it is common for Web-
based content editing software to use actions within query
parameters, such as "page?do=delete". If the purpose of such a
resource is to perform an unsafe action, then the resource owner MUST
disable or disallow that action when it is accessed using a safe
request method. Failure to do so will result in unfortunate side
effects when automated processes perform a GET on every URI reference
for the sake of link maintenance, pre-fetching, building a search
index, etc.

9.2.2. Idempotent Methods

A request method is considered "idempotent" if the intended effect on
the server of multiple identical requests with that method is the
same as the effect for a single such request. Of the request methods
defined by this specification, PUT, DELETE, and safe request methods
are idempotent.

Like the definition of safe, the idempotent property only applies to
what has been requested by the user; a server is free to log each
request separately, retain a revision control history, or implement
other non-idempotent side effects for each idempotent request.

Idempotent methods are distinguished because the request can be
repeated automatically if a communication failure occurs before the
client is able to read the server's response. For example, if a
client sends a PUT request and the underlying connection is closed
before any response is received, then the client can establish a new
connection and retry the idempotent request. It knows that repeating
the request will have the same intended effect, even if the original
request succeeded, though the response might differ.

A client SHOULD NOT automatically retry a request with a non-
idempotent method unless it has some means to know that the request
semantics are actually idempotent, regardless of the method, or some
means to detect that the original request was never applied.

For example, a user agent can repeat a POST request automatically if
it knows (through design or configuration) that the request is safe
for that resource. Likewise, a user agent designed specifically to
operate on a version control repository might be able to recover from
partial failure conditions by checking the target resource
revision(s) after a failed connection, reverting or fixing any
changes that were partially applied, and then automatically retrying
the requests that failed.

Some clients take a riskier approach and attempt to guess when an
automatic retry is possible. For example, a client might
automatically retry a POST request if the underlying transport
connection closed before any part of a response is received,
particularly if an idle persistent connection was used.

A proxy MUST NOT automatically retry non-idempotent requests. A
client SHOULD NOT automatically retry a failed automatic retry.

9.2.3. Methods and Caching

For a cache to store and use a response, the associated method needs
to explicitly allow caching and to detail under what conditions a
response can be used to satisfy subsequent requests; a method
definition that does not do so cannot be cached. For additional
requirements see [CACHING].

This specification defines caching semantics for GET, HEAD, and POST,
although the overwhelming majority of cache implementations only
support GET and HEAD.

9.3. Method Definitions

9.3.1. GET

The GET method requests transfer of a current selected representation
for the target resource. A successful response reflects the quality
of "sameness" identified by the target URI (Section 1.2.2 of [URI]).
Hence, retrieving identifiable information via HTTP is usually
performed by making a GET request on an identifier associated with
the potential for providing that information in a 200 (OK) response.

GET is the primary mechanism of information retrieval and the focus
of almost all performance optimizations. Applications that produce a
URI for each important resource can benefit from those optimizations
while enabling their reuse by other applications, creating a network
effect that promotes further expansion of the Web.

It is tempting to think of resource identifiers as remote file system
pathnames and of representations as being a copy of the contents of
such files. In fact, that is how many resources are implemented (see
Section 17.3 for related security considerations). However, there
are no such limitations in practice.

The HTTP interface for a resource is just as likely to be implemented
as a tree of content objects, a programmatic view on various database
records, or a gateway to other information systems. Even when the
URI mapping mechanism is tied to a file system, an origin server
might be configured to execute the files with the request as input
and send the output as the representation rather than transfer the
files directly. Regardless, only the origin server needs to know how
each resource identifier corresponds to an implementation and how
that implementation manages to select and send a current
representation of the target resource.

A client can alter the semantics of GET to be a "range request",
requesting transfer of only some part(s) of the selected
representation, by sending a Range header field in the request
(Section 14.2).

Although request message framing is independent of the method used,
content received in a GET request has no generally defined semantics,
cannot alter the meaning or target of the request, and might lead
some implementations to reject the request and close the connection
because of its potential as a request smuggling attack (Section 11.2
of [HTTP/1.1]). A client SHOULD NOT generate content in a GET
request unless it is made directly to an origin server that has
previously indicated, in or out of band, that such a request has a
purpose and will be adequately supported. An origin server SHOULD
NOT rely on private agreements to receive content, since participants
in HTTP communication are often unaware of intermediaries along the
request chain.

The response to a GET request is cacheable; a cache MAY use it to
satisfy subsequent GET and HEAD requests unless otherwise indicated
by the Cache-Control header field (Section 5.2 of [CACHING]).

When information retrieval is performed with a mechanism that
constructs a target URI from user-provided information, such as the
query fields of a form using GET, potentially sensitive data might be
provided that would not be appropriate for disclosure within a URI
(see Section 17.9). In some cases, the data can be filtered or
transformed such that it would not reveal such information. In
others, particularly when there is no benefit from caching a
response, using the POST method (Section 9.3.3) instead of GET can
transmit such information in the request content rather than within
the target URI.

9.3.2. HEAD

The HEAD method is identical to GET except that the server MUST NOT
send content in the response. HEAD is used to obtain metadata about
the selected representation without transferring its representation
data, often for the sake of testing hypertext links or finding recent
modifications.

The server SHOULD send the same header fields in response to a HEAD
request as it would have sent if the request method had been GET.
However, a server MAY omit header fields for which a value is
determined only while generating the content. For example, some
servers buffer a dynamic response to GET until a minimum amount of
data is generated so that they can more efficiently delimit small
responses or make late decisions with regard to content selection.
Such a response to GET might contain Content-Length and Vary fields,
for example, that are not generated within a HEAD response. These
minor inconsistencies are considered preferable to generating and
discarding the content for a HEAD request, since HEAD is usually
requested for the sake of efficiency.

Although request message framing is independent of the method used,
content received in a HEAD request has no generally defined
semantics, cannot alter the meaning or target of the request, and
might lead some implementations to reject the request and close the
connection because of its potential as a request smuggling attack
(Section 11.2 of [HTTP/1.1]). A client SHOULD NOT generate content
in a HEAD request unless it is made directly to an origin server that
has previously indicated, in or out of band, that such a request has
a purpose and will be adequately supported. An origin server SHOULD
NOT rely on private agreements to receive content, since participants
in HTTP communication are often unaware of intermediaries along the
request chain.

The response to a HEAD request is cacheable; a cache MAY use it to
satisfy subsequent HEAD requests unless otherwise indicated by the
Cache-Control header field (Section 5.2 of [CACHING]). A HEAD
response might also affect previously cached responses to GET; see
Section 4.3.5 of [CACHING].

9.3.3. POST

The POST method requests that the target resource process the
representation enclosed in the request according to the resource's
own specific semantics. For example, POST is used for the following
functions (among others):

- Providing a block of data, such as the fields entered into an HTML
  form, to a data-handling process;

- Posting a message to a bulletin board, newsgroup, mailing list,
  blog, or similar group of articles;

- Creating a new resource that has yet to be identified by the
  origin server; and

- Appending data to a resource's existing representation(s).

An origin server indicates response semantics by choosing an
appropriate status code depending on the result of processing the
POST request; almost all of the status codes defined by this
specification could be received in a response to POST (the exceptions
being 206 (Partial Content), 304 (Not Modified), and 416 (Range Not
Satisfiable)).

If one or more resources has been created on the origin server as a
result of successfully processing a POST request, the origin server
SHOULD send a 201 (Created) response containing a Location header
field that provides an identifier for the primary resource created
(Section 10.2.2) and a representation that describes the status of
the request while referring to the new resource(s).

Responses to POST requests are only cacheable when they include
explicit freshness information (see Section 4.2.1 of [CACHING]) and a
Content-Location header field that has the same value as the POST's
target URI (Section 8.7). A cached POST response can be reused to
satisfy a later GET or HEAD request. In contrast, a POST request
cannot be satisfied by a cached POST response because POST is
potentially unsafe; see Section 4 of [CACHING].

If the result of processing a POST would be equivalent to a
representation of an existing resource, an origin server MAY redirect
the user agent to that resource by sending a 303 (See Other) response
with the existing resource's identifier in the Location field. This
has the benefits of providing the user agent a resource identifier
and transferring the representation via a method more amenable to
shared caching, though at the cost of an extra request if the user
agent does not already have the representation cached.

9.3.4. PUT

The PUT method requests that the state of the target resource be
created or replaced with the state defined by the representation
enclosed in the request message content. A successful PUT of a given
representation would suggest that a subsequent GET on that same
target resource will result in an equivalent representation being
sent in a 200 (OK) response. However, there is no guarantee that
such a state change will be observable, since the target resource
might be acted upon by other user agents in parallel, or might be
subject to dynamic processing by the origin server, before any
subsequent GET is received. A successful response only implies that
the user agent's intent was achieved at the time of its processing by
the origin server.

If the target resource does not have a current representation and the
PUT successfully creates one, then the origin server MUST inform the
user agent by sending a 201 (Created) response. If the target
resource does have a current representation and that representation
is successfully modified in accordance with the state of the enclosed
representation, then the origin server MUST send either a 200 (OK) or
a 204 (No Content) response to indicate successful completion of the
request.

An origin server SHOULD verify that the PUT representation is
consistent with its configured constraints for the target resource.
For example, if an origin server determines a resource's
representation metadata based on the URI, then the origin server
needs to ensure that the content received in a successful PUT request
is consistent with that metadata. When a PUT representation is
inconsistent with the target resource, the origin server SHOULD
either make them consistent, by transforming the representation or
changing the resource configuration, or respond with an appropriate
error message containing sufficient information to explain why the
representation is unsuitable. The 409 (Conflict) or 415 (Unsupported
Media Type) status codes are suggested, with the latter being
specific to constraints on Content-Type values.

For example, if the target resource is configured to always have a
Content-Type of "text/html" and the representation being PUT has a
Content-Type of "image/jpeg", the origin server ought to do one of:

a. reconfigure the target resource to reflect the new media type;

b. transform the PUT representation to a format consistent with that
of the resource before saving it as the new resource state; or,

c. reject the request with a 415 (Unsupported Media Type) response
indicating that the target resource is limited to "text/html",
perhaps including a link to a different resource that would be a
suitable target for the new representation.

HTTP does not define exactly how a PUT method affects the state of an
origin server beyond what can be expressed by the intent of the user
agent request and the semantics of the origin server response. It
does not define what a resource might be, in any sense of that word,
beyond the interface provided via HTTP. It does not define how
resource state is "stored", nor how such storage might change as a
result of a change in resource state, nor how the origin server
translates resource state into representations. Generally speaking,
all implementation details behind the resource interface are
intentionally hidden by the server.

This extends to how header and trailer fields are stored; while
common header fields like Content-Type will typically be stored and
returned upon subsequent GET requests, header and trailer field
handling is specific to the resource that received the request. As a
result, an origin server SHOULD ignore unrecognized header and
trailer fields received in a PUT request (i.e., not save them as part
of the resource state).

An origin server MUST NOT send a validator field (Section 8.8), such
as an ETag or Last-Modified field, in a successful response to PUT
unless the request's representation data was saved without any
transformation applied to the content (i.e., the resource's new
representation data is identical to the content received in the PUT
request) and the validator field value reflects the new
representation. This requirement allows a user agent to know when
the representation it sent (and retains in memory) is the result of
the PUT, and thus it doesn't need to be retrieved again from the
origin server. The new validator(s) received in the response can be
used for future conditional requests in order to prevent accidental
overwrites (Section 13.1).

The fundamental difference between the POST and PUT methods is
highlighted by the different intent for the enclosed representation.
The target resource in a POST request is intended to handle the
enclosed representation according to the resource's own semantics,
whereas the enclosed representation in a PUT request is defined as
replacing the state of the target resource. Hence, the intent of PUT
is idempotent and visible to intermediaries, even though the exact
effect is only known by the origin server.

Proper interpretation of a PUT request presumes that the user agent
knows which target resource is desired. A service that selects a
proper URI on behalf of the client, after receiving a state-changing
request, SHOULD be implemented using the POST method rather than PUT.
If the origin server will not make the requested PUT state change to
the target resource and instead wishes to have it applied to a
different resource, such as when the resource has been moved to a
different URI, then the origin server MUST send an appropriate 3xx
(Redirection) response; the user agent MAY then make its own decision
regarding whether or not to redirect the request.

A PUT request applied to the target resource can have side effects on
other resources. For example, an article might have a URI for
identifying "the current version" (a resource) that is separate from
the URIs identifying each particular version (different resources
that at one point shared the same state as the current version
resource). A successful PUT request on "the current version" URI
might therefore create a new version resource in addition to changing
the state of the target resource, and might also cause links to be
added between the related resources.

Some origin servers support use of the Content-Range header field
(Section 14.4) as a request modifier to perform a partial PUT, as
described in Section 14.5.

Responses to the PUT method are not cacheable. If a successful PUT
request passes through a cache that has one or more stored responses
for the target URI, those stored responses will be invalidated (see
Section 4.4 of [CACHING]).

9.3.5. DELETE

The DELETE method requests that the origin server remove the
association between the target resource and its current
functionality. In effect, this method is similar to the "rm" command
in UNIX: it expresses a deletion operation on the URI mapping of the
origin server rather than an expectation that the previously
associated information be deleted.

If the target resource has one or more current representations, they
might or might not be destroyed by the origin server, and the
associated storage might or might not be reclaimed, depending
entirely on the nature of the resource and its implementation by the
origin server (which are beyond the scope of this specification).
Likewise, other implementation aspects of a resource might need to be
deactivated or archived as a result of a DELETE, such as database or
gateway connections. In general, it is assumed that the origin
server will only allow DELETE on resources for which it has a
prescribed mechanism for accomplishing the deletion.

Relatively few resources allow the DELETE method -- its primary use
is for remote authoring environments, where the user has some
direction regarding its effect. For example, a resource that was
previously created using a PUT request, or identified via the
Location header field after a 201 (Created) response to a POST
request, might allow a corresponding DELETE request to undo those
actions. Similarly, custom user agent implementations that implement
an authoring function, such as revision control clients using HTTP
for remote operations, might use DELETE based on an assumption that
the server's URI space has been crafted to correspond to a version
repository.

If a DELETE method is successfully applied, the origin server SHOULD
send

- a 202 (Accepted) status code if the action will likely succeed but
  has not yet been enacted,

- a 204 (No Content) status code if the action has been enacted and
  no further information is to be supplied, or

- a 200 (OK) status code if the action has been enacted and the
  response message includes a representation describing the status.

Although request message framing is independent of the method used,
content received in a DELETE request has no generally defined
semantics, cannot alter the meaning or target of the request, and
might lead some implementations to reject the request and close the
connection because of its potential as a request smuggling attack
(Section 11.2 of [HTTP/1.1]). A client SHOULD NOT generate content
in a DELETE request unless it is made directly to an origin server
that has previously indicated, in or out of band, that such a request
has a purpose and will be adequately supported. An origin server
SHOULD NOT rely on private agreements to receive content, since
participants in HTTP communication are often unaware of
intermediaries along the request chain.

Responses to the DELETE method are not cacheable. If a successful
DELETE request passes through a cache that has one or more stored
responses for the target URI, those stored responses will be
invalidated (see Section 4.4 of [CACHING]).

9.3.6. CONNECT

The CONNECT method requests that the recipient establish a tunnel to
the destination origin server identified by the request target and,
if successful, thereafter restrict its behavior to blind forwarding
of data, in both directions, until the tunnel is closed. Tunnels are
commonly used to create an end-to-end virtual connection, through one
or more proxies, which can then be secured using TLS (Transport Layer
Security, [TLS13]).

CONNECT uses a special form of request target, unique to this method,
consisting of only the host and port number of the tunnel
destination, separated by a colon. There is no default port; a
client MUST send the port number even if the CONNECT request is based
on a URI reference that contains an authority component with an
elided port (Section 4.1). For example,

CONNECT server.example.com:80 HTTP/1.1
Host: server.example.com

A server MUST reject a CONNECT request that targets an empty or
invalid port number, typically by responding with a 400 (Bad Request)
status code.

Because CONNECT changes the request/response nature of an HTTP
connection, specific HTTP versions might have different ways of
mapping its semantics into the protocol's wire format.

CONNECT is intended for use in requests to a proxy. The recipient
can establish a tunnel either by directly connecting to the server
identified by the request target or, if configured to use another
proxy, by forwarding the CONNECT request to the next inbound proxy.
An origin server MAY accept a CONNECT request, but most origin
servers do not implement CONNECT.

Any 2xx (Successful) response indicates that the sender (and all
inbound proxies) will switch to tunnel mode immediately after the
response header section; data received after that header section is
from the server identified by the request target. Any response other
than a successful response indicates that the tunnel has not yet been
formed.

A tunnel is closed when a tunnel intermediary detects that either
side has closed its connection: the intermediary MUST attempt to send
any outstanding data that came from the closed side to the other
side, close both connections, and then discard any remaining data
left undelivered.

Proxy authentication might be used to establish the authority to
create a tunnel. For example,

CONNECT server.example.com:443 HTTP/1.1
Host: server.example.com:443
Proxy-Authorization: basic aGVsbG86d29ybGQ=

There are significant risks in establishing a tunnel to arbitrary
servers, particularly when the destination is a well-known or
reserved TCP port that is not intended for Web traffic. For example,
a CONNECT to "example.com:25" would suggest that the proxy connect to
the reserved port for SMTP traffic; if allowed, that could trick the
proxy into relaying spam email. Proxies that support CONNECT SHOULD
restrict its use to a limited set of known ports or a configurable
list of safe request targets.

A server MUST NOT send any Transfer-Encoding or Content-Length header
fields in a 2xx (Successful) response to CONNECT. A client MUST
ignore any Content-Length or Transfer-Encoding header fields received
in a successful response to CONNECT.

A CONNECT request message does not have content. The interpretation
of data sent after the header section of the CONNECT request message
is specific to the version of HTTP in use.

Responses to the CONNECT method are not cacheable.

9.3.7. OPTIONS

The OPTIONS method requests information about the communication
options available for the target resource, at either the origin
server or an intervening intermediary. This method allows a client
to determine the options and/or requirements associated with a
resource, or the capabilities of a server, without implying a
resource action.

An OPTIONS request with an asterisk ("_") as the request target
(Section 7.1) applies to the server in general rather than to a
specific resource. Since a server's communication options typically
depend on the resource, the "_" request is only useful as a "ping" or
"no-op" type of method; it does nothing beyond allowing the client to
test the capabilities of the server. For example, this can be used
to test a proxy for HTTP/1.1 conformance (or lack thereof).

If the request target is not an asterisk, the OPTIONS request applies
to the options that are available when communicating with the target
resource.

A server generating a successful response to OPTIONS SHOULD send any
header that might indicate optional features implemented by the
server and applicable to the target resource (e.g., Allow), including
potential extensions not defined by this specification. The response
content, if any, might also describe the communication options in a
machine or human-readable representation. A standard format for such
a representation is not defined by this specification, but might be
defined by future extensions to HTTP.

A client MAY send a Max-Forwards header field in an OPTIONS request
to target a specific recipient in the request chain (see
Section 7.6.2). A proxy MUST NOT generate a Max-Forwards header
field while forwarding a request unless that request was received
with a Max-Forwards field.

A client that generates an OPTIONS request containing content MUST
send a valid Content-Type header field describing the representation
media type. Note that this specification does not define any use for
such content.

Responses to the OPTIONS method are not cacheable.

9.3.8. TRACE

The TRACE method requests a remote, application-level loop-back of
the request message. The final recipient of the request SHOULD
reflect the message received, excluding some fields described below,
back to the client as the content of a 200 (OK) response. The
"message/http" format (Section 10.1 of [HTTP/1.1]) is one way to do
so. The final recipient is either the origin server or the first
server to receive a Max-Forwards value of zero (0) in the request
(Section 7.6.2).

A client MUST NOT generate fields in a TRACE request containing
sensitive data that might be disclosed by the response. For example,
it would be foolish for a user agent to send stored user credentials
(Section 11) or cookies [COOKIE] in a TRACE request. The final
recipient of the request SHOULD exclude any request fields that are
likely to contain sensitive data when that recipient generates the
response content.

TRACE allows the client to see what is being received at the other
end of the request chain and use that data for testing or diagnostic
information. The value of the Via header field (Section 7.6.3) is of
particular interest, since it acts as a trace of the request chain.
Use of the Max-Forwards header field allows the client to limit the
length of the request chain, which is useful for testing a chain of
proxies forwarding messages in an infinite loop.

A client MUST NOT send content in a TRACE request.

Responses to the TRACE method are not cacheable.

10. Message Context

10.1. Request Context Fields

The request header fields below provide additional information about
the request context, including information about the user, user
agent, and resource behind the request.

10.1.1. Expect

The "Expect" header field in a request indicates a certain set of
behaviors (expectations) that need to be supported by the server in
order to properly handle this request.

     Expect =      #expectation
     expectation = token [ "=" ( token / quoted-string ) parameters ]

The Expect field value is case-insensitive.

The only expectation defined by this specification is "100-continue"
(with no defined parameters).

A server that receives an Expect field value containing a member
other than 100-continue MAY respond with a 417 (Expectation Failed)
status code to indicate that the unexpected expectation cannot be
met.

A "100-continue" expectation informs recipients that the client is
about to send (presumably large) content in this request and wishes
to receive a 100 (Continue) interim response if the method, target
URI, and header fields are not sufficient to cause an immediate
success, redirect, or error response. This allows the client to wait
for an indication that it is worthwhile to send the content before
actually doing so, which can improve efficiency when the data is huge
or when the client anticipates that an error is likely (e.g., when
sending a state-changing method, for the first time, without
previously verified authentication credentials).

For example, a request that begins with

PUT /somewhere/fun HTTP/1.1
Host: origin.example.com
Content-Type: video/h264
Content-Length: 1234567890987
Expect: 100-continue

allows the origin server to immediately respond with an error
message, such as 401 (Unauthorized) or 405 (Method Not Allowed),
before the client starts filling the pipes with an unnecessary data
transfer.

Requirements for clients:

- A client MUST NOT generate a 100-continue expectation in a request
  that does not include content.

- A client that will wait for a 100 (Continue) response before
  sending the request content MUST send an Expect header field
  containing a 100-continue expectation.

- A client that sends a 100-continue expectation is not required to
  wait for any specific length of time; such a client MAY proceed to
  send the content even if it has not yet received a response.
  Furthermore, since 100 (Continue) responses cannot be sent through
  an HTTP/1.0 intermediary, such a client SHOULD NOT wait for an
  indefinite period before sending the content.

- A client that receives a 417 (Expectation Failed) status code in
  response to a request containing a 100-continue expectation SHOULD
  repeat that request without a 100-continue expectation, since the
  417 response merely indicates that the response chain does not
  support expectations (e.g., it passes through an HTTP/1.0 server).

Requirements for servers:

- A server that receives a 100-continue expectation in an HTTP/1.0
  request MUST ignore that expectation.

- A server MAY omit sending a 100 (Continue) response if it has
  already received some or all of the content for the corresponding
  request, or if the framing indicates that there is no content.

- A server that sends a 100 (Continue) response MUST ultimately send
  a final status code, once it receives and processes the request
  content, unless the connection is closed prematurely.

- A server that responds with a final status code before reading the
  entire request content SHOULD indicate whether it intends to close
  the connection (e.g., see Section 9.6 of [HTTP/1.1]) or continue
  reading the request content.

Upon receiving an HTTP/1.1 (or later) request that has a method,
target URI, and complete header section that contains a 100-continue
expectation and an indication that request content will follow, an
origin server MUST send either:

- an immediate response with a final status code, if that status can
  be determined by examining just the method, target URI, and header
  fields, or

- an immediate 100 (Continue) response to encourage the client to
  send the request content.

The origin server MUST NOT wait for the content before sending the
100 (Continue) response.

Upon receiving an HTTP/1.1 (or later) request that has a method,
target URI, and complete header section that contains a 100-continue
expectation and indicates a request content will follow, a proxy MUST
either:

- send an immediate response with a final status code, if that
  status can be determined by examining just the method, target URI,
  and header fields, or

- forward the request toward the origin server by sending a
  corresponding request-line and header section to the next inbound
  server.

If the proxy believes (from configuration or past interaction) that
the next inbound server only supports HTTP/1.0, the proxy MAY
generate an immediate 100 (Continue) response to encourage the client
to begin sending the content.

10.1.2. From

The "From" header field contains an Internet email address for a
human user who controls the requesting user agent. The address ought
to be machine-usable, as defined by "mailbox" in Section 3.4 of
[RFC5322]:

     From    = mailbox

     mailbox = <mailbox, see [RFC5322], Section 3.4>

An example is:

From: spider-admin@example.org

The From header field is rarely sent by non-robotic user agents. A
user agent SHOULD NOT send a From header field without explicit
configuration by the user, since that might conflict with the user's
privacy interests or their site's security policy.

A robotic user agent SHOULD send a valid From header field so that
the person responsible for running the robot can be contacted if
problems occur on servers, such as if the robot is sending excessive,
unwanted, or invalid requests.

A server SHOULD NOT use the From header field for access control or
authentication, since its value is expected to be visible to anyone
receiving or observing the request and is often recorded within
logfiles and error reports without any expectation of privacy.

10.1.3. Referer

The "Referer" [sic] header field allows the user agent to specify a
URI reference for the resource from which the target URI was obtained
(i.e., the "referrer", though the field name is misspelled). A user
agent MUST NOT include the fragment and userinfo components of the
URI reference [URI], if any, when generating the Referer field value.

     Referer = absolute-URI / partial-URI

The field value is either an absolute-URI or a partial-URI. In the
latter case (Section 4), the referenced URI is relative to the target
URI ([URI], Section 5).

The Referer header field allows servers to generate back-links to
other resources for simple analytics, logging, optimized caching,
etc. It also allows obsolete or mistyped links to be found for
maintenance. Some servers use the Referer header field as a means of
denying links from other sites (so-called "deep linking") or
restricting cross-site request forgery (CSRF), but not all requests
contain it.

Example:

Referer: http://www.example.org/hypertext/Overview.html

If the target URI was obtained from a source that does not have its
own URI (e.g., input from the user keyboard, or an entry within the
user's bookmarks/favorites), the user agent MUST either exclude the
Referer header field or send it with a value of "about:blank".

The Referer header field value need not convey the full URI of the
referring resource; a user agent MAY truncate parts other than the
referring origin.

The Referer header field has the potential to reveal information
about the request context or browsing history of the user, which is a
privacy concern if the referring resource's identifier reveals
personal information (such as an account name) or a resource that is
supposed to be confidential (such as behind a firewall or internal to
a secured service). Most general-purpose user agents do not send the
Referer header field when the referring resource is a local "file" or
"data" URI. A user agent SHOULD NOT send a Referer header field if
the referring resource was accessed with a secure protocol and the
request target has an origin differing from that of the referring
resource, unless the referring resource explicitly allows Referer to
be sent. A user agent MUST NOT send a Referer header field in an
unsecured HTTP request if the referring resource was accessed with a
secure protocol. See Section 17.9 for additional security
considerations.

Some intermediaries have been known to indiscriminately remove
Referer header fields from outgoing requests. This has the
unfortunate side effect of interfering with protection against CSRF
attacks, which can be far more harmful to their users.
Intermediaries and user agent extensions that wish to limit
information disclosure in Referer ought to restrict their changes to
specific edits, such as replacing internal domain names with
pseudonyms or truncating the query and/or path components. An
intermediary SHOULD NOT modify or delete the Referer header field
when the field value shares the same scheme and host as the target
URI.

10.1.4. TE

The "TE" header field describes capabilities of the client with
regard to transfer codings and trailer sections.

As described in Section 6.5, a TE field with a "trailers" member sent
in a request indicates that the client will not discard trailer
fields.

TE is also used within HTTP/1.1 to advise servers about which
transfer codings the client is able to accept in a response. As of
publication, only HTTP/1.1 uses transfer codings (see Section 7 of
[HTTP/1.1]).

The TE field value is a list of members, with each member (aside from
"trailers") consisting of a transfer coding name token with an
optional weight indicating the client's relative preference for that
transfer coding (Section 12.4.2) and optional parameters for that
transfer coding.

     TE                 = #t-codings
     t-codings          = "trailers" / ( transfer-coding [ weight ] )
     transfer-coding    = token *( OWS ";" OWS transfer-parameter )
     transfer-parameter = token BWS "=" BWS ( token / quoted-string )

A sender of TE MUST also send a "TE" connection option within the
Connection header field (Section 7.6.1) to inform intermediaries not
to forward this field.

10.1.5. User-Agent

The "User-Agent" header field contains information about the user
agent originating the request, which is often used by servers to help
identify the scope of reported interoperability problems, to work
around or tailor responses to avoid particular user agent
limitations, and for analytics regarding browser or operating system
use. A user agent SHOULD send a User-Agent header field in each
request unless specifically configured not to do so.

     User-Agent = product *( RWS ( product / comment ) )

The User-Agent field value consists of one or more product
identifiers, each followed by zero or more comments (Section 5.6.5),
which together identify the user agent software and its significant
subproducts. By convention, the product identifiers are listed in
decreasing order of their significance for identifying the user agent
software. Each product identifier consists of a name and optional
version.

     product         = token ["/" product-version]
     product-version = token

A sender SHOULD limit generated product identifiers to what is
necessary to identify the product; a sender MUST NOT generate
advertising or other nonessential information within the product
identifier. A sender SHOULD NOT generate information in
product-version that is not a version identifier (i.e., successive
versions of the same product name ought to differ only in the
product-version portion of the product identifier).

Example:

User-Agent: CERN-LineMode/2.15 libwww/2.17b3

A user agent SHOULD NOT generate a User-Agent header field containing
needlessly fine-grained detail and SHOULD limit the addition of
subproducts by third parties. Overly long and detailed User-Agent
field values increase request latency and the risk of a user being
identified against their wishes ("fingerprinting").

Likewise, implementations are encouraged not to use the product
tokens of other implementations in order to declare compatibility
with them, as this circumvents the purpose of the field. If a user
agent masquerades as a different user agent, recipients can assume
that the user intentionally desires to see responses tailored for
that identified user agent, even if they might not work as well for
the actual user agent being used.

10.2. Response Context Fields

The response header fields below provide additional information about
the response, beyond what is implied by the status code, including
information about the server, about the target resource, or about
related resources.

10.2.1. Allow

The "Allow" header field lists the set of methods advertised as
supported by the target resource. The purpose of this field is
strictly to inform the recipient of valid request methods associated
with the resource.

     Allow = #method

Example of use:

Allow: GET, HEAD, PUT

The actual set of allowed methods is defined by the origin server at
the time of each request. An origin server MUST generate an Allow
header field in a 405 (Method Not Allowed) response and MAY do so in
any other response. An empty Allow field value indicates that the
resource allows no methods, which might occur in a 405 response if
the resource has been temporarily disabled by configuration.

A proxy MUST NOT modify the Allow header field -- it does not need to
understand all of the indicated methods in order to handle them
according to the generic message handling rules.

10.2.2. Location

The "Location" header field is used in some responses to refer to a
specific resource in relation to the response. The type of
relationship is defined by the combination of request method and
status code semantics.

     Location = URI-reference

The field value consists of a single URI-reference. When it has the
form of a relative reference ([URI], Section 4.2), the final value is
computed by resolving it against the target URI ([URI], Section 5).

For 201 (Created) responses, the Location value refers to the primary
resource created by the request. For 3xx (Redirection) responses,
the Location value refers to the preferred target resource for
automatically redirecting the request.

If the Location value provided in a 3xx (Redirection) response does
not have a fragment component, a user agent MUST process the
redirection as if the value inherits the fragment component of the
URI reference used to generate the target URI (i.e., the redirection
inherits the original reference's fragment, if any).

For example, a GET request generated for the URI reference
"http://www.example.org/~tim" might result in a 303 (See Other)
response containing the header field:

Location: /People.html#tim

which suggests that the user agent redirect to
"http://www.example.org/People.html#tim"

Likewise, a GET request generated for the URI reference
"http://www.example.org/index.html#larry" might result in a 301
(Moved Permanently) response containing the header field:

Location: http://www.example.net/index.html

which suggests that the user agent redirect to
"http://www.example.net/index.html#larry", preserving the original
fragment identifier.

There are circumstances in which a fragment identifier in a Location
value would not be appropriate. For example, the Location header
field in a 201 (Created) response is supposed to provide a URI that
is specific to the created resource.

      |  *Note:* Some recipients attempt to recover from Location header
      |  fields that are not valid URI references.  This specification
      |  does not mandate or define such processing, but does allow it
      |  for the sake of robustness.  A Location field value cannot
      |  allow a list of members because the comma list separator is a
      |  valid data character within a URI-reference.  If an invalid
      |  message is sent with multiple Location field lines, a recipient
      |  along the path might combine those field lines into one value.
      |  Recovery of a valid Location field value from that situation is
      |  difficult and not interoperable across implementations.

      |  *Note:* The Content-Location header field (Section 8.7) differs
      |  from Location in that the Content-Location refers to the most
      |  specific resource corresponding to the enclosed representation.
      |  It is therefore possible for a response to contain both the
      |  Location and Content-Location header fields.

10.2.3. Retry-After

Servers send the "Retry-After" header field to indicate how long the
user agent ought to wait before making a follow-up request. When
sent with a 503 (Service Unavailable) response, Retry-After indicates
how long the service is expected to be unavailable to the client.
When sent with any 3xx (Redirection) response, Retry-After indicates
the minimum time that the user agent is asked to wait before issuing
the redirected request.

The Retry-After field value can be either an HTTP-date or a number of
seconds to delay after receiving the response.

     Retry-After = HTTP-date / delay-seconds

A delay-seconds value is a non-negative decimal integer, representing
time in seconds.

     delay-seconds  = 1*DIGIT

Two examples of its use are

Retry-After: Fri, 31 Dec 1999 23:59:59 GMT
Retry-After: 120

In the latter example, the delay is 2 minutes.

10.2.4. Server

The "Server" header field contains information about the software
used by the origin server to handle the request, which is often used
by clients to help identify the scope of reported interoperability
problems, to work around or tailor requests to avoid particular
server limitations, and for analytics regarding server or operating
system use. An origin server MAY generate a Server header field in
its responses.

     Server = product *( RWS ( product / comment ) )

The Server header field value consists of one or more product
identifiers, each followed by zero or more comments (Section 5.6.5),
which together identify the origin server software and its
significant subproducts. By convention, the product identifiers are
listed in decreasing order of their significance for identifying the
origin server software. Each product identifier consists of a name
and optional version, as defined in Section 10.1.5.

Example:

Server: CERN/3.0 libwww/2.17

An origin server SHOULD NOT generate a Server header field containing
needlessly fine-grained detail and SHOULD limit the addition of
subproducts by third parties. Overly long and detailed Server field
values increase response latency and potentially reveal internal
implementation details that might make it (slightly) easier for
attackers to find and exploit known security holes.

11. HTTP Authentication

11.1. Authentication Scheme

HTTP provides a general framework for access control and
authentication, via an extensible set of challenge-response
authentication schemes, which can be used by a server to challenge a
client request and by a client to provide authentication information.
It uses a case-insensitive token to identify the authentication
scheme:

     auth-scheme    = token

Aside from the general framework, this document does not specify any
authentication schemes. New and existing authentication schemes are
specified independently and ought to be registered within the
"Hypertext Transfer Protocol (HTTP) Authentication Scheme Registry".
For example, the "basic" and "digest" authentication schemes are
defined by [RFC7617] and [RFC7616], respectively.

11.2. Authentication Parameters

The authentication scheme is followed by additional information
necessary for achieving authentication via that scheme as either a
comma-separated list of parameters or a single sequence of characters
capable of holding base64-encoded information.

     token68        = 1*( ALPHA / DIGIT /
                          "-" / "." / "_" / "~" / "+" / "/" ) *"="

The token68 syntax allows the 66 unreserved URI characters ([URI]),
plus a few others, so that it can hold a base64, base64url (URL and
filename safe alphabet), base32, or base16 (hex) encoding, with or
without padding, but excluding whitespace ([RFC4648]).

Authentication parameters are name/value pairs, where the name token
is matched case-insensitively and each parameter name MUST only occur
once per challenge.

     auth-param     = token BWS "=" BWS ( token / quoted-string )

Parameter values can be expressed either as "token" or as "quoted-
string" (Section 5.6). Authentication scheme definitions need to
accept both notations, both for senders and recipients, to allow
recipients to use generic parsing components regardless of the
authentication scheme.

For backwards compatibility, authentication scheme definitions can
restrict the format for senders to one of the two variants. This can
be important when it is known that deployed implementations will fail
when encountering one of the two formats.

11.3. Challenge and Response

A 401 (Unauthorized) response message is used by an origin server to
challenge the authorization of a user agent, including a
WWW-Authenticate header field containing at least one challenge
applicable to the requested resource.

A 407 (Proxy Authentication Required) response message is used by a
proxy to challenge the authorization of a client, including a
Proxy-Authenticate header field containing at least one challenge
applicable to the proxy for the requested resource.

     challenge   = auth-scheme [ 1*SP ( token68 / #auth-param ) ]

      |  *Note:* Many clients fail to parse a challenge that contains an
      |  unknown scheme.  A workaround for this problem is to list well-
      |  supported schemes (such as "basic") first.

A user agent that wishes to authenticate itself with an origin server
-- usually, but not necessarily, after receiving a 401 (Unauthorized)
-- can do so by including an Authorization header field with the
request.

A client that wishes to authenticate itself with a proxy -- usually,
but not necessarily, after receiving a 407 (Proxy Authentication
Required) -- can do so by including a Proxy-Authorization header
field with the request.

11.4. Credentials

Both the Authorization field value and the Proxy-Authorization field
value contain the client's credentials for the realm of the resource
being requested, based upon a challenge received in a response
(possibly at some point in the past). When creating their values,
the user agent ought to do so by selecting the challenge with what it
considers to be the most secure auth-scheme that it understands,
obtaining credentials from the user as appropriate. Transmission of
credentials within header field values implies significant security
considerations regarding the confidentiality of the underlying
connection, as described in Section 17.16.1.

     credentials = auth-scheme [ 1*SP ( token68 / #auth-param ) ]

Upon receipt of a request for a protected resource that omits
credentials, contains invalid credentials (e.g., a bad password) or
partial credentials (e.g., when the authentication scheme requires
more than one round trip), an origin server SHOULD send a 401
(Unauthorized) response that contains a WWW-Authenticate header field
with at least one (possibly new) challenge applicable to the
requested resource.

Likewise, upon receipt of a request that omits proxy credentials or
contains invalid or partial proxy credentials, a proxy that requires
authentication SHOULD generate a 407 (Proxy Authentication Required)
response that contains a Proxy-Authenticate header field with at
least one (possibly new) challenge applicable to the proxy.

A server that receives valid credentials that are not adequate to
gain access ought to respond with the 403 (Forbidden) status code
(Section 15.5.4).

HTTP does not restrict applications to this simple challenge-response
framework for access authentication. Additional mechanisms can be
used, such as authentication at the transport level or via message
encapsulation, and with additional header fields specifying
authentication information. However, such additional mechanisms are
not defined by this specification.

Note that various custom mechanisms for user authentication use the
Set-Cookie and Cookie header fields, defined in [COOKIE], for passing
tokens related to authentication.

11.5. Establishing a Protection Space (Realm)

The "realm" authentication parameter is reserved for use by
authentication schemes that wish to indicate a scope of protection.

A "protection space" is defined by the origin (see Section 4.3.1) of
the server being accessed, in combination with the realm value if
present. These realms allow the protected resources on a server to
be partitioned into a set of protection spaces, each with its own
authentication scheme and/or authorization database. The realm value
is a string, generally assigned by the origin server, that can have
additional semantics specific to the authentication scheme. Note
that a response can have multiple challenges with the same auth-
scheme but with different realms.

The protection space determines the domain over which credentials can
be automatically applied. If a prior request has been authorized,
the user agent MAY reuse the same credentials for all other requests
within that protection space for a period of time determined by the
authentication scheme, parameters, and/or user preferences (such as a
configurable inactivity timeout).

The extent of a protection space, and therefore the requests to which
credentials might be automatically applied, is not necessarily known
to clients without additional information. An authentication scheme
might define parameters that describe the extent of a protection
space. Unless specifically allowed by the authentication scheme, a
single protection space cannot extend outside the scope of its
server.

For historical reasons, a sender MUST only generate the quoted-string
syntax. Recipients might have to support both token and quoted-
string syntax for maximum interoperability with existing clients that
have been accepting both notations for a long time.

11.6. Authenticating Users to Origin Servers

11.6.1. WWW-Authenticate

The "WWW-Authenticate" response header field indicates the
authentication scheme(s) and parameters applicable to the target
resource.

     WWW-Authenticate = #challenge

A server generating a 401 (Unauthorized) response MUST send a WWW-
Authenticate header field containing at least one challenge. A
server MAY generate a WWW-Authenticate header field in other response
messages to indicate that supplying credentials (or different
credentials) might affect the response.

A proxy forwarding a response MUST NOT modify any WWW-Authenticate
header fields in that response.

User agents are advised to take special care in parsing the field
value, as it might contain more than one challenge, and each
challenge can contain a comma-separated list of authentication
parameters. Furthermore, the header field itself can occur multiple
times.

For instance:

WWW-Authenticate: Basic realm="simple", Newauth realm="apps",
type=1, title="Login to \"apps\""

This header field contains two challenges, one for the "Basic" scheme
with a realm value of "simple" and another for the "Newauth" scheme
with a realm value of "apps". It also contains two additional
parameters, "type" and "title".

Some user agents do not recognize this form, however. As a result,
sending a WWW-Authenticate field value with more than one member on
the same field line might not be interoperable.

      |  *Note:* The challenge grammar production uses the list syntax
      |  as well.  Therefore, a sequence of comma, whitespace, and comma
      |  can be considered either as applying to the preceding
      |  challenge, or to be an empty entry in the list of challenges.
      |  In practice, this ambiguity does not affect the semantics of
      |  the header field value and thus is harmless.

11.6.2. Authorization

The "Authorization" header field allows a user agent to authenticate
itself with an origin server -- usually, but not necessarily, after
receiving a 401 (Unauthorized) response. Its value consists of
credentials containing the authentication information of the user
agent for the realm of the resource being requested.

     Authorization = credentials

If a request is authenticated and a realm specified, the same
credentials are presumed to be valid for all other requests within
this realm (assuming that the authentication scheme itself does not
require otherwise, such as credentials that vary according to a
challenge value or using synchronized clocks).

A proxy forwarding a request MUST NOT modify any Authorization header
fields in that request. See Section 3.5 of [CACHING] for details of
and requirements pertaining to handling of the Authorization header
field by HTTP caches.

11.6.3. Authentication-Info

HTTP authentication schemes can use the "Authentication-Info"
response field to communicate information after the client's
authentication credentials have been accepted. This information can
include a finalization message from the server (e.g., it can contain
the server authentication).

The field value is a list of parameters (name/value pairs), using the
"auth-param" syntax defined in Section 11.3. This specification only
describes the generic format; authentication schemes using
Authentication-Info will define the individual parameters. The
"Digest" Authentication Scheme, for instance, defines multiple
parameters in Section 3.5 of [RFC7616].

     Authentication-Info = #auth-param

The Authentication-Info field can be used in any HTTP response,
independently of request method and status code. Its semantics are
defined by the authentication scheme indicated by the Authorization
header field (Section 11.6.2) of the corresponding request.

A proxy forwarding a response is not allowed to modify the field
value in any way.

Authentication-Info can be sent as a trailer field (Section 6.5) when
the authentication scheme explicitly allows this.

11.7. Authenticating Clients to Proxies

11.7.1. Proxy-Authenticate

The "Proxy-Authenticate" header field consists of at least one
challenge that indicates the authentication scheme(s) and parameters
applicable to the proxy for this request. A proxy MUST send at least
one Proxy-Authenticate header field in each 407 (Proxy Authentication
Required) response that it generates.

     Proxy-Authenticate = #challenge

Unlike WWW-Authenticate, the Proxy-Authenticate header field applies
only to the next outbound client on the response chain. This is
because only the client that chose a given proxy is likely to have
the credentials necessary for authentication. However, when multiple
proxies are used within the same administrative domain, such as
office and regional caching proxies within a large corporate network,
it is common for credentials to be generated by the user agent and
passed through the hierarchy until consumed. Hence, in such a
configuration, it will appear as if Proxy-Authenticate is being
forwarded because each proxy will send the same challenge set.

Note that the parsing considerations for WWW-Authenticate apply to
this header field as well; see Section 11.6.1 for details.

11.7.2. Proxy-Authorization

The "Proxy-Authorization" header field allows the client to identify
itself (or its user) to a proxy that requires authentication. Its
value consists of credentials containing the authentication
information of the client for the proxy and/or realm of the resource
being requested.

     Proxy-Authorization = credentials

Unlike Authorization, the Proxy-Authorization header field applies
only to the next inbound proxy that demanded authentication using the
Proxy-Authenticate header field. When multiple proxies are used in a
chain, the Proxy-Authorization header field is consumed by the first
inbound proxy that was expecting to receive credentials. A proxy MAY
relay the credentials from the client request to the next proxy if
that is the mechanism by which the proxies cooperatively authenticate
a given request.

11.7.3. Proxy-Authentication-Info

The "Proxy-Authentication-Info" response header field is equivalent
to Authentication-Info, except that it applies to proxy
authentication (Section 11.3) and its semantics are defined by the
authentication scheme indicated by the Proxy-Authorization header
field (Section 11.7.2) of the corresponding request:

     Proxy-Authentication-Info = #auth-param

However, unlike Authentication-Info, the Proxy-Authentication-Info
header field applies only to the next outbound client on the response
chain. This is because only the client that chose a given proxy is
likely to have the credentials necessary for authentication.
However, when multiple proxies are used within the same
administrative domain, such as office and regional caching proxies
within a large corporate network, it is common for credentials to be
generated by the user agent and passed through the hierarchy until
consumed. Hence, in such a configuration, it will appear as if
Proxy-Authentication-Info is being forwarded because each proxy will
send the same field value.

Proxy-Authentication-Info can be sent as a trailer field
(Section 6.5) when the authentication scheme explicitly allows this.

12. Content Negotiation

When responses convey content, whether indicating a success or an
error, the origin server often has different ways of representing
that information; for example, in different formats, languages, or
encodings. Likewise, different users or user agents might have
differing capabilities, characteristics, or preferences that could
influence which representation, among those available, would be best
to deliver. For this reason, HTTP provides mechanisms for content
negotiation.

This specification defines three patterns of content negotiation that
can be made visible within the protocol: "proactive" negotiation,
where the server selects the representation based upon the user
agent's stated preferences; "reactive" negotiation, where the server
provides a list of representations for the user agent to choose from;
and "request content" negotiation, where the user agent selects the
representation for a future request based upon the server's stated
preferences in past responses.

Other patterns of content negotiation include "conditional content",
where the representation consists of multiple parts that are
selectively rendered based on user agent parameters, "active
content", where the representation contains a script that makes
additional (more specific) requests based on the user agent
characteristics, and "Transparent Content Negotiation" ([RFC2295]),
where content selection is performed by an intermediary. These
patterns are not mutually exclusive, and each has trade-offs in
applicability and practicality.

Note that, in all cases, HTTP is not aware of the resource semantics.
The consistency with which an origin server responds to requests,
over time and over the varying dimensions of content negotiation, and
thus the "sameness" of a resource's observed representations over
time, is determined entirely by whatever entity or algorithm selects
or generates those responses.

12.1. Proactive Negotiation

When content negotiation preferences are sent by the user agent in a
request to encourage an algorithm located at the server to select the
preferred representation, it is called "proactive negotiation"
(a.k.a., "server-driven negotiation"). Selection is based on the
available representations for a response (the dimensions over which
it might vary, such as language, content coding, etc.) compared to
various information supplied in the request, including both the
explicit negotiation header fields below and implicit
characteristics, such as the client's network address or parts of the
User-Agent field.

Proactive negotiation is advantageous when the algorithm for
selecting from among the available representations is difficult to
describe to a user agent, or when the server desires to send its
"best guess" to the user agent along with the first response (when
that "best guess" is good enough for the user, this avoids the round-
trip delay of a subsequent request). In order to improve the
server's guess, a user agent MAY send request header fields that
describe its preferences.

Proactive negotiation has serious disadvantages:

- It is impossible for the server to accurately determine what might
  be "best" for any given user, since that would require complete
  knowledge of both the capabilities of the user agent and the
  intended use for the response (e.g., does the user want to view it
  on screen or print it on paper?);

- Having the user agent describe its capabilities in every request
  can be both very inefficient (given that only a small percentage
  of responses have multiple representations) and a potential risk
  to the user's privacy;

- It complicates the implementation of an origin server and the
  algorithms for generating responses to a request; and,

- It limits the reusability of responses for shared caching.

A user agent cannot rely on proactive negotiation preferences being
consistently honored, since the origin server might not implement
proactive negotiation for the requested resource or might decide that
sending a response that doesn't conform to the user agent's
preferences is better than sending a 406 (Not Acceptable) response.

A Vary header field (Section 12.5.5) is often sent in a response
subject to proactive negotiation to indicate what parts of the
request information were used in the selection algorithm.

The request header fields Accept, Accept-Charset, Accept-Encoding,
and Accept-Language are defined below for a user agent to engage in
proactive negotiation of the response content. The preferences sent
in these fields apply to any content in the response, including
representations of the target resource, representations of error or
processing status, and potentially even the miscellaneous text
strings that might appear within the protocol.

12.2. Reactive Negotiation

With "reactive negotiation" (a.k.a., "agent-driven negotiation"),
selection of content (regardless of the status code) is performed by
the user agent after receiving an initial response. The mechanism
for reactive negotiation might be as simple as a list of references
to alternative representations.

If the user agent is not satisfied by the initial response content,
it can perform a GET request on one or more of the alternative
resources to obtain a different representation. Selection of such
alternatives might be performed automatically (by the user agent) or
manually (e.g., by the user selecting from a hypertext menu).

A server might choose not to send an initial representation, other
than the list of alternatives, and thereby indicate that reactive
negotiation by the user agent is preferred. For example, the
alternatives listed in responses with the 300 (Multiple Choices) and
406 (Not Acceptable) status codes include information about available
representations so that the user or user agent can react by making a
selection.

Reactive negotiation is advantageous when the response would vary
over commonly used dimensions (such as type, language, or encoding),
when the origin server is unable to determine a user agent's
capabilities from examining the request, and generally when public
caches are used to distribute server load and reduce network usage.

Reactive negotiation suffers from the disadvantages of transmitting a
list of alternatives to the user agent, which degrades user-perceived
latency if transmitted in the header section, and needing a second
request to obtain an alternate representation. Furthermore, this
specification does not define a mechanism for supporting automatic
selection, though it does not prevent such a mechanism from being
developed.

12.3. Request Content Negotiation

When content negotiation preferences are sent in a server's response,
the listed preferences are called "request content negotiation"
because they intend to influence selection of an appropriate content
for subsequent requests to that resource. For example, the Accept
(Section 12.5.1) and Accept-Encoding (Section 12.5.3) header fields
can be sent in a response to indicate preferred media types and
content codings for subsequent requests to that resource.

Similarly, Section 3.1 of [RFC5789] defines the "Accept-Patch"
response header field, which allows discovery of which content types
are accepted in PATCH requests.

12.4. Content Negotiation Field Features

12.4.1. Absence

For each of the content negotiation fields, a request that does not
contain the field implies that the sender has no preference on that
dimension of negotiation.

If a content negotiation header field is present in a request and
none of the available representations for the response can be
considered acceptable according to it, the origin server can either
honor the header field by sending a 406 (Not Acceptable) response or
disregard the header field by treating the response as if it is not
subject to content negotiation for that request header field. This
does not imply, however, that the client will be able to use the
representation.

      |  *Note:* A user agent sending these header fields makes it
      |  easier for a server to identify an individual by virtue of the
      |  user agent's request characteristics (Section 17.13).

12.4.2. Quality Values

The content negotiation fields defined by this specification use a
common parameter, named "q" (case-insensitive), to assign a relative
"weight" to the preference for that associated kind of content. This
weight is referred to as a "quality value" (or "qvalue") because the
same parameter name is often used within server configurations to
assign a weight to the relative quality of the various
representations that can be selected for a resource.

The weight is normalized to a real number in the range 0 through 1,
where 0.001 is the least preferred and 1 is the most preferred; a
value of 0 means "not acceptable". If no "q" parameter is present,
the default weight is 1.

     weight = OWS ";" OWS "q=" qvalue
     qvalue = ( "0" [ "." 0*3DIGIT ] )
            / ( "1" [ "." 0*3("0") ] )

A sender of qvalue MUST NOT generate more than three digits after the
decimal point. User configuration of these values ought to be
limited in the same fashion.

12.4.3. Wildcard Values

Most of these header fields, where indicated, define a wildcard value
("\*") to select unspecified values. If no wildcard is present,
values that are not explicitly mentioned in the field are considered
unacceptable. Within Vary, the wildcard value means that the
variance is unlimited.

      |  *Note:* In practice, using wildcards in content negotiation has
      |  limited practical value because it is seldom useful to say, for
      |  example, "I prefer image/* more or less than (some other
      |  specific value)".  By sending Accept: */*;q=0, clients can
      |  explicitly request a 406 (Not Acceptable) response if a more
      |  preferred format is not available, but they still need to be
      |  able to handle a different response since the server is allowed
      |  to ignore their preference.

12.5. Content Negotiation Fields

12.5.1. Accept

The "Accept" header field can be used by user agents to specify their
preferences regarding response media types. For example, Accept
header fields can be used to indicate that the request is
specifically limited to a small set of desired types, as in the case
of a request for an in-line image.

When sent by a server in a response, Accept provides information
about which content types are preferred in the content of a
subsequent request to the same resource.

     Accept = #( media-range [ weight ] )

     media-range    = ( "*/*"
                        / ( type "/" "*" )
                        / ( type "/" subtype )
                      ) parameters

The asterisk "_" character is used to group media types into ranges,
with "_/_" indicating all media types and "type/_" indicating all
subtypes of that type. The media-range can include media type
parameters that are applicable to that range.

Each media-range might be followed by optional applicable media type
parameters (e.g., charset), followed by an optional "q" parameter for
indicating a relative weight (Section 12.4.2).

Previous specifications allowed additional extension parameters to
appear after the weight parameter. The accept extension grammar
(accept-params, accept-ext) has been removed because it had a
complicated definition, was not being used in practice, and is more
easily deployed through new header fields. Senders using weights
SHOULD send "q" last (after all media-range parameters). Recipients
SHOULD process any parameter named "q" as weight, regardless of
parameter ordering.

      |  *Note:* Use of the "q" parameter name to control content
      |  negotiation would interfere with any media type parameter
      |  having the same name.  Hence, the media type registry disallows
      |  parameters named "q".

The example

Accept: audio/\*; q=0.2, audio/basic

is interpreted as "I prefer audio/basic, but send me any audio type
if it is the best available after an 80% markdown in quality".

A more elaborate example is

Accept: text/plain; q=0.5, text/html,
text/x-dvi; q=0.8, text/x-c

Verbally, this would be interpreted as "text/html and text/x-c are
the equally preferred media types, but if they do not exist, then
send the text/x-dvi representation, and if that does not exist, send
the text/plain representation".

Media ranges can be overridden by more specific media ranges or
specific media types. If more than one media range applies to a
given type, the most specific reference has precedence. For example,

Accept: text/_, text/plain, text/plain;format=flowed, _/\*

have the following precedence:

1.  text/plain;format=flowed

2.  text/plain

3.  text/\*

4.  _/_

The media type quality factor associated with a given type is
determined by finding the media range with the highest precedence
that matches the type. For example,

Accept: text/_;q=0.3, text/plain;q=0.7, text/plain;format=flowed,
text/plain;format=fixed;q=0.4, _/\*;q=0.5

would cause the following values to be associated:

+==========================+===============+
| Media Type | Quality Value |
+==========================+===============+
| text/plain;format=flowed | 1 |
+--------------------------+---------------+
| text/plain | 0.7 |
+--------------------------+---------------+
| text/html | 0.3 |
+--------------------------+---------------+
| image/jpeg | 0.5 |
+--------------------------+---------------+
| text/plain;format=fixed | 0.4 |
+--------------------------+---------------+
| text/html;level=3 | 0.7 |
+--------------------------+---------------+

                     Table 5

      |  *Note:* A user agent might be provided with a default set of
      |  quality values for certain media ranges.  However, unless the
      |  user agent is a closed system that cannot interact with other
      |  rendering agents, this default set ought to be configurable by
      |  the user.

12.5.2. Accept-Charset

The "Accept-Charset" header field can be sent by a user agent to
indicate its preferences for charsets in textual response content.
For example, this field allows user agents capable of understanding
more comprehensive or special-purpose charsets to signal that
capability to an origin server that is capable of representing
information in those charsets.

     Accept-Charset = #( ( token / "*" ) [ weight ] )

Charset names are defined in Section 8.3.2. A user agent MAY
associate a quality value with each charset to indicate the user's
relative preference for that charset, as defined in Section 12.4.2.
An example is

Accept-Charset: iso-8859-5, unicode-1-1;q=0.8

The special value "\*", if present in the Accept-Charset header field,
matches every charset that is not mentioned elsewhere in the field.

      |  *Note:* Accept-Charset is deprecated because UTF-8 has become
      |  nearly ubiquitous and sending a detailed list of user-preferred
      |  charsets wastes bandwidth, increases latency, and makes passive
      |  fingerprinting far too easy (Section 17.13).  Most general-
      |  purpose user agents do not send Accept-Charset unless
      |  specifically configured to do so.

12.5.3. Accept-Encoding

The "Accept-Encoding" header field can be used to indicate
preferences regarding the use of content codings (Section 8.4.1).

When sent by a user agent in a request, Accept-Encoding indicates the
content codings acceptable in a response.

When sent by a server in a response, Accept-Encoding provides
information about which content codings are preferred in the content
of a subsequent request to the same resource.

An "identity" token is used as a synonym for "no encoding" in order
to communicate when no encoding is preferred.

     Accept-Encoding  = #( codings [ weight ] )
     codings          = content-coding / "identity" / "*"

Each codings value MAY be given an associated quality value (weight)
representing the preference for that encoding, as defined in
Section 12.4.2. The asterisk "\*" symbol in an Accept-Encoding field
matches any available content coding not explicitly listed in the
field.

Examples:

Accept-Encoding: compress, gzip
Accept-Encoding:
Accept-Encoding: _
Accept-Encoding: compress;q=0.5, gzip;q=1.0
Accept-Encoding: gzip;q=1.0, identity; q=0.5, _;q=0

A server tests whether a content coding for a given representation is
acceptable using these rules:

1.  If no Accept-Encoding header field is in the request, any content
    coding is considered acceptable by the user agent.

2.  If the representation has no content coding, then it is
    acceptable by default unless specifically excluded by the Accept-
    Encoding header field stating either "identity;q=0" or "\*;q=0"
    without a more specific entry for "identity".

3.  If the representation's content coding is one of the content
    codings listed in the Accept-Encoding field value, then it is
    acceptable unless it is accompanied by a qvalue of 0. (As
    defined in Section 12.4.2, a qvalue of 0 means "not acceptable".)

A representation could be encoded with multiple content codings.
However, most content codings are alternative ways to accomplish the
same purpose (e.g., data compression). When selecting between
multiple content codings that have the same purpose, the acceptable
content coding with the highest non-zero qvalue is preferred.

An Accept-Encoding header field with a field value that is empty
implies that the user agent does not want any content coding in
response. If a non-empty Accept-Encoding header field is present in
a request and none of the available representations for the response
have a content coding that is listed as acceptable, the origin server
SHOULD send a response without any content coding unless the identity
coding is indicated as unacceptable.

When the Accept-Encoding header field is present in a response, it
indicates what content codings the resource was willing to accept in
the associated request. The field value is evaluated the same way as
in a request.

Note that this information is specific to the associated request; the
set of supported encodings might be different for other resources on
the same server and could change over time or depend on other aspects
of the request (such as the request method).

Servers that fail a request due to an unsupported content coding
ought to respond with a 415 (Unsupported Media Type) status and
include an Accept-Encoding header field in that response, allowing
clients to distinguish between issues related to content codings and
media types. In order to avoid confusion with issues related to
media types, servers that fail a request with a 415 status for
reasons unrelated to content codings MUST NOT include the Accept-
Encoding header field.

The most common use of Accept-Encoding is in responses with a 415
(Unsupported Media Type) status code, in response to optimistic use
of a content coding by clients. However, the header field can also
be used to indicate to clients that content codings are supported in
order to optimize future interactions. For example, a resource might
include it in a 2xx (Successful) response when the request content
was big enough to justify use of a compression coding but the client
failed do so.

12.5.4. Accept-Language

The "Accept-Language" header field can be used by user agents to
indicate the set of natural languages that are preferred in the
response. Language tags are defined in Section 8.5.1.

     Accept-Language = #( language-range [ weight ] )
     language-range  =
               <language-range, see [RFC4647], Section 2.1>

Each language-range can be given an associated quality value
representing an estimate of the user's preference for the languages
specified by that range, as defined in Section 12.4.2. For example,

Accept-Language: da, en-gb;q=0.8, en;q=0.7

would mean: "I prefer Danish, but will accept British English and
other types of English".

Note that some recipients treat the order in which language tags are
listed as an indication of descending priority, particularly for tags
that are assigned equal quality values (no value is the same as q=1).
However, this behavior cannot be relied upon. For consistency and to
maximize interoperability, many user agents assign each language tag
a unique quality value while also listing them in order of decreasing
quality. Additional discussion of language priority lists can be
found in Section 2.3 of [RFC4647].

For matching, Section 3 of [RFC4647] defines several matching
schemes. Implementations can offer the most appropriate matching
scheme for their requirements. The "Basic Filtering" scheme
([RFC4647], Section 3.3.1) is identical to the matching scheme that
was previously defined for HTTP in Section 14.4 of [RFC2616].

It might be contrary to the privacy expectations of the user to send
an Accept-Language header field with the complete linguistic
preferences of the user in every request (Section 17.13).

Since intelligibility is highly dependent on the individual user,
user agents need to allow user control over the linguistic preference
(either through configuration of the user agent itself or by
defaulting to a user controllable system setting). A user agent that
does not provide such control to the user MUST NOT send an Accept-
Language header field.

      |  *Note:* User agents ought to provide guidance to users when
      |  setting a preference, since users are rarely familiar with the
      |  details of language matching as described above.  For example,
      |  users might assume that on selecting "en-gb", they will be
      |  served any kind of English document if British English is not
      |  available.  A user agent might suggest, in such a case, to add
      |  "en" to the list for better matching behavior.

12.5.5. Vary

The "Vary" header field in a response describes what parts of a
request message, aside from the method and target URI, might have
influenced the origin server's process for selecting the content of
this response.

     Vary = #( "*" / field-name )

A Vary field value is either the wildcard member "\*" or a list of
request field names, known as the selecting header fields, that might
have had a role in selecting the representation for this response.
Potential selecting header fields are not limited to fields defined
by this specification.

A list containing the member "_" signals that other aspects of the
request might have played a role in selecting the response
representation, possibly including aspects outside the message syntax
(e.g., the client's network address). A recipient will not be able
to determine whether this response is appropriate for a later request
without forwarding the request to the origin server. A proxy MUST
NOT generate "_" in a Vary field value.

For example, a response that contains

Vary: accept-encoding, accept-language

indicates that the origin server might have used the request's
Accept-Encoding and Accept-Language header fields (or lack thereof)
as determining factors while choosing the content for this response.

A Vary field containing a list of field names has two purposes:

1.  To inform cache recipients that they MUST NOT use this response
    to satisfy a later request unless the later request has the same
    values for the listed header fields as the original request
    (Section 4.1 of [CACHING]) or reuse of the response has been
    validated by the origin server. In other words, Vary expands the
    cache key required to match a new request to the stored cache
    entry.

2.  To inform user agent recipients that this response was subject to
    content negotiation (Section 12) and a different representation
    might be sent in a subsequent request if other values are
    provided in the listed header fields (proactive negotiation).

An origin server SHOULD generate a Vary header field on a cacheable
response when it wishes that response to be selectively reused for
subsequent requests. Generally, that is the case when the response
content has been tailored to better fit the preferences expressed by
those selecting header fields, such as when an origin server has
selected the response's language based on the request's
Accept-Language header field.

Vary might be elided when an origin server considers variance in
content selection to be less significant than Vary's performance
impact on caching, particularly when reuse is already limited by
cache response directives (Section 5.2 of [CACHING]).

There is no need to send the Authorization field name in Vary because
reuse of that response for a different user is prohibited by the
field definition (Section 11.6.2). Likewise, if the response content
has been selected or influenced by network region, but the origin
server wants the cached response to be reused even if recipients move
from one region to another, then there is no need for the origin
server to indicate such variance in Vary.

13. Conditional Requests

A conditional request is an HTTP request with one or more request
header fields that indicate a precondition to be tested before
applying the request method to the target resource. Section 13.2
defines when to evaluate preconditions and their order of precedence
when more than one precondition is present.

Conditional GET requests are the most efficient mechanism for HTTP
cache updates [CACHING]. Conditionals can also be applied to state-
changing methods, such as PUT and DELETE, to prevent the "lost
update" problem: one client accidentally overwriting the work of
another client that has been acting in parallel.

13.1. Preconditions

Preconditions are usually defined with respect to a state of the
target resource as a whole (its current value set) or the state as
observed in a previously obtained representation (one value in that
set). If a resource has multiple current representations, each with
its own observable state, a precondition will assume that the mapping
of each request to a selected representation (Section 3.2) is
consistent over time. Regardless, if the mapping is inconsistent or
the server is unable to select an appropriate representation, then no
harm will result when the precondition evaluates to false.

Each precondition defined below consists of a comparison between a
set of validators obtained from prior representations of the target
resource to the current state of validators for the selected
representation (Section 8.8). Hence, these preconditions evaluate
whether the state of the target resource has changed since a given
state known by the client. The effect of such an evaluation depends
on the method semantics and choice of conditional, as defined in
Section 13.2.

Other preconditions, defined by other specifications as extension
fields, might place conditions on all recipients, on the state of the
target resource in general, or on a group of resources. For
instance, the "If" header field in WebDAV can make a request
conditional on various aspects of multiple resources, such as locks,
if the recipient understands and implements that field ([WEBDAV],
Section 10.4).

Extensibility of preconditions is only possible when the precondition
can be safely ignored if unknown (like If-Modified-Since), when
deployment can be assumed for a given use case, or when
implementation is signaled by some other property of the target
resource. This encourages a focus on mutually agreed deployment of
common standards.

13.1.1. If-Match

The "If-Match" header field makes the request method conditional on
the recipient origin server either having at least one current
representation of the target resource, when the field value is "\*",
or having a current representation of the target resource that has an
entity tag matching a member of the list of entity tags provided in
the field value.

An origin server MUST use the strong comparison function when
comparing entity tags for If-Match (Section 8.8.3.2), since the
client intends this precondition to prevent the method from being
applied if there have been any changes to the representation data.

     If-Match = "*" / #entity-tag

Examples:

If-Match: "xyzzy"
If-Match: "xyzzy", "r2d2xxxx", "c3piozzzz"
If-Match: \*

If-Match is most often used with state-changing methods (e.g., POST,
PUT, DELETE) to prevent accidental overwrites when multiple user
agents might be acting in parallel on the same resource (i.e., to
prevent the "lost update" problem). In general, it can be used with
any method that involves the selection or modification of a
representation to abort the request if the selected representation's
current entity tag is not a member within the If-Match field value.

When an origin server receives a request that selects a
representation and that request includes an If-Match header field,
the origin server MUST evaluate the If-Match condition per
Section 13.2 prior to performing the method.

To evaluate a received If-Match header field:

1.  If the field value is "\*", the condition is true if the origin
    server has a current representation for the target resource.

2.  If the field value is a list of entity tags, the condition is
    true if any of the listed tags match the entity tag of the
    selected representation.

3.  Otherwise, the condition is false.

An origin server that evaluates an If-Match condition MUST NOT
perform the requested method if the condition evaluates to false.
Instead, the origin server MAY indicate that the conditional request
failed by responding with a 412 (Precondition Failed) status code.
Alternatively, if the request is a state-changing operation that
appears to have already been applied to the selected representation,
the origin server MAY respond with a 2xx (Successful) status code
(i.e., the change requested by the user agent has already succeeded,
but the user agent might not be aware of it, perhaps because the
prior response was lost or an equivalent change was made by some
other user agent).

Allowing an origin server to send a success response when a change
request appears to have already been applied is more efficient for
many authoring use cases, but comes with some risk if multiple user
agents are making change requests that are very similar but not
cooperative. For example, multiple user agents writing to a common
resource as a semaphore (e.g., a nonatomic increment) are likely to
collide and potentially lose important state transitions. For those
kinds of resources, an origin server is better off being stringent in
sending 412 for every failed precondition on an unsafe method. In
other cases, excluding the ETag field from a success response might
encourage the user agent to perform a GET as its next request to
eliminate confusion about the resource's current state.

A client MAY send an If-Match header field in a GET request to
indicate that it would prefer a 412 (Precondition Failed) response if
the selected representation does not match. However, this is only
useful in range requests (Section 14) for completing a previously
received partial representation when there is no desire for a new
representation. If-Range (Section 13.1.5) is better suited for range
requests when the client prefers to receive a new representation.

A cache or intermediary MAY ignore If-Match because its
interoperability features are only necessary for an origin server.

Note that an If-Match header field with a list value containing "_"
and other values (including other instances of "_") is syntactically
invalid (therefore not allowed to be generated) and furthermore is
unlikely to be interoperable.

13.1.2. If-None-Match

The "If-None-Match" header field makes the request method conditional
on a recipient cache or origin server either not having any current
representation of the target resource, when the field value is "\*",
or having a selected representation with an entity tag that does not
match any of those listed in the field value.

A recipient MUST use the weak comparison function when comparing
entity tags for If-None-Match (Section 8.8.3.2), since weak entity
tags can be used for cache validation even if there have been changes
to the representation data.

     If-None-Match = "*" / #entity-tag

Examples:

If-None-Match: "xyzzy"
If-None-Match: W/"xyzzy"
If-None-Match: "xyzzy", "r2d2xxxx", "c3piozzzz"
If-None-Match: W/"xyzzy", W/"r2d2xxxx", W/"c3piozzzz"
If-None-Match: \*

If-None-Match is primarily used in conditional GET requests to enable
efficient updates of cached information with a minimum amount of
transaction overhead. When a client desires to update one or more
stored responses that have entity tags, the client SHOULD generate an
If-None-Match header field containing a list of those entity tags
when making a GET request; this allows recipient servers to send a
304 (Not Modified) response to indicate when one of those stored
responses matches the selected representation.

If-None-Match can also be used with a value of "\*" to prevent an
unsafe request method (e.g., PUT) from inadvertently modifying an
existing representation of the target resource when the client
believes that the resource does not have a current representation
(Section 9.2.1). This is a variation on the "lost update" problem
that might arise if more than one client attempts to create an
initial representation for the target resource.

When an origin server receives a request that selects a
representation and that request includes an If-None-Match header
field, the origin server MUST evaluate the If-None-Match condition
per Section 13.2 prior to performing the method.

To evaluate a received If-None-Match header field:

1.  If the field value is "\*", the condition is false if the origin
    server has a current representation for the target resource.

2.  If the field value is a list of entity tags, the condition is
    false if one of the listed tags matches the entity tag of the
    selected representation.

3.  Otherwise, the condition is true.

An origin server that evaluates an If-None-Match condition MUST NOT
perform the requested method if the condition evaluates to false;
instead, the origin server MUST respond with either a) the 304 (Not
Modified) status code if the request method is GET or HEAD or b) the
412 (Precondition Failed) status code for all other request methods.

Requirements on cache handling of a received If-None-Match header
field are defined in Section 4.3.2 of [CACHING].

Note that an If-None-Match header field with a list value containing
"_" and other values (including other instances of "_") is
syntactically invalid (therefore not allowed to be generated) and
furthermore is unlikely to be interoperable.

13.1.3. If-Modified-Since

The "If-Modified-Since" header field makes a GET or HEAD request
method conditional on the selected representation's modification date
being more recent than the date provided in the field value.
Transfer of the selected representation's data is avoided if that
data has not changed.

     If-Modified-Since = HTTP-date

An example of the field is:

If-Modified-Since: Sat, 29 Oct 1994 19:43:31 GMT

A recipient MUST ignore If-Modified-Since if the request contains an
If-None-Match header field; the condition in If-None-Match is
considered to be a more accurate replacement for the condition in If-
Modified-Since, and the two are only combined for the sake of
interoperating with older intermediaries that might not implement
If-None-Match.

A recipient MUST ignore the If-Modified-Since header field if the
received field value is not a valid HTTP-date, the field value has
more than one member, or if the request method is neither GET nor
HEAD.

A recipient MUST ignore the If-Modified-Since header field if the
resource does not have a modification date available.

A recipient MUST interpret an If-Modified-Since field value's
timestamp in terms of the origin server's clock.

If-Modified-Since is typically used for two distinct purposes: 1) to
allow efficient updates of a cached representation that does not have
an entity tag and 2) to limit the scope of a web traversal to
resources that have recently changed.

When used for cache updates, a cache will typically use the value of
the cached message's Last-Modified header field to generate the field
value of If-Modified-Since. This behavior is most interoperable for
cases where clocks are poorly synchronized or when the server has
chosen to only honor exact timestamp matches (due to a problem with
Last-Modified dates that appear to go "back in time" when the origin
server's clock is corrected or a representation is restored from an
archived backup). However, caches occasionally generate the field
value based on other data, such as the Date header field of the
cached message or the clock time at which the message was received,
particularly when the cached message does not contain a Last-Modified
header field.

When used for limiting the scope of retrieval to a recent time
window, a user agent will generate an If-Modified-Since field value
based on either its own clock or a Date header field received from
the server in a prior response. Origin servers that choose an exact
timestamp match based on the selected representation's Last-Modified
header field will not be able to help the user agent limit its data
transfers to only those changed during the specified window.

When an origin server receives a request that selects a
representation and that request includes an If-Modified-Since header
field without an If-None-Match header field, the origin server SHOULD
evaluate the If-Modified-Since condition per Section 13.2 prior to
performing the method.

To evaluate a received If-Modified-Since header field:

1.  If the selected representation's last modification date is
    earlier or equal to the date provided in the field value, the
    condition is false.

2.  Otherwise, the condition is true.

An origin server that evaluates an If-Modified-Since condition SHOULD
NOT perform the requested method if the condition evaluates to false;
instead, the origin server SHOULD generate a 304 (Not Modified)
response, including only those metadata that are useful for
identifying or updating a previously cached response.

Requirements on cache handling of a received If-Modified-Since header
field are defined in Section 4.3.2 of [CACHING].

13.1.4. If-Unmodified-Since

The "If-Unmodified-Since" header field makes the request method
conditional on the selected representation's last modification date
being earlier than or equal to the date provided in the field value.
This field accomplishes the same purpose as If-Match for cases where
the user agent does not have an entity tag for the representation.

     If-Unmodified-Since = HTTP-date

An example of the field is:

If-Unmodified-Since: Sat, 29 Oct 1994 19:43:31 GMT

A recipient MUST ignore If-Unmodified-Since if the request contains
an If-Match header field; the condition in If-Match is considered to
be a more accurate replacement for the condition in If-Unmodified-
Since, and the two are only combined for the sake of interoperating
with older intermediaries that might not implement If-Match.

A recipient MUST ignore the If-Unmodified-Since header field if the
received field value is not a valid HTTP-date (including when the
field value appears to be a list of dates).

A recipient MUST ignore the If-Unmodified-Since header field if the
resource does not have a modification date available.

A recipient MUST interpret an If-Unmodified-Since field value's
timestamp in terms of the origin server's clock.

If-Unmodified-Since is most often used with state-changing methods
(e.g., POST, PUT, DELETE) to prevent accidental overwrites when
multiple user agents might be acting in parallel on a resource that
does not supply entity tags with its representations (i.e., to
prevent the "lost update" problem). In general, it can be used with
any method that involves the selection or modification of a
representation to abort the request if the selected representation's
last modification date has changed since the date provided in the If-
Unmodified-Since field value.

When an origin server receives a request that selects a
representation and that request includes an If-Unmodified-Since
header field without an If-Match header field, the origin server MUST
evaluate the If-Unmodified-Since condition per Section 13.2 prior to
performing the method.

To evaluate a received If-Unmodified-Since header field:

1.  If the selected representation's last modification date is
    earlier than or equal to the date provided in the field value,
    the condition is true.

2.  Otherwise, the condition is false.

An origin server that evaluates an If-Unmodified-Since condition MUST
NOT perform the requested method if the condition evaluates to false.
Instead, the origin server MAY indicate that the conditional request
failed by responding with a 412 (Precondition Failed) status code.
Alternatively, if the request is a state-changing operation that
appears to have already been applied to the selected representation,
the origin server MAY respond with a 2xx (Successful) status code
(i.e., the change requested by the user agent has already succeeded,
but the user agent might not be aware of it, perhaps because the
prior response was lost or an equivalent change was made by some
other user agent).

Allowing an origin server to send a success response when a change
request appears to have already been applied is more efficient for
many authoring use cases, but comes with some risk if multiple user
agents are making change requests that are very similar but not
cooperative. In those cases, an origin server is better off being
stringent in sending 412 for every failed precondition on an unsafe
method.

A client MAY send an If-Unmodified-Since header field in a GET
request to indicate that it would prefer a 412 (Precondition Failed)
response if the selected representation has been modified. However,
this is only useful in range requests (Section 14) for completing a
previously received partial representation when there is no desire
for a new representation. If-Range (Section 13.1.5) is better suited
for range requests when the client prefers to receive a new
representation.

A cache or intermediary MAY ignore If-Unmodified-Since because its
interoperability features are only necessary for an origin server.

13.1.5. If-Range

The "If-Range" header field provides a special conditional request
mechanism that is similar to the If-Match and If-Unmodified-Since
header fields but that instructs the recipient to ignore the Range
header field if the validator doesn't match, resulting in transfer of
the new selected representation instead of a 412 (Precondition
Failed) response.

If a client has a partial copy of a representation and wishes to have
an up-to-date copy of the entire representation, it could use the
Range header field with a conditional GET (using either or both of
If-Unmodified-Since and If-Match.) However, if the precondition
fails because the representation has been modified, the client would
then have to make a second request to obtain the entire current
representation.

The "If-Range" header field allows a client to "short-circuit" the
second request. Informally, its meaning is as follows: if the
representation is unchanged, send me the part(s) that I am requesting
in Range; otherwise, send me the entire representation.

     If-Range = entity-tag / HTTP-date

A valid entity-tag can be distinguished from a valid HTTP-date by
examining the first three characters for a DQUOTE.

A client MUST NOT generate an If-Range header field in a request that
does not contain a Range header field. A server MUST ignore an If-
Range header field received in a request that does not contain a
Range header field. An origin server MUST ignore an If-Range header
field received in a request for a target resource that does not
support Range requests.

A client MUST NOT generate an If-Range header field containing an
entity tag that is marked as weak. A client MUST NOT generate an If-
Range header field containing an HTTP-date unless the client has no
entity tag for the corresponding representation and the date is a
strong validator in the sense defined by Section 8.8.2.2.

A server that receives an If-Range header field on a Range request
MUST evaluate the condition per Section 13.2 prior to performing the
method.

To evaluate a received If-Range header field containing an HTTP-date:

1.  If the HTTP-date validator provided is not a strong validator in
    the sense defined by Section 8.8.2.2, the condition is false.

2.  If the HTTP-date validator provided exactly matches the
    Last-Modified field value for the selected representation, the
    condition is true.

3.  Otherwise, the condition is false.

To evaluate a received If-Range header field containing an
entity-tag:

1.  If the entity-tag validator provided exactly matches the ETag
    field value for the selected representation using the strong
    comparison function (Section 8.8.3.2), the condition is true.

2.  Otherwise, the condition is false.

A recipient of an If-Range header field MUST ignore the Range header
field if the If-Range condition evaluates to false. Otherwise, the
recipient SHOULD process the Range header field as requested.

Note that the If-Range comparison is by exact match, including when
the validator is an HTTP-date, and so it differs from the "earlier
than or equal to" comparison used when evaluating an
If-Unmodified-Since conditional.

13.2. Evaluation of Preconditions

13.2.1. When to Evaluate

Except when excluded below, a recipient cache or origin server MUST
evaluate received request preconditions after it has successfully
performed its normal request checks and just before it would process
the request content (if any) or perform the action associated with
the request method. A server MUST ignore all received preconditions
if its response to the same request without those conditions, prior
to processing the request content, would have been a status code
other than a 2xx (Successful) or 412 (Precondition Failed). In other
words, redirects and failures that can be detected before significant
processing occurs take precedence over the evaluation of
preconditions.

A server that is not the origin server for the target resource and
cannot act as a cache for requests on the target resource MUST NOT
evaluate the conditional request header fields defined by this
specification, and it MUST forward them if the request is forwarded,
since the generating client intends that they be evaluated by a
server that can provide a current representation. Likewise, a server
MUST ignore the conditional request header fields defined by this
specification when received with a request method that does not
involve the selection or modification of a selected representation,
such as CONNECT, OPTIONS, or TRACE.

Note that protocol extensions can modify the conditions under which
preconditions are evaluated or the consequences of their evaluation.
For example, the immutable cache directive (defined by [RFC8246])
instructs caches to forgo forwarding conditional requests when they
hold a fresh response.

Although conditional request header fields are defined as being
usable with the HEAD method (to keep HEAD's semantics consistent with
those of GET), there is no point in sending a conditional HEAD
because a successful response is around the same size as a 304 (Not
Modified) response and more useful than a 412 (Precondition Failed)
response.

13.2.2. Precedence of Preconditions

When more than one conditional request header field is present in a
request, the order in which the fields are evaluated becomes
important. In practice, the fields defined in this document are
consistently implemented in a single, logical order, since "lost
update" preconditions have more strict requirements than cache
validation, a validated cache is more efficient than a partial
response, and entity tags are presumed to be more accurate than date
validators.

A recipient cache or origin server MUST evaluate the request
preconditions defined by this specification in the following order:

1.  When recipient is the origin server and If-Match is present,
    evaluate the If-Match precondition:

    - if true, continue to step 3

    - if false, respond 412 (Precondition Failed) unless it can be
      determined that the state-changing request has already
      succeeded (see Section 13.1.1)

2.  When recipient is the origin server, If-Match is not present, and
    If-Unmodified-Since is present, evaluate the If-Unmodified-Since
    precondition:

    - if true, continue to step 3

    - if false, respond 412 (Precondition Failed) unless it can be
      determined that the state-changing request has already
      succeeded (see Section 13.1.4)

3.  When If-None-Match is present, evaluate the If-None-Match
    precondition:

    - if true, continue to step 5

    - if false for GET/HEAD, respond 304 (Not Modified)

    - if false for other methods, respond 412 (Precondition Failed)

4.  When the method is GET or HEAD, If-None-Match is not present, and
    If-Modified-Since is present, evaluate the If-Modified-Since
    precondition:

    - if true, continue to step 5

    - if false, respond 304 (Not Modified)

5.  When the method is GET and both Range and If-Range are present,
    evaluate the If-Range precondition:

    - if true and the Range is applicable to the selected
      representation, respond 206 (Partial Content)

    - otherwise, ignore the Range header field and respond 200 (OK)

6.  Otherwise,

    - perform the requested method and respond according to its
      success or failure.

Any extension to HTTP that defines additional conditional request
header fields ought to define the order for evaluating such fields in
relation to those defined in this document and other conditionals
that might be found in practice.

14. Range Requests

Clients often encounter interrupted data transfers as a result of
canceled requests or dropped connections. When a client has stored a
partial representation, it is desirable to request the remainder of
that representation in a subsequent request rather than transfer the
entire representation. Likewise, devices with limited local storage
might benefit from being able to request only a subset of a larger
representation, such as a single page of a very large document, or
the dimensions of an embedded image.

Range requests are an OPTIONAL feature of HTTP, designed so that
recipients not implementing this feature (or not supporting it for
the target resource) can respond as if it is a normal GET request
without impacting interoperability. Partial responses are indicated
by a distinct status code to not be mistaken for full responses by
caches that might not implement the feature.

14.1. Range Units

Representation data can be partitioned into subranges when there are
addressable structural units inherent to that data's content coding
or media type. For example, octet (a.k.a. byte) boundaries are a
structural unit common to all representation data, allowing
partitions of the data to be identified as a range of bytes at some
offset from the start or end of that data.

This general notion of a "range unit" is used in the Accept-Ranges
(Section 14.3) response header field to advertise support for range
requests, the Range (Section 14.2) request header field to delineate
the parts of a representation that are requested, and the
Content-Range (Section 14.4) header field to describe which part of a
representation is being transferred.

     range-unit       = token

All range unit names are case-insensitive and ought to be registered
within the "HTTP Range Unit Registry", as defined in Section 16.5.1.

Range units are intended to be extensible, as described in
Section 16.5.

14.1.1. Range Specifiers

Ranges are expressed in terms of a range unit paired with a set of
range specifiers. The range unit name determines what kinds of
range-spec are applicable to its own specifiers. Hence, the
following grammar is generic: each range unit is expected to specify
requirements on when int-range, suffix-range, and other-range are
allowed.

A range request can specify a single range or a set of ranges within
a single representation.

     ranges-specifier = range-unit "=" range-set
     range-set        = 1#range-spec
     range-spec       = int-range
                      / suffix-range
                      / other-range

An int-range is a range expressed as two non-negative integers or as
one non-negative integer through to the end of the representation
data. The range unit specifies what the integers mean (e.g., they
might indicate unit offsets from the beginning, inclusive numbered
parts, etc.).

     int-range     = first-pos "-" [ last-pos ]
     first-pos     = 1*DIGIT
     last-pos      = 1*DIGIT

An int-range is invalid if the last-pos value is present and less
than the first-pos.

A suffix-range is a range expressed as a suffix of the representation
data with the provided non-negative integer maximum length (in range
units). In other words, the last N units of the representation data.

     suffix-range  = "-" suffix-length
     suffix-length = 1*DIGIT

To provide for extensibility, the other-range rule is a mostly
unconstrained grammar that allows application-specific or future
range units to define additional range specifiers.

     other-range   = 1*( %x21-2B / %x2D-7E )
                   ; 1*(VCHAR excluding comma)

A ranges-specifier is invalid if it contains any range-spec that is
invalid or undefined for the indicated range-unit.

A valid ranges-specifier is "satisfiable" if it contains at least one
range-spec that is satisfiable, as defined by the indicated
range-unit. Otherwise, the ranges-specifier is "unsatisfiable".

14.1.2. Byte Ranges

The "bytes" range unit is used to express subranges of a
representation data's octet sequence. Each byte range is expressed
as an integer range at some offset, relative to either the beginning
(int-range) or end (suffix-range) of the representation data. Byte
ranges do not use the other-range specifier.

The first-pos value in a bytes int-range gives the offset of the
first byte in a range. The last-pos value gives the offset of the
last byte in the range; that is, the byte positions specified are
inclusive. Byte offsets start at zero.

If the representation data has a content coding applied, each byte
range is calculated with respect to the encoded sequence of bytes,
not the sequence of underlying bytes that would be obtained after
decoding.

Examples of bytes range specifiers:

- The first 500 bytes (byte offsets 0-499, inclusive):

       bytes=0-499

- The second 500 bytes (byte offsets 500-999, inclusive):

       bytes=500-999

A client can limit the number of bytes requested without knowing the
size of the selected representation. If the last-pos value is
absent, or if the value is greater than or equal to the current
length of the representation data, the byte range is interpreted as
the remainder of the representation (i.e., the server replaces the
value of last-pos with a value that is one less than the current
length of the selected representation).

A client can refer to the last N bytes (N > 0) of the selected
representation using a suffix-range. If the selected representation
is shorter than the specified suffix-length, the entire
representation is used.

Additional examples, assuming a representation of length 10000:

- The final 500 bytes (byte offsets 9500-9999, inclusive):

       bytes=-500

  Or:

       bytes=9500-

- The first and last bytes only (bytes 0 and 9999):

       bytes=0-0,-1

- The first, middle, and last 1000 bytes:

       bytes= 0-999, 4500-5499, -1000

- Other valid (but not canonical) specifications of the second 500
  bytes (byte offsets 500-999, inclusive):

       bytes=500-600,601-999
       bytes=500-700,601-999

For a GET request, a valid bytes range-spec is satisfiable if it is
either:

- an int-range with a first-pos that is less than the current length
  of the selected representation or

- a suffix-range with a non-zero suffix-length.

When a selected representation has zero length, the only satisfiable
form of range-spec in a GET request is a suffix-range with a non-zero
suffix-length.

In the byte-range syntax, first-pos, last-pos, and suffix-length are
expressed as decimal number of octets. Since there is no predefined
limit to the length of content, recipients MUST anticipate
potentially large decimal numerals and prevent parsing errors due to
integer conversion overflows.

14.2. Range

The "Range" header field on a GET request modifies the method
semantics to request transfer of only one or more subranges of the
selected representation data (Section 8.1), rather than the entire
selected representation.

     Range = ranges-specifier

A server MAY ignore the Range header field. However, origin servers
and intermediate caches ought to support byte ranges when possible,
since they support efficient recovery from partially failed transfers
and partial retrieval of large representations.

A server MUST ignore a Range header field received with a request
method that is unrecognized or for which range handling is not
defined. For this specification, GET is the only method for which
range handling is defined.

An origin server MUST ignore a Range header field that contains a
range unit it does not understand. A proxy MAY discard a Range
header field that contains a range unit it does not understand.

A server that supports range requests MAY ignore or reject a Range
header field that contains an invalid ranges-specifier
(Section 14.1.1), a ranges-specifier with more than two overlapping
ranges, or a set of many small ranges that are not listed in
ascending order, since these are indications of either a broken
client or a deliberate denial-of-service attack (Section 17.15). A
client SHOULD NOT request multiple ranges that are inherently less
efficient to process and transfer than a single range that
encompasses the same data.

A server that supports range requests MAY ignore a Range header field
when the selected representation has no content (i.e., the selected
representation's data is of zero length).

A client that is requesting multiple ranges SHOULD list those ranges
in ascending order (the order in which they would typically be
received in a complete representation) unless there is a specific
need to request a later part earlier. For example, a user agent
processing a large representation with an internal catalog of parts
might need to request later parts first, particularly if the
representation consists of pages stored in reverse order and the user
agent wishes to transfer one page at a time.

The Range header field is evaluated after evaluating the precondition
header fields defined in Section 13.1, and only if the result in
absence of the Range header field would be a 200 (OK) response. In
other words, Range is ignored when a conditional GET would result in
a 304 (Not Modified) response.

The If-Range header field (Section 13.1.5) can be used as a
precondition to applying the Range header field.

If all of the preconditions are true, the server supports the Range
header field for the target resource, the received Range field-value
contains a valid ranges-specifier with a range-unit supported for
that target resource, and that ranges-specifier is satisfiable with
respect to the selected representation, the server SHOULD send a 206
(Partial Content) response with content containing one or more
partial representations that correspond to the satisfiable
range-spec(s) requested.

The above does not imply that a server will send all requested
ranges. In some cases, it may only be possible (or efficient) to
send a portion of the requested ranges first, while expecting the
client to re-request the remaining portions later if they are still
desired (see Section 15.3.7).

If all of the preconditions are true, the server supports the Range
header field for the target resource, the received Range field-value
contains a valid ranges-specifier, and either the range-unit is not
supported for that target resource or the ranges-specifier is
unsatisfiable with respect to the selected representation, the server
SHOULD send a 416 (Range Not Satisfiable) response.

14.3. Accept-Ranges

The "Accept-Ranges" field in a response indicates whether an upstream
server supports range requests for the target resource.

     Accept-Ranges     = acceptable-ranges
     acceptable-ranges = 1#range-unit

For example, a server that supports byte-range requests
(Section 14.1.2) can send the field

Accept-Ranges: bytes

to indicate that it supports byte range requests for that target
resource, thereby encouraging its use by the client for future
partial requests on the same request path. Range units are defined
in Section 14.1.

A client MAY generate range requests regardless of having received an
Accept-Ranges field. The information only provides advice for the
sake of improving performance and reducing unnecessary network
transfers.

Conversely, a client MUST NOT assume that receiving an Accept-Ranges
field means that future range requests will return partial responses.
The content might change, the server might only support range
requests at certain times or under certain conditions, or a different
intermediary might process the next request.

A server that does not support any kind of range request for the
target resource MAY send

Accept-Ranges: none

to advise the client not to attempt a range request on the same
request path. The range unit "none" is reserved for this purpose.

The Accept-Ranges field MAY be sent in a trailer section, but is
preferred to be sent as a header field because the information is
particularly useful for restarting large information transfers that
have failed in mid-content (before the trailer section is received).

14.4. Content-Range

The "Content-Range" header field is sent in a single part 206
(Partial Content) response to indicate the partial range of the
selected representation enclosed as the message content, sent in each
part of a multipart 206 response to indicate the range enclosed
within each body part (Section 14.6), and sent in 416 (Range Not
Satisfiable) responses to provide information about the selected
representation.

     Content-Range       = range-unit SP
                           ( range-resp / unsatisfied-range )

     range-resp          = incl-range "/" ( complete-length / "*" )
     incl-range          = first-pos "-" last-pos
     unsatisfied-range   = "*/" complete-length

     complete-length     = 1*DIGIT

If a 206 (Partial Content) response contains a Content-Range header
field with a range unit (Section 14.1) that the recipient does not
understand, the recipient MUST NOT attempt to recombine it with a
stored representation. A proxy that receives such a message SHOULD
forward it downstream.

Content-Range might also be sent as a request modifier to request a
partial PUT, as described in Section 14.5, based on private
agreements between client and origin server. A server MUST ignore a
Content-Range header field received in a request with a method for
which Content-Range support is not defined.

For byte ranges, a sender SHOULD indicate the complete length of the
representation from which the range has been extracted, unless the
complete length is unknown or difficult to determine. An asterisk
character ("\*") in place of the complete-length indicates that the
representation length was unknown when the header field was
generated.

The following example illustrates when the complete length of the
selected representation is known by the sender to be 1234 bytes:

Content-Range: bytes 42-1233/1234

and this second example illustrates when the complete length is
unknown:

Content-Range: bytes 42-1233/\*

A Content-Range field value is invalid if it contains a range-resp
that has a last-pos value less than its first-pos value, or a
complete-length value less than or equal to its last-pos value. The
recipient of an invalid Content-Range MUST NOT attempt to recombine
the received content with a stored representation.

A server generating a 416 (Range Not Satisfiable) response to a byte-
range request SHOULD send a Content-Range header field with an
unsatisfied-range value, as in the following example:

Content-Range: bytes \*/1234

The complete-length in a 416 response indicates the current length of
the selected representation.

The Content-Range header field has no meaning for status codes that
do not explicitly describe its semantic. For this specification,
only the 206 (Partial Content) and 416 (Range Not Satisfiable) status
codes describe a meaning for Content-Range.

The following are examples of Content-Range values in which the
selected representation contains a total of 1234 bytes:

- The first 500 bytes:

  Content-Range: bytes 0-499/1234

- The second 500 bytes:

  Content-Range: bytes 500-999/1234

- All except for the first 500 bytes:

  Content-Range: bytes 500-1233/1234

- The last 500 bytes:

  Content-Range: bytes 734-1233/1234

  14.5. Partial PUT

Some origin servers support PUT of a partial representation when the
user agent sends a Content-Range header field (Section 14.4) in the
request, though such support is inconsistent and depends on private
agreements with user agents. In general, it requests that the state
of the target resource be partly replaced with the enclosed content
at an offset and length indicated by the Content-Range value, where
the offset is relative to the current selected representation.

An origin server SHOULD respond with a 400 (Bad Request) status code
if it receives Content-Range on a PUT for a target resource that does
not support partial PUT requests.

Partial PUT is not backwards compatible with the original definition
of PUT. It may result in the content being written as a complete
replacement for the current representation.

Partial resource updates are also possible by targeting a separately
identified resource with state that overlaps or extends a portion of
the larger resource, or by using a different method that has been
specifically defined for partial updates (for example, the PATCH
method defined in [RFC5789]).

14.6. Media Type multipart/byteranges

When a 206 (Partial Content) response message includes the content of
multiple ranges, they are transmitted as body parts in a multipart
message body ([RFC2046], Section 5.1) with the media type of
"multipart/byteranges".

The "multipart/byteranges" media type includes one or more body
parts, each with its own Content-Type and Content-Range fields. The
required boundary parameter specifies the boundary string used to
separate each body part.

Implementation Notes:

1.  Additional CRLFs might precede the first boundary string in the
    body.

2.  Although [RFC2046] permits the boundary string to be quoted, some
    existing implementations handle a quoted boundary string
    incorrectly.

3.  A number of clients and servers were coded to an early draft of
    the byteranges specification that used a media type of
    "multipart/x-byteranges", which is almost (but not quite)
    compatible with this type.

Despite the name, the "multipart/byteranges" media type is not
limited to byte ranges. The following example uses an "exampleunit"
range unit:

HTTP/1.1 206 Partial Content
Date: Tue, 14 Nov 1995 06:25:24 GMT
Last-Modified: Tue, 14 July 04:58:08 GMT
Content-Length: 2331785
Content-Type: multipart/byteranges; boundary=THIS_STRING_SEPARATES

--THIS_STRING_SEPARATES
Content-Type: video/example
Content-Range: exampleunit 1.2-4.3/25

...the first range...
--THIS_STRING_SEPARATES
Content-Type: video/example
Content-Range: exampleunit 11.2-14.3/25

...the second range
--THIS_STRING_SEPARATES--

The following information serves as the registration form for the
"multipart/byteranges" media type.

Type name: multipart

Subtype name: byteranges

Required parameters: boundary

Optional parameters: N/A

Encoding considerations: only "7bit", "8bit", or "binary" are
permitted

Security considerations: see Section 17

Interoperability considerations: N/A

Published specification: RFC 9110 (see Section 14.6)

Applications that use this media type: HTTP components supporting
multiple ranges in a single request

Fragment identifier considerations: N/A

Additional information: Deprecated alias names for this type: N/A

                            Magic number(s):  N/A

                            File extension(s):  N/A

                            Macintosh file type code(s):  N/A

Person and email address to contact for further information: See Aut
hors' Addresses section.

Intended usage: COMMON

Restrictions on usage: N/A

Author: See Authors' Addresses section.

Change controller: IESG

## 15. 상태 코드

응답의 상태 코드는 요청의 결과와 요청이 성공했는지의 여부 및 어떤 콘텐츠가 동봉되어 있는지를 포함한 응답의 의미를 설명하는 세 자리 정수 코드다 (있는 경우). 모든 유효한 상태 코드는 100부터 599까지의 범위 내에 있다.

상태 코드의 첫 번째 자리는 응답의 클래스를 정의한다. 마지막 두 자리는 분류 역할이 없다. 첫 번째 숫자에는 다섯 가지의 정보가 있다:

- 1xx (Informational): 요청을 받았고, 처리를 계속 한다.

- 2xx (Successful): 요청을 성공적으로 받았고, 이해했고, 승인했다.

- 3xx (Redirection): 요청을 완료하려면 추가 조치가 필요하다.

- 4xx (Client Error): 요청에 잘못된 구문이 포함되어 있거나 수행할 수 없다.

- 5xx (Server Error): 서버가 명백히 유효한 요청을 수행하지 못했다.

HTTP 상태 코드는 확장 가능하다.클라이언트는 등록된 모른 상태 코드의 의미를 이해할 필요는 없지만, 이해하는것이 분명히 바람직하다. 그러나, 클라이언트는 반드시(MUST) 첫 번째 숫자로 표시된 상태코드의 클래스를 이해하고 인식할 수 없는 상태 코드를 해당 클래스의 x00 상태 코드와 동등한 것으로 처리해야 한다.

예시로, 클라이언트가 471이라는 인식되지 않는 상태 코드를 받은 경우, 첫 번째 숫자를 통해 요청에 문제가 있음을 파악하고 응답을 400(Bad Request) 상태 코드를 받은 것처럼 처리할 수 있다. 응답 메시지에는 일반적으로 상태를 설명하는 표현이 포함된다.

100..599 범위를 벗어난 값은 유효하지 않다. 구현에서는 종종 HTTP가 아닌 상태의 내부 통신을 위해 해당 범위를 벗어난 세 자리 정수 값을(예: 600..999) 사용하는 경우가 많다. (예: 라이브러리 에러) 유효하지않은 상태 코드를 가진 응답을 수신한 클라이언트는 왠만하면(SHOULD) 5xx(Server Error) 상태 코드를 가진 것처럼 응답을 처리해야 한다.

단일 요청은 여러 개의 관련 응답이 있을 수 있다: 상태 코드가 “정보”(1xx) 범위인 “중간”(최종이 아님) 응답이 0개 이상이고, 다른 범위 중 하나의 상태 코드가 있는 “최종” 응답이 정확히 하나만 있는 경우다.

### 15.1. 상태 코드의 개요

아래에 나열된 상태 코드는 본 명세서에 정의되어 있다. 여기에 나열된 이유 문구는 권장 사항일 뿐이다 — 프로토콜에 영향을 주지 않고 로컬에 상응하는 문구로 대체하거나 아예 생략할 수 있다.

Heuristic하게 캐시 가능한 것으로 정의된 상태 코드를 갖는 응답은 (예: 본 명세서에서 200, 203, 204, 206, 300, 301, 308, 404, 405, 410, 414, 501) 메서드 정의 또는 명시적 캐시 컨트롤[CACHING]; 에 달리 명시되지 않는 한 Heuristic 만료를 갖는 캐시에 의해 재사용할 수 있으며, 다른 모든 상태 코드들은 Heuristic하게 캐시할 수 없다.

본 명세서의 범위를 벗어난 추가적인 상태 코드는 HTTP에서 사용하도록 규정되어 있다. 이러한 모든 상태 코드는 16.2절에 설명된 것과 같이 “Hypertext Transfer Protocol (HTTP) Status Code Registry”에 등록해야 한다.

### 15.2. Informational 1xx

1xx(Informational) 상태 코드 클래스는 요청된 작업을 완료하고 최종 응답을 전송하기 전에 연결 상태 또는 요청 진행 상황을 전달하기 위한 중간 응답을 나타낸다. HTTP/1.0 은 어떠한 1xx 상태 코드가 정의되어 있지 않으므로, 서버는 HTTP/1.0 클라이언트에 1xx 응답을 절대(MUST NOT) 전송해서는 안 된다.

1xx 응답은 헤더 섹션이 끝날 때 종료되며, 콘텐츠나 트레일러를 포함 할 수 없다.

클라이언트는 최종 응답 전에 수신된 하나 이상의 1xx 응답을 클라이언트가 예상하지 못하더라도 반드시(MUST) 파싱할 수 있어야 한다. 사용자 에이전트는 예상지 못한 1xx 응답을 아마(MAY) 무시할 수 있다.

프록시는 프록시 자체가 1xx 응답 생성을 요청하지 않는 한 반드시(MUST) 1xx 응답을 전달해야 한다. 예를 들어 프록시가 요청을 전달할 때 “예상: 100-continue” 헤더 필드를 추가하는 경우, 해당 100(Continue) 응답을 전달할 필요가 없다.

#### 15.2.1. 100 Continue

100(Continue) 상태 코드는 요청의 초기 부분이 수신되었으나 아직 서버에 의해 거절되지 않았음을 나타낸다. 서버는 요청을 완전히 수신하고 조치된 후에 최종 응답을 전송하고자 한다.

요청에 100-continue 기대가 포함된 Expect 헤더 필드를 포함하는 경우, 100 응답은 10.1.1절에 설명된 대로 서버가 요청 콘텐츠를 수신하기를 원한다는 것을 나타낸다. 클라이언트는 요청을 계속 전송하고 100 응답을 폐기해야 한다.

요청에 100-continue 기대가 포함된 Expect 헤더 필드가 포함되지 않은 경우, 클라이언트는 이 임시 응답을 간단히 폐기할 수 있다.

#### 15.2.2. 101 Switching Protocols

101(Switching Protocols) 상태 코드는 서버가 업그레이드 헤더 필드(7.8절)를 통해 이 연결에서 사용 중인 애플리케이션 프로토콜의 변경에 대한 클라이언트의 요청을 이해하고, 이를 준수할 것임을 나타낸다. 서버는 반드시(MUST) 응답 후에 어떤 프로토콜이 적용될 것인지를 나타내는 업그레이드 헤더 필드를 응답에 생성해야 한다.

서버는 프로토콜을 전환하는 것이 유리할 때만 프로토콜 전환에 동의한다고 가정한다. 예를 들어, 최신 버전의 HTTP로 전환하는 것이 이전 버전보다 유리할 수 있으며, 이러한 기능을 사용하는 리소스를 제공할 때 실시간 동기 프로토콜로 전환하는 것이 유리할 수 있다.

### 15.3. Successful 2xx

상태 코드의 2xx (Successful) 클래스는 클라이언트의 요청이 성공적으로 수신, 이해 및 수락되었음을 나타낸다.

#### 15.3.1. 200 OK

200(OK) 상태 코드는 요청이 성공했음을 나타낸다.
200 응답으로 전송되는 내용은 요청 메소드에 따라 다르다.
본 명세서에서 정의된 메소드의 경우, 콘텐츠의 의도된 의미는 다음과 같이 요약할 수 있다:

+================+============================================+
| Request Method | 응답 콘텐츠는 다음을 나타낸다: |
+================+============================================+
| GET | 목표 리소스 |
+----------------+--------------------------------------------+
| HEAD | GET과 같이 대상 리소스를 전송하지만 표현 데이터는 전송하지 않는다. |
+----------------+--------------------------------------------+
| POST | 작업의 상태 또는 작업으로부터 얻은 결과 |
+----------------+--------------------------------------------+
| PUT, DELETE | 작업의 상태 |
+----------------+--------------------------------------------+
| OPTIONS | 대상 리소스에 대한 통신 옵션 |
+----------------+--------------------------------------------+
| TRACE | trace가 반환하는 서버가 수신한 요청 메세지 |
+----------------+--------------------------------------------+

                               Table 6

CONNECT에 대한 응답과 별도로, 메세지 프레임에 콘텐츠 길이가 0임을 명시적으로 표시하지 않는 한 200 응답은 메세지 콘텐츠를 포함할 것으로 예상한다. 요청의 일부 측면이 성공 시 콘텐츠가 없는 것을 선호하는 경우, origin 서버는 대신 204 (No Content) 응답을 전송해야 한다. CONNECT의 경우, 성공적인 결과는 200 응답 헤더 섹션 바로 뒤에 시작되는 터널이므로 콘텐츠가 없다.

200 응답은 메소드 정의 또는 명시적 캐시 컨트롤에 달리 명시되지 않는 한 heuristic 하게 캐시할 수 있다. ([CACHING] 4.2.2절 참조)

origin 서버는 GET 또는 HEAD에 대한 200 응답에서 선택한 표현에 대해 사용 가능한 검증자 필드 (8.8절)를 보내야 하며, 강력한 엔티티 태그와 마지막 수정 날짜가 선호된다.

상태 변경 메소드에 대한 200 응답에서, 응답에 전송된 임의의 검증자 필드 (8.8절)는 요청 semantics를 성공적으로 적용한 결과로 형성된 새로운 표현에 대한 현재 검증자를 전달한다. PUT 방식 (9.3.4절)에는 이러한 검증자의 전송을 방해할 수 있는 추가 요구 사항이 있다는 점을 유의하라.

#### 15.3.2. 201 Created

201(Created) 상태 코드는 요청이 완료되어 하나 이상의 새로운 리소스가 생성되었음을 나타낸다. 요청에 의해 생선된 기본 리소스는 응답의 Location 헤더 필드 또는 Location 헤더 필드가 수신되지 않은 경우 대상 URI로 식별된다.

201 응답 콘텐츠는 일반적으로 생성된 리소스에 대한 설명 및 링크를 제공한다. 응답에 전송된 모든 검증자 필드 (8.8절) 는 요청에 의해 생성된 새로운 표현에 대한 현재 검증자를 전달한다. PUT 방식 (9.3.4절) 에는 이러한 검증자의 전송을 방해할 수 있는 추가 요구 사항이 있음에 유의한다.

#### 15.3.3. 202 Accepted

202 (Accepted) 상태 코드는 요청이 처리를 위해 수락되었지만 처리가 완료되지 않았음을 나타낸다. 요청이 실제로 처리될 때 허용되지 않을 수 있기 때문에 요청이 처리될 수도 있고 처리되지 않을 수도 있다. HTTP에는 비동기 작업에서 상태 코드를 다시 전송하는 기능이 없다.

202 응답은 의도적으로 응답하지 않는다. 이것의 목적은 프로세스가 완료될 때까지 서버에 대한 사용자 에이전트의 연결을 유지할 필요 없이 서버가 다른 프로세스 (아마 하루에 한 번만 실행되는 배치 지향 프로세스) 에 대한 요청을 수락할 수 있도록 하는 것이다. 이 응답과 함께 전송되는 표현은 요청의 현재 상태를 설명하고 사용자에게 요청이 언제 완료될지 예상할 수 있는 상태 모니터를 가리키거나 포함해야 한다.

#### 15.3.4. 203 Non-Authoritative Information

203 (Non-Authoritative Information) 상태 코드는 요청이 성공했지만 변환 프로식에 의해 원본 서버의 200 (OK) 응답에서 동봉된 콘텐츠가 수정되었음을 나타낸다 (7.7절). 이 상태 코드를 통해 프록시는 변환이 적용되었을 때 수신자에게 알릴 수 있고, 이 지식은 나중에 콘텐츠와 관련된 의사 결정에 영향을 미칠 수 있다. 예를 들어, 콘텐츠에 대한 향후 캐시 유효성 검사 요청은 동일한 요청 경로 (동일한 프록시를 통해) 를 통해서만 적용할 수 있다.

203 응답은 메서드 정의 또는 명시적 캐시 컨트롤에 달리 명시되지 않는 한 (4.2.2절 참조 [CACHING]) heuristic하게 캐시 할 수 있다.

#### 15.3.5. 204 No Content

204 (No Content) 상태 코드는 서버가 성공적으로 요청을 처리했고, 응답 콘텐츠에서 보낼 추가 콘텐츠가 없음을 나타낸다. 응답 헤더 필드의 메타데이터는 대상 리소스와 요청된 작업이 적용된 후 선택한 표현을 참조한다.

예를 들어, PUT 요청에 대한 응답으로 204 상태 코드가 수신되고 응답에 ETag 필드가 포함하는 경우, PUT이 성공했으며 ETag 필드 값에 대상 리소스의 새로운 표현을 위한 엔티티 태그가 포함한다.

204 응답을 통해 서버는 대상 리소스에 성공적으로 적용되었음을 나타낼 수 있게 하고, 동시에 사용자 에이전트가 현재의 “문서보기” (있는 경우)에서 이동할 필요가 없음을 암시한다. 서버는 사용자 에이전트가 자제 인터페이스에 따라 사용자에게 성공 여부를 표시하고 활성 표현에 대한 응답으로 새로운 메타데이터 또는 업데이트된 메타데이터를 적용할 것으로 가정한다.

예를 들어, 204 상태 코드는 일반적으로 “저장” 작업에 해당하는 문서 편집 인터페이스에 사용되며, 저장중인 문서는 사용자가 편집을 위해 사용할 수 있도록 한다. 또한 분산 버전 관리 시스템과 같이 자동화된 데이터 전송이 보편화 될 것으로 예상되는 인터페이스에서도 자주 사용된다.

204 응답은 헤더 섹션의 끝에 의해 종료된다; 그것은 콘텐츠나 트레일러를 포함할 수 없다.

204 응답은 메서드 정의 또는 명시적 캐시 컨트롤에 달리 명시되지 않는 한 (4.2.2절 참조 [CACHING]) heuristic하게 캐시 할 수 있다.


#### 15.3.6. 205 Reset Content

205 (Reset Content) 상태 코드는 서버가 요청을 처리했으며 사용자 에이전트가 요청을 전송한 “문서 보기” 를 origin 서버 에서 받은 원래 상태로 재설정하기를 원한다는 것을 나타낸다.

이 응답은 사용자가 데이터 입력을 지원하는 컨텐츠(양식, 메모장, 캔버스 등)를 수신하고, 해당 공간에 데이터를 입력하거나 조작하여, 입력된 데이터가 요청에 제출되도록 한 다음, 사용자가 다른 입력 작업을 쉽게 시작할 수 있도록 다음 입력을 위해 데이터 입력 메커니즘이 재설정되는 일반적인 데이터 입력 사용 사례를 지원하기 위한 것이다.


205 상태 코드는 추가 콘텐츠가 제공되지 않음을 의미하므로, 서버는 205 응답으로 콘텐츠를 생성해서는 안 된다.

#### 15.3.7. 206 Partial Content

206 (Partial Content) 상태 코드는 서버가 선택한 표현의 하나 이상의 부분을 전송하여 대상 리소스에 대한 범위 요청을 성공적으로 수행하고 있음을 나타낸다.

범위 요청을 지원하는 서버 (14절) 는 일반적으로 요청된 모든 범위를 충족하려고 시도하는데, 이는 데이터를 적게 보내면 나머지 부분에 대한 또 다른 클라이언트 요청이 발생할 가능성이 있기 때문이다. 그러나 서버는 일시적인 사용 불가, 캐시 효율성, 로드 밸런싱등과 같은 이유로 요청된 데이터의 일부만 전송하고자 할 수 있다. 206 응답은 자기 설명적이기 때문에, 클라이언트는 범위 요청을 부분적으로만 만족시키는 응답을 이해할 수 있다.

클라이언트는 206 응답의 콘텐츠 유형 및 콘텐츠 범위 필드를 검사하여 어떤 부분이 포함되는지, 추가 요청이 필요한지 여부를 반드시(MUST) 확인해야 한다.

206 응답을 생성하는 서버는 동일한 요청에 대한 200 (OK) 응답으로 전송되었을 경우, 아래 하위 항목에 필요한 필드 외에 다음과 같은 헤더 필드를 반드시(MUST) 생성해야 한다. 날짜, 캐시-제어, ETag, 만료, 콘텐츠 위치, 등등


206 응답에 있는 Content-Length 헤더 필드는 이 메세지 콘텐츠의 옥텟 수를 나타내며, 일반적으로 선택한 표현의 전체 길이가 아니다. 각 Content-Range 헤더 필드는 선택한 표현의 전체 길이에 대한 정보가 포함되어 있다.

If-Range 헤더 필드를 갖는 요청에 대한 206 응답을 생성하는 발신자는 클라이언트가 이미 해당 헤더 필드를 포함하는 이전 응답을 가지고 있기 때문에 요구되는 것 이상의 다른 표현 헤더 필드를 웬만해서는(SHOULD NOT) 생성해서는 안 된다. 그렇지 않으면 발신자는 동일한 요청에 대해 200(OK) 응답으로 전송되었을 모든 표현 헤더 필드를 반드시(MUST) 생성해야 한다.

206 응답은 메서드 정의 또는 명시적 캐시 컨트롤에 달리 명시되지 않는 한 (4.2.2절 참조 [CACHING]) heuristic하게 캐시 할 수 있다.

15.3.7.1. Single Part

If a single part is being transferred, the server generating the 206
response MUST generate a Content-Range header field, describing what
range of the selected representation is enclosed, and a content
consisting of the range. For example:

HTTP/1.1 206 Partial Content
Date: Wed, 15 Nov 1995 06:25:24 GMT
Last-Modified: Wed, 15 Nov 1995 04:58:08 GMT
Content-Range: bytes 21010-47021/47022
Content-Length: 26012
Content-Type: image/gif

... 26012 bytes of partial image data ...

15.3.7.2. Multiple Parts

If multiple parts are being transferred, the server generating the
206 response MUST generate "multipart/byteranges" content, as defined
in Section 14.6, and a Content-Type header field containing the
"multipart/byteranges" media type and its required boundary
parameter. To avoid confusion with single-part responses, a server
MUST NOT generate a Content-Range header field in the HTTP header
section of a multiple part response (this field will be sent in each
part instead).

Within the header area of each body part in the multipart content,
the server MUST generate a Content-Range header field corresponding
to the range being enclosed in that body part. If the selected
representation would have had a Content-Type header field in a 200
(OK) response, the server SHOULD generate that same Content-Type
header field in the header area of each body part. For example:

HTTP/1.1 206 Partial Content
Date: Wed, 15 Nov 1995 06:25:24 GMT
Last-Modified: Wed, 15 Nov 1995 04:58:08 GMT
Content-Length: 1741
Content-Type: multipart/byteranges; boundary=THIS_STRING_SEPARATES

--THIS_STRING_SEPARATES
Content-Type: application/pdf
Content-Range: bytes 500-999/8000

...the first range...
--THIS_STRING_SEPARATES
Content-Type: application/pdf
Content-Range: bytes 7000-7999/8000

...the second range
--THIS_STRING_SEPARATES--

When multiple ranges are requested, a server MAY coalesce any of the
ranges that overlap, or that are separated by a gap that is smaller
than the overhead of sending multiple parts, regardless of the order
in which the corresponding range-spec appeared in the received Range
header field. Since the typical overhead between each part of a
"multipart/byteranges" is around 80 bytes, depending on the selected
representation's media type and the chosen boundary parameter length,
it can be less efficient to transfer many small disjoint parts than
it is to transfer the entire selected representation.

A server MUST NOT generate a multipart response to a request for a
single range, since a client that does not request multiple parts
might not support multipart responses. However, a server MAY
generate a "multipart/byteranges" response with only a single body
part if multiple ranges were requested and only one range was found
to be satisfiable or only one range remained after coalescing. A
client that cannot process a "multipart/byteranges" response MUST NOT
generate a request that asks for multiple ranges.

A server that generates a multipart response SHOULD send the parts in
the same order that the corresponding range-spec appeared in the
received Range header field, excluding those ranges that were deemed
unsatisfiable or that were coalesced into other ranges. A client
that receives a multipart response MUST inspect the Content-Range
header field present in each body part in order to determine which
range is contained in that body part; a client cannot rely on
receiving the same ranges that it requested, nor the same order that
it requested.

15.3.7.3. Combining Parts

A response might transfer only a subrange of a representation if the
connection closed prematurely or if the request used one or more
Range specifications. After several such transfers, a client might
have received several ranges of the same representation. These
ranges can only be safely combined if they all have in common the
same strong validator (Section 8.8.1).

A client that has received multiple partial responses to GET requests
on a target resource MAY combine those responses into a larger
continuous range if they share the same strong validator.

If the most recent response is an incomplete 200 (OK) response, then
the header fields of that response are used for any combined response
and replace those of the matching stored responses.

If the most recent response is a 206 (Partial Content) response and
at least one of the matching stored responses is a 200 (OK), then the
combined response header fields consist of the most recent 200
response's header fields. If all of the matching stored responses
are 206 responses, then the stored response with the most recent
header fields is used as the source of header fields for the combined
response, except that the client MUST use other header fields
provided in the new response, aside from Content-Range, to replace
all instances of the corresponding header fields in the stored
response.

The combined response content consists of the union of partial
content ranges within the new response and all of the matching stored
responses. If the union consists of the entire range of the
representation, then the client MUST process the combined response as
if it were a complete 200 (OK) response, including a Content-Length
header field that reflects the complete length. Otherwise, the
client MUST process the set of continuous ranges as one of the
following: an incomplete 200 (OK) response if the combined response
is a prefix of the representation, a single 206 (Partial Content)
response containing "multipart/byteranges" content, or multiple 206
(Partial Content) responses, each with one continuous range that is
indicated by a Content-Range header field.

15.4. Redirection 3xx

The 3xx (Redirection) class of status code indicates that further
action needs to be taken by the user agent in order to fulfill the
request. There are several types of redirects:

1.  Redirects that indicate this resource might be available at a
    different URI, as provided by the Location header field, as in
    the status codes 301 (Moved Permanently), 302 (Found), 307
    (Temporary Redirect), and 308 (Permanent Redirect).

2.  Redirection that offers a choice among matching resources capable
    of representing this resource, as in the 300 (Multiple Choices)
    status code.

3.  Redirection to a different resource, identified by the Location
    header field, that can represent an indirect response to the
    request, as in the 303 (See Other) status code.

4.  Redirection to a previously stored result, as in the 304 (Not
    Modified) status code.

    | _Note:_ In HTTP/1.0, the status codes 301 (Moved Permanently)
    | and 302 (Found) were originally defined as method-preserving
    | ([HTTP/1.0], Section 9.3) to match their implementation at
    | CERN; 303 (See Other) was defined for a redirection that
    | changed its method to GET. However, early user agents split on
    | whether to redirect POST requests as POST (according to then-
    | current specification) or as GET (the safer alternative when
    | redirected to a different site). Prevailing practice
    | eventually converged on changing the method to GET. 307
    | (Temporary Redirect) and 308 (Permanent Redirect) [RFC7538]
    | were later added to unambiguously indicate method-preserving
    | redirects, and status codes 301 and 302 have been adjusted to
    | allow a POST request to be redirected as GET.

If a Location header field (Section 10.2.2) is provided, the user
agent MAY automatically redirect its request to the URI referenced by
the Location field value, even if the specific status code is not
understood. Automatic redirection needs to be done with care for
methods not known to be safe, as defined in Section 9.2.1, since the
user might not wish to redirect an unsafe request.

When automatically following a redirected request, the user agent
SHOULD resend the original request message with the following
modifications:

1.  Replace the target URI with the URI referenced by the redirection
    response's Location header field value after resolving it
    relative to the original request's target URI.

2.  Remove header fields that were automatically generated by the
    implementation, replacing them with updated values as appropriate
    to the new request. This includes:

    1.  Connection-specific header fields (see Section 7.6.1),

    2.  Header fields specific to the client's proxy configuration,
        including (but not limited to) Proxy-Authorization,

    3.  Origin-specific header fields (if any), including (but not
        limited to) Host,

    4.  Validating header fields that were added by the
        implementation's cache (e.g., If-None-Match,
        If-Modified-Since), and

    5.  Resource-specific header fields, including (but not limited
        to) Referer, Origin, Authorization, and Cookie.

3.  Consider removing header fields that were not automatically
    generated by the implementation (i.e., those present in the
    request because they were added by the calling context) where
    there are security implications; this includes but is not limited
    to Authorization and Cookie.

4.  Change the request method according to the redirecting status
    code's semantics, if applicable.

5.  If the request method has been changed to GET or HEAD, remove
    content-specific header fields, including (but not limited to)
    Content-Encoding, Content-Language, Content-Location,
    Content-Type, Content-Length, Digest, Last-Modified.

A client SHOULD detect and intervene in cyclical redirections (i.e.,
"infinite" redirection loops).

      |  *Note:* An earlier version of this specification recommended a
      |  maximum of five redirections ([RFC2068], Section 10.3).
      |  Content developers need to be aware that some clients might
      |  implement such a fixed limitation.

15.4.1. 300 Multiple Choices

The 300 (Multiple Choices) status code indicates that the target
resource has more than one representation, each with its own more
specific identifier, and information about the alternatives is being
provided so that the user (or user agent) can select a preferred
representation by redirecting its request to one or more of those
identifiers. In other words, the server desires that the user agent
engage in reactive negotiation to select the most appropriate
representation(s) for its needs (Section 12).

If the server has a preferred choice, the server SHOULD generate a
Location header field containing a preferred choice's URI reference.
The user agent MAY use the Location field value for automatic
redirection.

For request methods other than HEAD, the server SHOULD generate
content in the 300 response containing a list of representation
metadata and URI reference(s) from which the user or user agent can
choose the one most preferred. The user agent MAY make a selection
from that list automatically if it understands the provided media
type. A specific format for automatic selection is not defined by
this specification because HTTP tries to remain orthogonal to the
definition of its content. In practice, the representation is
provided in some easily parsed format believed to be acceptable to
the user agent, as determined by shared design or content
negotiation, or in some commonly accepted hypertext format.

A 300 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

      |  *Note:* The original proposal for the 300 status code defined
      |  the URI header field as providing a list of alternative
      |  representations, such that it would be usable for 200, 300, and
      |  406 responses and be transferred in responses to the HEAD
      |  method.  However, lack of deployment and disagreement over
      |  syntax led to both URI and Alternates (a subsequent proposal)
      |  being dropped from this specification.  It is possible to
      |  communicate the list as a Link header field value [RFC8288]
      |  whose members have a relationship of "alternate", though
      |  deployment is a chicken-and-egg problem.

15.4.2. 301 Moved Permanently

The 301 (Moved Permanently) status code indicates that the target
resource has been assigned a new permanent URI and any future
references to this resource ought to use one of the enclosed URIs.
The server is suggesting that a user agent with link-editing
capability can permanently replace references to the target URI with
one of the new references sent by the server. However, this
suggestion is usually ignored unless the user agent is actively
editing references (e.g., engaged in authoring content), the
connection is secured, and the origin server is a trusted authority
for the content being edited.

The server SHOULD generate a Location header field in the response
containing a preferred URI reference for the new permanent URI. The
user agent MAY use the Location field value for automatic
redirection. The server's response content usually contains a short
hypertext note with a hyperlink to the new URI(s).

      |  *Note:* For historical reasons, a user agent MAY change the
      |  request method from POST to GET for the subsequent request.  If
      |  this behavior is undesired, the 308 (Permanent Redirect) status
      |  code can be used instead.

A 301 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

15.4.3. 302 Found

The 302 (Found) status code indicates that the target resource
resides temporarily under a different URI. Since the redirection
might be altered on occasion, the client ought to continue to use the
target URI for future requests.

The server SHOULD generate a Location header field in the response
containing a URI reference for the different URI. The user agent MAY
use the Location field value for automatic redirection. The server's
response content usually contains a short hypertext note with a
hyperlink to the different URI(s).

      |  *Note:* For historical reasons, a user agent MAY change the
      |  request method from POST to GET for the subsequent request.  If
      |  this behavior is undesired, the 307 (Temporary Redirect) status
      |  code can be used instead.

15.4.4. 303 See Other

The 303 (See Other) status code indicates that the server is
redirecting the user agent to a different resource, as indicated by a
URI in the Location header field, which is intended to provide an
indirect response to the original request. A user agent can perform
a retrieval request targeting that URI (a GET or HEAD request if
using HTTP), which might also be redirected, and present the eventual
result as an answer to the original request. Note that the new URI
in the Location header field is not considered equivalent to the
target URI.

This status code is applicable to any HTTP method. It is primarily
used to allow the output of a POST action to redirect the user agent
to a different resource, since doing so provides the information
corresponding to the POST response as a resource that can be
separately identified, bookmarked, and cached.

A 303 response to a GET request indicates that the origin server does
not have a representation of the target resource that can be
transferred by the server over HTTP. However, the Location field
value refers to a resource that is descriptive of the target
resource, such that making a retrieval request on that other resource
might result in a representation that is useful to recipients without
implying that it represents the original target resource. Note that
answers to the questions of what can be represented, what
representations are adequate, and what might be a useful description
are outside the scope of HTTP.

Except for responses to a HEAD request, the representation of a 303
response ought to contain a short hypertext note with a hyperlink to
the same URI reference provided in the Location header field.

15.4.5. 304 Not Modified

The 304 (Not Modified) status code indicates that a conditional GET
or HEAD request has been received and would have resulted in a 200
(OK) response if it were not for the fact that the condition
evaluated to false. In other words, there is no need for the server
to transfer a representation of the target resource because the
request indicates that the client, which made the request
conditional, already has a valid representation; the server is
therefore redirecting the client to make use of that stored
representation as if it were the content of a 200 (OK) response.

The server generating a 304 response MUST generate any of the
following header fields that would have been sent in a 200 (OK)
response to the same request:

- Content-Location, Date, ETag, and Vary

- Cache-Control and Expires (see [CACHING])

Since the goal of a 304 response is to minimize information transfer
when the recipient already has one or more cached representations, a
sender SHOULD NOT generate representation metadata other than the
above listed fields unless said metadata exists for the purpose of
guiding cache updates (e.g., Last-Modified might be useful if the
response does not have an ETag field).

Requirements on a cache that receives a 304 response are defined in
Section 4.3.4 of [CACHING]. If the conditional request originated
with an outbound client, such as a user agent with its own cache
sending a conditional GET to a shared proxy, then the proxy SHOULD
forward the 304 response to that client.

A 304 response is terminated by the end of the header section; it
cannot contain content or trailers.

15.4.6. 305 Use Proxy

The 305 (Use Proxy) status code was defined in a previous version of
this specification and is now deprecated (Appendix B of [RFC7231]).

15.4.7. 306 (Unused)

The 306 status code was defined in a previous version of this
specification, is no longer used, and the code is reserved.

15.4.8. 307 Temporary Redirect

The 307 (Temporary Redirect) status code indicates that the target
resource resides temporarily under a different URI and the user agent
MUST NOT change the request method if it performs an automatic
redirection to that URI. Since the redirection can change over time,
the client ought to continue using the original target URI for future
requests.

The server SHOULD generate a Location header field in the response
containing a URI reference for the different URI. The user agent MAY
use the Location field value for automatic redirection. The server's
response content usually contains a short hypertext note with a
hyperlink to the different URI(s).

15.4.9. 308 Permanent Redirect

The 308 (Permanent Redirect) status code indicates that the target
resource has been assigned a new permanent URI and any future
references to this resource ought to use one of the enclosed URIs.
The server is suggesting that a user agent with link-editing
capability can permanently replace references to the target URI with
one of the new references sent by the server. However, this
suggestion is usually ignored unless the user agent is actively
editing references (e.g., engaged in authoring content), the
connection is secured, and the origin server is a trusted authority
for the content being edited.

The server SHOULD generate a Location header field in the response
containing a preferred URI reference for the new permanent URI. The
user agent MAY use the Location field value for automatic
redirection. The server's response content usually contains a short
hypertext note with a hyperlink to the new URI(s).

A 308 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

      |  *Note:* This status code is much younger (June 2014) than its
      |  sibling codes and thus might not be recognized everywhere.  See
      |  Section 4 of [RFC7538] for deployment considerations.

15.5. Client Error 4xx

The 4xx (Client Error) class of status code indicates that the client
seems to have erred. Except when responding to a HEAD request, the
server SHOULD send a representation containing an explanation of the
error situation, and whether it is a temporary or permanent
condition. These status codes are applicable to any request method.
User agents SHOULD display any included representation to the user.

15.5.1. 400 Bad Request

The 400 (Bad Request) status code indicates that the server cannot or
will not process the request due to something that is perceived to be
a client error (e.g., malformed request syntax, invalid request
message framing, or deceptive request routing).

15.5.2. 401 Unauthorized

The 401 (Unauthorized) status code indicates that the request has not
been applied because it lacks valid authentication credentials for
the target resource. The server generating a 401 response MUST send
a WWW-Authenticate header field (Section 11.6.1) containing at least
one challenge applicable to the target resource.

If the request included authentication credentials, then the 401
response indicates that authorization has been refused for those
credentials. The user agent MAY repeat the request with a new or
replaced Authorization header field (Section 11.6.2). If the 401
response contains the same challenge as the prior response, and the
user agent has already attempted authentication at least once, then
the user agent SHOULD present the enclosed representation to the
user, since it usually contains relevant diagnostic information.

15.5.3. 402 Payment Required

The 402 (Payment Required) status code is reserved for future use.

15.5.4. 403 Forbidden

The 403 (Forbidden) status code indicates that the server understood
the request but refuses to fulfill it. A server that wishes to make
public why the request has been forbidden can describe that reason in
the response content (if any).

If authentication credentials were provided in the request, the
server considers them insufficient to grant access. The client
SHOULD NOT automatically repeat the request with the same
credentials. The client MAY repeat the request with new or different
credentials. However, a request might be forbidden for reasons
unrelated to the credentials.

An origin server that wishes to "hide" the current existence of a
forbidden target resource MAY instead respond with a status code of
404 (Not Found).

15.5.5. 404 Not Found

The 404 (Not Found) status code indicates that the origin server did
not find a current representation for the target resource or is not
willing to disclose that one exists. A 404 status code does not
indicate whether this lack of representation is temporary or
permanent; the 410 (Gone) status code is preferred over 404 if the
origin server knows, presumably through some configurable means, that
the condition is likely to be permanent.

A 404 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

15.5.6. 405 Method Not Allowed

The 405 (Method Not Allowed) status code indicates that the method
received in the request-line is known by the origin server but not
supported by the target resource. The origin server MUST generate an
Allow header field in a 405 response containing a list of the target
resource's currently supported methods.

A 405 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

15.5.7. 406 Not Acceptable

The 406 (Not Acceptable) status code indicates that the target
resource does not have a current representation that would be
acceptable to the user agent, according to the proactive negotiation
header fields received in the request (Section 12.1), and the server
is unwilling to supply a default representation.

The server SHOULD generate content containing a list of available
representation characteristics and corresponding resource identifiers
from which the user or user agent can choose the one most
appropriate. A user agent MAY automatically select the most
appropriate choice from that list. However, this specification does
not define any standard for such automatic selection, as described in
Section 15.4.1.

15.5.8. 407 Proxy Authentication Required

The 407 (Proxy Authentication Required) status code is similar to 401
(Unauthorized), but it indicates that the client needs to
authenticate itself in order to use a proxy for this request. The
proxy MUST send a Proxy-Authenticate header field (Section 11.7.1)
containing a challenge applicable to that proxy for the request. The
client MAY repeat the request with a new or replaced
Proxy-Authorization header field (Section 11.7.2).

15.5.9. 408 Request Timeout

The 408 (Request Timeout) status code indicates that the server did
not receive a complete request message within the time that it was
prepared to wait.

If the client has an outstanding request in transit, it MAY repeat
that request. If the current connection is not usable (e.g., as it
would be in HTTP/1.1 because request delimitation is lost), a new
connection will be used.

15.5.10. 409 Conflict

The 409 (Conflict) status code indicates that the request could not
be completed due to a conflict with the current state of the target
resource. This code is used in situations where the user might be
able to resolve the conflict and resubmit the request. The server
SHOULD generate content that includes enough information for a user
to recognize the source of the conflict.

Conflicts are most likely to occur in response to a PUT request. For
example, if versioning were being used and the representation being
PUT included changes to a resource that conflict with those made by
an earlier (third-party) request, the origin server might use a 409
response to indicate that it can't complete the request. In this
case, the response representation would likely contain information
useful for merging the differences based on the revision history.

15.5.11. 410 Gone

The 410 (Gone) status code indicates that access to the target
resource is no longer available at the origin server and that this
condition is likely to be permanent. If the origin server does not
know, or has no facility to determine, whether or not the condition
is permanent, the status code 404 (Not Found) ought to be used
instead.

The 410 response is primarily intended to assist the task of web
maintenance by notifying the recipient that the resource is
intentionally unavailable and that the server owners desire that
remote links to that resource be removed. Such an event is common
for limited-time, promotional services and for resources belonging to
individuals no longer associated with the origin server's site. It
is not necessary to mark all permanently unavailable resources as
"gone" or to keep the mark for any length of time -- that is left to
the discretion of the server owner.

A 410 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

15.5.12. 411 Length Required

The 411 (Length Required) status code indicates that the server
refuses to accept the request without a defined Content-Length
(Section 8.6). The client MAY repeat the request if it adds a valid
Content-Length header field containing the length of the request
content.

15.5.13. 412 Precondition Failed

The 412 (Precondition Failed) status code indicates that one or more
conditions given in the request header fields evaluated to false when
tested on the server (Section 13). This response status code allows
the client to place preconditions on the current resource state (its
current representations and metadata) and, thus, prevent the request
method from being applied if the target resource is in an unexpected
state.

15.5.14. 413 Content Too Large

The 413 (Content Too Large) status code indicates that the server is
refusing to process a request because the request content is larger
than the server is willing or able to process. The server MAY
terminate the request, if the protocol version in use allows it;
otherwise, the server MAY close the connection.

If the condition is temporary, the server SHOULD generate a
Retry-After header field to indicate that it is temporary and after
what time the client MAY try again.

15.5.15. 414 URI Too Long

The 414 (URI Too Long) status code indicates that the server is
refusing to service the request because the target URI is longer than
the server is willing to interpret. This rare condition is only
likely to occur when a client has improperly converted a POST request
to a GET request with long query information, when the client has
descended into an infinite loop of redirection (e.g., a redirected
URI prefix that points to a suffix of itself) or when the server is
under attack by a client attempting to exploit potential security
holes.

A 414 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

15.5.16. 415 Unsupported Media Type

The 415 (Unsupported Media Type) status code indicates that the
origin server is refusing to service the request because the content
is in a format not supported by this method on the target resource.

The format problem might be due to the request's indicated
Content-Type or Content-Encoding, or as a result of inspecting the
data directly.

If the problem was caused by an unsupported content coding, the
Accept-Encoding response header field (Section 12.5.3) ought to be
used to indicate which (if any) content codings would have been
accepted in the request.

On the other hand, if the cause was an unsupported media type, the
Accept response header field (Section 12.5.1) can be used to indicate
which media types would have been accepted in the request.

15.5.17. 416 Range Not Satisfiable

The 416 (Range Not Satisfiable) status code indicates that the set of
ranges in the request's Range header field (Section 14.2) has been
rejected either because none of the requested ranges are satisfiable
or because the client has requested an excessive number of small or
overlapping ranges (a potential denial of service attack).

Each range unit defines what is required for its own range sets to be
satisfiable. For example, Section 14.1.2 defines what makes a bytes
range set satisfiable.

A server that generates a 416 response to a byte-range request SHOULD
generate a Content-Range header field specifying the current length
of the selected representation (Section 14.4).

For example:

HTTP/1.1 416 Range Not Satisfiable
Date: Fri, 20 Jan 2012 15:41:54 GMT
Content-Range: bytes \*/47022

      |  *Note:* Because servers are free to ignore Range, many
      |  implementations will respond with the entire selected
      |  representation in a 200 (OK) response.  That is partly because
      |  most clients are prepared to receive a 200 (OK) to complete the
      |  task (albeit less efficiently) and partly because clients might
      |  not stop making an invalid range request until they have
      |  received a complete representation.  Thus, clients cannot
      |  depend on receiving a 416 (Range Not Satisfiable) response even
      |  when it is most appropriate.

15.5.18. 417 Expectation Failed

The 417 (Expectation Failed) status code indicates that the
expectation given in the request's Expect header field
(Section 10.1.1) could not be met by at least one of the inbound
servers.

15.5.19. 418 (Unused)

[RFC2324] was an April 1 RFC that lampooned the various ways HTTP was
abused; one such abuse was the definition of an application-specific
418 status code, which has been deployed as a joke often enough for
the code to be unusable for any future use.

Therefore, the 418 status code is reserved in the IANA HTTP Status
Code Registry. This indicates that the status code cannot be
assigned to other applications currently. If future circumstances
require its use (e.g., exhaustion of 4NN status codes), it can be re-
assigned to another use.

15.5.20. 421 Misdirected Request

The 421 (Misdirected Request) status code indicates that the request
was directed at a server that is unable or unwilling to produce an
authoritative response for the target URI. An origin server (or
gateway acting on behalf of the origin server) sends 421 to reject a
target URI that does not match an origin for which the server has
been configured (Section 4.3.1) or does not match the connection
context over which the request was received (Section 7.4).

A client that receives a 421 (Misdirected Request) response MAY retry
the request, whether or not the request method is idempotent, over a
different connection, such as a fresh connection specific to the
target resource's origin, or via an alternative service [ALTSVC].

A proxy MUST NOT generate a 421 response.

15.5.21. 422 Unprocessable Content

The 422 (Unprocessable Content) status code indicates that the server
understands the content type of the request content (hence a 415
(Unsupported Media Type) status code is inappropriate), and the
syntax of the request content is correct, but it was unable to
process the contained instructions. For example, this status code
can be sent if an XML request content contains well-formed (i.e.,
syntactically correct), but semantically erroneous XML instructions.

15.5.22. 426 Upgrade Required

The 426 (Upgrade Required) status code indicates that the server
refuses to perform the request using the current protocol but might
be willing to do so after the client upgrades to a different
protocol. The server MUST send an Upgrade header field in a 426
response to indicate the required protocol(s) (Section 7.8).

Example:

HTTP/1.1 426 Upgrade Required
Upgrade: HTTP/3.0
Connection: Upgrade
Content-Length: 53
Content-Type: text/plain

This service requires use of the HTTP/3.0 protocol.

15.6. Server Error 5xx

The 5xx (Server Error) class of status code indicates that the server
is aware that it has erred or is incapable of performing the
requested method. Except when responding to a HEAD request, the
server SHOULD send a representation containing an explanation of the
error situation, and whether it is a temporary or permanent
condition. A user agent SHOULD display any included representation
to the user. These status codes are applicable to any request
method.

15.6.1. 500 Internal Server Error

The 500 (Internal Server Error) status code indicates that the server
encountered an unexpected condition that prevented it from fulfilling
the request.

15.6.2. 501 Not Implemented

The 501 (Not Implemented) status code indicates that the server does
not support the functionality required to fulfill the request. This
is the appropriate response when the server does not recognize the
request method and is not capable of supporting it for any resource.

A 501 response is heuristically cacheable; i.e., unless otherwise
indicated by the method definition or explicit cache controls (see
Section 4.2.2 of [CACHING]).

15.6.3. 502 Bad Gateway

The 502 (Bad Gateway) status code indicates that the server, while
acting as a gateway or proxy, received an invalid response from an
inbound server it accessed while attempting to fulfill the request.

15.6.4. 503 Service Unavailable

The 503 (Service Unavailable) status code indicates that the server
is currently unable to handle the request due to a temporary overload
or scheduled maintenance, which will likely be alleviated after some
delay. The server MAY send a Retry-After header field
(Section 10.2.3) to suggest an appropriate amount of time for the
client to wait before retrying the request.

      |  *Note:* The existence of the 503 status code does not imply
      |  that a server has to use it when becoming overloaded.  Some
      |  servers might simply refuse the connection.

15.6.5. 504 Gateway Timeout

The 504 (Gateway Timeout) status code indicates that the server,
while acting as a gateway or proxy, did not receive a timely response
from an upstream server it needed to access in order to complete the
request.

15.6.6. 505 HTTP Version Not Supported

The 505 (HTTP Version Not Supported) status code indicates that the
server does not support, or refuses to support, the major version of
HTTP that was used in the request message. The server is indicating
that it is unable or unwilling to complete the request using the same
major version as the client, as described in Section 2.5, other than
with this error message. The server SHOULD generate a representation
for the 505 response that describes why that version is not supported
and what other protocols are supported by that server.

16. Extending HTTP

HTTP defines a number of generic extension points that can be used to
introduce capabilities to the protocol without introducing a new
version, including methods, status codes, field names, and further
extensibility points within defined fields, such as authentication
schemes and cache directives (see Cache-Control extensions in
Section 5.2.3 of [CACHING]). Because the semantics of HTTP are not
versioned, these extension points are persistent; the version of the
protocol in use does not affect their semantics.

Version-independent extensions are discouraged from depending on or
interacting with the specific version of the protocol in use. When
this is unavoidable, careful consideration needs to be given to how
the extension can interoperate across versions.

Additionally, specific versions of HTTP might have their own
extensibility points, such as transfer codings in HTTP/1.1
(Section 6.1 of [HTTP/1.1]) and HTTP/2 SETTINGS or frame types
([HTTP/2]). These extension points are specific to the version of
the protocol they occur within.

Version-specific extensions cannot override or modify the semantics
of a version-independent mechanism or extension point (like a method
or header field) without explicitly being allowed by that protocol
element. For example, the CONNECT method (Section 9.3.6) allows
this.

These guidelines assure that the protocol operates correctly and
predictably, even when parts of the path implement different versions
of HTTP.

16.1. Method Extensibility

16.1.1. Method Registry

The "Hypertext Transfer Protocol (HTTP) Method Registry", maintained
by IANA at <https://www.iana.org/assignments/http-methods>, registers
method names.

HTTP method registrations MUST include the following fields:

- Method Name (see Section 9)

- Safe ("yes" or "no", see Section 9.2.1)

- Idempotent ("yes" or "no", see Section 9.2.2)

- Pointer to specification text

Values to be added to this namespace require IETF Review (see
[RFC8126], Section 4.8).

16.1.2. Considerations for New Methods

Standardized methods are generic; that is, they are potentially
applicable to any resource, not just one particular media type, kind
of resource, or application. As such, it is preferred that new
methods be registered in a document that isn't specific to a single
application or data format, since orthogonal technologies deserve
orthogonal specification.

Since message parsing (Section 6) needs to be independent of method
semantics (aside from responses to HEAD), definitions of new methods
cannot change the parsing algorithm or prohibit the presence of
content on either the request or the response message. Definitions
of new methods can specify that only a zero-length content is allowed
by requiring a Content-Length header field with a value of "0".

Likewise, new methods cannot use the special host:port and asterisk
forms of request target that are allowed for CONNECT and OPTIONS,
respectively (Section 7.1). A full URI in absolute form is needed
for the target URI, which means either the request target needs to be
sent in absolute form or the target URI will be reconstructed from
the request context in the same way it is for other methods.

A new method definition needs to indicate whether it is safe
(Section 9.2.1), idempotent (Section 9.2.2), cacheable
(Section 9.2.3), what semantics are to be associated with the request
content (if any), and what refinements the method makes to header
field or status code semantics. If the new method is cacheable, its
definition ought to describe how, and under what conditions, a cache
can store a response and use it to satisfy a subsequent request. The
new method ought to describe whether it can be made conditional
(Section 13.1) and, if so, how a server responds when the condition
is false. Likewise, if the new method might have some use for
partial response semantics (Section 14.2), it ought to document this,
too.

      |  *Note:* Avoid defining a method name that starts with "M-",
      |  since that prefix might be misinterpreted as having the
      |  semantics assigned to it by [RFC2774].

16.2. Status Code Extensibility

16.2.1. Status Code Registry

The "Hypertext Transfer Protocol (HTTP) Status Code Registry",
maintained by IANA at <https://www.iana.org/assignments/http-status-
codes>, registers status code numbers.

A registration MUST include the following fields:

- Status Code (3 digits)

- Short Description

- Pointer to specification text

Values to be added to the HTTP status code namespace require IETF
Review (see [RFC8126], Section 4.8).

16.2.2. Considerations for New Status Codes

When it is necessary to express semantics for a response that are not
defined by current status codes, a new status code can be registered.
Status codes are generic; they are potentially applicable to any
resource, not just one particular media type, kind of resource, or
application of HTTP. As such, it is preferred that new status codes
be registered in a document that isn't specific to a single
application.

New status codes are required to fall under one of the categories
defined in Section 15. To allow existing parsers to process the
response message, new status codes cannot disallow content, although
they can mandate a zero-length content.

Proposals for new status codes that are not yet widely deployed ought
to avoid allocating a specific number for the code until there is
clear consensus that it will be registered; instead, early drafts can
use a notation such as "4NN", or "3N0" .. "3N9", to indicate the
class of the proposed status code(s) without consuming a number
prematurely.

The definition of a new status code ought to explain the request
conditions that would cause a response containing that status code
(e.g., combinations of request header fields and/or method(s)) along
with any dependencies on response header fields (e.g., what fields
are required, what fields can modify the semantics, and what field
semantics are further refined when used with the new status code).

By default, a status code applies only to the request corresponding
to the response it occurs within. If a status code applies to a
larger scope of applicability -- for example, all requests to the
resource in question or all requests to a server -- this must be
explicitly specified. When doing so, it should be noted that not all
clients can be expected to consistently apply a larger scope because
they might not understand the new status code.

The definition of a new final status code ought to specify whether or
not it is heuristically cacheable. Note that any response with a
final status code can be cached if the response has explicit
freshness information. A status code defined as heuristically
cacheable is allowed to be cached without explicit freshness
information. Likewise, the definition of a status code can place
constraints upon cache behavior if the must-understand cache
directive is used. See [CACHING] for more information.

Finally, the definition of a new status code ought to indicate
whether the content has any implied association with an identified
resource (Section 6.4.2).

16.3. Field Extensibility

HTTP's most widely used extensibility point is the definition of new
header and trailer fields.

New fields can be defined such that, when they are understood by a
recipient, they override or enhance the interpretation of previously
defined fields, define preconditions on request evaluation, or refine
the meaning of responses.

However, defining a field doesn't guarantee its deployment or
recognition by recipients. Most fields are designed with the
expectation that a recipient can safely ignore (but forward
downstream) any field not recognized. In other cases, the sender's
ability to understand a given field might be indicated by its prior
communication, perhaps in the protocol version or fields that it sent
in prior messages, or its use of a specific media type. Likewise,
direct inspection of support might be possible through an OPTIONS
request or by interacting with a defined well-known URI [RFC8615] if
such inspection is defined along with the field being introduced.

16.3.1. Field Name Registry

The "Hypertext Transfer Protocol (HTTP) Field Name Registry" defines
the namespace for HTTP field names.

Any party can request registration of an HTTP field. See
Section 16.3.2 for considerations to take into account when creating
a new HTTP field.

The "Hypertext Transfer Protocol (HTTP) Field Name Registry" is
located at <https://www.iana.org/assignments/http-fields/>.
Registration requests can be made by following the instructions
located there or by sending an email to the "ietf-http-wg@w3.org"
mailing list.

Field names are registered on the advice of a designated expert
(appointed by the IESG or their delegate). Fields with the status
'permanent' are Specification Required ([RFC8126], Section 4.6).

Registration requests consist of the following information:

Field name:
The requested field name. It MUST conform to the field-name
syntax defined in Section 5.1, and it SHOULD be restricted to just
letters, digits, and hyphen ('-') characters, with the first
character being a letter.

Status:
"permanent", "provisional", "deprecated", or "obsoleted".

Specification document(s):
Reference to the document that specifies the field, preferably
including a URI that can be used to retrieve a copy of the
document. Optional but encouraged for provisional registrations.
An indication of the relevant section(s) can also be included, but
is not required.

And optionally:

Comments: Additional information, such as about reserved entries.

The expert(s) can define additional fields to be collected in the
registry, in consultation with the community.

Standards-defined names have a status of "permanent". Other names
can also be registered as permanent if the expert(s) finds that they
are in use, in consultation with the community. Other names should
be registered as "provisional".

Provisional entries can be removed by the expert(s) if -- in
consultation with the community -- the expert(s) find that they are
not in use. The expert(s) can change a provisional entry's status to
permanent at any time.

Note that names can be registered by third parties (including the
expert(s)) if the expert(s) determines that an unregistered name is
widely deployed and not likely to be registered in a timely manner
otherwise.

16.3.2. Considerations for New Fields

HTTP header and trailer fields are a widely used extension point for
the protocol. While they can be used in an ad hoc fashion, fields
that are intended for wider use need to be carefully documented to
ensure interoperability.

In particular, authors of specifications defining new fields are
advised to consider and, where appropriate, document the following
aspects:

- Under what conditions the field can be used; e.g., only in
  responses or requests, in all messages, only on responses to a
  particular request method, etc.

- Whether the field semantics are further refined by their context,
  such as their use with certain request methods or status codes.

- The scope of applicability for the information conveyed. By
  default, fields apply only to the message they are associated
  with, but some response fields are designed to apply to all
  representations of a resource, the resource itself, or an even
  broader scope. Specifications that expand the scope of a response
  field will need to carefully consider issues such as content
  negotiation, the time period of applicability, and (in some cases)
  multi-tenant server deployments.

- Under what conditions intermediaries are allowed to insert,
  delete, or modify the field's value.

- If the field is allowable in trailers; by default, it will not be
  (see Section 6.5.1).

- Whether it is appropriate or even required to list the field name
  in the Connection header field (i.e., if the field is to be hop-
  by-hop; see Section 7.6.1).

- Whether the field introduces any additional security
  considerations, such as disclosure of privacy-related data.

Request header fields have additional considerations that need to be
documented if the default behavior is not appropriate:

- If it is appropriate to list the field name in a Vary response
  header field (e.g., when the request header field is used by an
  origin server's content selection algorithm; see Section 12.5.5).

- If the field is intended to be stored when received in a PUT
  request (see Section 9.3.4).

- If the field ought to be removed when automatically redirecting a
  request due to security concerns (see Section 15.4).

  16.3.2.1. Considerations for New Field Names

Authors of specifications defining new fields are advised to choose a
short but descriptive field name. Short names avoid needless data
transmission; descriptive names avoid confusion and "squatting" on
names that might have broader uses.

To that end, limited-use fields (such as a header confined to a
single application or use case) are encouraged to use a name that
includes that use (or an abbreviation) as a prefix; for example, if
the Foo Application needs a Description field, it might use "Foo-
Desc"; "Description" is too generic, and "Foo-Description" is
needlessly long.

While the field-name syntax is defined to allow any token character,
in practice some implementations place limits on the characters they
accept in field-names. To be interoperable, new field names SHOULD
constrain themselves to alphanumeric characters, "-", and ".", and
SHOULD begin with a letter. For example, the underscore ("\_")
character can be problematic when passed through non-HTTP gateway
interfaces (see Section 17.10).

Field names ought not be prefixed with "X-"; see [BCP178] for further
information.

Other prefixes are sometimes used in HTTP field names; for example,
"Accept-" is used in many content negotiation headers, and "Content-"
is used as explained in Section 6.4. These prefixes are only an aid
to recognizing the purpose of a field and do not trigger automatic
processing.

16.3.2.2. Considerations for New Field Values

A major task in the definition of a new HTTP field is the
specification of the field value syntax: what senders should
generate, and how recipients should infer semantics from what is
received.

Authors are encouraged (but not required) to use either the ABNF
rules in this specification or those in [RFC8941] to define the
syntax of new field values.

Authors are advised to carefully consider how the combination of
multiple field lines will impact them (see Section 5.3). Because
senders might erroneously send multiple values, and both
intermediaries and HTTP libraries can perform combination
automatically, this applies to all field values -- even when only a
single value is anticipated.

Therefore, authors are advised to delimit or encode values that
contain commas (e.g., with the quoted-string rule of Section 5.6.4,
the String data type of [RFC8941], or a field-specific encoding).
This ensures that commas within field data are not confused with the
commas that delimit a list value.

For example, the Content-Type field value only allows commas inside
quoted strings, which can be reliably parsed even when multiple
values are present. The Location field value provides a counter-
example that should not be emulated: because URIs can include commas,
it is not possible to reliably distinguish between a single value
that includes a comma from two values.

Authors of fields with a singleton value (see Section 5.5) are
additionally advised to document how to treat messages where the
multiple members are present (a sensible default would be to ignore
the field, but this might not always be the right choice).

16.4. Authentication Scheme Extensibility

16.4.1. Authentication Scheme Registry

The "Hypertext Transfer Protocol (HTTP) Authentication Scheme
Registry" defines the namespace for the authentication schemes in
challenges and credentials. It is maintained at
<https://www.iana.org/assignments/http-authschemes>.

Registrations MUST include the following fields:

- Authentication Scheme Name

- Pointer to specification text

- Notes (optional)

Values to be added to this namespace require IETF Review (see
[RFC8126], Section 4.8).

16.4.2. Considerations for New Authentication Schemes

There are certain aspects of the HTTP Authentication framework that
put constraints on how new authentication schemes can work:

- HTTP authentication is presumed to be stateless: all of the
  information necessary to authenticate a request MUST be provided
  in the request, rather than be dependent on the server remembering
  prior requests. Authentication based on, or bound to, the
  underlying connection is outside the scope of this specification
  and inherently flawed unless steps are taken to ensure that the
  connection cannot be used by any party other than the
  authenticated user (see Section 3.3).

- The authentication parameter "realm" is reserved for defining
  protection spaces as described in Section 11.5. New schemes MUST
  NOT use it in a way incompatible with that definition.

- The "token68" notation was introduced for compatibility with
  existing authentication schemes and can only be used once per
  challenge or credential. Thus, new schemes ought to use the auth-
  param syntax instead, because otherwise future extensions will be
  impossible.

- The parsing of challenges and credentials is defined by this
  specification and cannot be modified by new authentication
  schemes. When the auth-param syntax is used, all parameters ought
  to support both token and quoted-string syntax, and syntactical
  constraints ought to be defined on the field value after parsing
  (i.e., quoted-string processing). This is necessary so that
  recipients can use a generic parser that applies to all
  authentication schemes.

  _Note:_ The fact that the value syntax for the "realm" parameter
  is restricted to quoted-string was a bad design choice not to be
  repeated for new parameters.

- Definitions of new schemes ought to define the treatment of
  unknown extension parameters. In general, a "must-ignore" rule is
  preferable to a "must-understand" rule, because otherwise it will
  be hard to introduce new parameters in the presence of legacy
  recipients. Furthermore, it's good to describe the policy for
  defining new parameters (such as "update the specification" or
  "use this registry").

- Authentication schemes need to document whether they are usable in
  origin-server authentication (i.e., using WWW-Authenticate), and/
  or proxy authentication (i.e., using Proxy-Authenticate).

- The credentials carried in an Authorization header field are
  specific to the user agent and, therefore, have the same effect on
  HTTP caches as the "private" cache response directive
  (Section 5.2.2.7 of [CACHING]), within the scope of the request in
  which they appear.

  Therefore, new authentication schemes that choose not to carry
  credentials in the Authorization header field (e.g., using a newly
  defined header field) will need to explicitly disallow caching, by
  mandating the use of cache response directives (e.g., "private").

- Schemes using Authentication-Info, Proxy-Authentication-Info, or
  any other authentication related response header field need to
  consider and document the related security considerations (see
  Section 17.16.4).

  16.5. Range Unit Extensibility

  16.5.1. Range Unit Registry

The "HTTP Range Unit Registry" defines the namespace for the range
unit names and refers to their corresponding specifications. It is
maintained at <https://www.iana.org/assignments/http-parameters>.

Registration of an HTTP Range Unit MUST include the following fields:

- Name

- Description

- Pointer to specification text

Values to be added to this namespace require IETF Review (see
[RFC8126], Section 4.8).

16.5.2. Considerations for New Range Units

Other range units, such as format-specific boundaries like pages,
sections, records, rows, or time, are potentially usable in HTTP for
application-specific purposes, but are not commonly used in practice.
Implementors of alternative range units ought to consider how they
would work with content codings and general-purpose intermediaries.

16.6. Content Coding Extensibility

16.6.1. Content Coding Registry

The "HTTP Content Coding Registry", maintained by IANA at
<https://www.iana.org/assignments/http-parameters/>, registers
content-coding names.

Content coding registrations MUST include the following fields:

- Name

- Description

- Pointer to specification text

Names of content codings MUST NOT overlap with names of transfer
codings (per the "HTTP Transfer Coding Registry" located at
<https://www.iana.org/assignments/http-parameters/>) unless the
encoding transformation is identical (as is the case for the
compression codings defined in Section 8.4.1).

Values to be added to this namespace require IETF Review (see
Section 4.8 of [RFC8126]) and MUST conform to the purpose of content
coding defined in Section 8.4.1.

16.6.2. Considerations for New Content Codings

New content codings ought to be self-descriptive whenever possible,
with optional parameters discoverable within the coding format
itself, rather than rely on external metadata that might be lost
during transit.

16.7. Upgrade Token Registry

The "Hypertext Transfer Protocol (HTTP) Upgrade Token Registry"
defines the namespace for protocol-name tokens used to identify
protocols in the Upgrade header field. The registry is maintained at
<https://www.iana.org/assignments/http-upgrade-tokens>.

Each registered protocol name is associated with contact information
and an optional set of specifications that details how the connection
will be processed after it has been upgraded.

Registrations happen on a "First Come First Served" basis (see
Section 4.4 of [RFC8126]) and are subject to the following rules:

1.  A protocol-name token, once registered, stays registered forever.

2.  A protocol-name token is case-insensitive and registered with the
    preferred case to be generated by senders.

3.  The registration MUST name a responsible party for the
    registration.

4.  The registration MUST name a point of contact.

5.  The registration MAY name a set of specifications associated with
    that token. Such specifications need not be publicly available.

6.  The registration SHOULD name a set of expected "protocol-version"
    tokens associated with that token at the time of registration.

7.  The responsible party MAY change the registration at any time.
    The IANA will keep a record of all such changes, and make them
    available upon request.

8.  The IESG MAY reassign responsibility for a protocol token. This
    will normally only be used in the case when a responsible party
    cannot be contacted.

9.  Security Considerations

This section is meant to inform developers, information providers,
and users of known security concerns relevant to HTTP semantics and
its use for transferring information over the Internet.
Considerations related to caching are discussed in Section 7 of
[CACHING], and considerations related to HTTP/1.1 message syntax and
parsing are discussed in Section 11 of [HTTP/1.1].

The list of considerations below is not exhaustive. Most security
concerns related to HTTP semantics are about securing server-side
applications (code behind the HTTP interface), securing user agent
processing of content received via HTTP, or secure use of the
Internet in general, rather than security of the protocol. The
security considerations for URIs, which are fundamental to HTTP
operation, are discussed in Section 7 of [URI]. Various
organizations maintain topical information and links to current
research on Web application security (e.g., [OWASP]).

17.1. Establishing Authority

HTTP relies on the notion of an "authoritative response": a response
that has been determined by (or at the direction of) the origin
server identified within the target URI to be the most appropriate
response for that request given the state of the target resource at
the time of response message origination.

When a registered name is used in the authority component, the "http"
URI scheme (Section 4.2.1) relies on the user's local name resolution
service to determine where it can find authoritative responses. This
means that any attack on a user's network host table, cached names,
or name resolution libraries becomes an avenue for attack on
establishing authority for "http" URIs. Likewise, the user's choice
of server for Domain Name Service (DNS), and the hierarchy of servers
from which it obtains resolution results, could impact the
authenticity of address mappings; DNS Security Extensions (DNSSEC,
[RFC4033]) are one way to improve authenticity, as are the various
mechanisms for making DNS requests over more secure transfer
protocols.

Furthermore, after an IP address is obtained, establishing authority
for an "http" URI is vulnerable to attacks on Internet Protocol
routing.

The "https" scheme (Section 4.2.2) is intended to prevent (or at
least reveal) many of these potential attacks on establishing
authority, provided that the negotiated connection is secured and the
client properly verifies that the communicating server's identity
matches the target URI's authority component (Section 4.3.4).
Correctly implementing such verification can be difficult (see
[Georgiev]).

Authority for a given origin server can be delegated through protocol
extensions; for example, [ALTSVC]. Likewise, the set of servers for
which a connection is considered authoritative can be changed with a
protocol extension like [RFC8336].

Providing a response from a non-authoritative source, such as a
shared proxy cache, is often useful to improve performance and
availability, but only to the extent that the source can be trusted
or the distrusted response can be safely used.

Unfortunately, communicating authority to users can be difficult.
For example, "phishing" is an attack on the user's perception of
authority, where that perception can be misled by presenting similar
branding in hypertext, possibly aided by userinfo obfuscating the
authority component (see Section 4.2.1). User agents can reduce the
impact of phishing attacks by enabling users to easily inspect a
target URI prior to making an action, by prominently distinguishing
(or rejecting) userinfo when present, and by not sending stored
credentials and cookies when the referring document is from an
unknown or untrusted source.

17.2. Risks of Intermediaries

HTTP intermediaries are inherently situated for on-path attacks.
Compromise of the systems on which the intermediaries run can result
in serious security and privacy problems. Intermediaries might have
access to security-related information, personal information about
individual users and organizations, and proprietary information
belonging to users and content providers. A compromised
intermediary, or an intermediary implemented or configured without
regard to security and privacy considerations, might be used in the
commission of a wide range of potential attacks.

Intermediaries that contain a shared cache are especially vulnerable
to cache poisoning attacks, as described in Section 7 of [CACHING].

Implementers need to consider the privacy and security implications
of their design and coding decisions, and of the configuration
options they provide to operators (especially the default
configuration).

Intermediaries are no more trustworthy than the people and policies
under which they operate; HTTP cannot solve this problem.

17.3. Attacks Based on File and Path Names

Origin servers frequently make use of their local file system to
manage the mapping from target URI to resource representations. Most
file systems are not designed to protect against malicious file or
path names. Therefore, an origin server needs to avoid accessing
names that have a special significance to the system when mapping the
target resource to files, folders, or directories.

For example, UNIX, Microsoft Windows, and other operating systems use
".." as a path component to indicate a directory level above the
current one, and they use specially named paths or file names to send
data to system devices. Similar naming conventions might exist
within other types of storage systems. Likewise, local storage
systems have an annoying tendency to prefer user-friendliness over
security when handling invalid or unexpected characters,
recomposition of decomposed characters, and case-normalization of
case-insensitive names.

Attacks based on such special names tend to focus on either denial-
of-service (e.g., telling the server to read from a COM port) or
disclosure of configuration and source files that are not meant to be
served.

17.4. Attacks Based on Command, Code, or Query Injection

Origin servers often use parameters within the URI as a means of
identifying system services, selecting database entries, or choosing
a data source. However, data received in a request cannot be
trusted. An attacker could construct any of the request data
elements (method, target URI, header fields, or content) to contain
data that might be misinterpreted as a command, code, or query when
passed through a command invocation, language interpreter, or
database interface.

For example, SQL injection is a common attack wherein additional
query language is inserted within some part of the target URI or
header fields (e.g., Host, Referer, etc.). If the received data is
used directly within a SELECT statement, the query language might be
interpreted as a database command instead of a simple string value.
This type of implementation vulnerability is extremely common, in
spite of being easy to prevent.

In general, resource implementations ought to avoid use of request
data in contexts that are processed or interpreted as instructions.
Parameters ought to be compared to fixed strings and acted upon as a
result of that comparison, rather than passed through an interface
that is not prepared for untrusted data. Received data that isn't
based on fixed parameters ought to be carefully filtered or encoded
to avoid being misinterpreted.

Similar considerations apply to request data when it is stored and
later processed, such as within log files, monitoring tools, or when
included within a data format that allows embedded scripts.

17.5. Attacks via Protocol Element Length

Because HTTP uses mostly textual, character-delimited fields, parsers
are often vulnerable to attacks based on sending very long (or very
slow) streams of data, particularly where an implementation is
expecting a protocol element with no predefined length (Section 2.3).

To promote interoperability, specific recommendations are made for
minimum size limits on fields (Section 5.4). These are minimum
recommendations, chosen to be supportable even by implementations
with limited resources; it is expected that most implementations will
choose substantially higher limits.

A server can reject a message that has a target URI that is too long
(Section 15.5.15) or request content that is too large
(Section 15.5.14). Additional status codes related to capacity
limits have been defined by extensions to HTTP [RFC6585].

Recipients ought to carefully limit the extent to which they process
other protocol elements, including (but not limited to) request
methods, response status phrases, field names, numeric values, and
chunk lengths. Failure to limit such processing can result in
arbitrary code execution due to buffer or arithmetic overflows, and
increased vulnerability to denial-of-service attacks.

17.6. Attacks Using Shared-Dictionary Compression

Some attacks on encrypted protocols use the differences in size
created by dynamic compression to reveal confidential information;
for example, [BREACH]. These attacks rely on creating a redundancy
between attacker-controlled content and the confidential information,
such that a dynamic compression algorithm using the same dictionary
for both content will compress more efficiently when the attacker-
controlled content matches parts of the confidential content.

HTTP messages can be compressed in a number of ways, including using
TLS compression, content codings, transfer codings, and other
extension or version-specific mechanisms.

The most effective mitigation for this risk is to disable compression
on sensitive data, or to strictly separate sensitive data from
attacker-controlled data so that they cannot share the same
compression dictionary. With careful design, a compression scheme
can be designed in a way that is not considered exploitable in
limited use cases, such as HPACK ([HPACK]).

17.7. Disclosure of Personal Information

Clients are often privy to large amounts of personal information,
including both information provided by the user to interact with
resources (e.g., the user's name, location, mail address, passwords,
encryption keys, etc.) and information about the user's browsing
activity over time (e.g., history, bookmarks, etc.). Implementations
need to prevent unintentional disclosure of personal information.

17.8. Privacy of Server Log Information

A server is in the position to save personal data about a user's
requests over time, which might identify their reading patterns or
subjects of interest. In particular, log information gathered at an
intermediary often contains a history of user agent interaction,
across a multitude of sites, that can be traced to individual users.

HTTP log information is confidential in nature; its handling is often
constrained by laws and regulations. Log information needs to be
securely stored and appropriate guidelines followed for its analysis.
Anonymization of personal information within individual entries
helps, but it is generally not sufficient to prevent real log traces
from being re-identified based on correlation with other access
characteristics. As such, access traces that are keyed to a specific
client are unsafe to publish even if the key is pseudonymous.

To minimize the risk of theft or accidental publication, log
information ought to be purged of personally identifiable
information, including user identifiers, IP addresses, and user-
provided query parameters, as soon as that information is no longer
necessary to support operational needs for security, auditing, or
fraud control.

17.9. Disclosure of Sensitive Information in URIs

URIs are intended to be shared, not secured, even when they identify
secure resources. URIs are often shown on displays, added to
templates when a page is printed, and stored in a variety of
unprotected bookmark lists. Many servers, proxies, and user agents
log or display the target URI in places where it might be visible to
third parties. It is therefore unwise to include information within
a URI that is sensitive, personally identifiable, or a risk to
disclose.

When an application uses client-side mechanisms to construct a target
URI out of user-provided information, such as the query fields of a
form using GET, potentially sensitive data might be provided that
would not be appropriate for disclosure within a URI. POST is often
preferred in such cases because it usually doesn't construct a URI;
instead, POST of a form transmits the potentially sensitive data in
the request content. However, this hinders caching and uses an
unsafe method for what would otherwise be a safe request.
Alternative workarounds include transforming the user-provided data
prior to constructing the URI or filtering the data to only include
common values that are not sensitive. Likewise, redirecting the
result of a query to a different (server-generated) URI can remove
potentially sensitive data from later links and provide a cacheable
response for later reuse.

Since the Referer header field tells a target site about the context
that resulted in a request, it has the potential to reveal
information about the user's immediate browsing history and any
personal information that might be found in the referring resource's
URI. Limitations on the Referer header field are described in
Section 10.1.3 to address some of its security considerations.

17.10. Application Handling of Field Names

Servers often use non-HTTP gateway interfaces and frameworks to
process a received request and produce content for the response. For
historical reasons, such interfaces often pass received field names
as external variable names, using a name mapping suitable for
environment variables.

For example, the Common Gateway Interface (CGI) mapping of protocol-
specific meta-variables, defined by Section 4.1.18 of [RFC3875], is
applied to received header fields that do not correspond to one of
CGI's standard variables; the mapping consists of prepending "HTTP*"
to each name and changing all instances of hyphen ("-") to underscore
("*"). This same mapping has been inherited by many other
application frameworks in order to simplify moving applications from
one platform to the next.

In CGI, a received Content-Length field would be passed as the meta-
variable "CONTENT_LENGTH" with a string value matching the received
field's value. In contrast, a received "Content_Length" header field
would be passed as the protocol-specific meta-variable
"HTTP_CONTENT_LENGTH", which might lead to some confusion if an
application mistakenly reads the protocol-specific meta-variable
instead of the default one. (This historical practice is why
Section 16.3.2.1 discourages the creation of new field names that
contain an underscore.)

Unfortunately, mapping field names to different interface names can
lead to security vulnerabilities if the mapping is incomplete or
ambiguous. For example, if an attacker were to send a field named
"Transfer_Encoding", a naive interface might map that to the same
variable name as the "Transfer-Encoding" field, resulting in a
potential request smuggling vulnerability (Section 11.2 of
[HTTP/1.1]).

To mitigate the associated risks, implementations that perform such
mappings are advised to make the mapping unambiguous and complete for
the full range of potential octets received as a name (including
those that are discouraged or forbidden by the HTTP grammar). For
example, a field with an unusual name character might result in the
request being blocked, the specific field being removed, or the name
being passed with a different prefix to distinguish it from other
fields.

17.11. Disclosure of Fragment after Redirects

Although fragment identifiers used within URI references are not sent
in requests, implementers ought to be aware that they will be visible
to the user agent and any extensions or scripts running as a result
of the response. In particular, when a redirect occurs and the
original request's fragment identifier is inherited by the new
reference in Location (Section 10.2.2), this might have the effect of
disclosing one site's fragment to another site. If the first site
uses personal information in fragments, it ought to ensure that
redirects to other sites include a (possibly empty) fragment
component in order to block that inheritance.

17.12. Disclosure of Product Information

The User-Agent (Section 10.1.5), Via (Section 7.6.3), and Server
(Section 10.2.4) header fields often reveal information about the
respective sender's software systems. In theory, this can make it
easier for an attacker to exploit known security holes; in practice,
attackers tend to try all potential holes regardless of the apparent
software versions being used.

Proxies that serve as a portal through a network firewall ought to
take special precautions regarding the transfer of header information
that might identify hosts behind the firewall. The Via header field
allows intermediaries to replace sensitive machine names with
pseudonyms.

17.13. Browser Fingerprinting

Browser fingerprinting is a set of techniques for identifying a
specific user agent over time through its unique set of
characteristics. These characteristics might include information
related to how it uses the underlying transport protocol, feature
capabilities, and scripting environment, though of particular
interest here is the set of unique characteristics that might be
communicated via HTTP. Fingerprinting is considered a privacy
concern because it enables tracking of a user agent's behavior over
time ([Bujlow]) without the corresponding controls that the user
might have over other forms of data collection (e.g., cookies). Many
general-purpose user agents (i.e., Web browsers) have taken steps to
reduce their fingerprints.

There are a number of request header fields that might reveal
information to servers that is sufficiently unique to enable
fingerprinting. The From header field is the most obvious, though it
is expected that From will only be sent when self-identification is
desired by the user. Likewise, Cookie header fields are deliberately
designed to enable re-identification, so fingerprinting concerns only
apply to situations where cookies are disabled or restricted by the
user agent's configuration.

The User-Agent header field might contain enough information to
uniquely identify a specific device, usually when combined with other
characteristics, particularly if the user agent sends excessive
details about the user's system or extensions. However, the source
of unique information that is least expected by users is proactive
negotiation (Section 12.1), including the Accept, Accept-Charset,
Accept-Encoding, and Accept-Language header fields.

In addition to the fingerprinting concern, detailed use of the
Accept-Language header field can reveal information the user might
consider to be of a private nature. For example, understanding a
given language set might be strongly correlated to membership in a
particular ethnic group. An approach that limits such loss of
privacy would be for a user agent to omit the sending of Accept-
Language except for sites that have been explicitly permitted,
perhaps via interaction after detecting a Vary header field that
indicates language negotiation might be useful.

In environments where proxies are used to enhance privacy, user
agents ought to be conservative in sending proactive negotiation
header fields. General-purpose user agents that provide a high
degree of header field configurability ought to inform users about
the loss of privacy that might result if too much detail is provided.
As an extreme privacy measure, proxies could filter the proactive
negotiation header fields in relayed requests.

17.14. Validator Retention

The validators defined by this specification are not intended to
ensure the validity of a representation, guard against malicious
changes, or detect on-path attacks. At best, they enable more
efficient cache updates and optimistic concurrent writes when all
participants are behaving nicely. At worst, the conditions will fail
and the client will receive a response that is no more harmful than
an HTTP exchange without conditional requests.

An entity tag can be abused in ways that create privacy risks. For
example, a site might deliberately construct a semantically invalid
entity tag that is unique to the user or user agent, send it in a
cacheable response with a long freshness time, and then read that
entity tag in later conditional requests as a means of re-identifying
that user or user agent. Such an identifying tag would become a
persistent identifier for as long as the user agent retained the
original cache entry. User agents that cache representations ought
to ensure that the cache is cleared or replaced whenever the user
performs privacy-maintaining actions, such as clearing stored cookies
or changing to a private browsing mode.

17.15. Denial-of-Service Attacks Using Range

Unconstrained multiple range requests are susceptible to denial-of-
service attacks because the effort required to request many
overlapping ranges of the same data is tiny compared to the time,
memory, and bandwidth consumed by attempting to serve the requested
data in many parts. Servers ought to ignore, coalesce, or reject
egregious range requests, such as requests for more than two
overlapping ranges or for many small ranges in a single set,
particularly when the ranges are requested out of order for no
apparent reason. Multipart range requests are not designed to
support random access.

17.16. Authentication Considerations

Everything about the topic of HTTP authentication is a security
consideration, so the list of considerations below is not exhaustive.
Furthermore, it is limited to security considerations regarding the
authentication framework, in general, rather than discussing all of
the potential considerations for specific authentication schemes
(which ought to be documented in the specifications that define those
schemes). Various organizations maintain topical information and
links to current research on Web application security (e.g.,
[OWASP]), including common pitfalls for implementing and using the
authentication schemes found in practice.

17.16.1. Confidentiality of Credentials

The HTTP authentication framework does not define a single mechanism
for maintaining the confidentiality of credentials; instead, each
authentication scheme defines how the credentials are encoded prior
to transmission. While this provides flexibility for the development
of future authentication schemes, it is inadequate for the protection
of existing schemes that provide no confidentiality on their own, or
that do not sufficiently protect against replay attacks.
Furthermore, if the server expects credentials that are specific to
each individual user, the exchange of those credentials will have the
effect of identifying that user even if the content within
credentials remains confidential.

HTTP depends on the security properties of the underlying transport-
or session-level connection to provide confidential transmission of
fields. Services that depend on individual user authentication
require a secured connection prior to exchanging credentials
(Section 4.2.2).

17.16.2. Credentials and Idle Clients

Existing HTTP clients and user agents typically retain authentication
information indefinitely. HTTP does not provide a mechanism for the
origin server to direct clients to discard these cached credentials,
since the protocol has no awareness of how credentials are obtained
or managed by the user agent. The mechanisms for expiring or
revoking credentials can be specified as part of an authentication
scheme definition.

Circumstances under which credential caching can interfere with the
application's security model include but are not limited to:

- Clients that have been idle for an extended period, following
  which the server might wish to cause the client to re-prompt the
  user for credentials.

- Applications that include a session termination indication (such
  as a "logout" or "commit" button on a page) after which the server
  side of the application "knows" that there is no further reason
  for the client to retain the credentials.

User agents that cache credentials are encouraged to provide a
readily accessible mechanism for discarding cached credentials under
user control.

17.16.3. Protection Spaces

Authentication schemes that solely rely on the "realm" mechanism for
establishing a protection space will expose credentials to all
resources on an origin server. Clients that have successfully made
authenticated requests with a resource can use the same
authentication credentials for other resources on the same origin
server. This makes it possible for a different resource to harvest
authentication credentials for other resources.

This is of particular concern when an origin server hosts resources
for multiple parties under the same origin (Section 11.5). Possible
mitigation strategies include restricting direct access to
authentication credentials (i.e., not making the content of the
Authorization request header field available), and separating
protection spaces by using a different host name (or port number) for
each party.

17.16.4. Additional Response Fields

Adding information to responses that are sent over an unencrypted
channel can affect security and privacy. The presence of the
Authentication-Info and Proxy-Authentication-Info header fields alone
indicates that HTTP authentication is in use. Additional information
could be exposed by the contents of the authentication-scheme
specific parameters; this will have to be considered in the
definitions of these schemes.

18. IANA Considerations

The change controller for the following registrations is: "IETF
(iesg@ietf.org) - Internet Engineering Task Force".

18.1. URI Scheme Registration

IANA has updated the "Uniform Resource Identifier (URI) Schemes"
registry [BCP35] at <https://www.iana.org/assignments/uri-schemes/>
with the permanent schemes listed in Table 2 in Section 4.2.

18.2. Method Registration

IANA has updated the "Hypertext Transfer Protocol (HTTP) Method
Registry" at <https://www.iana.org/assignments/http-methods> with the
registration procedure of Section 16.1.1 and the method names
summarized in the following table.

                 +=========+======+============+=========+
                 | Method  | Safe | Idempotent | Section |
                 +=========+======+============+=========+
                 | CONNECT | no   | no         | 9.3.6   |
                 +---------+------+------------+---------+
                 | DELETE  | no   | yes        | 9.3.5   |
                 +---------+------+------------+---------+
                 | GET     | yes  | yes        | 9.3.1   |
                 +---------+------+------------+---------+
                 | HEAD    | yes  | yes        | 9.3.2   |
                 +---------+------+------------+---------+
                 | OPTIONS | yes  | yes        | 9.3.7   |
                 +---------+------+------------+---------+
                 | POST    | no   | no         | 9.3.3   |
                 +---------+------+------------+---------+
                 | PUT     | no   | yes        | 9.3.4   |
                 +---------+------+------------+---------+
                 | TRACE   | yes  | yes        | 9.3.8   |
                 +---------+------+------------+---------+
                 | *       | no   | no         | 18.2    |
                 +---------+------+------------+---------+

                                  Table 7

The method name "_" is reserved because using "_" as a method name
would conflict with its usage as a wildcard in some fields (e.g.,
"Access-Control-Request-Method").

18.3. Status Code Registration

IANA has updated the "Hypertext Transfer Protocol (HTTP) Status Code
Registry" at <https://www.iana.org/assignments/http-status-codes>
with the registration procedure of Section 16.2.1 and the status code
values summarized in the following table.

            +=======+===============================+=========+
            | Value | Description                   | Section |
            +=======+===============================+=========+
            | 100   | Continue                      | 15.2.1  |
            +-------+-------------------------------+---------+
            | 101   | Switching Protocols           | 15.2.2  |
            +-------+-------------------------------+---------+
            | 200   | OK                            | 15.3.1  |
            +-------+-------------------------------+---------+
            | 201   | Created                       | 15.3.2  |
            +-------+-------------------------------+---------+
            | 202   | Accepted                      | 15.3.3  |
            +-------+-------------------------------+---------+
            | 203   | Non-Authoritative Information | 15.3.4  |
            +-------+-------------------------------+---------+
            | 204   | No Content                    | 15.3.5  |
            +-------+-------------------------------+---------+
            | 205   | Reset Content                 | 15.3.6  |
            +-------+-------------------------------+---------+
            | 206   | Partial Content               | 15.3.7  |
            +-------+-------------------------------+---------+
            | 300   | Multiple Choices              | 15.4.1  |
            +-------+-------------------------------+---------+
            | 301   | Moved Permanently             | 15.4.2  |
            +-------+-------------------------------+---------+
            | 302   | Found                         | 15.4.3  |
            +-------+-------------------------------+---------+
            | 303   | See Other                     | 15.4.4  |
            +-------+-------------------------------+---------+
            | 304   | Not Modified                  | 15.4.5  |
            +-------+-------------------------------+---------+
            | 305   | Use Proxy                     | 15.4.6  |
            +-------+-------------------------------+---------+
            | 306   | (Unused)                      | 15.4.7  |
            +-------+-------------------------------+---------+
            | 307   | Temporary Redirect            | 15.4.8  |
            +-------+-------------------------------+---------+
            | 308   | Permanent Redirect            | 15.4.9  |
            +-------+-------------------------------+---------+
            | 400   | Bad Request                   | 15.5.1  |
            +-------+-------------------------------+---------+
            | 401   | Unauthorized                  | 15.5.2  |
            +-------+-------------------------------+---------+
            | 402   | Payment Required              | 15.5.3  |
            +-------+-------------------------------+---------+
            | 403   | Forbidden                     | 15.5.4  |
            +-------+-------------------------------+---------+
            | 404   | Not Found                     | 15.5.5  |
            +-------+-------------------------------+---------+
            | 405   | Method Not Allowed            | 15.5.6  |
            +-------+-------------------------------+---------+
            | 406   | Not Acceptable                | 15.5.7  |
            +-------+-------------------------------+---------+
            | 407   | Proxy Authentication Required | 15.5.8  |
            +-------+-------------------------------+---------+
            | 408   | Request Timeout               | 15.5.9  |
            +-------+-------------------------------+---------+
            | 409   | Conflict                      | 15.5.10 |
            +-------+-------------------------------+---------+
            | 410   | Gone                          | 15.5.11 |
            +-------+-------------------------------+---------+
            | 411   | Length Required               | 15.5.12 |
            +-------+-------------------------------+---------+
            | 412   | Precondition Failed           | 15.5.13 |
            +-------+-------------------------------+---------+
            | 413   | Content Too Large             | 15.5.14 |
            +-------+-------------------------------+---------+
            | 414   | URI Too Long                  | 15.5.15 |
            +-------+-------------------------------+---------+
            | 415   | Unsupported Media Type        | 15.5.16 |
            +-------+-------------------------------+---------+
            | 416   | Range Not Satisfiable         | 15.5.17 |
            +-------+-------------------------------+---------+
            | 417   | Expectation Failed            | 15.5.18 |
            +-------+-------------------------------+---------+
            | 418   | (Unused)                      | 15.5.19 |
            +-------+-------------------------------+---------+
            | 421   | Misdirected Request           | 15.5.20 |
            +-------+-------------------------------+---------+
            | 422   | Unprocessable Content         | 15.5.21 |
            +-------+-------------------------------+---------+
            | 426   | Upgrade Required              | 15.5.22 |
            +-------+-------------------------------+---------+
            | 500   | Internal Server Error         | 15.6.1  |
            +-------+-------------------------------+---------+
            | 501   | Not Implemented               | 15.6.2  |
            +-------+-------------------------------+---------+
            | 502   | Bad Gateway                   | 15.6.3  |
            +-------+-------------------------------+---------+
            | 503   | Service Unavailable           | 15.6.4  |
            +-------+-------------------------------+---------+
            | 504   | Gateway Timeout               | 15.6.5  |
            +-------+-------------------------------+---------+
            | 505   | HTTP Version Not Supported    | 15.6.6  |
            +-------+-------------------------------+---------+

                                  Table 8

18.4. Field Name Registration

This specification updates the HTTP-related aspects of the existing
registration procedures for message header fields defined in
[RFC3864]. It replaces the old procedures as they relate to HTTP by
defining a new registration procedure and moving HTTP field
definitions into a separate registry.

IANA has created a new registry titled "Hypertext Transfer Protocol
(HTTP) Field Name Registry" as outlined in Section 16.3.1.

IANA has moved all entries in the "Permanent Message Header Field
Names" and "Provisional Message Header Field Names" registries (see
<https://www.iana.org/assignments/message-headers/>) with the
protocol 'http' to this registry and has applied the following
changes:

1.  The 'Applicable Protocol' field has been omitted.

2.  Entries that had a status of 'standard', 'experimental',
    'reserved', or 'informational' have been made to have a status of
    'permanent'.

3.  Provisional entries without a status have been made to have a
    status of 'provisional'.

4.  Permanent entries without a status (after confirmation that the
    registration document did not define one) have been made to have
    a status of 'provisional'. The expert(s) can choose to update
    the entries' status if there is evidence that another is more
    appropriate.

IANA has annotated the "Permanent Message Header Field Names" and
"Provisional Message Header Field Names" registries with the
following note to indicate that HTTP field name registrations have
moved:

      |  *Note*
      |
      |  HTTP field name registrations have been moved to
      |  [https://www.iana.org/assignments/http-fields] per [RFC9110].

IANA has updated the "Hypertext Transfer Protocol (HTTP) Field Name
Registry" with the field names listed in the following table.

+===========================+============+=========+============+
| Field Name | Status | Section | Comments |
+===========================+============+=========+============+
| Accept | permanent | 12.5.1 | |
+---------------------------+------------+---------+------------+
| Accept-Charset | deprecated | 12.5.2 | |
+---------------------------+------------+---------+------------+
| Accept-Encoding | permanent | 12.5.3 | |
+---------------------------+------------+---------+------------+
| Accept-Language | permanent | 12.5.4 | |
+---------------------------+------------+---------+------------+
| Accept-Ranges | permanent | 14.3 | |
+---------------------------+------------+---------+------------+
| Allow | permanent | 10.2.1 | |
+---------------------------+------------+---------+------------+
| Authentication-Info | permanent | 11.6.3 | |
+---------------------------+------------+---------+------------+
| Authorization | permanent | 11.6.2 | |
+---------------------------+------------+---------+------------+
| Connection | permanent | 7.6.1 | |
+---------------------------+------------+---------+------------+
| Content-Encoding | permanent | 8.4 | |
+---------------------------+------------+---------+------------+
| Content-Language | permanent | 8.5 | |
+---------------------------+------------+---------+------------+
| Content-Length | permanent | 8.6 | |
+---------------------------+------------+---------+------------+
| Content-Location | permanent | 8.7 | |
+---------------------------+------------+---------+------------+
| Content-Range | permanent | 14.4 | |
+---------------------------+------------+---------+------------+
| Content-Type | permanent | 8.3 | |
+---------------------------+------------+---------+------------+
| Date | permanent | 6.6.1 | |
+---------------------------+------------+---------+------------+
| ETag | permanent | 8.8.3 | |
+---------------------------+------------+---------+------------+
| Expect | permanent | 10.1.1 | |
+---------------------------+------------+---------+------------+
| From | permanent | 10.1.2 | |
+---------------------------+------------+---------+------------+
| Host | permanent | 7.2 | |
+---------------------------+------------+---------+------------+
| If-Match | permanent | 13.1.1 | |
+---------------------------+------------+---------+------------+
| If-Modified-Since | permanent | 13.1.3 | |
+---------------------------+------------+---------+------------+
| If-None-Match | permanent | 13.1.2 | |
+---------------------------+------------+---------+------------+
| If-Range | permanent | 13.1.5 | |
+---------------------------+------------+---------+------------+
| If-Unmodified-Since | permanent | 13.1.4 | |
+---------------------------+------------+---------+------------+
| Last-Modified | permanent | 8.8.2 | |
+---------------------------+------------+---------+------------+
| Location | permanent | 10.2.2 | |
+---------------------------+------------+---------+------------+
| Max-Forwards | permanent | 7.6.2 | |
+---------------------------+------------+---------+------------+
| Proxy-Authenticate | permanent | 11.7.1 | |
+---------------------------+------------+---------+------------+
| Proxy-Authentication-Info | permanent | 11.7.3 | |
+---------------------------+------------+---------+------------+
| Proxy-Authorization | permanent | 11.7.2 | |
+---------------------------+------------+---------+------------+
| Range | permanent | 14.2 | |
+---------------------------+------------+---------+------------+
| Referer | permanent | 10.1.3 | |
+---------------------------+------------+---------+------------+
| Retry-After | permanent | 10.2.3 | |
+---------------------------+------------+---------+------------+
| Server | permanent | 10.2.4 | |
+---------------------------+------------+---------+------------+
| TE | permanent | 10.1.4 | |
+---------------------------+------------+---------+------------+
| Trailer | permanent | 6.6.2 | |
+---------------------------+------------+---------+------------+
| Upgrade | permanent | 7.8 | |
+---------------------------+------------+---------+------------+
| User-Agent | permanent | 10.1.5 | |
+---------------------------+------------+---------+------------+
| Vary | permanent | 12.5.5 | |
+---------------------------+------------+---------+------------+
| Via | permanent | 7.6.3 | |
+---------------------------+------------+---------+------------+
| WWW-Authenticate | permanent | 11.6.1 | |
+---------------------------+------------+---------+------------+
| \* | permanent | 12.5.5 | (reserved) |
+---------------------------+------------+---------+------------+

                                Table 9

The field name "\*" is reserved because using that name as an HTTP
header field might conflict with its special semantics in the Vary
header field (Section 12.5.5).

IANA has updated the "Content-MD5" entry in the new registry to have
a status of 'obsoleted' with references to Section 14.15 of [RFC2616]
(for the definition of the header field) and Appendix B of [RFC7231]
(which removed the field definition from the updated specification).

18.5. Authentication Scheme Registration

IANA has updated the "Hypertext Transfer Protocol (HTTP)
Authentication Scheme Registry" at <https://www.iana.org/assignments/
http-authschemes> with the registration procedure of Section 16.4.1.
No authentication schemes are defined in this document.

18.6. Content Coding Registration

IANA has updated the "HTTP Content Coding Registry" at
<https://www.iana.org/assignments/http-parameters/> with the
registration procedure of Section 16.6.1 and the content coding names
summarized in the table below.

+============+===========================================+=========+
| Name | Description | Section |
+============+===========================================+=========+
| compress | UNIX "compress" data format [Welch] | 8.4.1.1 |
+------------+-------------------------------------------+---------+
| deflate | "deflate" compressed data ([RFC1951]) | 8.4.1.2 |
| | inside the "zlib" data format ([RFC1950]) | |
+------------+-------------------------------------------+---------+
| gzip | GZIP file format [RFC1952] | 8.4.1.3 |
+------------+-------------------------------------------+---------+
| identity | Reserved | 12.5.3 |
+------------+-------------------------------------------+---------+
| x-compress | Deprecated (alias for compress) | 8.4.1.1 |
+------------+-------------------------------------------+---------+
| x-gzip | Deprecated (alias for gzip) | 8.4.1.3 |
+------------+-------------------------------------------+---------+

                                 Table 10

18.7. Range Unit Registration

IANA has updated the "HTTP Range Unit Registry" at
<https://www.iana.org/assignments/http-parameters/> with the
registration procedure of Section 16.5.1 and the range unit names
summarized in the table below.

+=================+==================================+=========+
| Range Unit Name | Description | Section |
+=================+==================================+=========+
| bytes | a range of octets | 14.1.2 |
+-----------------+----------------------------------+---------+
| none | reserved as keyword to indicate | 14.3 |
| | range requests are not supported | |
+-----------------+----------------------------------+---------+

                               Table 11

18.8. Media Type Registration

IANA has updated the "Media Types" registry at
<https://www.iana.org/assignments/media-types> with the registration
information in Section 14.6 for the media type "multipart/
byteranges".

IANA has updated the registry note about "q" parameters with a link
to Section 12.5.1 of this document.

18.9. Port Registration

IANA has updated the "Service Name and Transport Protocol Port Number
Registry" at <https://www.iana.org/assignments/service-names-port-
numbers/> for the services on ports 80 and 443 that use UDP or TCP
to:

1.  use this document as "Reference", and

2.  when currently unspecified, set "Assignee" to "IESG" and
    "Contact" to "IETF_Chair".

18.10. Upgrade Token Registration

IANA has updated the "Hypertext Transfer Protocol (HTTP) Upgrade
Token Registry" at <https://www.iana.org/assignments/http-upgrade-
tokens> with the registration procedure described in Section 16.7 and
the upgrade token names summarized in the following table.

+======+===================+=========================+=========+
| Name | Description | Expected Version Tokens | Section |
+======+===================+=========================+=========+
| HTTP | Hypertext | any DIGIT.DIGIT (e.g., | 2.5 |
| | Transfer Protocol | "2.0") | |
+------+-------------------+-------------------------+---------+

                               Table 12

## 19. 레퍼런스

19.1. Normative References

[CACHING] Fielding, R., Ed., Nottingham, M., Ed., and J. Reschke,
Ed., "HTTP Caching", STD 98, RFC 9111,
DOI 10.17487/RFC9111, June 2022,
<https://www.rfc-editor.org/info/rfc9111>.

[RFC1950] Deutsch, P. and J-L. Gailly, "ZLIB Compressed Data Format
Specification version 3.3", RFC 1950,
DOI 10.17487/RFC1950, May 1996,
<https://www.rfc-editor.org/info/rfc1950>.

[RFC1951] Deutsch, P., "DEFLATE Compressed Data Format Specification
version 1.3", RFC 1951, DOI 10.17487/RFC1951, May 1996,
<https://www.rfc-editor.org/info/rfc1951>.

[RFC1952] Deutsch, P., "GZIP file format specification version 4.3",
RFC 1952, DOI 10.17487/RFC1952, May 1996,
<https://www.rfc-editor.org/info/rfc1952>.

[RFC2046] Freed, N. and N. Borenstein, "Multipurpose Internet Mail
Extensions (MIME) Part Two: Media Types", RFC 2046,
DOI 10.17487/RFC2046, November 1996,
<https://www.rfc-editor.org/info/rfc2046>.

[RFC2119] Bradner, S., "Key words for use in RFCs to Indicate
Requirement Levels", BCP 14, RFC 2119,
DOI 10.17487/RFC2119, March 1997,
<https://www.rfc-editor.org/info/rfc2119>.

[RFC4647] Phillips, A., Ed. and M. Davis, Ed., "Matching of Language
Tags", BCP 47, RFC 4647, DOI 10.17487/RFC4647, September
2006, <https://www.rfc-editor.org/info/rfc4647>.

[RFC4648] Josefsson, S., "The Base16, Base32, and Base64 Data
Encodings", RFC 4648, DOI 10.17487/RFC4648, October 2006,
<https://www.rfc-editor.org/info/rfc4648>.

[RFC5234] Crocker, D., Ed. and P. Overell, "Augmented BNF for Syntax
Specifications: ABNF", STD 68, RFC 5234,
DOI 10.17487/RFC5234, January 2008,
<https://www.rfc-editor.org/info/rfc5234>.

[RFC5280] Cooper, D., Santesson, S., Farrell, S., Boeyen, S.,
Housley, R., and W. Polk, "Internet X.509 Public Key
Infrastructure Certificate and Certificate Revocation List
(CRL) Profile", RFC 5280, DOI 10.17487/RFC5280, May 2008,
<https://www.rfc-editor.org/info/rfc5280>.

[RFC5322] Resnick, P., Ed., "Internet Message Format", RFC 5322,
DOI 10.17487/RFC5322, October 2008,
<https://www.rfc-editor.org/info/rfc5322>.

[RFC5646] Phillips, A., Ed. and M. Davis, Ed., "Tags for Identifying
Languages", BCP 47, RFC 5646, DOI 10.17487/RFC5646,
September 2009, <https://www.rfc-editor.org/info/rfc5646>.

[RFC6125] Saint-Andre, P. and J. Hodges, "Representation and
Verification of Domain-Based Application Service Identity
within Internet Public Key Infrastructure Using X.509
(PKIX) Certificates in the Context of Transport Layer
Security (TLS)", RFC 6125, DOI 10.17487/RFC6125, March
2011, <https://www.rfc-editor.org/info/rfc6125>.

[RFC6365] Hoffman, P. and J. Klensin, "Terminology Used in
Internationalization in the IETF", BCP 166, RFC 6365,
DOI 10.17487/RFC6365, September 2011,
<https://www.rfc-editor.org/info/rfc6365>.

[RFC7405] Kyzivat, P., "Case-Sensitive String Support in ABNF",
RFC 7405, DOI 10.17487/RFC7405, December 2014,
<https://www.rfc-editor.org/info/rfc7405>.

[RFC8174] Leiba, B., "Ambiguity of Uppercase vs Lowercase in RFC
2119 Key Words", BCP 14, RFC 8174, DOI 10.17487/RFC8174,
May 2017, <https://www.rfc-editor.org/info/rfc8174>.

[TCP] Postel, J., "Transmission Control Protocol", STD 7,
RFC 793, DOI 10.17487/RFC0793, September 1981,
<https://www.rfc-editor.org/info/rfc793>.

[TLS13] Rescorla, E., "The Transport Layer Security (TLS) Protocol
Version 1.3", RFC 8446, DOI 10.17487/RFC8446, August 2018,
<https://www.rfc-editor.org/info/rfc8446>.

[URI] Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform
Resource Identifier (URI): Generic Syntax", STD 66,
RFC 3986, DOI 10.17487/RFC3986, January 2005,
<https://www.rfc-editor.org/info/rfc3986>.

[USASCII] American National Standards Institute, "Coded Character
Set -- 7-bit American Standard Code for Information
Interchange", ANSI X3.4, 1986.

[Welch] Welch, T., "A Technique for High-Performance Data
Compression", IEEE Computer 17(6),
DOI 10.1109/MC.1984.1659158, June 1984,
<https://ieeexplore.ieee.org/document/1659158/>.

19.2. Informative References

[ALTSVC] Nottingham, M., McManus, P., and J. Reschke, "HTTP
Alternative Services", RFC 7838, DOI 10.17487/RFC7838,
April 2016, <https://www.rfc-editor.org/info/rfc7838>.

[BCP13] Freed, N. and J. Klensin, "Multipurpose Internet Mail
Extensions (MIME) Part Four: Registration Procedures",
BCP 13, RFC 4289, December 2005.

              Freed, N., Klensin, J., and T. Hansen, "Media Type
              Specifications and Registration Procedures", BCP 13,
              RFC 6838, January 2013.

              <https://www.rfc-editor.org/info/bcp13>

[BCP178] Saint-Andre, P., Crocker, D., and M. Nottingham,
"Deprecating the "X-" Prefix and Similar Constructs in
Application Protocols", BCP 178, RFC 6648, June 2012.

              <https://www.rfc-editor.org/info/bcp178>

[BCP35] Thaler, D., Ed., Hansen, T., and T. Hardie, "Guidelines
and Registration Procedures for URI Schemes", BCP 35,
RFC 7595, June 2015.

              <https://www.rfc-editor.org/info/bcp35>

[BREACH] Gluck, Y., Harris, N., and A. Prado, "BREACH: Reviving the
CRIME Attack", July 2013,
<http://breachattack.com/resources/
BREACH%20-%20SSL,%20gone%20in%2030%20seconds.pdf>.

[Bujlow] Bujlow, T., Carela-Español, V., Solé-Pareta, J., and P.
Barlet-Ros, "A Survey on Web Tracking: Mechanisms,
Implications, and Defenses", In Proceedings of the IEEE
105(8), DOI 10.1109/JPROC.2016.2637878, August 2017,
<https://doi.org/10.1109/JPROC.2016.2637878>.

[COOKIE] Barth, A., "HTTP State Management Mechanism", RFC 6265,
DOI 10.17487/RFC6265, April 2011,
<https://www.rfc-editor.org/info/rfc6265>.

[Err1912] RFC Errata, Erratum ID 1912, RFC 2978,
<https://www.rfc-editor.org/errata/eid1912>.

[Err5433] RFC Errata, Erratum ID 5433, RFC 2978,
<https://www.rfc-editor.org/errata/eid5433>.

[Georgiev] Georgiev, M., Iyengar, S., Jana, S., Anubhai, R., Boneh,
D., and V. Shmatikov, "The Most Dangerous Code in the
World: Validating SSL Certificates in Non-Browser
Software", In Proceedings of the 2012 ACM Conference on
Computer and Communications Security (CCS '12), pp. 38-49,
DOI 10.1145/2382196.2382204, October 2012,
<https://doi.org/10.1145/2382196.2382204>.

[HPACK] Peon, R. and H. Ruellan, "HPACK: Header Compression for
HTTP/2", RFC 7541, DOI 10.17487/RFC7541, May 2015,
<https://www.rfc-editor.org/info/rfc7541>.

[HTTP/1.0] Berners-Lee, T., Fielding, R., and H. Frystyk, "Hypertext
Transfer Protocol -- HTTP/1.0", RFC 1945,
DOI 10.17487/RFC1945, May 1996,
<https://www.rfc-editor.org/info/rfc1945>.

[HTTP/1.1] Fielding, R., Ed., Nottingham, M., Ed., and J. Reschke,
Ed., "HTTP/1.1", STD 99, RFC 9112, DOI 10.17487/RFC9112,
June 2022, <https://www.rfc-editor.org/info/rfc9112>.

[HTTP/2] Thomson, M., Ed. and C. Benfield, Ed., "HTTP/2", RFC 9113,
DOI 10.17487/RFC9113, June 2022,
<https://www.rfc-editor.org/info/rfc9113>.

[HTTP/3] Bishop, M., Ed., "HTTP/3", RFC 9114, DOI 10.17487/RFC9114,
June 2022, <https://www.rfc-editor.org/info/rfc9114>.

[ISO-8859-1]
International Organization for Standardization,
"Information technology -- 8-bit single-byte coded graphic
character sets -- Part 1: Latin alphabet No. 1", ISO/
IEC 8859-1:1998, 1998.

[Kri2001] Kristol, D., "HTTP Cookies: Standards, Privacy, and
Politics", ACM Transactions on Internet Technology 1(2),
November 2001, <http://arxiv.org/abs/cs.SE/0105018>.

[OWASP] The Open Web Application Security Project,
<https://www.owasp.org/>.

[REST] Fielding, R.T., "Architectural Styles and the Design of
Network-based Software Architectures", Doctoral
Dissertation, University of California, Irvine, September
2000, <https://roy.gbiv.com/pubs/dissertation/top.htm>.

[RFC1919] Chatel, M., "Classical versus Transparent IP Proxies",
RFC 1919, DOI 10.17487/RFC1919, March 1996,
<https://www.rfc-editor.org/info/rfc1919>.

[RFC2047] Moore, K., "MIME (Multipurpose Internet Mail Extensions)
Part Three: Message Header Extensions for Non-ASCII Text",
RFC 2047, DOI 10.17487/RFC2047, November 1996,
<https://www.rfc-editor.org/info/rfc2047>.

[RFC2068] Fielding, R., Gettys, J., Mogul, J., Frystyk, H., and T.
Berners-Lee, "Hypertext Transfer Protocol -- HTTP/1.1",
RFC 2068, DOI 10.17487/RFC2068, January 1997,
<https://www.rfc-editor.org/info/rfc2068>.

[RFC2145] Mogul, J. C., Fielding, R., Gettys, J., and H. Frystyk,
"Use and Interpretation of HTTP Version Numbers",
RFC 2145, DOI 10.17487/RFC2145, May 1997,
<https://www.rfc-editor.org/info/rfc2145>.

[RFC2295] Holtman, K. and A. Mutz, "Transparent Content Negotiation
in HTTP", RFC 2295, DOI 10.17487/RFC2295, March 1998,
<https://www.rfc-editor.org/info/rfc2295>.

[RFC2324] Masinter, L., "Hyper Text Coffee Pot Control Protocol
(HTCPCP/1.0)", RFC 2324, DOI 10.17487/RFC2324, 1 April
1998, <https://www.rfc-editor.org/info/rfc2324>.

[RFC2557] Palme, J., Hopmann, A., and N. Shelness, "MIME
Encapsulation of Aggregate Documents, such as HTML
(MHTML)", RFC 2557, DOI 10.17487/RFC2557, March 1999,
<https://www.rfc-editor.org/info/rfc2557>.

[RFC2616] Fielding, R., Gettys, J., Mogul, J., Frystyk, H.,
Masinter, L., Leach, P., and T. Berners-Lee, "Hypertext
Transfer Protocol -- HTTP/1.1", RFC 2616,
DOI 10.17487/RFC2616, June 1999,
<https://www.rfc-editor.org/info/rfc2616>.

[RFC2617] Franks, J., Hallam-Baker, P., Hostetler, J., Lawrence, S.,
Leach, P., Luotonen, A., and L. Stewart, "HTTP
Authentication: Basic and Digest Access Authentication",
RFC 2617, DOI 10.17487/RFC2617, June 1999,
<https://www.rfc-editor.org/info/rfc2617>.

[RFC2774] Nielsen, H., Leach, P., and S. Lawrence, "An HTTP
Extension Framework", RFC 2774, DOI 10.17487/RFC2774,
February 2000, <https://www.rfc-editor.org/info/rfc2774>.

[RFC2818] Rescorla, E., "HTTP Over TLS", RFC 2818,
DOI 10.17487/RFC2818, May 2000,
<https://www.rfc-editor.org/info/rfc2818>.

[RFC2978] Freed, N. and J. Postel, "IANA Charset Registration
Procedures", BCP 19, RFC 2978, DOI 10.17487/RFC2978,
October 2000, <https://www.rfc-editor.org/info/rfc2978>.

[RFC3040] Cooper, I., Melve, I., and G. Tomlinson, "Internet Web
Replication and Caching Taxonomy", RFC 3040,
DOI 10.17487/RFC3040, January 2001,
<https://www.rfc-editor.org/info/rfc3040>.

[RFC3864] Klyne, G., Nottingham, M., and J. Mogul, "Registration
Procedures for Message Header Fields", BCP 90, RFC 3864,
DOI 10.17487/RFC3864, September 2004,
<https://www.rfc-editor.org/info/rfc3864>.

[RFC3875] Robinson, D. and K. Coar, "The Common Gateway Interface
(CGI) Version 1.1", RFC 3875, DOI 10.17487/RFC3875,
October 2004, <https://www.rfc-editor.org/info/rfc3875>.

[RFC4033] Arends, R., Austein, R., Larson, M., Massey, D., and S.
Rose, "DNS Security Introduction and Requirements",
RFC 4033, DOI 10.17487/RFC4033, March 2005,
<https://www.rfc-editor.org/info/rfc4033>.

[RFC4559] Jaganathan, K., Zhu, L., and J. Brezak, "SPNEGO-based
Kerberos and NTLM HTTP Authentication in Microsoft
Windows", RFC 4559, DOI 10.17487/RFC4559, June 2006,
<https://www.rfc-editor.org/info/rfc4559>.

[RFC5789] Dusseault, L. and J. Snell, "PATCH Method for HTTP",
RFC 5789, DOI 10.17487/RFC5789, March 2010,
<https://www.rfc-editor.org/info/rfc5789>.

[RFC5905] Mills, D., Martin, J., Ed., Burbank, J., and W. Kasch,
"Network Time Protocol Version 4: Protocol and Algorithms
Specification", RFC 5905, DOI 10.17487/RFC5905, June 2010,
<https://www.rfc-editor.org/info/rfc5905>.

[RFC6454] Barth, A., "The Web Origin Concept", RFC 6454,
DOI 10.17487/RFC6454, December 2011,
<https://www.rfc-editor.org/info/rfc6454>.

[RFC6585] Nottingham, M. and R. Fielding, "Additional HTTP Status
Codes", RFC 6585, DOI 10.17487/RFC6585, April 2012,
<https://www.rfc-editor.org/info/rfc6585>.

[RFC7230] Fielding, R., Ed. and J. Reschke, Ed., "Hypertext Transfer
Protocol (HTTP/1.1): Message Syntax and Routing",
RFC 7230, DOI 10.17487/RFC7230, June 2014,
<https://www.rfc-editor.org/info/rfc7230>.

[RFC7231] Fielding, R., Ed. and J. Reschke, Ed., "Hypertext Transfer
Protocol (HTTP/1.1): Semantics and Content", RFC 7231,
DOI 10.17487/RFC7231, June 2014,
<https://www.rfc-editor.org/info/rfc7231>.

[RFC7232] Fielding, R., Ed. and J. Reschke, Ed., "Hypertext Transfer
Protocol (HTTP/1.1): Conditional Requests", RFC 7232,
DOI 10.17487/RFC7232, June 2014,
<https://www.rfc-editor.org/info/rfc7232>.

[RFC7233] Fielding, R., Ed., Lafon, Y., Ed., and J. Reschke, Ed.,
"Hypertext Transfer Protocol (HTTP/1.1): Range Requests",
RFC 7233, DOI 10.17487/RFC7233, June 2014,
<https://www.rfc-editor.org/info/rfc7233>.

[RFC7234] Fielding, R., Ed., Nottingham, M., Ed., and J. Reschke,
Ed., "Hypertext Transfer Protocol (HTTP/1.1): Caching",
RFC 7234, DOI 10.17487/RFC7234, June 2014,
<https://www.rfc-editor.org/info/rfc7234>.

[RFC7235] Fielding, R., Ed. and J. Reschke, Ed., "Hypertext Transfer
Protocol (HTTP/1.1): Authentication", RFC 7235,
DOI 10.17487/RFC7235, June 2014,
<https://www.rfc-editor.org/info/rfc7235>.

[RFC7538] Reschke, J., "The Hypertext Transfer Protocol Status Code
308 (Permanent Redirect)", RFC 7538, DOI 10.17487/RFC7538,
April 2015, <https://www.rfc-editor.org/info/rfc7538>.

[RFC7540] Belshe, M., Peon, R., and M. Thomson, Ed., "Hypertext
Transfer Protocol Version 2 (HTTP/2)", RFC 7540,
DOI 10.17487/RFC7540, May 2015,
<https://www.rfc-editor.org/info/rfc7540>.

[RFC7578] Masinter, L., "Returning Values from Forms: multipart/
form-data", RFC 7578, DOI 10.17487/RFC7578, July 2015,
<https://www.rfc-editor.org/info/rfc7578>.

[RFC7615] Reschke, J., "HTTP Authentication-Info and Proxy-
Authentication-Info Response Header Fields", RFC 7615,
DOI 10.17487/RFC7615, September 2015,
<https://www.rfc-editor.org/info/rfc7615>.

[RFC7616] Shekh-Yusef, R., Ed., Ahrens, D., and S. Bremer, "HTTP
Digest Access Authentication", RFC 7616,
DOI 10.17487/RFC7616, September 2015,
<https://www.rfc-editor.org/info/rfc7616>.

[RFC7617] Reschke, J., "The 'Basic' HTTP Authentication Scheme",
RFC 7617, DOI 10.17487/RFC7617, September 2015,
<https://www.rfc-editor.org/info/rfc7617>.

[RFC7694] Reschke, J., "Hypertext Transfer Protocol (HTTP) Client-
Initiated Content-Encoding", RFC 7694,
DOI 10.17487/RFC7694, November 2015,
<https://www.rfc-editor.org/info/rfc7694>.

[RFC8126] Cotton, M., Leiba, B., and T. Narten, "Guidelines for
Writing an IANA Considerations Section in RFCs", BCP 26,
RFC 8126, DOI 10.17487/RFC8126, June 2017,
<https://www.rfc-editor.org/info/rfc8126>.

[RFC8187] Reschke, J., "Indicating Character Encoding and Language
for HTTP Header Field Parameters", RFC 8187,
DOI 10.17487/RFC8187, September 2017,
<https://www.rfc-editor.org/info/rfc8187>.

[RFC8246] McManus, P., "HTTP Immutable Responses", RFC 8246,
DOI 10.17487/RFC8246, September 2017,
<https://www.rfc-editor.org/info/rfc8246>.

[RFC8288] Nottingham, M., "Web Linking", RFC 8288,
DOI 10.17487/RFC8288, October 2017,
<https://www.rfc-editor.org/info/rfc8288>.

[RFC8336] Nottingham, M. and E. Nygren, "The ORIGIN HTTP/2 Frame",
RFC 8336, DOI 10.17487/RFC8336, March 2018,
<https://www.rfc-editor.org/info/rfc8336>.

[RFC8615] Nottingham, M., "Well-Known Uniform Resource Identifiers
(URIs)", RFC 8615, DOI 10.17487/RFC8615, May 2019,
<https://www.rfc-editor.org/info/rfc8615>.

[RFC8941] Nottingham, M. and P-H. Kamp, "Structured Field Values for
HTTP", RFC 8941, DOI 10.17487/RFC8941, February 2021,
<https://www.rfc-editor.org/info/rfc8941>.

[Sniffing] WHATWG, "MIME Sniffing",
<https://mimesniff.spec.whatwg.org>.

[WEBDAV] Dusseault, L., Ed., "HTTP Extensions for Web Distributed
Authoring and Versioning (WebDAV)", RFC 4918,
DOI 10.17487/RFC4918, June 2007,
<https://www.rfc-editor.org/info/rfc4918>.

Appendix A. Collected ABNF

In the collected ABNF below, list rules are expanded per
Section 5.6.1.

Accept = [ ( media-range [ weight ] ) _( OWS "," OWS ( media-range [
weight ] ) ) ]
Accept-Charset = [ ( ( token / "_" ) [ weight ] ) _( OWS "," OWS ( (
token / "_" ) [ weight ] ) ) ]
Accept-Encoding = [ ( codings [ weight ] ) _( OWS "," OWS ( codings [
weight ] ) ) ]
Accept-Language = [ ( language-range [ weight ] ) _( OWS "," OWS (
language-range [ weight ] ) ) ]
Accept-Ranges = acceptable-ranges
Allow = [ method *( OWS "," OWS method ) ]
Authentication-Info = [ auth-param *( OWS "," OWS auth-param ) ]
Authorization = credentials

BWS = OWS

Connection = [ connection-option *( OWS "," OWS connection-option )
]
Content-Encoding = [ content-coding *( OWS "," OWS content-coding )
]
Content-Language = [ language-tag *( OWS "," OWS language-tag ) ]
Content-Length = 1\*DIGIT
Content-Location = absolute-URI / partial-URI
Content-Range = range-unit SP ( range-resp / unsatisfied-range )
Content-Type = media-type

Date = HTTP-date

ETag = entity-tag
Expect = [ expectation *( OWS "," OWS expectation ) ]

From = mailbox

GMT = %x47.4D.54 ; GMT

HTTP-date = IMF-fixdate / obs-date
Host = uri-host [ ":" port ]

IMF-fixdate = day-name "," SP date1 SP time-of-day SP GMT
If-Match = "_" / [ entity-tag _( OWS "," OWS entity-tag ) ]
If-Modified-Since = HTTP-date
If-None-Match = "_" / [ entity-tag _( OWS "," OWS entity-tag ) ]
If-Range = entity-tag / HTTP-date
If-Unmodified-Since = HTTP-date

Last-Modified = HTTP-date
Location = URI-reference

Max-Forwards = 1\*DIGIT

OWS = \*( SP / HTAB )

Proxy-Authenticate = [ challenge *( OWS "," OWS challenge ) ]
Proxy-Authentication-Info = [ auth-param *( OWS "," OWS auth-param )
]
Proxy-Authorization = credentials

RWS = 1\*( SP / HTAB )
Range = ranges-specifier
Referer = absolute-URI / partial-URI
Retry-After = HTTP-date / delay-seconds

Server = product \*( RWS ( product / comment ) )

TE = [ t-codings *( OWS "," OWS t-codings ) ]
Trailer = [ field-name *( OWS "," OWS field-name ) ]

URI-reference = <URI-reference, see [URI], Section 4.1>
Upgrade = [ protocol *( OWS "," OWS protocol ) ]
User-Agent = product \*( RWS ( product / comment ) )

Vary = [ ( "*" / field-name ) *( OWS "," OWS ( "*" / field-name ) )
]
Via = [ ( received-protocol RWS received-by [ RWS comment ] ) \*( OWS
"," OWS ( received-protocol RWS received-by [ RWS comment ] ) ) ]

WWW-Authenticate = [ challenge *( OWS "," OWS challenge ) ]

absolute-URI = <absolute-URI, see [URI], Section 4.3>
absolute-path = 1*( "/" segment )
acceptable-ranges = range-unit *( OWS "," OWS range-unit )
asctime-date = day-name SP date3 SP time-of-day SP year
auth-param = token BWS "=" BWS ( token / quoted-string )
auth-scheme = token
authority = <authority, see [URI], Section 3.2>

challenge = auth-scheme [ 1*SP ( token68 / [ auth-param *( OWS ","
OWS auth-param ) ] ) ]
codings = content-coding / "identity" / "_"
comment = "(" _( ctext / quoted-pair / comment ) ")"
complete-length = 1*DIGIT
connection-option = token
content-coding = token
credentials = auth-scheme [ 1*SP ( token68 / [ auth-param *( OWS ","
OWS auth-param ) ] ) ]
ctext = HTAB / SP / %x21-27 ; '!'-'''
/ %x2A-5B ; '\*'-'['
/ %x5D-7E ; ']'-'~'
/ obs-text

date1 = day SP month SP year
date2 = day "-" month "-" 2DIGIT
date3 = month SP ( 2DIGIT / ( SP DIGIT ) )
day = 2DIGIT
day-name = %x4D.6F.6E ; Mon
/ %x54.75.65 ; Tue
/ %x57.65.64 ; Wed
/ %x54.68.75 ; Thu
/ %x46.72.69 ; Fri
/ %x53.61.74 ; Sat
/ %x53.75.6E ; Sun
day-name-l = %x4D.6F.6E.64.61.79 ; Monday
/ %x54.75.65.73.64.61.79 ; Tuesday
/ %x57.65.64.6E.65.73.64.61.79 ; Wednesday
/ %x54.68.75.72.73.64.61.79 ; Thursday
/ %x46.72.69.64.61.79 ; Friday
/ %x53.61.74.75.72.64.61.79 ; Saturday
/ %x53.75.6E.64.61.79 ; Sunday
delay-seconds = 1\*DIGIT

entity-tag = [ weak ] opaque-tag
etagc = "!" / %x23-7E ; '#'-'~'
/ obs-text
expectation = token [ "=" ( token / quoted-string ) parameters ]

field-content = field-vchar [ 1*( SP / HTAB / field-vchar )
field-vchar ]
field-name = token
field-value = *field-content
field-vchar = VCHAR / obs-text
first-pos = 1*DIGIT

hour = 2DIGIT
http-URI = "http://" authority path-abempty [ "?" query ]
https-URI = "https://" authority path-abempty [ "?" query ]

incl-range = first-pos "-" last-pos
int-range = first-pos "-" [ last-pos ]

language-range = <language-range, see [RFC4647], Section 2.1>
language-tag = <Language-Tag, see [RFC5646], Section 2.1>
last-pos = 1\*DIGIT

mailbox = <mailbox, see [RFC5322], Section 3.4>
media-range = ( "_/_" / ( type "/\*" ) / ( type "/" subtype ) )
parameters
media-type = type "/" subtype parameters
method = token
minute = 2DIGIT
month = %x4A.61.6E ; Jan
/ %x46.65.62 ; Feb
/ %x4D.61.72 ; Mar
/ %x41.70.72 ; Apr
/ %x4D.61.79 ; May
/ %x4A.75.6E ; Jun
/ %x4A.75.6C ; Jul
/ %x41.75.67 ; Aug
/ %x53.65.70 ; Sep
/ %x4F.63.74 ; Oct
/ %x4E.6F.76 ; Nov
/ %x44.65.63 ; Dec

obs-date = rfc850-date / asctime-date
obs-text = %x80-FF
opaque-tag = DQUOTE _etagc DQUOTE
other-range = 1_( %x21-2B ; '!'-'+'
/ %x2D-7E ; '-'-'~'
)

parameter = parameter-name "=" parameter-value
parameter-name = token
parameter-value = ( token / quoted-string )
parameters = \*( OWS ";" OWS [ parameter ] )
partial-URI = relative-part [ "?" query ]
path-abempty = <path-abempty, see [URI], Section 3.3>
port = <port, see [URI], Section 3.2.3>
product = token [ "/" product-version ]
product-version = token
protocol = protocol-name [ "/" protocol-version ]
protocol-name = token
protocol-version = token
pseudonym = token

qdtext = HTAB / SP / "!" / %x23-5B ; '#'-'['
/ %x5D-7E ; ']'-'~'
/ obs-text
query = <query, see [URI], Section 3.4>
quoted-pair = "\" ( HTAB / SP / VCHAR / obs-text )
quoted-string = DQUOTE *( qdtext / quoted-pair ) DQUOTE
qvalue = ( "0" [ "." *3DIGIT ] ) / ( "1" [ "." *3"0" ] )

range-resp = incl-range "/" ( complete-length / "_" )
range-set = range-spec _( OWS "," OWS range-spec )
range-spec = int-range / suffix-range / other-range
range-unit = token
ranges-specifier = range-unit "=" range-set
received-by = pseudonym [ ":" port ]
received-protocol = [ protocol-name "/" ] protocol-version
relative-part = <relative-part, see [URI], Section 4.2>
rfc850-date = day-name-l "," SP date2 SP time-of-day SP GMT

second = 2DIGIT
segment = <segment, see [URI], Section 3.3>
subtype = token
suffix-length = 1\*DIGIT
suffix-range = "-" suffix-length

t-codings = "trailers" / ( transfer-coding [ weight ] )
tchar = "!" / "#" / "$" / "%" / "&" / "'" / "*" / "+" / "-" / "." /
"^" / "\_" / "`" / "|" / "~" / DIGIT / ALPHA
time-of-day = hour ":" minute ":" second
token = 1*tchar
token68 = 1*( ALPHA / DIGIT / "-" / "." / "\_" / "~" / "+" / "/" )
*"="
transfer-coding = token \*( OWS ";" OWS transfer-parameter )
transfer-parameter = token BWS "=" BWS ( token / quoted-string )
type = token

unsatisfied-range = "\*/" complete-length
uri-host = <host, see [URI], Section 3.2.2>

weak = %x57.2F ; W/
weight = OWS ";" OWS "q=" qvalue

year = 4DIGIT

Appendix B. Changes from Previous RFCs

B.1. Changes from RFC 2818

None.

B.2. Changes from RFC 7230

The sections introducing HTTP's design goals, history, architecture,
conformance criteria, protocol versioning, URIs, message routing, and
header fields have been moved here.

The requirement on semantic conformance has been replaced with
permission to ignore or work around implementation-specific failures.
(Section 2.2)

The description of an origin and authoritative access to origin
servers has been extended for both "http" and "https" URIs to account
for alternative services and secured connections that are not
necessarily based on TCP. (Sections 4.2.1, 4.2.2, 4.3.1, and 7.3.3)

Explicit requirements have been added to check the target URI
scheme's semantics and reject requests that don't meet any associated
requirements. (Section 7.4)

Parameters in media type, media range, and expectation can be empty
via one or more trailing semicolons. (Section 5.6.6)

"Field value" now refers to the value after multiple field lines are
combined with commas -- by far the most common use. To refer to a
single header line's value, use "field line value". (Section 6.3)

Trailer field semantics now transcend the specifics of chunked
transfer coding. The use of trailer fields has been further limited
to allow generation as a trailer field only when the sender knows the
field defines that usage and to allow merging into the header section
only if the recipient knows the corresponding field definition
permits and defines how to merge. In all other cases,
implementations are encouraged either to store the trailer fields
separately or to discard them instead of merging. (Section 6.5.1)

The priority of the absolute form of the request URI over the Host
header field by origin servers has been made explicit to align with
proxy handling. (Section 7.2)

The grammar definition for the Via field's "received-by" was expanded
in RFC 7230 due to changes in the URI grammar for host [URI] that are
not desirable for Via. For simplicity, we have removed uri-host from
the received-by production because it can be encompassed by the
existing grammar for pseudonym. In particular, this change removed
comma from the allowed set of characters for a host name in received-
by. (Section 7.6.3)

B.3. Changes from RFC 7231

Minimum URI lengths to be supported by implementations are now
recommended. (Section 4.1)

The following have been clarified: CR and NUL in field values are to
be rejected or mapped to SP, and leading and trailing whitespace
needs to be stripped from field values before they are consumed.
(Section 5.5)

Parameters in media type, media range, and expectation can be empty
via one or more trailing semicolons. (Section 5.6.6)

An abstract data type for HTTP messages has been introduced to define
the components of a message and their semantics as an abstraction
across multiple HTTP versions, rather than in terms of the specific
syntax form of HTTP/1.1 in [HTTP/1.1], and reflect the contents after
the message is parsed. This makes it easier to distinguish between
requirements on the content (what is conveyed) versus requirements on
the messaging syntax (how it is conveyed) and avoids baking
limitations of early protocol versions into the future of HTTP.
(Section 6)

The terms "payload" and "payload body" have been replaced with
"content", to better align with its usage elsewhere (e.g., in field
names) and to avoid confusion with frame payloads in HTTP/2 and
HTTP/3. (Section 6.4)

The term "effective request URI" has been replaced with "target URI".
(Section 7.1)

Restrictions on client retries have been loosened to reflect
implementation behavior. (Section 9.2.2)

The fact that request bodies on GET, HEAD, and DELETE are not
interoperable has been clarified. (Sections 9.3.1, 9.3.2, and 9.3.5)

The use of the Content-Range header field (Section 14.4) as a request
modifier on PUT is allowed. (Section 9.3.4)

A superfluous requirement about setting Content-Length has been
removed from the description of the OPTIONS method. (Section 9.3.7)

The normative requirement to use the "message/http" media type in
TRACE responses has been removed. (Section 9.3.8)

List-based grammar for Expect has been restored for compatibility
with RFC 2616. (Section 10.1.1)

Accept and Accept-Encoding are allowed in response messages; the
latter was introduced by [RFC7694]. (Section 12.3)

"Accept Parameters" (accept-params and accept-ext ABNF production)
have been removed from the definition of the Accept field.
(Section 12.5.1)

The Accept-Charset field is now deprecated. (Section 12.5.2)

The semantics of "\*" in the Vary header field when other values are
present was clarified. (Section 12.5.5)

Range units are compared in a case-insensitive fashion.
(Section 14.1)

The use of the Accept-Ranges field is not restricted to origin
servers. (Section 14.3)

The process of creating a redirected request has been clarified.
(Section 15.4)

Status code 308 (previously defined in [RFC7538]) has been added so
that it's defined closer to status codes 301, 302, and 307.
(Section 15.4.9)

Status code 421 (previously defined in Section 9.1.2 of [RFC7540])
has been added because of its general applicability. 421 is no longer
defined as heuristically cacheable since the response is specific to
the connection (not the target resource). (Section 15.5.20)

Status code 422 (previously defined in Section 11.2 of [WEBDAV]) has
been added because of its general applicability. (Section 15.5.21)

B.4. Changes from RFC 7232

Previous revisions of HTTP imposed an arbitrary 60-second limit on
the determination of whether Last-Modified was a strong validator to
guard against the possibility that the Date and Last-Modified values
are generated from different clocks or at somewhat different times
during the preparation of the response. This specification has
relaxed that to allow reasonable discretion. (Section 8.8.2.2)

An edge-case requirement on If-Match and If-Unmodified-Since has been
removed that required a validator not to be sent in a 2xx response if
validation fails because the change request has already been applied.
(Sections 13.1.1 and 13.1.4)

The fact that If-Unmodified-Since does not apply to a resource
without a concept of modification time has been clarified.
(Section 13.1.4)

Preconditions can now be evaluated before the request content is
processed rather than waiting until the response would otherwise be
successful. (Section 13.2)

B.5. Changes from RFC 7233

Refactored the range-unit and ranges-specifier grammars to simplify
and reduce artificial distinctions between bytes and other
(extension) range units, removing the overlapping grammar of other-
range-unit by defining range units generically as a token and placing
extensions within the scope of a range-spec (other-range). This
disambiguates the role of list syntax (commas) in all range sets,
including extension range units, for indicating a range-set of more
than one range. Moving the extension grammar into range specifiers
also allows protocol specific to byte ranges to be specified
separately.

It is now possible to define Range handling on extension methods.
(Section 14.2)

Described use of the Content-Range header field (Section 14.4) as a
request modifier to perform a partial PUT. (Section 14.5)

B.6. Changes from RFC 7235

None.

B.7. Changes from RFC 7538

None.

B.8. Changes from RFC 7615

None.

B.9. Changes from RFC 7694

This specification includes the extension defined in [RFC7694] but
leaves out examples and deployment considerations.

Acknowledgements

Aside from the current editors, the following individuals deserve
special recognition for their contributions to early aspects of HTTP
and its core specifications: Marc Andreessen, Tim Berners-Lee, Robert
Cailliau, Daniel W. Connolly, Bob Denny, John Franks, Jim Gettys,
Jean-François Groff, Phillip M. Hallam-Baker, Koen Holtman, Jeffery
L. Hostetler, Shel Kaphan, Dave Kristol, Yves Lafon, Scott
D. Lawrence, Paul J. Leach, Håkon W. Lie, Ari Luotonen, Larry
Masinter, Rob McCool, Jeffrey C. Mogul, Lou Montulli, David Morris,
Henrik Frystyk Nielsen, Dave Raggett, Eric Rescorla, Tony Sanders,
Lawrence C. Stewart, Marc VanHeyningen, and Steve Zilles.

This document builds on the many contributions that went into past
specifications of HTTP, including [HTTP/1.0], [RFC2068], [RFC2145],
[RFC2616], [RFC2617], [RFC2818], [RFC7230], [RFC7231], [RFC7232],
[RFC7233], [RFC7234], and [RFC7235]. The acknowledgements within
those documents still apply.

Since 2014, the following contributors have helped improve this
specification by reporting bugs, asking smart questions, drafting or
reviewing text, and evaluating issues:

Alan Egerton, Alex Rousskov, Amichai Rothman, Amos Jeffries, Anders
Kaseorg, Andreas Gebhardt, Anne van Kesteren, Armin Abfalterer, Aron
Duby, Asanka Herath, Asbjørn Ulsberg, Asta Olofsson, Attila Gulyas,
Austin Wright, Barry Pollard, Ben Burkert, Benjamin Kaduk, Björn
Höhrmann, Brad Fitzpatrick, Chris Pacejo, Colin Bendell, Cory
Benfield, Cory Nelson, Daisuke Miyakawa, Dale Worley, Daniel
Stenberg, Danil Suits, David Benjamin, David Matson, David Schinazi,
Дилян Палаузов (Dilyan Palauzov), Eric Anderson, Eric Rescorla, Éric
Vyncke, Erik Kline, Erwin Pe, Etan Kissling, Evert Pot, Evgeny
Vrublevsky, Florian Best, Francesca Palombini, Igor Lubashev, James
Callahan, James Peach, Jeffrey Yasskin, Kalin Gyokov, Kannan Goundan,
奥 一穂 (Kazuho Oku), Ken Murchison, Krzysztof Maczyński, Lars Eggert,
Lucas Pardue, Martin Duke, Martin Dürst, Martin Thomson, Martynas
Jusevičius, Matt Menke, Matthias Pigulla, Mattias Grenfeldt, Michael
Osipov, Mike Bishop, Mike Pennisi, Mike Taylor, Mike West, Mohit
Sethi, Murray Kucherawy, Nathaniel J. Smith, Nicholas Hurley, Nikita
Prokhorov, Patrick McManus, Piotr Sikora, Poul-Henning Kamp, Rick van
Rein, Robert Wilton, Roberto Polli, Roman Danyliw, Samuel Williams,
Semyon Kholodnov, Simon Pieters, Simon Schüppel, Stefan Eissing,
Taylor Hunt, Todd Greer, Tommy Pauly, Vasiliy Faronov, Vladimir
Lashchev, Wenbo Zhu, William A. Rowe Jr., Willy Tarreau, Xingwei Liu,
Yishuai Li, and Zaheduzzaman Sarker.

Index

1 2 3 4 5 A B C D E F G H I L M N O P R S T U V W X

      1

         100 Continue (status code)  *_Section 15.2.1_*
         100-continue (expect value)  *_Section 10.1.1_*
         101 Switching Protocols (status code)  *_Section 15.2.2_*
         1xx Informational (status code class)  *_Section 15.2_*

      2

         200 OK (status code)  *_Section 15.3.1_*
         201 Created (status code)  *_Section 15.3.2_*
         202 Accepted (status code)  *_Section 15.3.3_*
         203 Non-Authoritative Information (status code)  *_Section 15.3
            .4_*
         204 No Content (status code)  *_Section 15.3.5_*
         205 Reset Content (status code)  *_Section 15.3.6_*
         206 Partial Content (status code)  *_Section 15.3.7_*
         2xx Successful (status code class)  *_Section 15.3_*

      3

         300 Multiple Choices (status code)  *_Section 15.4.1_*
         301 Moved Permanently (status code)  *_Section 15.4.2_*
         302 Found (status code)  *_Section 15.4.3_*
         303 See Other (status code)  *_Section 15.4.4_*
         304 Not Modified (status code)  *_Section 15.4.5_*
         305 Use Proxy (status code)  *_Section 15.4.6_*
         306 (Unused) (status code)  *_Section 15.4.7_*
         307 Temporary Redirect (status code)  *_Section 15.4.8_*
         308 Permanent Redirect (status code)  *_Section 15.4.9_*
         3xx Redirection (status code class)  *_Section 15.4_*

      4

         400 Bad Request (status code)  *_Section 15.5.1_*
         401 Unauthorized (status code)  *_Section 15.5.2_*
         402 Payment Required (status code)  *_Section 15.5.3_*
         403 Forbidden (status code)  *_Section 15.5.4_*
         404 Not Found (status code)  *_Section 15.5.5_*
         405 Method Not Allowed (status code)  *_Section 15.5.6_*
         406 Not Acceptable (status code)  *_Section 15.5.7_*
         407 Proxy Authentication Required (status code)  *_Section 15.5
            .8_*
         408 Request Timeout (status code)  *_Section 15.5.9_*
         409 Conflict (status code)  *_Section 15.5.10_*
         410 Gone (status code)  *_Section 15.5.11_*
         411 Length Required (status code)  *_Section 15.5.12_*
         412 Precondition Failed (status code)  *_Section 15.5.13_*
         413 Content Too Large (status code)  *_Section 15.5.14_*
         414 URI Too Long (status code)  *_Section 15.5.15_*
         415 Unsupported Media Type (status code)  *_Section 15.5.16_*
         416 Range Not Satisfiable (status code)  *_Section 15.5.17_*
         417 Expectation Failed (status code)  *_Section 15.5.18_*
         418 (Unused) (status code)  *_Section 15.5.19_*
         421 Misdirected Request (status code)  *_Section 15.5.20_*
         422 Unprocessable Content (status code)  *_Section 15.5.21_*
         426 Upgrade Required (status code)  *_Section 15.5.22_*
         4xx Client Error (status code class)  *_Section 15.5_*

      5

         500 Internal Server Error (status code)  *_Section 15.6.1_*
         501 Not Implemented (status code)  *_Section 15.6.2_*
         502 Bad Gateway (status code)  *_Section 15.6.3_*
         503 Service Unavailable (status code)  *_Section 15.6.4_*
         504 Gateway Timeout (status code)  *_Section 15.6.5_*
         505 HTTP Version Not Supported (status code)  *_Section 15.6.6_
            *
         5xx Server Error (status code class)  *_Section 15.6_*

      A

         accelerator  *_Section 3.7, Paragraph 6_*
         Accept header field  *_Section 12.5.1_*
         Accept-Charset header field  *_Section 12.5.2_*
         Accept-Encoding header field  *_Section 12.5.3_*
         Accept-Language header field  *_Section 12.5.4_*
         Accept-Ranges header field  *_Section 14.3_*
         Allow header field  *_Section 10.2.1_*
         Authentication-Info header field  *_Section 11.6.3_*
         authoritative response  *_Section 17.1_*
         Authorization header field  *_Section 11.6.2_*

      B

         browser  *_Section 3.5_*

      C

         cache  *_Section 3.8_*
         cacheable  *_Section 3.8, Paragraph 4_*
         client  *_Section 3.3_*
         clock  *_Section 5.6.7_*
         complete  *_Section 6.1_*
         compress (Coding Format)  Section 8.4.1.1
         compress (content coding)  *_Section 8.4.1_*
         conditional request  *_Section 13_*
         CONNECT method  *_Section 9.3.6_*
         connection  *_Section 3.3_*
         Connection header field  *_Section 7.6.1_*
         content  Section 6.4
         content coding  *_Section 8.4.1_*
         content negotiation  Section 1.3, Paragraph 4
         Content-Encoding header field  *_Section 8.4_*
         Content-Language header field  *_Section 8.5_*
         Content-Length header field  *_Section 8.6_*
         Content-Location header field  *_Section 8.7_*
         Content-MD5 header field  *_Section 18.4, Paragraph 10_*
         Content-Range header field  *_Section 14.4_*; Section 14.5
         Content-Type header field  *_Section 8.3_*
         control data  *_Section 6.2_*

      D

         Date header field  *_Section 6.6.1_*
         deflate (Coding Format)  Section 8.4.1.2
         deflate (content coding)  *_Section 8.4.1_*
         DELETE method  *_Section 9.3.5_*
         Delimiters  Section 5.6.2, Paragraph 3
         downstream  *_Section 3.7, Paragraph 4_*

      E

         effective request URI  *_Section 7.1, Paragraph 8.1_*
         ETag field  *_Section 8.8.3_*
         Expect header field  *_Section 10.1.1_*

      F

         field  *_Section 5_*; Section 6.3
         field line  Section 5.2, Paragraph 1
         field line value  Section 5.2, Paragraph 1
         field name  Section 5.2, Paragraph 1
         field value  Section 5.2, Paragraph 2
         Fields
            *  *_Section 18.4, Paragraph 9_*
            Accept  *_Section 12.5.1_*
            Accept-Charset  *_Section 12.5.2_*
            Accept-Encoding  *_Section 12.5.3_*
            Accept-Language  *_Section 12.5.4_*
            Accept-Ranges  *_Section 14.3_*
            Allow  *_Section 10.2.1_*
            Authentication-Info  *_Section 11.6.3_*
            Authorization  *_Section 11.6.2_*
            Connection  *_Section 7.6.1_*
            Content-Encoding  *_Section 8.4_*
            Content-Language  *_Section 8.5_*
            Content-Length  *_Section 8.6_*
            Content-Location  *_Section 8.7_*
            Content-MD5  *_Section 18.4, Paragraph 10_*
            Content-Range  *_Section 14.4_*; Section 14.5
            Content-Type  *_Section 8.3_*
            Date  *_Section 6.6.1_*
            ETag  *_Section 8.8.3_*
            Expect  *_Section 10.1.1_*
            From  *_Section 10.1.2_*
            Host  *_Section 7.2_*
            If-Match  *_Section 13.1.1_*
            If-Modified-Since  *_Section 13.1.3_*
            If-None-Match  *_Section 13.1.2_*
            If-Range  *_Section 13.1.5_*
            If-Unmodified-Since  *_Section 13.1.4_*
            Last-Modified  *_Section 8.8.2_*
            Location  *_Section 10.2.2_*
            Max-Forwards  *_Section 7.6.2_*
            Proxy-Authenticate  *_Section 11.7.1_*
            Proxy-Authentication-Info  *_Section 11.7.3_*
            Proxy-Authorization  *_Section 11.7.2_*
            Range  *_Section 14.2_*
            Referer  *_Section 10.1.3_*
            Retry-After  *_Section 10.2.3_*
            Server  *_Section 10.2.4_*
            TE  *_Section 10.1.4_*
            Trailer  *_Section 6.6.2_*
            Upgrade  *_Section 7.8_*
            User-Agent  *_Section 10.1.5_*
            Vary  *_Section 12.5.5_*
            Via  *_Section 7.6.3_*
            WWW-Authenticate  *_Section 11.6.1_*
         Fragment Identifiers  Section 4.2.5
         From header field  *_Section 10.1.2_*

      G

         gateway  *_Section 3.7, Paragraph 6_*
         GET method  *_Section 9.3.1_*
         Grammar
            ALPHA  *_Section 2.1_*
            Accept  *_Section 12.5.1_*
            Accept-Charset  *_Section 12.5.2_*
            Accept-Encoding  *_Section 12.5.3_*
            Accept-Language  *_Section 12.5.4_*
            Accept-Ranges  *_Section 14.3_*
            Allow  *_Section 10.2.1_*
            Authentication-Info  *_Section 11.6.3_*
            Authorization  *_Section 11.6.2_*
            BWS  *_Section 5.6.3_*
            CR  *_Section 2.1_*
            CRLF  *_Section 2.1_*
            CTL  *_Section 2.1_*
            Connection  *_Section 7.6.1_*
            Content-Encoding  *_Section 8.4_*
            Content-Language  *_Section 8.5_*
            Content-Length  *_Section 8.6_*
            Content-Location  *_Section 8.7_*
            Content-Range  *_Section 14.4_*
            Content-Type  *_Section 8.3_*
            DIGIT  *_Section 2.1_*
            DQUOTE  *_Section 2.1_*
            Date  *_Section 6.6.1_*
            ETag  *_Section 8.8.3_*
            Expect  *_Section 10.1.1_*
            From  *_Section 10.1.2_*
            GMT  *_Section 5.6.7_*
            HEXDIG  *_Section 2.1_*
            HTAB  *_Section 2.1_*
            HTTP-date  *_Section 5.6.7_*
            Host  *_Section 7.2_*
            IMF-fixdate  *_Section 5.6.7_*
            If-Match  *_Section 13.1.1_*
            If-Modified-Since  *_Section 13.1.3_*
            If-None-Match  *_Section 13.1.2_*
            If-Range  *_Section 13.1.5_*
            If-Unmodified-Since  *_Section 13.1.4_*
            LF  *_Section 2.1_*
            Last-Modified  *_Section 8.8.2_*
            Location  *_Section 10.2.2_*
            Max-Forwards  *_Section 7.6.2_*
            OCTET  *_Section 2.1_*
            OWS  *_Section 5.6.3_*
            Proxy-Authenticate  *_Section 11.7.1_*
            Proxy-Authentication-Info  *_Section 11.7.3_*
            Proxy-Authorization  *_Section 11.7.2_*
            RWS  *_Section 5.6.3_*
            Range  *_Section 14.2_*
            Referer  *_Section 10.1.3_*
            Retry-After  *_Section 10.2.3_*
            SP  *_Section 2.1_*
            Server  *_Section 10.2.4_*
            TE  *_Section 10.1.4_*
            Trailer  *_Section 6.6.2_*
            URI-reference  *_Section 4.1_*
            Upgrade  *_Section 7.8_*
            User-Agent  *_Section 10.1.5_*
            VCHAR  *_Section 2.1_*
            Vary  *_Section 12.5.5_*
            Via  *_Section 7.6.3_*
            WWW-Authenticate  *_Section 11.6.1_*
            absolute-URI  *_Section 4.1_*
            absolute-path  *_Section 4.1_*
            acceptable-ranges  *_Section 14.3_*
            asctime-date  *_Section 5.6.7_*
            auth-param  *_Section 11.2_*
            auth-scheme  *_Section 11.1_*
            authority  *_Section 4.1_*
            challenge  *_Section 11.3_*
            codings  *_Section 12.5.3_*
            comment  *_Section 5.6.5_*
            complete-length  *_Section 14.4_*
            connection-option  *_Section 7.6.1_*
            content-coding  *_Section 8.4.1_*
            credentials  *_Section 11.4_*
            ctext  *_Section 5.6.5_*
            date1  *_Section 5.6.7_*
            day  *_Section 5.6.7_*
            day-name  *_Section 5.6.7_*
            day-name-l  *_Section 5.6.7_*
            delay-seconds  *_Section 10.2.3_*
            entity-tag  *_Section 8.8.3_*
            etagc  *_Section 8.8.3_*
            field-content  *_Section 5.5_*
            field-name  *_Section 5.1_*; Section 6.6.2
            field-value  *_Section 5.5_*
            field-vchar  *_Section 5.5_*
            first-pos  *_Section 14.1.1_*; Section 14.4
            hour  *_Section 5.6.7_*
            http-URI  *_Section 4.2.1_*
            https-URI  *_Section 4.2.2_*
            incl-range  *_Section 14.4_*
            int-range  *_Section 14.1.1_*
            language-range  *_Section 12.5.4_*
            language-tag  *_Section 8.5.1_*
            last-pos  *_Section 14.1.1_*; Section 14.4
            media-range  *_Section 12.5.1_*
            media-type  *_Section 8.3.1_*
            method  *_Section 9.1_*
            minute  *_Section 5.6.7_*
            month  *_Section 5.6.7_*
            obs-date  *_Section 5.6.7_*
            obs-text  *_Section 5.5_*
            opaque-tag  *_Section 8.8.3_*
            other-range  *_Section 14.1.1_*
            parameter  *_Section 5.6.6_*
            parameter-name  *_Section 5.6.6_*
            parameter-value  *_Section 5.6.6_*
            parameters  *_Section 5.6.6_*
            partial-URI  *_Section 4.1_*
            port  *_Section 4.1_*
            product  *_Section 10.1.5_*
            product-version  *_Section 10.1.5_*
            protocol-name  *_Section 7.6.3_*
            protocol-version  *_Section 7.6.3_*
            pseudonym  *_Section 7.6.3_*
            qdtext  *_Section 5.6.4_*
            query  *_Section 4.1_*
            quoted-pair  *_Section 5.6.4_*
            quoted-string  *_Section 5.6.4_*
            qvalue  *_Section 12.4.2_*
            range-resp  *_Section 14.4_*
            range-set  *_Section 14.1.1_*
            range-spec  *_Section 14.1.1_*
            range-unit  *_Section 14.1_*
            ranges-specifier  *_Section 14.1.1_*
            received-by  *_Section 7.6.3_*
            received-protocol  *_Section 7.6.3_*
            rfc850-date  *_Section 5.6.7_*
            second  *_Section 5.6.7_*
            segment  *_Section 4.1_*
            subtype  *_Section 8.3.1_*
            suffix-length  *_Section 14.1.1_*
            suffix-range  *_Section 14.1.1_*
            t-codings  *_Section 10.1.4_*
            tchar  *_Section 5.6.2_*
            time-of-day  *_Section 5.6.7_*
            token  *_Section 5.6.2_*
            token68  *_Section 11.2_*
            transfer-coding  *_Section 10.1.4_*
            transfer-parameter  *_Section 10.1.4_*
            type  *_Section 8.3.1_*
            unsatisfied-range  *_Section 14.4_*
            uri-host  *_Section 4.1_*
            weak  *_Section 8.8.3_*
            weight  *_Section 12.4.2_*
            year  *_Section 5.6.7_*
         gzip (Coding Format)  Section 8.4.1.3
         gzip (content coding)  *_Section 8.4.1_*

      H

         HEAD method  *_Section 9.3.2_*
         Header Fields
            Accept  *_Section 12.5.1_*
            Accept-Charset  *_Section 12.5.2_*
            Accept-Encoding  *_Section 12.5.3_*
            Accept-Language  *_Section 12.5.4_*
            Accept-Ranges  *_Section 14.3_*
            Allow  *_Section 10.2.1_*
            Authentication-Info  *_Section 11.6.3_*
            Authorization  *_Section 11.6.2_*
            Connection  *_Section 7.6.1_*
            Content-Encoding  *_Section 8.4_*
            Content-Language  *_Section 8.5_*
            Content-Length  *_Section 8.6_*
            Content-Location  *_Section 8.7_*
            Content-MD5  *_Section 18.4, Paragraph 10_*
            Content-Range  *_Section 14.4_*; Section 14.5
            Content-Type  *_Section 8.3_*
            Date  *_Section 6.6.1_*
            ETag  *_Section 8.8.3_*
            Expect  *_Section 10.1.1_*
            From  *_Section 10.1.2_*
            Host  *_Section 7.2_*
            If-Match  *_Section 13.1.1_*
            If-Modified-Since  *_Section 13.1.3_*
            If-None-Match  *_Section 13.1.2_*
            If-Range  *_Section 13.1.5_*
            If-Unmodified-Since  *_Section 13.1.4_*
            Last-Modified  *_Section 8.8.2_*
            Location  *_Section 10.2.2_*
            Max-Forwards  *_Section 7.6.2_*
            Proxy-Authenticate  *_Section 11.7.1_*
            Proxy-Authentication-Info  *_Section 11.7.3_*
            Proxy-Authorization  *_Section 11.7.2_*
            Range  *_Section 14.2_*
            Referer  *_Section 10.1.3_*
            Retry-After  *_Section 10.2.3_*
            Server  *_Section 10.2.4_*
            TE  *_Section 10.1.4_*
            Trailer  *_Section 6.6.2_*
            Upgrade  *_Section 7.8_*
            User-Agent  *_Section 10.1.5_*
            Vary  *_Section 12.5.5_*
            Via  *_Section 7.6.3_*
            WWW-Authenticate  *_Section 11.6.1_*
         header section  *_Section 6.3_*
         Host header field  *_Section 7.2_*
         http URI scheme  *_Section 4.2.1_*
         https URI scheme  *_Section 4.2.2_*

      I

         idempotent  *_Section 9.2.2_*
         If-Match header field  *_Section 13.1.1_*
         If-Modified-Since header field  *_Section 13.1.3_*
         If-None-Match header field  *_Section 13.1.2_*
         If-Range header field  *_Section 13.1.5_*
         If-Unmodified-Since header field  *_Section 13.1.4_*
         inbound  *_Section 3.7, Paragraph 4_*
         incomplete  *_Section 6.1_*
         interception proxy  *_Section 3.7, Paragraph 10_*
         intermediary  *_Section 3.7_*

      L

         Last-Modified header field  *_Section 8.8.2_*
         list-based field  Section 5.5, Paragraph 7
         Location header field  *_Section 10.2.2_*

      M

         Max-Forwards header field  *_Section 7.6.2_*
         Media Type
            multipart/byteranges  *_Section 14.6_*
            multipart/x-byteranges  Section 14.6, Paragraph 4, Item 3
         message  Section 3.4; *_Section 6_*
         message abstraction  *_Section 6_*
         messages  *_Section 3.4_*
         metadata  *_Section 8.8_*
         Method
            *  *_Section 18.2, Paragraph 3_*
            CONNECT  *_Section 9.3.6_*
            DELETE  *_Section 9.3.5_*
            GET  *_Section 9.3.1_*
            HEAD  *_Section 9.3.2_*
            OPTIONS  *_Section 9.3.7_*
            POST  *_Section 9.3.3_*
            PUT  *_Section 9.3.4_*
            TRACE  *_Section 9.3.8_*
         multipart/byteranges Media Type  *_Section 14.6_*
         multipart/x-byteranges Media Type  Section 14.6, Paragraph 4,
            Item 3

      N

         non-transforming proxy  *_Section 7.7_*

      O

         OPTIONS method  *_Section 9.3.7_*
         origin  *_Section 4.3.1_*; Section 11.5
         origin server  *_Section 3.6_*
         outbound  *_Section 3.7, Paragraph 4_*

      P

         phishing  *_Section 17.1_*
         POST method  *_Section 9.3.3_*
         Protection Space  Section 11.5
         proxy  *_Section 3.7, Paragraph 5_*
         Proxy-Authenticate header field  *_Section 11.7.1_*
         Proxy-Authentication-Info header field  *_Section 11.7.3_*
         Proxy-Authorization header field  *_Section 11.7.2_*
         PUT method  *_Section 9.3.4_*

      R

         Range header field  *_Section 14.2_*
         Realm  Section 11.5
         recipient  *_Section 3.4_*
         Referer header field  *_Section 10.1.3_*
         representation  *_Section 3.2_*
         request  *_Section 3.4_*
         request target  *_Section 7.1_*
         resource  *_Section 3.1_*; Section 4
         response  *_Section 3.4_*
         Retry-After header field  *_Section 10.2.3_*
         reverse proxy  *_Section 3.7, Paragraph 6_*

      S

         safe  *_Section 9.2.1_*
         satisfiable range  *_Section 14.1.1_*
         secured  *_Section 4.2.2_*
         selected representation  *_Section 3.2, Paragraph 4_*;
            Section 8.8; Section 13.1
         self-descriptive  *_Section 6_*
         sender  *_Section 3.4_*
         server  *_Section 3.3_*
         Server header field  *_Section 10.2.4_*
         singleton field  Section 5.5, Paragraph 6
         spider  *_Section 3.5_*
         Status Code  Section 15
         Status Codes
            Final  Section 15, Paragraph 7
            Informational  Section 15, Paragraph 7
            Interim  Section 15, Paragraph 7
         Status Codes Classes
            1xx Informational  *_Section 15.2_*
            2xx Successful  *_Section 15.3_*
            3xx Redirection  *_Section 15.4_*
            4xx Client Error  *_Section 15.5_*
            5xx Server Error  *_Section 15.6_*

      T

         target resource  *_Section 7.1_*
         target URI  *_Section 7.1_*
         TE header field  *_Section 10.1.4_*
         TRACE method  *_Section 9.3.8_*
         Trailer Fields  *_Section 6.5_*
            ETag  *_Section 8.8.3_*
         Trailer header field  *_Section 6.6.2_*
         trailer section  *_Section 6.5_*
         trailers  *_Section 6.5_*
         transforming proxy  *_Section 7.7_*
         transparent proxy  *_Section 3.7, Paragraph 10_*
         tunnel  *_Section 3.7, Paragraph 8_*

      U

         unsatisfiable range  *_Section 14.1.1_*
         Upgrade header field  *_Section 7.8_*
         upstream  *_Section 3.7, Paragraph 4_*
         URI  *_Section 4_*
            origin  *_Section 4.3.1_*
         URI reference  *_Section 4.1_*
         URI scheme
            http  *_Section 4.2.1_*
            https  *_Section 4.2.2_*
         user agent  *_Section 3.5_*
         User-Agent header field  *_Section 10.1.5_*

      V

         validator  *_Section 8.8_*
            strong  *_Section 8.8.1_*
            weak  *_Section 8.8.1_*
         Vary header field  *_Section 12.5.5_*
         Via header field  *_Section 7.6.3_*

      W

         WWW-Authenticate header field  *_Section 11.6.1_*

      X

         x-compress (content coding)  *_Section 8.4.1_*
         x-gzip (content coding)  *_Section 8.4.1_*

Authors' Addresses

Roy T. Fielding (editor)
Adobe
345 Park Ave
San Jose, CA 95110
United States of America
Email: fielding@gbiv.com
URI: https://roy.gbiv.com/

Mark Nottingham (editor)
Fastly
Prahran
Australia
Email: mnot@mnot.net
URI: https://www.mnot.net/

Julian Reschke (editor)
greenbytes GmbH
Hafenweg 16
48155 Münster
Germany
Email: julian.reschke@greenbytes.de
URI: https://greenbytes.de/tech/webdav/
