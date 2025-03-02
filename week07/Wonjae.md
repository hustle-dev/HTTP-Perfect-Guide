# 7장 캐시

## 7.1 불필요한 데이터 전송

- 복수의 클라이언트가 자주 쓰이는 원 서버 페이지에 접근할 때 서버는 모든 클라이언트에게 똑같은 데이터를 반복해서 전송한다.
- 캐시를 이용하면 첫번째 서버 응답은 캐시에 보관되고 캐시된 사본을 응답으로 사용하기때문에 원 서버의 트래픽을 줄일 수 있다.

## 7.2 대역폭 병목

> 대역폭이란?
>
> 대역폭(帶域幅, 영어: bandwidth)은 특정한 기능을 수행할 수 있는 주파수의 범위

- 캐시를 사용하지 않으면 대역폭이 늘어나 성능이 저하된다.
- 캐시를 사용하면 빠른 LAN에 있는 캐시를 사용하여 성능을 대폭 향상할 수 있다.

## 7.3 갑작스런 요청 쇄도(Flash Crowds)

- 갑작스런 사건으로 인해 많은 사람이 거의 동시에 웹 문서에 접근할 때 트래픽이 급증되는데 이는 네트워크와 웹 서버의 심각한 장애를 야기시킨다.
- 캐시를 사용하면 요청 자체를 줄일 수 있기 때문에 이를 개선하는데 도움이 된다.

## 7.4 거리로 인한 지연

- 캐시를 사용하면 물리적인 거리가 멀더라도 가까운 거리에 있는 캐시를 사용하면 요청과 응답시간이 줄어든다.

## 7.5 적중과 부적중

> 캐시 적중<br>
> 캐시에 요청이 도착했을 때, 대응하는 사본으로 요청을 처리하는 것

> 캐시 부적중<br>
> 대응하는 사본이 없다면 원서버로 전달

### 7.5.1 재검사(신선도 검사)

- 원 서버 콘텐츠는 변경될 수 있다.
- 때문에 캐시는 반드시 그들이 갖고 있는 사본이 여전히 최신인지 서버를 통해 때때로 점검해야 한다.
- 캐시는 캐시된 사본이 재검사가 필요할 때 원 서버에 작은 재검사 요청을 보낸다.
- 이후 콘텐츠가 변경되지 않았다면 서버는 아주 작은 `304 Not Modified`응답을 보낸다.
- 사본이 여전히 유효햠을 알게 된 캐시는 즉각 사본이 신선하다고 임시로 다시 표시한 뒤 그 사본을 클라이언트에게 제공한다.
- 느린 적중은 순수 캐시 적중보다는 느리지만 캐시 부적중보다는 빠르다.

#### 느린 적중

- 캐시는 캐시된 사본이 재검사가 필요할 때 원 서버에 작은 재검사 요청을 보낸다.
- 이후 콘텐츠가 변경되지 않았다면 서버는 아주 작은 `304 Not Modified`응답을 보낸다.
- 사본이 여전히 유효햠을 알게 된 캐시는 즉각 사본이 신선하다고 임시로 다시 표시한 뒤 그 사본을 클라이언트에게 제공한다.

#### 재검사 적중

만약 서버 객체가 변경되지 않았다면 `304 Not Modified`응답

#### 재검사 부적중

만약 서버 객체가 캐시된 사본과 다르다면 서버는 콘텐츠 전체와 `200 OK`응답

#### 객체 삭제

만약 서버 객체가 삭제되었다면, 서버는 `404 Not Found`를 응답하며 캐시를 삭제한다.

### 7.5.2 적중률

> 캐시가 요청을 처리하는 비율

- 0%: 모든 요청이 캐시 부적중
- 100%: 모든 요청이 캐시 적중
- 문서 적중률을 개선하면 전체 대기시간이 줄어든다.

### 7.5.3 바이트 적중률

> 문서들이 모두 같은 크기가 아니므로 문서 적중률이 모든 것을 말해주지 않는다. <br>
> 이를 바이트단위로 보다 상세하게 표시한 것이 바이트 적중률이다.

> 얼마나 많은 바이트가 인터넷으로 나가지 않았는지 표시하는 지표

- 바이트 적중률을 개선하면 대역폭 절약을 최적화한다.

### 7.5.4 적중과 부적중의 구별

- HTTP는 클라이언트에게 응답이 캐시 적중이었는지 원 서버 접근인지 말해줄 수 있는 방법을 제공하지 않는다.
  - 두 경우 모두 응답이 본문을 갖고 있음을 나타내는 `200 OK`가 될 것이다.
  - 특정 상용 프락시 캐시는 캐시에 무슨 일이 일어났는지 설명하기 위해 `Via` 헤더에 추가 정보를 붙인다.
- 응답의 `Date`헤더 값을 현재 시각과 비교하여 응답의 생성일이 더 오래되었다면 클라이언트는 응답이 캐시된 것임을 알아낼 수 있다.(`Age`헤더도 활용 가능)

## 7.6 캐시 토폴로지

> 개인 전용 캐시<br>
> 한 명에게만 할당된 개인만을 위한 캐시<br>
> 한 명의 사용자가 자주 찾는 페이지를 담는다.

> 공용 캐시<br>
> 공유된 캐시로서 사용자 집단에게 자주 쓰이는 페이지를 담는다.

### 7.6.1 개인 전용 캐시

- 많은 에너지나 저장 공간을 필요로 하지 않기 때문에 작고 저렴하 수 있다.
- 웹 브라우저는 개인 전용 캐시를 내장하고 있다.

### 7.6.2 공용 프락시 캐시

- 캐시 프락시 서버 혹은 더 흔히 프락시 캐시라고 불리는 특별한 종류의 공유된 프락시 서버

### 7.6.3 프락시 캐시 계층들

- 작은 캐시에서 캐시 부적중이 발생했을 때 더 큰 부모 캐시가 `걸러 남겨진` 트래픽을 처리하는 것을 계층으로 구현했다.
- 가까운 1단계 캐시에서 적중을 얻고 얻지 못했다면 계층을 타고 올라가 캐시를 가져오는 것이다.
- 다만 프락시 연쇄가 길어질수록 중간 프락시는 현저한 성능저하가 발생할 것이다.

### 7.6.4 캐시망, 콘텐츠 라우팅, 피어링

> 인터넷 트랜짓<br>
> 트래픽이 다른 네트워크로 건너가는 것

- 몇몇 네트워크 아키텍처는 단순한 캐시 계층 대신 복잡한 캐시망을 만들다.
- 캐시망이란 캐시 커뮤니케이션을 동적으로 결정하는 것이다.
  - URL에 근거하여, 부모 캐시와 원 서버 중 하나를 동적으로 선택한다.
  - URL에 근거하여 특정 부모 캐시를 동적으로 선택한다.
  - 부모 캐시에게 가기 전에, 캐시된 사본을 로컬에서 찾아본다.
  - 다른 캐시들이 그들의 캐시된 콘텐츠에 부분적으로 접근할 수 있도록 허용하되 그들의 캐시를 통한 인터넷 트랜짓(`Internet transit`)은 허용하지 않는다.

## 7.7 캐시 처리 단계

1. 요청 받기: 캐시는 네트워크로부터 도착한 요청 메시지를 읽는다.
2. 파싱: 캐시는 메시지를 파싱하여 URL과 헤더들을 추출한다.
3. 검색: 캐시는 로컬 복사본이 있는지 검사하고, 사본이 없다면 사본을 받아온다.(그리고 로컬에 저장한다.)
4. 신선도 검사: 캐시는 캐시된 사본이 충분히 신선한지 검사하고, 신선하지 않다면 변경사항이 있는지 서버에게 물어본다.
5. 응답 생성: 캐시는 새로운 헤더와 캐시된 본문으로 응답 메시지를 만든다.
6. 발송: 캐시는 네트워크를 통해 응답을 클라이언트에게 돌려준다.
7. 로깅: 선택적으로, 캐시는 로그파일에 트랜잭션에 대해 서술한 로그 하나를 남긴다.

### 7.7.8 캐시 처리 플로 차트

![Group 63](https://user-images.githubusercontent.com/87295692/161195957-09e80c06-18a8-4b56-8f06-d62770d349ca.png)

## 7.8 사본을 신선하게 유지하기

### 7.8.1 문서 만료

HTTP는 Cache-Control과 Expires라는 특별한 헤더들을 이용해서 원 서버가 각 문서에 유효기간을 붙일 수 있게 해준다.

#### Expires 헤더

```
HTTP/1.0 200 OK
DATE: Sat, 29 Jun 2002, 14:30:00 GMT
Content-type: text/plain
Content-length: 67
Expires: Fri, 05 Jul 2002, 05:00:00 GMT

Independence Day sale at Joe's Hardware Come shop with us today!
```

#### Cache-Control: max-age 헤더

```
HTTP/1.0 200 OK
DATE: Sat, 29 Jun 2002, 14:30:00 GMT
Content-type: text/plain
Content-length: 67
Cache-Control: max-age=484200

Independence Day sale at Joe's Hardware Come shop with us today!
```

### 7.8.2 유효기간과 나이

서버는 응답 본문과 함계하는 Expires나 Cache-Control: max-age 응답 헤더를 이용해서 유효기간을 명시한다.

#### Expries

절대 유효기간을 명시하며, 만약 유효기간이 경과했다면, 그 문서는 더 이상 신선하지 않다.

```
Expires: Fri, 05 Jul 2002, 05:00:00 GMT
```

#### Cache-Control: max-age

`max-age`값은 문서의 최대 나이를 정의한다, 최대 나이는 문서가 처음 생성된 이후부터, 제공하기엔 더 이상 신선하지 않다고 간주될 때까지 경과한 시간의 합법적인 초단위의 최댓값이다.

### 7.8.3 서버 재검사

- 캐시된 문서가 만료되었다는 것은 다만 검사할 시간이 되었따는 것이다.
- 이런 검사를 캐시가 원 서버에게 문서가 변경되었는지 여부를 물어보는 것을 `서버 재검사`라고 부른다.
  - 재검사 결과 콘텐츠가 변경되었다면, 캐시는 새로운 사본을 사져와 저장한 뒤 클라이언트에게도 보내준다.
  - 콘텐츠가 변경되지 않았다면, 캐시는 새 만료일을 포함한 새 헤더들만 가져와서 캐시 안의 헤더들을 갱신한다.

### 7.8.4 조건부 메서드와의 재검사

- 조건부 GET은 GET 요청 메시지에 특별한 조건부 헤더를 추가함으로서 시작된다.
- 웹 서버는 조건이 참인 경우에만 객체를 반환한다.
- HTTP는 다섯 가지 조건부 쵸어 헤더를 정의한다.
- 그 중 `If-Modified-Since`와 `If-None-Match`가 가장 유용하다.

#### `If-Modified-Since`: `<date>`

- 만약 문서가 주어진 날짜 이후로 수정되었다면 요청 메서드를 처리한다.
- 캐시된 버전으로부터 콘텐츠가 변경된 경우메만 콘텐츠를 가져오기 위해 `Last-Modified` 서버 응답 헤더와 함께 사용된다.

#### `If-None-Match`: `<tags>`

- 마지막 변경된 날짜를 맞춰보는 대신 특별한 태그를 제공하여, `If-None-Match` 헤더는 캐시된 태그가 서버에 있는 문서의 태그와 다를 때만 요청을 처리한다.

## 7.9 캐시 제어

HTTP는 문서가 만료되기 전까지 얼마나 오랫동안 캐시될 수 있게 할 것인지 서버가 설정할 수 있는 여러가지 방법을 정의한다.

- Cache-Control: no-store 헤더를 응답에 첨부할 수 있다.
- Cache-Control: no-cache 헤더를 응답에 첨부할 수 있다.
- Cache-Control: must-revalidate 헤더를 응답에 첨부할 수 있다.
- Cache-Control: max-age 헤더를 응답에 첨부할 수 있다.
- Expires 날짜 헤더를 응답에 첨부할 수 있다.
- 아무 만료 정보도 주지 않고, 캐시가 스스로 체험적인(휴리스틱) 방법으로 결정하게 할 수 있다.

### 7.9.1 `no-cache`와 `no-store`응답 헤더

`no-store`와 `no-cache`헤더는 캐시가 검증되지 않은 캐시된 객체로 응답하는 것을 막는다.

#### `no-store`

그 응답의 사본을 만드는 것을 금지한다.

#### `no-cache`

로컬 캐시 저장소에 저장될 수 있지만, 먼저 서버와 재검사를 하지 않고서는 캐시에서 클라이언트로 제공될 수 없다.

#### `Pragma`

`no-cache`헤더는 `HTTP/1.0+`와의 하위호환성을 위해 `HTTP/1.1`에 포함되어 있다.

### 7.9.2 `Max-Age`응답 헤더

- `max-age`헤더는 신선하다고 간주되었던 문서가 서버로부터 온 이후로 흐른 시간이며 초로 나타낸다.
- `s-maxage`헤더는 `max-age`처럼 행동하지만 공유된 캐시에만 적용된다.
- 0으로 설정하면 매 접근마다 문서를 캐시하거나 리프레시하지 않도록 요청할 수 있다.

### 7.9.3 `Expires` 응답 헤더

```
Expires: Fri, 05 Jul 2002, 05:00:00 GMT
```

위와 같이 실제 만료 날짜를 명시한다.

### 7.9.4 `Must-Revalidate` 응답 헤더

서버와의 최초 재검사 없이는 제공해서는 안됨을 의미한다.

### 7.9.5 휴리스틱 만료

만약 응답이 `Cache-Control: max-age`헤더나 `Expires`헤더 중 어느 것도 포함하지 않고 있다면, 캐시는 경험적인 방법으로(heuristic) 최대 나이를 계산할 것이다.

- 대표적으로 LM 인자 알고리즘이 있다.
- 이는 최근 변경 일시를 포함하고 있다면 사용할 수 있다.
- 문서가 최근 변경 일시를 문서가 얼마나 자주 바뀌늕에 대한 추정에 사용한다.
  - 만약 캐시된 문서가 마지막으로 변경된 것이 상당히 예전이라면 그것은 아마 안정적인 문서일 것이기 때문에 바뀔 가능성을 크지 않으므로 캐시에 더 오래 보관하고 있어도 안전하다.
  - 만약 최근에 변경되었다면, 그것은 변경 주기가 짧으므로 서버와 재검사하기 전까지 짧은 기간 동안만 캐시해야 한다.

### 7.9.6 클라이언트 신선도 제약

- 웹 브라우저는 브라우저나 프락시 캐시의 신선하지 않은 콘텐츠를 강제로 갱신시켜주는 리프레시나 리로드 버튼을 갖고 있다.
- 이 리프리세 버튼은 `Cache-control` 요청 헤더가 추가도니 GET 요청을 발생시켜서, 강제로 재검사하거나 서버로부터 콘텐츠를 무조건 가져온다.
- 정확한 리프레시 동작은 각 브라우저나 문서, 중간 캐시 설정에 달려있다.
- 클라이언트 역시 성ㅈ능, 신뢰성, 비용 개선을 위한 절충안으로 `Cache-Control` 요청 지시어를 통해 신선도 요구사항을 느슨하게 하고자 할 수도 있다.

### 7.9.7 주의할 점

- 문서 만료는 완벽한 시스템이 아니다.
- 만약 퍼블리셔가 유효기간을 까마득한 미래로 설정해버리면 어떤 병경도 캐시에 반영되지 않을 것이다.

## 7.10 캐시 제어 설정

### 7.10.1 아파치로 HTTP 헤더 제어하기

### 7.10.2 HTTP-EQUIV를 통한 HTML 캐시 제어

```html
<html>
  <head>
    <title>My Document</title>
    <meta http-equiv="Cache-control" content="no-cache" />
  </head>
</html>
```

HTML에서 `<META HTTP-EQUIV>`를 파싱하여 HTTP 응답에 정해진 헤더를 삽입한다.

## 7.11 자세한 알고리즘

## 7.12 캐시와 광고

### 7.12.1 광고 회사의 딜레마

콘텐츠 제공자들은 캐시가 모든 곳에 있다면 수요를 견디기 위해 대용량 멀티프로세서 웹 서버를 살 필요가 없어지고, 이에 따른 네트워크 서비스 요금도 절약할 수 있기 때문에 캐시를 좋아할 것이다.

그러나 많은 콘텐츠 제공자의 사용자가 광고를 볼 때마다 돈을 번다.<br>
그리고 그것이 캐시와 관련되면 문제가 된다,<br>
캐시는 원 서버가 실제 접근 횟수를 알 수 없게 숨길 수 있으므로 캐싱이 완벽하게 동작한다면 원 서버는 HTTP접근을 전혀 수신하지 않고 캐시가 해당 접근들을 모두 흡수하게 된다.

때문에 접근 횟수에 따라 돈을 벌고 있다면, 캐시가 오히려 좋지 않을 수 있다.

### 7.12.2 퍼블리셔의 응답

오늘날 광고회사들은 캐시가 광고 시청 수를 가로채지 못하도록 모든 종류의 `캐시 무력화`기법을 사용한다.

- 매 접근마다 광고 URL을 고쳐 쓴다.

하지만 이 캐시 무력화 기법으로 인해 광고 시청 수를 관리하려는 의욕적인 시도는 캐싱의 긍정적인 효과를 감소시키고 있다.

이상적으로는 캐시는 올바르게 작동하되 캐시가 그들에게 적중이 얼마나 많이 일어났는지 알려주어야 한다.

한가지 방법으로 모든 접근에 대해 원 서버와 재검사하도록 캐시를 설정하는 것이다.<br>
이는 데이터를 전송하지 않기 때문에 효율적이지만 트랜잭션을 느리게 만들기는 한다.

### 7.12.3 로그 마이그레이션

### 7.12.4 적중 측정과 사용량 제한
