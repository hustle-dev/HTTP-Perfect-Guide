## 1장 HTTML 개관

> 전 세계의 웹 브라우저, 서버, 웹 앱은 모두 HTTP를 통해 서로 대화하며, <br>**HTTP는 현대 인터넷의 공용어이다.**

<br>

### HTTP: 인터넷의 멀티미디어 배달부

- HTTP는 신뢰성 있는 데이터 전송 프로토콜(`TCP`)을 사용한다.

<br>

### 웹 클라이언트와 서버

- 웹 콘텐츠는 웹 서버에 존재한다.
- 웹 서버는 HTTP 프로토콜로 의사소통하므로 보통 HTTP서버라고 불린다.
- 웹 서버는 인터넷의 데이터를 저장하고 HTTP 클라이언트가 요청한 데이터를 제공(응답)한다.
- 가장 흔한 HTTP 클라이언트는 웹 브라우저(Chrome, IE, Safari...)이다.
- 웹 서버는 요청 받은 객체를 찾고 성공했다면 타입, 길이 등의 정보를 응답한다.

<br>

### 리소스

- 웹 리소스는 웹 서버에서 관리하고 제공된다.
- 웹 리소스는 웹 콘텐츠의 원천이다.
- 웹 리소스는 정적 리소스와 동적 리소스가 있다.
- 정적 리소스는 `txt`, `html`, `jpg`, `img`, `avi` 등 모든 종류의 파일을 포함한다.
- 동적 리소스는 사용자가 누구인지, 어떤 정보를 요청했는지, 몇 시인지 등 정보에 따라 콘텐츠를 생성한다.
  - 카메라에서 라이브 영상 제공, 주식 거래, 부동산 DB 검색, 쇼핑몰에서 선물 구입등을 가능하게 해준다.

<br>

### 미디어 타입

> `MIME`: Multipurpose Internet Mail Extensions(다목적 인터넷 메일 확장)

- 인터넷은 수 많은 데이터 타입을 다루기 때문에 HTTP는 웹에서 전송되는 객체 각각에 `MIME` 타입이라는 데이터 포맷 라벨을 붙인다.
- `MIME`은 이메일에서 잘 동작했기 때문에, HTTP에서도 멀티미디어 콘텐츠를 기술하고 라벨을 붙이기 위해 채택되었다.
- 웹 브라우저는 서버로부터 객체를 돌려받을 때, 다룰 수 있는 객체인지 `MIME`타입을 통해 확인한다.
- `MIME`타입은 (`primary object type`/`specific subtype`)으로 이루어진 문자열 라벨이다.
  - `text/html`
  - `text/plain`
  - `image/jpeg`
  - `image/gif`
  - ...

<br>

### URI

> 통합 자원 식별자
> <br> `Uniform Resource Identifier` > <br> 정보 리소스를 고유하게 식별하고 위치를 지정할 수 있다.

- `URI`는 `URL`, `URN` 두가지가 있다.

#### URL

> 통합 자원 지시자
> <br> `Uniform Resource Locator` > <br> 한 리소스에 대한 구체적인 위치를 서술

스킴(`scheme`: 보통 HTTP 프로토콜), 서버의 인터넷 주소, 리소스로 이루어져 있다.

#### URN

> 유니폼 리소스 이름
> <br> `Uniform Resource Name` > <br> 콘텐츠를 이루는 한 리소스에 대해 위치에 영향 받지 않는 유일무이한 이름 역할

<br>

### 트랜잭션

> HTTP 트랜잭션은 요청 명령과 응답 결과로 구성되어 있다.

#### 메서드

> 메서드를 요청 명령에서 어떤 동작을 수행해야하는지 명시하는 것이다.

- GET: 서버에서 클라이언트로 지정한 리소스를 보내라
- PUT: 클라이언트에서 서버로 보낸 데이터를 지정한 이름의 리소르로 저장하라
- DELETE: 지정한 리소스를 서버에서 삭제하라
- POST: 클라이언트 데이터를 서버 게이트웨이 애플리케이션으로 보내라
- HEAD: 지정한 리소스에 대한 응답에서, HTTP 헤더 부분만 보내라

#### 상태 코드

> 상태 코드는 클라이언트에 요청이 성공했는지 추가 조치가 필요한지 알려주는 세 자리 숫자이고, 각각 텍스트로 된 사유구절을 함께 보낸다.

- 200: 좋다, 문서가 바르게 반환되었다.
- 302: 다시보내라, 다른 곳에 가서 리소스를 가져가라
- 404: 없음, 리소스를 찾을 수 없다.

#### 메시지

> HTTP 메시지란 단순한 줄 단위의 문자열
> <br> 클라이언트에서 서버로 보낸 HTTP 메시지를 요청 메시지,
> <br> 서버에서 클라이언트로 가는 HTTP 메시지를 응답 메시지라고 한다.
> <br> 메시지는 세 부분으로 구성되어 있다.

1. 시작줄

   - 요청 메시지: 무엇을 해야하는지
     <br>`GET /test/hi-there.txt HTTP/1.0`

   - 응답 메시지: 무슨일이 일어났는지
     <br>`HTTP/1.0 200 OK`

2. 헤더

   - 0개 이상의 헤더 필드가 있다.
   - 콜론(`:`)으로 키와 값을 구분한다.
   - 헤더 필드를 추가하려면 한 줄을 더하기만 하면 된다.
   - 헤더는 빈 줄로 끝난다.

3. 본문

   > 임의의 이진 데이터를 포함할 수 있다.(이미지, 비디오, 오디오 트랙 등등)

   - 요청 본문: 실어보낼 데이터를 담음
   - 응답 본문: 알맞은 데이터 반환

### TCP 커넥션

> HTTP는 애플리케이션 계층 프로토콜이기 때문에 네트워크 통신의 핵심적인 세부사항에 대해서는 신경쓰지 않는다.
> <br> 대신 대중적이고 신뢰성 있는 인터넷 전송 프로토콜인 TCP/IP에게 이 부분을 맡긴다.

#### TCP 특징

- 오류 없는 데이터 전송
- 순서를 보장하는 전달
- 조각나지 않는 데이터 스트림

#### HTTP 네트워크 프로토콜 스택

1. 애플리케이션 계층: HTTP
2. 전송 계층: TCP
3. 네트워크 계층: IP
4. 데이터 링크 계층: 네트워크를 위한 링크 인터페이스
5. 물리 계층: 물리적인 네트워크 하드웨어

#### 접속, IP주소, 포트번호

> HTTP 클라이언트가 서버에 메시지를 전송할 수 있게 되기 전에, 인터넷 프로토콜(IP)주소와 포트번호를 사용하여 클라이언트와 서버 사이에 TCP/IP 커넥션을 맺어야 한다.

#### HTTP를 이용하여 HTML 리소스를 클라이언트에게 제공하는 과정

1. 브라우저는 서버의 URL에서 호스트명 추출
2. 브라우저는 서버의 호스트 명을 IP로 변환
3. 브라우저는 URL에서 포트번호를 추출
4. 브라우저는 서버와 TCP 커넥션을 맺음
5. 브라우저는 서버에 HTTP 요청을 보냄
6. 서버는 브라우저에게 HTTP 응답을 돌려줌
7. 커넥션이 닫히면 브라우저는 문서를 보여줌

### 프로토콜 버전

#### HTTP/0.9

- 원래 간단한 HTML 객체를 받아오기 위해 만들어졌기 때문에 디자인 결함이 다수 있고, 구식 클라이언트하고만 사용할 수 있다.
- 오직 GET 메서드만 지원하고 멀티미디어에 대한 MIME 타입이나 HTTP 헤더, 버전 번호는 지원하지 않는다.

#### HTTP/1.0

- 처음으로 널리 쓰이기 시작한 HTTP 버전
- 버전 번호, HTTP 헤더, 추가 메서드, 멀티미디어 객체 처리 추가
- 잘 정의된 명세가 이니다.
- 상업적, 학술적으로 급성장하던 시기에 만들어진, 잘 동작하는 용례들의 모음에 가깝다.

#### HTTP/1.0+

- 커넥션을 오래 지속시키는 `keep-alive`커넥션, 가상 호스팅 지원, 프락시 연결지원을 포함해 많은 기능이 공식적이진 않지만 사실상의 표준으로 추가된 HTTP버전을 HTTP/1.0+라고 한다.

#### HTTP/1.1

- HTTP 설계의 구조적 결함 교정, 성능 최적화, 잘못된 기능 제거에 집중

#### HTTP/2.0

- HTTP/1.1의 성능 문제를 개선하기 위해 구글의 SPDY 프로토콜을 기반으로 설계가 진행 중인 프로토콜?(옛날 책이라서 제대로 정의되어 있지 않은듯)

### 웹의 구성요소

#### 프락시

> 클라이언트와 서버 사이에 위치한 HTTP 중개자

- 주로 보안을 위해 사용됨

#### 캐시

> 많이 찾는 웹페이지를 클라이언트 가까이에 보관하는 HTTP 창고

- 캐시 프락시는 자신을 거쳐가는 문서들 중 자주 찾는 것의 사본을 저장해두는 특별한 종류의 HTTP 프락시 서버

#### 게이트웨이

> 다른 애플리케이션과 연결된 특별한 웹 서버

- 주로 HTTP 트래픽을 다른 프로토콜로 변환하기 위해 사용됨
- 스스로가 리소스를 갖고 있는 진짜 서버인 것처럼 요청을 다룸

#### 터널

> 단순히 HTTP 통신을 전달하기만 하는 특별한 프락시

- 비 HTTP 데이터를 하나 이상의 HTTP 연결을 통해 그대로 전송해주기 위해 사용됨
- 대표적으로 암호화된 SSL 트래픽을 HTTP 커넥션으로 전송함으로서 웹 트래픽만 허용하는 사내 방화벽을 통과시킴
  - HTTP/SSL 터널은 HTTP 요청을 받아들여 목적지의 주소와 포트번호로 커넥션을 맺는다.
  - 이후 암호화된 SSL 트래픽을 HTTP 채널을 통해 목적지 서버로 전송할 수 있게 된다.

#### 에이전트

> 자동화된 HTTP 요청을 만드는 준지능적 웹 클라이언트

- 대표적으로 사용자를 위해 HTTP 요청을 만들어주는 애플리케이션은 모두 HTTP 에이전트이다.

<br>

---

## 2장 URL과 리소스

> URL은 인터넷의 리소스를 가리키는 표준이름
> <br> 전자정보 일부를 가리키고 그것이 어디에 있고 어떻게 접근할 수 있는 알려준다.
