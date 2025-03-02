# 9주차 HTTP 완벽가이드 정리

## 10장 | HTTP/2.0

### HTTP/2.0 의 등장 배경

HTTP/1.1의 메시지 포맷은 구현의 단순성과 접근성에 주안점을 두고 최적화되었다.<br>
때문에 성능을 어느정도 희생시키게 되었다.<br>
커넥션 하나를 통해 요청 하나를 보내고 그에 대해 응답 하나만을 받는 HTTP의 메시지 교환 방식은 단순하지만 응답을 받아야만 다음 요청을 보낼 수 있기 때문에 심각한 회전 지연을 피할 수 없었다.<br>

이 문제를 회피하기 위해 병렬 커넥션이나 파이프라인 커넥션ㅇ이 도입되었지만 성능 개선에 대한 근본적인 해결책은 되지 못했다.

이에 여러 프로토콜들이 대안으로 제시되었는데 대표적으로 아래의 것들이 있다.

1. 로이 필딩: WAKE
2. 마이크로 소프트: Microsoft S+M
3. 구글: SPDY

이중 SPDY는 기존의 HTTP에 속도를 개선하기 위한 여러 기능을 추가한 것이다.<br>
헤더를 압축하여 대역폭을 절약했고,<br>
하나의 TCP 커넥션에 여러 요청을 동시에 보내 회전 지연을 줄이는 것이 가능해졌고,<br>
클러이언트가 요청을 보내지 않아도 서버가 능동적으로 리소스를 푸시하는 기능도 갖추고 있다.

이 모두는 결국 회전 지연을 줄이기 위한 것이다.

이 후 2012년 10월 3일 HTTP 작업 그룹은 SPDY를 기반으로 HTTP/2.0 프로토콜을 설계하기로 결정했다.

### HTTP/1.1 과의 차이점

1. 프레임

- HTTP/2.0에서 모든 메시지는 프레임에 담겨 전송된다.
- 모든 프레임은 8바이트의 크기의 헤더로 시작하며, 뒤이어 최대 16383바이트 크기의 페이로드가 온다.

2. 스트림과 멀티플렉싱

> 스트림: HTTP/2.0 커넥션을 통해 클라이언트와 서버 사이에서 교환되는 프레임들의 독립된 양방향 시퀀스이다.

- HTTP/1.1에서는 한 TCP 커넥션을 통해 요청을 보냈을 때, 그에 대한 응답이 도착하고 나서야 같은 TCP 커넥션으로 다시 요청을 보낼 수 있다.
- 그러나 HTTP/2.0에서는 하나의 커넥션에 여러 개의 스트림이 동시에 열릴 수 있다.
- 또한 스트림은 우선순위도 가질 수 있다.
  - 예를 들어 사용자가 웹브라우저로 어떤 웹페이지를 보려고 할 때, 네트워크 대역폭이 충분하지 않아 프레임의 전송이 느리다면, 웹 브라우저는 보다 중요한 리소스를 요청하는 스트림에게 더 높은 우선순위를 부여할 수 있다.(하지만 이 우선순위는 의무사항이 아니므로 요청이 우선순위대로 처리된다는 보장은 없다.)
- 반면 동시에 여러 스트림을 사용하면 스트림이 블록될 우려가 있다는 주장이 있다.
  - HTTP/2.0은 WINDOW_UPDATE 프레임을 이용한 흐름 제어(flow control)를 통해 스트림들이 서로 간섭해서 망가지는 것을 막아준다.

3. 헤더 압축

- HTTP/1.1에서 헤더는 아무런 압축 없이 그대로 전송되었다.
- HTTP/2.0에서는 헤더의 크기가 회전 지연과 대역폭 양쪽 모두에 실직적인 영향을 미친다는 점을 개선하기 위해 HTTP 메시지의 헤더를 압축하여 전송한다.

4. 서버 푸시

- HTTP/2.0 은 서버가 하나의 요청에 대해 응답으로 여러 개의 리소스를 보낼 수 있도록 해준다.
- 이 기능은 서버가 클라이언트에서 어떤 리소스를 요구할 것인지 미리 알 수 있는 상황에서 유용하다.

### 알려진 보안 이슈

1. 중개자 캡슐화 공격

- HTTP/2.0 메시지를 중간의 프락시가 HTTP/1.1 메시지로 변환할 때 메시지의 의미가 변질될 가능성이 있다.
- HTTP/1.1과 달리 HTTP/2.0은 헤더 필드의 이름과 값을 바이너리로 인코딩한다.
- 때문에 HTTP/2.0이 헤더 필드로 어떤 문자열이든 사용할 수 있게 해준다.
- 이는 정상적인 HTTP/2.0 요청이나 응답이 불법적이거나 위조된 HTTP/1.1메시지로 번역되는 것을 유발할 수 있다.
- HTTP/1.1 메시지를 HTTP/2.0 메시지로 번역하는 과정에서는 이런 문제가 발생하지 않는다.

2. 긴 커넥션 유지로 인한 개인정보 누출 우려

- HTTP/2.0은 사용자가 요청을 보낼 때의 회전 지연을 줄이기 위해 클라이언트와 서버 사이의 커넥션을 오래 유지한다.
- 이는 개인 정보의 유출에 악용될 가능성이 있다.

## 11장 | 클라이언트 식별과 쿠키

### 개별 접촉

> 현대의 웹 사이트들은 개인화된 서비스를 제공하고 싶어 한다.

1. 개별 인사

   - 개인에게 맞춤화된 환영 인사 제공

2. 사용자 맞춤 추천

   - 고객의 흥미를 학습해서 고객이 좋아할 것이라고 예상되는 제품 추천
   - 기념일에 맞는 특별한 제품 추천

3. 저장된 사용자 정보

   - 사용자를 식별하고 나면 플랫폼을 더 편하게 사용할 수 있도록 저장된 사용자 정보를 활용

4. 세션 추적

   - HTTP 트랜잭션은 상태가 없기 때문에 이를 별도의 방법으로 사용자의 상태를 남긴다.

- 상태를 유지하기 위해서 웹 사이트는 각 사용자에게서 오는 HTTP 트랜잭션을 식별할 방법이 필요하다.

### HTTP 헤더

| 헤더 이름       | 헤더 타입  | 설명                                     |
| :-------------- | :--------- | :--------------------------------------- |
| From            | 요청       | 사용자의 이메일 주소                     |
| User-Agent      | 요청       | 사용자의 브라우저                        |
| Referer         | 요청       | 사용자가 현재 링크를 타고 온 근원 페이지 |
| Authorization   | 요청       | 사용자 이름과 비밀번호                   |
| Client-ip       | 확장(요청) | 클라이언트의 IP 주소                     |
| X-Forwarded-For | 확장(요청) | 클라이언트의 IP 주소                     |
| Cookie          | 확장(요청) | 서버가 생성한 ID 라벨                    |

### 클라이언트 IP 주소

> 초기에는 사용자 식별에 클라이언트 IP 주소를 사용하려 했다.

하지만 클라이언트 IP 주소로 사용자를 식별하는 방식은 아래와 같은 약점이 있다.

1. IP 주소는 사용자가 아닌 사용하는 컴퓨터를 가리키기 때문에 사용자를 특정할 수 없다.
2. 사용자는 매번 다른 IP주소를 받으므로 IP주소로 사용자를 식별할 수 없다.
3. 방화벽으로 인해 실제 IP주소는 숨겨진다.

### 사용자 로그인

> IP주소로 사용자를 식별하려는 수동적 방식보다 사용자 이름과 비밀번호로 인증할 것을 요구해서 사용자에게 명시적으로 식별 요청을 할 수 있다.

1. `www.test.com`으로 요청
2. 사용자의 식별정보를 알지 못하기 때문에 401 응답 코드와 `WWW-Authenticate`헤더를 반환하여 로그인하라고 요청한다.
3. 아이디와 비밀번호를 입력하고 기존 요청을 다시 보낸다.
4. 이제 서버는 사용자의 식별정보를 안다.
5. 이 시점 이후의 요청에 대해서 브라우저는 서버로부터 사용자 식별 정보를 요청받으면 이를 매 요청마다 사용자의 식별정보 토큰을 `Authorization` 헤더에 담아 서버로 전송하여 한 세션이 진행되는 내내 그 사용자에 대한 식별을 유지한다.

### 뚱뚱한 URL

> 사용자의 상태 정보를 포함하고 있는 URL을 뚱뚱한 URL이라고 한다.

- 뚱뚱한 URL은 사이트를 브라우징하는 사용자를 식별하는 데 사용할 수 있지만 여러 심각한 문제가 있다.

1. 못생긴 URL

   - 브라우저에 보이는 뚱뚱한 URL은 새로운 사용자들에게 혼란을 준다.

2. 공유하지 못하는 URL

   - 뚱뚱한 URL은 특정 사용자에 대한 정보를 포함하므로 이를 노출할 시 개인정보가 노출되는 것이기 때문에 공유하면 안된다.

3. 캐시를 사용할 수 없음

   - URL로 만드는 것은 URL이 달라지기 때문에 기존 캐시에 접근할 수 없다는 것을 의미한다.

4. 서버 부하 가중

   - 서버는 뚱뚱한 URL에 해당하는 HTML 페이지를 다시 그려야 한다.

5. 이탈

   - 페이지 이동 등 특정 URL을 요청해서 의도치 않게 뚱뚱한 URL 세션에서 이탈하기 쉽다.

6. 세션 간 지속성의 부재

   - 사용자가 특정 뚱뚱한 URL을 북마킹하지 않는 이상, 로그아웃 했을 때 모든 정보를 잃게된다.

### 쿠키

> 쿠키는 사용자를 식별하고 세션을 유지하는 방식 중 현재까지 가장 널리 사용하는 방식이다.

#### 쿠키의 타입

쿠키는 크게 세션 쿠키와 지속 쿠키 두 가지 타입으로 나눌 수 있다.<br>
세션 쿠키는 사용자가 사이트를 탐색할 때 관련한 설정과 선호 사항들을 저장하는 임시 쿠키다.<br>
세션 쿠키는 사용자가 브라우저를 닫으면 삭제된다.<br>
지속 쿠키는 디스크에 저장되어 브라우저를 닫거나 컴퓨터를 재시작하더라도 남아있다.<br>
지속 쿠키는 주로 설정 정보나 로그인 이름을 유지하는데에 사용된다.

둘의 차이점은 파기되는 시점뿐이다.<br>
쿠키는 `Discard` 파라미터가 설정되어 있거나, 파기되기까지 남은 시간을 가리키는 `Expires` 혹은 `Max-Age`파라미터가 없으면 세션 쿠키가 된다.

#### 사이트마다 각기 다른 쿠키들

> 브라우저는 수백 수천 개의 쿠키를 가지고 있을 수 있지만, 그렇다고 브라우저가 쿠키 전부를 모든 사이트에 보내지는 않는다.<br>
> 보통 각 사이트에 두개 혹은 세개의 쿠키만을 보낸다. 그 이유는 아래와 같다.

1. 쿠키를 모두 전달하면 성능이 크게 저하된다.
2. 쿠키들 대부분은 서버에 특화된 이름/값 쌍을 포함하고 있기 때문에 대부분 사이트에서는 인식하지 않는 무의미한 값이다.
3. 모든 사이트에 쿠키 전체를 전달하는 것은, 특정 사이트에서 제공한 정보를 신뢰하지 않는 사이트에서 가져갈 수 있으므로 잠재적인 개인정보 문제를 일으킬 것이다.

#### 쿠키와 캐싱

> 쿠키 트랜잭션과 관련된 문서를 캐싱하는 것은 주의해야 한다.<br>
> 이전 사용자의 쿠키가 다른 사용자에게 할당돼버리거나, 누군가의 개인 정보가 다른 이에게 노출되는 최악의 상황이 일어날 수도 있다.

- 아래는 캐시를 다루는 기본 원칙에 대한 안내이다.

1. 캐시되지 말아야 할 문서가 있다면 표시하라

   - 문서를 캐시하면 될지 안 될지는 문서의 소유자가 가장 잘 안다.
   - 만약 문서가 `Set-Cookie`헤더를 제외하고 캐시를 해도 될 경우라면 그 문서에 명시적으로 `Cache-Control: no-cache="Set-Cookie"`를 기술해서 명확히 표시한다.
   - 또한 캐시를 해도 되는 문서에 `Cache-Control: public`을 사용하면 웹의 대역폭을 더 절약시켜준다.

2. `Set-Cookie`헤더를 캐시하는 것에 유의하라

   - 만약 응답이 `Set-Cookie`헤더를 가지고 있으면, 본문은 캐시할 수 있지만, `Set-Cookie` 헤더를 캐시하는 것은 주의를 기울여야만 한다.
   - 같은 `Set-Cookie`헤더를 여러 사용자에게 보내게 되면, 사용자 추적에 실패할 것이기 때문이다.
   - 어떤 캐시는 응답을 저장하기 전에 `Set-Cookie`헤더를 제거하기 때문에, 그 캐시 데이터를 받는 클라이언트는 `Set-Cookie` 헤더 정보가 없는 데이터를 받게 되어 문제가 발생할 수 있다.
   - `Cache-Control: must-revalidate, max-age=0`으로 설정하면 재검사가 일어나게 할 수 있다.

3. `Cookie`헤더를 가지고 있는 요청을 주의하라

   - 요청이 `Cookie`헤더와 함께 오면, 결과 콘텐츠가 개인정보를 담고 있을 수도 있다는 힌트다.
   - 개인정보는 캐시되지 않도록 표시되어 있어야 하지만, 그 표시를 하지 않는 서버도 있다.

<br>

---

## 12장 | 기본 인증

### HTTP의 인증요구/응답 프레임워크

1. 웹 앱이 HTTP 요청 메시지를 받으면, 서버는 요청을 처리하는 대신 현재 사용자가 누구인지를 알 수 있게 비밀번호 같이 개인 정보를 요구하는 `인증 요구`로 응답할 수 있다.
2. 사용자가 다시 요청을 보낼 때는 인증 정보를 첨부해야한다.
3. 만약 인증 정보가 맞지 않으면 서버는 클라이언트에 다시 인증요구를 보내거나 에러를 낸다.
4. 인증 정보가 맞으면 요청은 문제없이 처리가 완료된다.

### 인증 프로토콜과 헤더

- HTTP에는 기본 인증과 다이제스트 인증이라는 두 가지 공식적인 인증 프로토콜이 있다.
- 이중 HTTP의 인증요구/응답 프로토콜을 사용하는 대표적인 인증 프로토콜에는 `OAuth`가 있다.

1. 첫 번째 요청에는 인증 정보가 없다.
2. 서버는 사용자에게 인증정보를 제공하라는 지시의 의미로 401 상태 정보와 함께 요청을 반려한다.
   - 서버에는 각각 다른 비밀번호가 있는 영역들이 있을 것이므로, 서버는 `WWW-Authenticate` 헤더에 해당 영역을 설명해 놓는다.
3. 클라이언트는 인증 알고리즘과 인증 정보를 기술한 `Authorization`헤더를 함께 보낸다.
4. 인증 정보가 정확하면 서버는 문서와 함께 응답한다.

### Base-64 사용자 이름/비밀번호 인코딩

HTTP 기본 인증은 사용자 이름과 비밀번호를 콜론으로 이어서 합치고, `base-64`인코딩 메서드를 사용해 인코딩 한다.

1. id: `brian-totty`, password: `Ow!`
2. `brian-totty:Ow!`
3. `YnJpYW4tdG90dHk6T3ch`

### 프락시 인증

프락시 서버를 사용하면 프락시 서버에서 접근 정책을 중앙 관리 할 수 있기 때문에 회사 리소스 전체에 대해 통합적인 접근 제어에 유리하다.

- 프락시 인증은 웹 서버의 인증과 헤더와 상태 코드만 다르고 절차는 같다.

| 웹 서버               | 프락시 서버               |
| :-------------------- | :------------------------ |
| 비인증 상태 코드: 401 | 비인증 상태 코드: 407     |
| WWW-Authenticate      | Proxy-Authenticate        |
| Authorization         | Proxy-Authorization       |
| Authentication-Info   | Proxy-Authentication-Info |

### 기본 인증의 보안 결함

기본 인증은 단순하고 편리하지만 보안에 안심할 수 없으므로 SSL 같은 암호 기술과 혼용한다.

1. 기본 인증은 `Base-64`라는 공개적인 알고리즘을 사용하기 때문에 보안적인 장점이 없다.
2. 보안 비밀번호를 더 복잡하게 인코딩하더라도 인코딩 값을 그대로 서버로 보내 인증에 성공할 수 있다.

종합적으로 기본 인증은 일반적인 환경에서 개인화나 접근을 제어하는데 편리하며 다른 사람들이 보지 않기를 원하기는 하지만 보더라도 치명적이지 않은 경우에는 유용하다.
