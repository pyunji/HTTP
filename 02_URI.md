# URI
## URI (Uniform Resource Identifier)
- **U**niform: 리소스를 식별하는 통일된 방식
- **R**esource: 자원. URI로 식별할 수 있는 모든 것(제한 없음)
- **I**dentifier: 다른 항목과 구분하는데 필요한 정보
### URL, URN
- URI는 로케이터(**L**ocator), 이름(**N**ame) 또는 둘 다 추가로 분류될 수 있다.
- URI는 URL과 URN을 포함한다.
- URL - Locator: 리소스가 있는 위치를 지정
- URN - Name: 리소스에 이름을 부여

- 위치는 변할 수 있지만, 이름은 변하지 않는다.
- URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음
- 앞으로 URI와 URL을 같은 의미로 말하겠다.

## URL 문법
`scheme://[userinfo@]host[:port][/path][?query][#fragment]`
### URL scheme
- **scheme**:<k>//\[userinfo@]host\[:port]\[/path]\[?query]\[#fragment]
- **https**:<k>//www<k>.google.com:443/search?q=hello&hl=ko
- 주로 프로토콜 사용
- 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
	- ex) http, https, ftp 등..
- http는 80포트, https는 443포트를 주로 사용하고, 포트는 생략 가능하다.
- https는 http에 보안이 추가된 프로토콜이다. (HTTP Secure)

### URL userinfo
- URL에 사용자정보를 포함해서 인증해야할 때 사용
- 거의 사용하지 않음

### URL host
- scheme://\[userinfo@]**host**\[:port]\[/path]\[?query]\[#fragment]
- ht<k>tps://**www<k>.google.com**:443/search?q=hello&hl=ko
- 호스트명
- 도메인명 또는 IP 주소를 직접 사용가능

### URL PORT
- scheme://\[userinfo@]host<k>**\[:port]**\[/path]\[?query]\[#fragment]
- htt<k>ps://www<k>.google.com<k>**:443**/search?q=hello&hl=ko
- 포트(PORT)
- 접속 포트
- 일반적으로 생략, 생략시 http는 80, https는 443

### URL path
- scheme://\[userinfo@]host\[:port]**\[/path]**\[?query]\[#fragment]
- http<k>s://www.google.com:443<k>**/search**?q=hello&hl=ko
- 리소스 경로(path), 계층적 구조
- ex) /home/file1.jpg        /members/100      /items/iphone12

### URL query
- scheme://\[userinfo@]host\[:port]\[/path]**\[?query]**\[#fragment]
- http<k>s://www.google.com:443/search</k>**?q=hello&hl=ko**
- `key=value` 형태
- `?`로 시작, `&`로 추가 가능
	- ex) `?keyA=valueA&keyB=valueB`
- query parameter, query string 등으로 불림. 웹서버에 제공하는 문자 형태의 파라미터

### URL fragment
- scheme://\[userinfo@]host\[:port]\[/path]\[?query]**\[#fragment]**
- http<k>s://github.com/jjukkumi/Java-PS<k>**#-스터디-진행-규칙**
- html 내부 북마크 등에 사용
- 서버에 전송하는 정보는 아님

