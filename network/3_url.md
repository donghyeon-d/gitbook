# URL

## URL이란?
- Uniform Resourece Locator
- 웹 브라우저 주소창에 입력하는 웹페이지 주소
- 웹 사용자들이 원하는 자원을 정확히 찾아갈 수 있는 고유한 주소

## URL구조
`http://www.google.com/search?q=sweet+potato`
- 프로토콜://HostAddress:port/path?parameter또는query
- 프로토콜 : http, https가 쓰임. 
- Host Address : host의 주소 즉, IP주소가 해당됨. 사람이 이해하기 쉬운 주소인 domain을 사용할 수 있음
- port : 생략하면 프로토콜이 가진 기본 포트 번호가 적용됨. http는 80포트, https는 443포트
- path : 해당 자원에 대한 구체적인 위치. 웹서버에서 가져오고 싶은 파일(페이지)의 폴더 경로
- parameter : 서버에게 어떤 자원을 필요로하는지 구체적인 요구를 할 때 사용함. URL에서 ? 다음에 key=value로 입력하고, 여러개일때는 &을 넣어줌
- 인코딩, 디코딩 : URL에는 영문자와 숫자를 제외한 대부분의 문자가 허용되지 않기 때문에, 공백, 한글 등 문자는 변환해서 사용함
	- `http://www.google.com/search?q=고구마`
	- 인코딩 : `http://www.google.com/search?q=%EA%B3%A0%EA%B5%AC%EB%A7%88`

## URI, URN, URL
- 자원을 구분할 수 있는 유일한 식별자를 의미함
- URI가 URL과 URN을 포함하는 더 큰 개념임
- URI(Uniform Resuorce Identifier) : 자원의 고유한 식별자
- URN(Uniform Resource Name) : 자원의 고유한 이름
- URL(Uniform Resourece Locator) : 자원의 고유한 위치

## DNS
### 도메인 의미
- Domain Name System
- IP주소는 사람이 기억하고 입력하기 어려우니, 웹 사이트 주소에 쉽게 접근할 수 있도록 호스트 이름을 지정함. 그것이 도메인
### 도메인 구조
- 점을 기준으로 나눔
- 최상위 도메인
	- 맨 오른쪽에 있는 부분을 최상위 도메인(Top-Level Domain, TLD), 1차 도메인이라고 함
	- com, net, kr, us, uk 등을 최상위 도메인이라고 함
- 2차 도메인 : naver
- 3차 도메인 : www
### DNS Server
- 도메인과 IP주소를 서로 변환해주는 역할을 하는 시스템
- 네임 서버라고도 함
- 요청이 들어오면 도메인 주소와 연결된 IP주소를 찾아서 응답함
- DNS 서버는 여러 지역에서 여러 주체(사업자 등)가 운영하고 있음
- 한대의 서버가 모든 도메인에 대한 정보를 갖고 있진 않음
- DNS를 통해 주소를 알아오는 방식 : local DNS 서버(ISP가 제공) -> Root DNS(.com의 주소를 갖고있음. 전세계에 13대) -> com DNS -> google.com DNS 
- 한번 조회한 DNS에 대해서는 local DNS에서 캐싱함