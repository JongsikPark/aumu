# 📘 거의 쓸모없지만, 대체로 유용할지도 모르는 풀스택 웹 개발자를 위한 도감(Almost Useless, Mostly Useful Wiki For Full-Stack Web Developer)

이 도감은 풀스택 개발자가 실무에서 가장 자주 혼동하거나, 구현 시 선택의 기로에 서게 되는 핵심 개념들을 1:1로 비교 정리한 가이드라인입니다.

---

## 📝퀴즈도 한 번 풀어보기

❓ **[Quiz 풀기 바로가기](quiz.md)**

---

# 📍목차

- [웹 서버(Web Server) vs WAS(Web Application Server)](#웹-서버web-server-vs-wasweb-application-server)

- [RDBMS(SQL) vs NoSQL](#rdbmssql-vs-nosql)

- [인증(Authentication) vs 인가(Authorization)](#인증authentication-vs-인가authorization)

- [JWT vs 세션(Session) 인증](#-jwt-vs-세션session-인증)

- [CSR(Client-Side Rendering) vs SSR(Server-Side Rendering)](#-csrclient-side-rendering-vs-ssrserver-side-rendering)

- [레디스(Redis) vs 일반 관계형 데이터베이스(RDB)](#-레디스redis-vs-일반-관계형-데이터베이스rdb)

- [CI/CD (지속적 통합 / 지속적 제공 및 배포)](#-cicd-지속적-통합--지속적-제공-및-배포)

- [브라우저 쿠키(Cookie) vs 로컬 스토리지(Local Storage) vs 세션 스토리지(Session Storage)](#-브라우저-쿠키cookie-vs-로컬-스토리지local-storage-vs-세션-스토리지session-storage)

- [MariaDB 실행계획(Execution Plan) 및 쿼리 튜닝](#-mariadb-실행계획execution-plan-및-쿼리-튜닝)

---

# 📄웹 서버(Web Server) vs WAS(Web Application Server)

### 1) 10초 요약
* **웹 서버 (Web Server)**: 컴퓨터에 저장되어 있는 변하지 않는 정적 파일(HTML, CSS, 이미지 등)을 가공 없이 그대로 클라이언트에게 빠르게 던져주는 역할입니다.
* **WAS (Web Application Server)**: 사용자나 조건에 따라 동적으로 변하는 응답(DB 조회 결과, 개인화된 화면 등)을 만들기 위해 비즈니스 로직(코드)을 구동하는 역할입니다.

### 2) 핵심 비교

| 구분 | 웹 서버 (Web Server) | WAS (Web Application Server) |
| :--- | :--- | :--- |
| **주요 역할** | 정적 콘텐츠 제공, 클라이언트 요청의 1차 접수 | 동적 콘텐츠 생성, 비즈니스 로직 실행 |
| **동작 방식** | 요청받은 정적 리소스를 디스크에서 읽어 즉시 반환 | DB 조회나 연산 작업을 거쳐 결과 데이터를 가공 후 반환 |
| **대표 기술** | Nginx, Apache HTTP Server, IIS | Apache Tomcat, Node.js (Express), Django, Spring Boot |
| **직관적 비유** | 편의점 냉장고에서 음료수(완제품)를 꺼내서 결제해 주는 점원 | 주문을 받으면 주방에서 신선한 재료로 요리를 해 주는 요리사 |

### 3) 실무 유즈케이스
실무에서는 보통 **[클라이언트 ➔ 웹 서버(Nginx) ➔ WAS(Tomcat/Express)]**의 구조로 연동하여 사용합니다.

* **부하 분산 (Load Balancing)**: 앞단의 웹 서버가 수많은 사용자의 요청을 여러 대의 WAS로 고르게 분산하여 서버에 가해지는 부담을 덜어줍니다.
* **보안 강화 (Reverse Proxy)**: 외부 사용자가 실제 비즈니스 로직과 데이터베이스가 존재하는 WAS의 IP를 직접 알지 못하게 막아주는 강력한 일차 방어선 역할을 합니다.
* **정적 파일 효율화**: 변하지 않는 이미지, CSS, JS 등의 파일은 바쁜 WAS까지 요청을 보내지 않고 앞단의 웹 서버가 직접 캐싱하여 반환함으로써 WAS가 비즈니스 로직 연산에만 집중할 수 있게 합니다.
* **무중단 배포**: WAS를 업데이트하거나 점검할 때, 앞단의 웹 서버가 사용자 요청을 다른 정상 WAS로 유연하게 우회시켜 줌으로써 서비스 중단 없이 배포를 완료할 수 있습니다.

### 4) 관련 키워드
`Nginx` / `Apache HTTP Server` / `IIS` / `Apache Tomcat` / `Node.js (Express)` / `Django` / `Spring Boot` / `정적 콘텐츠(Static Content)` / `동적 콘텐츠(Dynamic Content)` / `리버스 프록시(Reverse Proxy)` / `포워드 프록시(Forward Proxy)` / `로드 밸런싱(Load Balancing)` / `무중단 배포(Blue-Green, Rolling)` / `CGI(Common Gateway Interface)` / `WSGI(Web Server Gateway Interface)` / `SSL/TLS Termination(보안 인증서 앞단 처리)` / `Keep-Alive Connection` / `PM2(Node 프로세스 관리)`

[🔝제일 위로](#)

---

# 📄RDBMS(SQL) vs NoSQL

### 1) 10초 요약
* **RDBMS (관계형 데이터베이스)**: 엑셀 표처럼 행(Row)과 열(Column)로 정해진 엄격한 규칙(스키마)에 맞추어 데이터를 꼼꼼하고 일관되게 저장하며, SQL을 사용하여 정교한 관계 정의와 트랜잭션을 보장합니다.
* **NoSQL (비관계형 데이터베이스)**: 정해진 스키마 없이 JSON(Document), Key-Value, Graph 등 매우 자유롭고 유연한 형태로 데이터를 쌓아 나가며, 대용량 데이터의 수평적 확장(Scale-out)이 용이합니다.

### 2) 핵심 비교
| 구분 | RDBMS (SQL) | NoSQL (Non-SQL) |
| :--- | :--- | :--- |
| **데이터 구조** | 테이블(Table) 형식 (행과 열) | Document(JSON), Key-Value, Column-Family, Graph 등 |
| **스키마(Schema)** | 고정된(Strict) 스키마 필요, 데이터 타입 및 제약 조건 엄격함 | 동적(Dynamic) 스키마, 수시로 필드를 추가하거나 변경 가능 |
| **핵심 가치 / 목표** | 데이터의 일관성, 무결성, 정밀한 트랜잭션 보장 (ACID) | 높은 읽기/쓰기 성능, 대용량 분산 처리, 유연한 데이터 확장성 (BASE) |
| **확장 방식** | 수직적 확장 (Scale-up) - 서버 자체의 스펙 업 | 수평적 확장 (Scale-out) - 여러 저렴한 서버를 클러스터링 |
| **대표 기술** | MySQL, PostgreSQL, Oracle, SQL Server, MariaDB | MongoDB, Redis, Cassandra, DynamoDB, Neo4j |
| **직관적 비유** | 규격과 칸이 정확하게 나뉜 잘 정리된 **약 상자**나 **수납함** | 필요한 물건을 자유롭게 넣고 뺄 수 있는 깊고 넓은 **정리용 바구니** |

### 3) 실무 유즈케이스
실무 프로젝트를 설계할 때 데이터베이스 선정은 비즈니스의 성격에 따라 결정됩니다.

* **RDBMS를 선택해야 하는 비즈니스 시나리오 (예: 결제 시스템, 전자상거래 주문 처리, 회원가입 정보)**
  - **데이터 무결성이 최우선인 경우**: 통장 잔액 정보나 결제 내역처럼 1원이라도 오차가 생기면 치명적인 금융, 주문 데이터는 정교한 트랜잭션(ACID) 처리가 필요합니다.
  - **관계의 깊이가 깊고 복잡한 경우**: 다대다(N:M)나 일대다(1:N) 구조가 복잡하게 얽혀 있고, 이를 조인(JOIN) 연산을 통해 정확하게 뽑아내야 하는 핵심 비즈니스 로직에는 RDBMS가 최고의 성능과 신뢰성을 보여줍니다.

* **NoSQL을 선택해야 하는 비즈니스 시나리오 (예: SNS 피드, 실시간 채팅 로그, 대용량 알림 시스템, 장바구니)**
  - **데이터 구조가 자주 변하는 경우**: 스타트업이나 초기의 빠른 서비스 개발 과정에서 비즈니스 요구사항에 따라 데이터 필드(구조)가 매주 업데이트되어야 할 때, 복잡한 마이그레이션 없이 데이터를 즉시 적재할 수 있습니다.
  - **대규모 트래픽과 읽기/쓰기 속도가 중요한 경우**: 실시간으로 초당 수만 건씩 쏟아지는 로그, 채팅, 알림 데이터는 꼼꼼히 검사하고 조인하는 연산 속도보다, 일단 디스크나 메모리에 유실 없이 초고속으로 때려 넣고 분산 처리하는 NoSQL(MongoDB, Cassandra 등)이 강력한 효율을 냅니다.
  - **수평적 확장(Scale-out)이 필수인 경우**: 트래픽의 변동이 극심하여 서버 여러 대를 병렬로 늘려야 할 때, NoSQL은 처음부터 대규모 분산 환경을 고려하여 설계되었으므로 비교적 손쉽게 스케일 아웃할 수 있습니다.

### 4) 관련 키워드
`MySQL` / `PostgreSQL` / `Oracle` / `MongoDB` / `Redis` / `Cassandra` / `DynamoDB` / `ACID (Atomicity, Consistency, Isolation, Durability)` / `BASE (Basically Available, Soft state, Eventual consistency)` / `CAP 이론 (Consistency, Availability, Partition tolerance)` / `트랜잭션(Transaction)` / `조인 연산(JOIN)` / `인덱싱(Indexing)` / `정규화(Normalization)` / `역정규화(Denormalization)` / `수직 확장(Scale-up)` / `수평 확장(Scale-out)` / `샤딩(Sharding)` / `복제(Replication)` / `클러스터링(Clustering)` / `Schema-less`

[🔝제일 위로](#)

---

# 📄인증(Authentication) vs 인가(Authorization)

### 1) 10초 요약
* **인증 (Authentication, AuthN)**: "당신은 누구십니까?"를 확인하는 과정으로, 아이디/비밀번호 입력이나 생체 인식 등을 통해 사용자의 신원(Identity)을 증명하는 절차입니다.
* **인가 (Authorization, AuthZ)**: "당신은 이 행동을 할 권한이 있습니까?"를 결정하는 과정으로, 이미 인증된 사용자가 특정 리소스에 접근하거나 특정 기능을 수행할 수 있는 권한을 확인하는 절차입니다.

### 2) 핵심 비교
| 구분 | 인증 (Authentication) | 인가 (Authorization) |
| :--- | :--- | :--- |
| **주요 목적** | 사용자가 주장하는 신원이 진짜인지 검증 | 인증된 사용자가 특정 리소스에 접근할 권한이 있는지 검증 |
| **핵심 질문** | "당신은 누구입니까? (Who are you?)" | "당신은 무엇을 할 수 있습니까? (What can you do?)" |
| **주요 수단** | ID/PW, OTP, 생체 정보(FaceID/지문), SNS 소셜 로그인 | 권한 등급(Role: User, Admin), Access Token, ACL(접근 제어 리스트) |
| **동작 순서** | 항상 인가보다 **먼저** 이루어져야 함 (1단계) | 인증이 **완료된 후**에만 진행될 수 있음 (2단계) |
| **실패 시 응답** | `401 Unauthorized` (신원 확인 실패 - 로그인이 안 되었거나 잘못됨) | `403 Forbidden` (신원은 확인되었으나 해당 행위 권한이 없음) |
| **직관적 비유** | 회사 정문에 들어갈 때 경비원에게 **사원증**을 보여주고 신원을 확인받아 건물 안으로 입장하는 것 | 건물 안에서 일반 직원이 임원실이나 서버실 문을 열려고 할 때, **지문 인식기**가 출입 권한을 체크하고 차단하는 것 |

### 3) 실무 유즈케이스
실무 개발 과정에서 인증과 인가는 보안 설계의 뼈대를 이루며, 다음과 같은 시나리오에서 긴밀하게 맞물려 작동합니다.

* **웹 애플리케이션의 일반적인 보안 흐름**
  - **인증 단계 (로그인)**: 사용자가 아이디와 비밀번호를 입력하면, 백엔드 서버는 DB에서 회원을 조회하여 일치 여부를 검증하고 세션 ID나 JWT(JSON Web Token)를 발급하여 클라이언트에게 돌려줍니다.
  - **인가 단계 (마이페이지 접근 및 게시글 수정)**: 로그인을 마친 일반 회원(User)이 관리자 페이지(`/admin`)에 진입하려고 시도합니다. 스프링 시큐리티나 Passport.js 같은 프레임워크의 미들웨어는 사용자의 토큰/세션에서 유저 권한(Role)을 확인한 뒤, 권한이 일반 회원에 불과하므로 해당 요청을 차단하고 `403 Forbidden` 에러를 응답합니다.

* **OAuth 2.0 프로토콜에서의 인증과 인가**
  - 우리가 자주 접하는 "구글로 로그인하기", "카카오로 로그인하기"는 단순한 로그인을 넘어 **인가(Authorization)**의 핵심 개념이 잘 드러난 기술입니다.
  - 사용자는 구글 서버에서 본인을 **인증**합니다. 이후 구글은 우리 서비스(3rd Party App)에 사용자의 이름이나 이메일을 조회할 수 있는 '권한(Access Token)'을 부여하는 **인가** 결정을 내리게 됩니다.

### 4) 관련 키워드
`Authentication` / `Authorization` / `HTTP 401 Unauthorized` / `HTTP 403 Forbidden` / `Session-based Auth` / `JWT (JSON Web Token)` / `OAuth 2.0` / `OpenID Connect (OIDC)` / `RBAC (Role-Based Access Control)` / `ABAC (Attribute-Based Access Control)` / `Multi-Factor Authentication (MFA)` / `Single Sign-On (SSO)` / `Access Token` / `Refresh Token` / `Spring Security` / `Passport.js` / `ACL (Access Control List)`

[🔝제일 위로](#)

---

## 📄 [JWT vs 세션(Session) 인증]

### 1) 10초 요약
* 세션 인증은 서버가 상태를 관리하는 '클럽 회원 명부' 방식입니다.
* JWT 인증은 클라이언트가 인증 정보를 직접 들고 다니는 '디지털 통행증' 방식입니다.

### 2) 핵심 비교

| 비교 기준 | 세션 인증 (Session) | JWT 인증 (Token) |
| :--- | :--- | :--- |
| 상태 관리 | Stateful (서버가 세션 정보 저장) | Stateless (토큰 자체에 정보 포함) |
| 저장 위치 | 서버 메모리 또는 DB (Redis) | 클라이언트 (쿠키, 로컬스토리지 등) |
| 보안성 | 탈취되어도 서버에서 무효화 가능 | 탈취 시 만료 전까지 제어 어려움 |
| 확장성 | 서버 분산 시 세션 공유 필요 | 서버 확장에 매우 자유로움 |
| 직관적 비유 | 클럽 입구에서 명단 확인 | 디지털 통행증을 직접 보여줌 |

### 3) 실무 유즈케이스
- **세션 인증**: 은행 앱, 사내 인트라넷, 실시간으로 사용자의 상태를 강제 로그아웃 시켜야 하거나 엄격한 보안 관리가 필요한 서버 중심 아키텍처 환경에서 주로 사용합니다.
- **JWT 인증**: 마이크로서비스 아키텍처(MSA), API 기반 서비스, 모바일 애플리케이션 등 서버가 사용자의 상태를 기억하지 않아야 하거나, 여러 서비스 간의 인증 정보를 공유해야 하는 확장성이 중요한 환경에서 주로 사용합니다.

### 4) 관련 키워드
`Cookie` / `Redis` / `Stateless` / `Stateful` / `OAuth` / `Refresh Token` / `Access Token` / `Payload` / `Header` / `Signature` / `XSS` / `CSRF` / `Scalability` / `Session ID` / `HMAC` / `RS256` / `Authorization Header` / `Bearer` / `Authentication` / `Authorization`

[🔝제일 위로](#)

---

# 📄 CSR(Client-Side Rendering) vs SSR(Server-Side Rendering)

### 1) 10초 요약
* **CSR**은 브라우저(클라이언트)가 자바스크립트를 다운로드받아 화면을 직접 동적으로 그리는 방식이며, 초기 로딩은 느리지만 첫 페이지 진입 후 화면 전환이 매우 매끄럽고 빠릅니다.
* **SSR**은 서버에서 데이터가 채워진 완성형 HTML을 미리 빌드하여 브라우저에 전달하는 방식이며, 초기 페이지 로딩 속도가 빠르고 검색 엔진 최적화(SEO)에 매우 유리합니다.

### 2) 핵심 비교

| 구분 | CSR (Client-Side Rendering) | SSR (Server-Side Rendering) |
| :--- | :--- | :--- |
| **렌더링 주체** | 브라우저 (클라이언트) | 웹 서버 (Server) |
| **동작 방식** | 비어 있는 HTML과 JS 번들을 받은 후, 브라우저에서 JS를 실행해 DOM을 그림 | 서버에서 데이터 패칭을 완료하고 완성된 HTML을 생성하여 브라우저에 송신 |
| **초기 로딩 속도 (TTV)** | 상대적으로 느림 (대용량 자바스크립트가 로드 및 실행되어야 화면이 보임) | 매우 빠름 (브라우저가 HTML을 받는 즉시 화면에 렌더링됨) |
| **인터랙션 가능 시점 (TTI)** | 화면이 보이는 순간 바로 인터랙션 가능 (TTV = TTI) | 화면이 보인 후, JS가 로드되고 연결(Hydration)되는 과정이 필요함 (TTV > TTI) |
| **페이지 이동 속도** | 매우 빠르고 매끄러움 (필요한 데이터만 API로 호출하여 부분 렌더링) | 느림 (새로운 페이지를 이동할 때마다 서버에 새 HTML을 요청함) |
| **검색 엔진 최적화 (SEO)** | 어려움 (크롤러가 JS를 실행하지 못하면 빈 HTML만 수집하게 됨) | 매우 수월함 (검색 엔진 크롤러가 텍스트와 메타데이터가 담긴 HTML을 즉시 읽음) |
| **서버 자원 소모** | 낮음 (서버는 정적 파일만 호스팅하고, 연산은 사용자 기기에서 처리) | 높음 (동시 접속자가 많을수록 서버가 매번 HTML을 빌드해야 하므로 부하 가중) |
| **대표적인 도구** | `React`, `Vue.js`, `Angular` (Vite, Create React App 등의 빌드 도구 활용) | `Next.js`, `Nuxt`, `SvelteKit` (서버 렌더링 엔진 내장 프레임워크) |
| **직관적 비유** | **밀키트 홈쿡** 🍳<br>집(브라우저)으로 재료와 조리법(JS)을 배송받아 직접 요리해서 먹습니다. 준비 과정은 번거롭지만, 한 번 요리를 시작하면 내 입맛대로 자유롭게 추가(동적 인터랙션)하기 좋습니다. | **완성품 배달** 🛵<br>전문 셰프(서버)가 완벽하게 조리해 둔 음식(HTML)을 집으로 바로 배달받아 먹습니다. 도착하자마자 바로 먹을 수 있어 아주 편리하지만, 다른 음식을 먹고 싶다면 매번 새로 주문해야 합니다. |

---

### 3) 실무 유즈케이스

* **CSR 방식을 채택하는 든든한 시나리오**
  * **사내 어드민 페이지 및 대시보드**: 외부 노출이 필요 없고, 권한을 가진 임직원들만 로그인하여 복잡한 차트와 실시간 데이터를 조회하는 서비스는 CSR이 정답입니다. 초기 로딩 속도보다는 사용자가 한 번 로그인한 후 깜빡임 없이 빠르고 스마트하게 데이터를 조작하는 인터랙션이 훨씬 중요하기 때문입니다.
  * **개인화된 SNS 마이페이지**: 사용자의 개인 정보나 마이페이지처럼 다른 사람들에게 공유되거나 검색 엔진에 노출될 필요가 전혀 없는 영역 역시 클라이언트 기기에 렌더링 책임을 위임하는 것이 서버 비용을 절감하는 똑똑한 선택입니다.

* **SSR 방식을 채택하는 필수적인 시나리오**
  * **이커머스(쇼핑몰) 플랫폼**: 소비자가 포털 사이트에서 상품을 검색했을 때 우리 쇼핑몰의 상품 페이지가 검색 결과 상단에 노출(SEO)되어야 하고, 링크를 클릭하자마자 상품 정보가 즉시 눈에 보여야 이탈률을 막을 수 있습니다. 따라서 이커머스 홈과 상품 상세 페이지는 SSR이 절대적으로 강력합니다.
  * **뉴스 및 블로그 미디어**: 최신 뉴스와 블로그 포스팅은 검색 엔진 크롤러가 수시로 긁어가야 하며, SNS로 링크를 공유했을 때 미리보기 카드(Open Graph)가 즉각적으로 이쁘게 노출되어야 하므로 SSR이 필수적입니다.

---

### 4) 관련 키워드

`SPA` / `MPA` / `SEO` / `TTV (Time To View)` / `TTI (Time To Interactive)` / `Hydration` / `Next.js` / `Vite` / `Static Site Generation (SSG)` / `Web Vitals` / `FCP (First Contentful Paint)` / `LCP (Largest Contentful Paint)` / `Client Component` / `Server Component` / `Prerendering` / `Search Engine Crawler`

[🔝제일 위로](#)

---


# 📄 레디스(Redis) vs 일반 관계형 데이터베이스(RDB)

### 1) 10초 요약
* **레디스(Redis)**는 메모리(RAM)에 데이터를 키-값 형태로 저장하는 인메모리 저장소로, 밀리초 미만의 극도로 빠른 응답 속도를 제공하지만 장비가 꺼지면 데이터가 유실될 수 있는 휘발성을 가집니다.
* **일반 관계형 데이터베이스(RDB)**는 디스크(HDD/SSD)에 데이터를 표 형태로 영구히 저장하는 저장소로, 엄격한 데이터 정합성(ACID)과 복잡한 관계 분석을 제공하지만 디스크 입출력 속도 한계가 존재합니다.

### 2) 핵심 요약
| 구분 | 레디스 (Redis) | 일반 관계형 데이터베이스 (RDB) |
| :--- | :--- | :--- |
| **주요 역할** | 캐시(Cache), 세션 상태 공유, 고속 대기열 처리 및 실시간 카운팅 | 비즈니스 중요 데이터의 영구 저장, 복잡한 트랜잭션 일관성 보장 |
| **저장 매체 & 속도** | **인메모리 (RAM)**: 전송 지연이 없어 읽기/쓰기가 극도로 빠름 | **디스크 (HDD/SSD)**: 디스크 읽기/쓰기(I/O) 병목으로 상대적으로 느림 |
| **데이터 구조** | Key-Value 기반 (Strings, Lists, Sets, Hashes, Sorted Sets 등 지원) | 행(Row)과 열(Column)로 이뤄진 2차원 테이블 (엄격한 Schema 정의) |
| **동작 방식 & 쿼리** | 싱글 스레드 이벤트 루프 구조, 심플한 Key 기반 명령어 호출 | 멀티 스레드 구조, SQL 질의 언어를 통한 복잡한 조인(Join)과 필터링 |
| **대표 기술** | Redis, Memcached, KeyDB | MySQL, PostgreSQL, Oracle, MS-SQL |
| **직관적 비유** | 책상 위에 붙여두고 바로 떼어 쓰는 **'포스트잇 메모장'** (매우 신속하나 기록 공간과 안정성에 한계가 있음) | 서재 책장에 일목요연하게 꽂아둔 **'백과사전 세트'** (원하는 문장을 찾으러 가기는 번거로우나 방대한 정보가 영구히 보호됨) |
| **장점** | 압도적인 처리량(TPS), 밀리초 이하의 레이턴시, 유연한 내장 자료구조 | 데이터의 영구 소실 방지, 복잡한 데이터 간 무결성 및 관계 유지 능력 |
| **단점** | RAM 용량의 제약으로 메모리 비용이 비쌈, 비정상 종료 시 유실 가능성 | 대규모 동시 트래픽 유입 시 디스크 I/O 병목으로 웹 서버 전체가 중단될 위험 |

### 3) 실무 유즈케이스
- **레디스 (Redis) 채택 시나리오**
  - **공통 세션 저장소 (Session Clustering)**: 여러 대의 애플리케이션 서버로 부하를 분산(Load Balancing)할 때, 어떤 서버로 접속하더라도 로그인 상태가 유지되도록 공통으로 접속하는 세션 데이터베이스로 사용합니다.
  - **읽기 위주의 캐싱 (Cache Aside)**: 메인 페이지 배너 정보, 베스트셀러 상품, 자주 묻는 질문(FAQ) 등 변경 빈도는 낮고 조회수가 압도적으로 높은 데이터를 레디스에 미리 올려두어 RDB의 트래픽 부담을 덜어줍니다.
  - **실시간 대기열 및 랭킹보드**: 수강신청, 선착순 티켓팅 시 밀려드는 트래픽을 순서대로 정렬하기 위해 레디스의 Sorted Set(ZSET) 자료구조를 활용해 실시간 랭킹과 대기 번표를 초고속으로 발급합니다.

- **일반 관계형 데이터베이스 (RDB) 채택 시나리오**
  - **전자 상거래 결제 및 주문 처리**: 결제 승인, 포인트 차감, 주문 내역 생성 등 연쇄적인 작업이 모두 완벽히 성공하거나 혹은 실패해야 하는 엄격한 금융/이커머스 결제 로직에 트랜잭션(ACID)을 적용하기 위해 사용합니다.
  - **연관 데이터가 복잡한 계정 구조**: 회원 기본 정보, 권한 그룹, 마케팅 수신 동의 정보 등 서로가 얽혀 있어 외래키(Foreign Key)나 JOIN을 통한 유효성 검증과 통계 산출이 필수적인 데이터 설계에 활용합니다.

- **실무 베스트 프랙티스 (시너지 조합)**
  - 현업에서는 두 데이터베이스를 대립 구조가 아닌 **보완 관계**로 결합하여 사용합니다. 
  - 클라이언트가 데이터 요청 시, 먼저 빠르고 가벼운 **레디스**를 확인합니다. 캐시에 찾는 데이터가 있다면(Cache Hit) RDB에 거치지 않고 초고속 응답을 반환합니다. 만약 데이터가 없다면(Cache Miss) **RDB**에서 정교하게 가공된 데이터를 꺼내와 클라이언트에 제공하면서 동시에 **레디스**에 저장하여 다음 요청부터는 캐시를 타게 만듭니다.

### 4) 관련 키워드
`In-Memory` / `RAM vs Disk` / `Cache-Aside Pattern` / `Write-Through Pattern` / `ACID Transactions` / `Single-Threaded Event Loop` / `Key-Value Store` / `Sorted Set (ZSET)` / `Data Persistence (RDB/AOF)` / `Session Clustering` / `Database Bottleneck` / `Replica & Sentinel` / `Redis Cluster` / `Cache Hit & Cache Miss` / `TTL (Time To Live)` / `Eviction Policy` / `Connection Pooling`

[🔝제일 위로](#)

---


# 📄 CI/CD (지속적 통합 / 지속적 제공 및 배포)

### 1) 10초 요약
* **CI(지속적 통합)**는 개발자들이 작성한 코드를 중앙 저장소에 자주 병합(Merge)하고, 자동으로 빌드 및 테스트를 수행하여 잠재적 코드 충돌과 버그를 조기에 걸러내는 기법입니다.
* **CD(지속적 제공/배포)**는 통합이 완료된 고품질 코드를 배포 직전 단계까지 항상 자동 준비(Delivery)해두거나, 최종 운영 환경까지 자동으로 직접 릴리스(Deployment)하는 일련의 파이프라인입니다.

### 2) 핵심 요약
| 구분 | 지속적 통합 (CI - Continuous Integration) | 지속적 제공 및 배포 (CD - Continuous Delivery & Deployment) |
| :--- | :--- | :--- |
| **주요 역할** | 코드 변경분 수집, 빌드 유효성 검증, 자동화 테스트 및 코드 정적 분석 | 빌드된 아티팩트를 스테이징 또는 운영 환경에 안정적으로 전달 및 무중단 배포 |
| **핵심 목표** | "코드 병합 헬(Merge Hell) 방지" 및 "소프트웨어 결함 조기 탐지" | "배포 휴먼 에러(수동 파일 업로드 등) 제로화" 및 "신속한 가치 전달" |
| **동작 흐름** | 개발자 Git Push → PR 생성 → CI 파이프라인 자동 실행 → 빌드 및 린터 체크 → 유닛/통합 테스트 구동 → 빌드 성공 시 머지 | Git Merge 발생 → CD 파이프라인 작동 → 빌드 파일 패키징 (Dockerizing 등) → 아티팩트 레지스트리 저장 → 가상머신/쿠버네티스 롤아웃 |
| **대표 기술** | Jenkins, GitHub Actions, GitLab CI/CD, CircleCI, SonarQube | Jenkins, ArgoCD, AWS CodeDeploy, Ansible, Octopus Deploy |
| **직관적 비유** | 공장 부품 생산 라인 끝자락에서 조립이 잘 되는지 규격을 상시 검사하는 **'자동 불량품 적출기'** | 합격된 최종 완성품을 창고로 포장 이송하거나(Delivery), 무인 배송 로봇이 고객 집 앞까지 완벽히 전송하는 **'자율 배송 시스템'** (Deployment) |
| **장점** | 개발 주기의 가속화, 안정적 소스코드 상태 유지, 리팩토링 및 신규 도입에 대한 자신감 부여 | 수동 릴리스 부담 소멸, 무중단 배포를 통한 다운타임 제거, 즉각적 장애 시 롤백 기능 제공 |
| **단점** | 초기 파이프라인 빌드 스크립트 작성 부담, 촘촘한 테스트 코드 작성에 필요한 러닝 커브 | 배포 파이프라인에 오류가 있을 경우 예기치 못한 전체 장애 초래 가능 (강력한 가시성 필요) |

### 3) 실무 유즈케이스
- **지속적 통합 (CI)의 실무 적용 시나리오**
  - **Pull Request(PR) 빌드 장벽**: 여러 개발자가 동시에 협업하는 도중 한 개발자가 실수로 컴파일이 깨지는 코드나 테스트를 통과하지 못하는 커밋을 올렸을 때, GitHub Actions가 `Test Failed`를 감지하여 메인 브랜치로의 머지 버튼을 원천 비활성화(Block)합니다. 덕분에 배포 브랜치는 언제나 빌드 가능한 '청정 상태'가 보장됩니다.
  - **코드 정적 분석과 보안 검사**: 정적 코드 분석 도구(SonarQube 등)를 CI 파이프라인에 이식하여, 보안 취약점(Secret Key 유출 여부 등)이나 코드 스멜(Code Smell), 중복 코드 비율 등을 자동으로 스캔하고 기준치 미달 시 배포 단계를 조기 종료시킵니다.

- **지속적 제공 및 배포 (CD)의 실무 적용 시나리오**
  - **개발/운영 분리 자동화 (Continuous Delivery)**: `main` 브랜치 머지 완료 시 개발(Dev) 서버에는 즉각 자동으로 배포되지만, 사용자들이 결제하고 있는 운영(Prod) 서버는 비즈니스 임팩트를 고려해 PM이나 QA 리더가 배포 대시보드에서 '승인(Approve)' 버튼을 누를 때만 배포가 들어가도록 수동 제어 게이트를 설정할 때 활용합니다.
  - **GitOps 환경에서 선언적 무중단 배포 (Continuous Deployment)**: 쿠버네티스 환경에서 ArgoCD를 활용해 Git 리포지토리의 배포 manifest 설정이 바뀔 때마다 실시간으로 클러스터에 **카나리(Canary)** 또는 **블루-그린(Blue-Green)** 무중단 기법으로 자동 업데이트를 적용하여 사용자 체감 끊김 없이 안전하게 배포합니다.

- **실무 베스트 프랙티스 (전체 파이프라인 구조)**
  - 현대의 민첩한 풀스택 팀에서는 **"개발 완료 → PR → CI 검증 및 리뷰 → 머지 → CD 패키징 → 개발 서버 자동 배포 → QA 테스트 승인 → 운영 서버 승인 배포"**라는 긴밀히 연결된 단일 파이프라인을 구축하여, 하루에도 수십 번 안전하게 비즈니스 가치를 배포해 낼 수 있는 능력을 확보하는 것을 목표로 삼습니다.

### 4) 관련 키워드
`CI/CD Pipeline` / `Jenkins` / `GitHub Actions` / `GitLab CI` / `ArgoCD (GitOps)` / `Lint & Static Analysis` / `Unit & Integration Testing` / `Build Automation` / `Docker & Containerization` / `Artifact Registry` / `Blue-Green Deployment` / `Canary Deployment` / `Rolling Update` / `Infrastructure as Code (IaC)` / `Webhook` / `Rollback` / `Regression Testing` / `Zero-Downtime Deployment`

[🔝제일 위로](#)

---


# 📄 브라우저 쿠키(Cookie) vs 로컬 스토리지(Local Storage) vs 세션 스토리지(Session Storage)

### 1) 10초 요약
* **쿠키(Cookie)**는 매 HTTP 요청마다 서버로 자동 전송되는 4KB 이하의 초소형 저장소로, 세션 식별 및 토큰 보관 등 주로 서버 연동 보안 시나리오에 활용됩니다.
* **웹 스토리지(로컬/세션)**는 서버로 자동 전달되지 않는 5MB 이하의 넉넉한 클라이언트 단 저장소로, 만료 없이 영구 보존하는 **로컬 스토리지**와 탭/창 종료 시 파괴되는 **세션 스토리지**로 분류됩니다.

### 2) 핵심 요약
| 구분 | 브라우저 쿠키 (Cookie) | 로컬 스토리지 (Local Storage) | 세션 스토리지 (Session Storage) |
| :--- | :--- | :--- | :--- |
| **주요 역할** | 상태 비보존(Stateless)인 HTTP 환경에서 클라이언트 식별 및 세션 유지 | 브라우저가 종료되어도 지워지지 않아야 하는 클라이언트 편의성 데이터 보관 | 현재 열려 있는 탭/창 내에서만 일시적으로 유지하면 되는 임시 데이터 기록 |
| **저장 용량** | **초소형** (도메인당 약 4KB 이내 제한) | **대용량** (도메인당 약 5MB ~ 10MB 권장) | **대용량** (도메인당 약 5MB ~ 10MB 권장) |
| **만료 기한** | 개발자가 설정한 `Expires` 또는 `Max-Age` 시각까지 유지 | 명시적으로 코드로 삭제하거나 브라우저 캐시를 날리지 않으면 **영구 보존** | **브라우저 탭 또는 창이 닫히는 즉시 자동 소멸** |
| **서버 전송 여부** | 매 HTTP API 요청 시 헤더에 실려 **서버로 항상 자동 전송됨** | **서버로 전혀 자동 전송되지 않음** (네트워크 트래픽 부담 없음) | **서버로 전혀 자동 전송되지 않음** (네트워크 트래픽 부담 없음) |
| **대표 기술 & API** | `document.cookie` 및 백엔드 `Set-Cookie` 헤더 활용 | `localStorage.setItem('key', 'value')`, `getItem()` | `sessionStorage.setItem('key', 'value')`, `getItem()` |
| **직관적 비유** | 카페에서 나갈 때 카운터 직원이 옷깃에 붙여주는 **'인증 스티커'** (카페 내 다른 부스에 갈 때마다 직원이 상시 식별함) | 개인 책상 깊숙한 전용 서랍 속의 **'일기장'** (서랍을 비우거나 버리지 않는 한 이사 가도 계속 보존됨) | 도서관 열람실 자리에 잠시 붙여두는 **'포스트잇 쪽지'** (공부 도중에는 유효하나, 자리를 정리하고 일어서는 순간 버려짐) |
| **장점** | 서버 제어 가능(`HttpOnly`를 통한 XSS 공격 방어, `Secure`를 통한 암호화 전송 등 보안 제어 수월) | 넉넉한 공간 제공, 여러 탭/창 간 데이터 공유 가능, 네트워크 오버헤드가 없음 | 탭 단위 완벽한 격리(동일 사이트라도 다중 탭 혼선 차단), 탭 종료 시 잔여 찌꺼기 데이터가 자동 파괴됨 |
| **단점** | 불필요하게 요청마다 헤더에 실리므로 트래픽을 차지함, `SameSite` 미설정 시 CSRF 공격에 노출됨 | 자바스크립트로 상시 평문 조회가 가능하여 XSS(크로스 사이트 스크립팅) 공격 시 데이터 유출 위험성이 매우 높음 | 다른 탭이나 창에서 데이터를 불러올 방법이 전혀 없어 범용 상태 유지 기능으로 부적합 |

### 3) 실무 유즈케이스
- **브라우저 쿠키 (Cookie) 채택 시나리오**
  - **로그인 인증 키 (Session ID / JWT 토큰)**: 로그인한 사용자의 로그인 상태를 신뢰성 있게 지속시킬 때 주로 씁니다. 브라우저에서 토큰을 자바스크립트로 절대 훔쳐 갈 수 없도록 `HttpOnly` 옵션을 켜서 스토리지 해킹(XSS)을 방어하고, HTTPS에서만 도는 `Secure`, 크로스 사이트 공격을 막아주는 `SameSite=Lax` 속성 등을 종합 적용해 안전하게 서버와 인증 상태를 조율합니다.
  - **비로그인 '하루 동안 이 팝업창 보지 않기' 설정**: 쿠키의 파기 시간(Expires)을 금일 23시 59분 59초로 설정하여, 당일이 지나기 전까지는 브라우저가 자동으로 팝업을 가리게 설정할 때 가장 유용합니다.

- **로컬 스토리지 (Local Storage) 채택 시나리오**
  - **다크 모드 & UI 개인화 테마**: 사용자가 라이트 모드/다크 모드를 선택했거나 글꼴 크기를 수정한 값을 보존할 때 씁니다. 다음 날 브라우저를 다시 켜더라도 서버 통신 없이 이전 상태를 매끄럽게 재현할 수 있습니다.
  - **아이디 기억하기 & 비회원 장바구니**: "아이디 기억하기" 체크 상태나 최근 검색했던 키워드 리스트, 그리고 비회원 상태에서 장바구니에 담아둔 상품 목록 리스트 등 영구적으로 클라이언트에 누적 보관하면서 즉각 활용해야 하는 데이터에 안성맞춤입니다.

- **세션 스토리지 (Session Storage) 채택 시나리오**
  - **다단계 예매 및 결제 폼 흐름**: 선착순 티켓팅이나 숙소 예약 단계(인원 선택 -> 숙소 선택 -> 결제 입력 -> 완료)에서 중간 입력값들을 임시로 적어둡니다. 사용자가 실수로 뒤로 가기를 가더라도 탭만 켜져 있다면 상태가 복구되며, 다중 탭을 띄우고 두 가지 예매를 동시에 하더라도 서로의 입력 폼 데이터가 꼬이는 사고를 원천 방지합니다.
  - **비디오 스트리밍 플레이어의 실시간 타임라인 복구**: 인강 등을 보다가 브라우저가 일시 새로고침되더라도 보던 지점부터 이어보기를 도와주되, 학습 환경(탭)을 이탈해 나가면 데이터를 파괴하고자 할 때 이상적입니다.

### 4) 관련 키워드
`Client-Side Storage` / `Web Storage API` / `HttpOnly Cookie` / `Secure Flag` / `SameSite Attribute` / `XSS (Cross-Site Scripting)` / `CSRF (Cross-Site Request Forgery)` / `JWT (JSON Web Token)` / `Session ID` / `document.cookie` / `localStorage` / `sessionStorage` / `Expires / Max-Age` / `Single-Sign-On (SSO)` / `Quota Exceeded Error` / `Window.sessionStorage`

[🔝제일 위로](#)

---


# 📄 MariaDB 실행계획(Execution Plan) 및 쿼리 튜닝

### 1) 10초 요약
* MariaDB 실행계획(`EXPLAIN`)은 옵티마이저가 쿼리를 수행하는 내부 경로를 보여주며, 이를 통해 전체 테이블 스캔(ALL)이나 비효율적인 조인 같은 성능 병목 구간을 탐색합니다.
* 쿼리 튜닝은 분석 결과를 바탕으로 인덱스 생성 및 변경, 쿼리문 구조 개선, 조인 순서 조정 등을 거쳐 데이터베이스 디스크 I/O를 최소화하고 실행 속도를 끌어올리는 과정입니다.

### 2) 핵심 요약

| 구분 | MariaDB 실행계획 분석 및 쿼리 튜닝 |
| :--- | :--- |
| **주요 역할** | 작성한 SQL 쿼리가 데이터베이스 내부에서 실제로 어떻게 실행되는지(인덱스 사용 여부, 검색 범위, 정렬 방식 등) 사전 진단하여 성능 저하의 원인을 찾는 도구 |
| **동작 방식** | - `EXPLAIN SELECT ...` : 옵티마이저가 예측한 가상의 실행 계획을 출력<br>- `EXPLAIN ANALYZE SELECT ...` : 쿼리를 실제로 실행하며 각 단계별 실시간 소요 시간과 처리된 행(Rows) 수를 측정 |
| **핵심 분석 항목** | - **type (접근 방식)**: `const` / `ref` / `range`는 안전하지만, `index`(인덱스 풀 스캔), `ALL`(테이블 풀 스캔)은 튜닝 대상<br>- **key**: 실제로 사용된 인덱스 이름 (선택되지 않았다면 인덱스 미적용 상태)<br>- **rows**: 쿼리를 수행하기 위해 접근할 것으로 예측되는 행의 수<br>- **Extra**: `Using filesort`(메모리/디스크 정렬), `Using temporary`(임시 테이블 생성)는 심각한 병목 요인 |
| **직관적 비유** | 운전하기 전, **내비게이션이 제안하는 추천 경로**(고속도로 vs 정체 구간 골목길)를 미리 확인하고 막히는 길을 우회하도록 목적지나 경유지를 최적화하는 과정 |
| **장점 및 튜닝 효과** | 서비스 규모 확장 시 데이터양이 증가해도 쿼리 응답 속도를 일정하게 유지할 수 있으며, CPU 및 디스크 I/O 자원 낭비를 막아 인프라 비용 절감 |

### 3) 실무 유즈케이스

#### 시나리오 A: 슬로우 쿼리 로그에서 발견된 전체 테이블 스캔(ALL) 튜닝
* **상황**: 가입자가 100만 명을 넘어선 서비스에서 "특정 날짜 범위의 주문 내역 조회" API 호출 시 3초 이상 지연되는 현상이 발생했습니다.
* **진단 (`EXPLAIN`)**: 
  ```sql
  EXPLAIN SELECT * FROM orders WHERE order_date BETWEEN '2026-07-01' AND '2026-07-15';
  ```
  - 분석 결과 `type`이 `ALL`로 나타났고, `key`는 `NULL`, `rows`는 `1,200,000`개에 달했습니다. 즉, 주문 테이블 전체를 처음부터 끝까지 다 읽고 있었습니다.
* **해결책 (인덱스 추가)**: `order_date` 컬럼에 단일 인덱스 혹은 자주 검색 조건으로 쓰이는 `user_id`와의 복합 인덱스를 생성합니다.
  ```sql
  CREATE INDEX idx_orders_date ON orders(order_date);
  ```
  - 인덱스 적용 후 다시 `EXPLAIN`을 돌려보니 `type`은 `range`로 개선되었고, `key`에는 `idx_orders_date`가 잡혔으며 `rows`가 단 `5,000`개로 급감해 성능이 0.01초대로 향상되었습니다.

#### 시나리오 B: 인덱스가 걸려있는데 타지 않는 범인 색출 (묵시적 형변환 및 함수 사용)
* **상황**: 회원 테이블의 휴대폰 번호(`phone_no`, VARCHAR 타입)에 분명 인덱스를 걸어두었는데, 검색 속도가 여전히 느립니다.
* **진단 (`EXPLAIN`)**:
  ```sql
  EXPLAIN SELECT * FROM users WHERE phone_no = 01012345678;
  ```
  - 분석해보니 분명히 인덱스가 있음에도 `type`이 `ALL`로 찍힙니다. 원인은 `01012345678`을 숫자형으로 넣으면서, MariaDB 내부적으로 문자열 타입인 `phone_no` 컬럼의 모든 값을 숫자로 변환하는 **묵시적 형변환**이 일어나 인덱스를 사용하지 못했기 때문입니다.
  - 또 다른 사례로 `WHERE YEAR(created_at) = 2026`처럼 좌변 컬럼을 가공하는 경우도 인덱스를 태우지 못합니다.
* **해결책 (쿼리 리팩토링)**:
  ```sql
  -- 1) 데이터 타입을 컬럼에 맞춤 (문자열 리터럴로 감싸기)
  EXPLAIN SELECT * FROM users WHERE phone_no = '01012345678';
  
  -- 2) 좌변 컬럼 가공 대신 우변 값을 범위로 조건 부여
  EXPLAIN SELECT * FROM users WHERE created_at >= '2026-01-01' AND created_at < '2027-01-01';
  ```
  - 수정한 후 다시 실행계획을 확인해보면 정상적으로 `type: ref` 또는 `type: range`로 인덱스를 타는 것을 볼 수 있습니다.

#### 시나리오 C: 대량의 조인(Join) 시 잘못된 드라이빙 테이블 선정 개선
* **상황**: 3개 테이블을 조인하는 통계 쿼리 실행 시 `EXPLAIN` 분석 중 드라이빙 테이블(먼저 읽는 테이블)이 엄청나게 큰 테이블로 잡혀 불필요한 루프를 돌고 있습니다.
* **진단 및 해결책 (`STRAIGHT_JOIN` 또는 인덱스 재정비)**:
  - 데이터양이 가장 적고 필터링 조건이 잘 먹히는 테이블이 먼저 읽혀야(Driving) 효율적입니다.
  - MariaDB 옵티마이저가 테이블 통계 정보를 오판하여 큰 테이블을 먼저 읽는 경우, `STRAIGHT_JOIN` 힌트를 사용하여 강제로 조인 순서를 제어하거나 테이블 통계치를 수동 갱신(`ANALYZE TABLE`)하여 스스로 최적의 경로를 찾게 유도합니다.

### 4) 관련 키워드
`EXPLAIN` / `EXPLAIN ANALYZE` / `Query Optimizer` / `Index Range Scan` / `Full Table Scan` / `Using filesort` / `Using temporary` / `Clustered Index` / `Secondary Index` / `Covering Index` / `B-Tree Index` / `Slow Query Log` / `Selectivity` / `Cardinality` / `Nested Loop Join` / `Driving Table` / `Driven Table` / `STRAIGHT_JOIN` / `Implicit Type Conversion`

[🔝제일 위로](#)

---