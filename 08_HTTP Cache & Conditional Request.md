캐시 기본 동작

캐시가 없을 때

- 데이터가 변경되지 않아도 계속 네트워크를 통해서 동일한 데이터를 다운로드 받아야 한ㄷ.
- 인터넷 네트워크는 매우 느리고 비싸다.
- 브라우저 로딩 속도가 느리다.
- 느린 사용자 경험

캐시 적용

- 캐시 덕분에 캐시 가능 시간동안 네트워크를 사용하지 않아도 된다.
- 비싼 네트워크 사용량을 줄일 수 있다.
- 브라우저 로딩 속도가 매우 빠르다.
- 빠른 사용자 경험

캐시 시간 초과

- 캐시 유효 시간이 초과하면, 서버를 통해 데이터를 다시 조회하고, 캐시를 갱신한다.
- 이때 다시 네트워크 다운로드가 발생한다.

너무 비효율적이지 않나?

검증 헤더와 조건부 요청

캐시 시간 초과

- 캐시 유효시간이 초과해서 서버에 다시 요청하면 다음 두 가지 상황이 나타난다.
  1. 서버에서 기존 데이터를 변경함
  2. 서버에서 기존 데이터를 변경하지 않음

캐시 만료후에도 서버에서 데이터를 변경하지 않음

- 데이터를 전송하는 대신에 저장해 두었던 캐시를 재사용 할 수 있다.
- 단 클라이언트의 데이터와 서버의 데이터가 같다는 사실을 확인할 수 있어야 한다.

정리

- 캐시 유효시간이 초과해도, 서버의 데이터가 갱신되지 않았다면
- 서버는 304 (Not Modified) 상태코드와 + 헤더 메타정보만 응답한다. (body는 포함하지 않는다.)
- 클라이언트는 서버가 보낸 응답 헤더 정보로 캐시의 메타 정보를 갱신한다.
- 클라이언트는 캐시에 저장되어 있는 데이터를 재활용한다.
- 결과적으로 네트워크 다운로드가 발생하지만 용량이 적은 헤더 정보만 다운로드 된다.
- 매우 실용적!

요청 - 응답

- 클라이언트에서 서버에 데이터를 요청하면 서버가 Last-Modified: 날짜 , cache-control: 초헤더를 추가해 데이터를 보낸다.
- 클라이언트에서 서버에 데이터 요청 시 cache-control 의 시간이 지났으면 if-modified-since: 날짜 헤더를 추가해 서버에게 요청한다
- 서버에서 해당 데이터가 변경되지 않았으면 304 Not Modified 상태코드를 보내고, HTTP Body를 보내지 않는다.



검증 헤더와 조건부 요청 2

- 검증헤더
  - 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터
  - Last-Modified, ETag
- 조건부 요청 헤더
  - 검증 헤더로 조건에 따른 분기
  - If-Modified-Since: Last-Modified 사용
  - If-None-Match: ETag사용
  - 조건이 만족하면 200 OK
  - 조건이 만족하지 않으면 304 Not Modified

If-Modified-Since: 이후에 데이터가 수정되었으면?

- 데이터 미변경 되었을 때
  - 캐시 : 2020년 11월 10일 10:00:00 vs 서버: 2020년 11월 10일 10:00:00
  - 304 Not Modified, 헤더 데이터만 전송(BODY 미포함)
  - 전송 용량 0.1M (헤더 0.1M, 바디 1.0M)
- 데이터 변경 되었을 때
  - 캐시: 2020년 11월 10일 10:00:00 vs 서버: 2020년 11월 10일 11:00:00
  - 200 OK, 모든 데이터 전송 (Body 포함)
  - 전송 용량 1.1M (헤더 0.1M, 바디 1.0M)

Last-Modified, If-Modified-Since 단점

- 1초 미만(0.x초) 단위로 캐시 조정이 불가능
- 날짜 기반의 로직 사용
- 데이터를 수정해서 날짜가 다르지만, 데이터 내용은 똑같은 경우
- 서버에서 별도의 캐시 로직을 관리하고 싶은 경우
  - ex) 스페이스나 주석처럼 크게 영향이 없는 변경에서 캐시를 유지하고 싶은 경우

검증 헤더와 조건부 요청 - ETag, If-None-Match

- ETag(Entity Tag)
- 캐시용 데이터에 임의의 고유한 버전 이름을 달아둠
  - ex) ETag: "v1.0", ETag: "fifififinal"
- 데이터가 변경되면 ETag를 바꾸어서 변경함 (Hash를 다시 생성)
  - ex) ETag: "aaaaa" -> ETag: "bbbbb"
- 단순히 ETag만 서버에 보내서 같으면 기존 데이터 유지, 다르면 다시 다운로드!

ETag, If-None-Match 정리

- 단순히 ETag만 서버에 보내서 같으면 기존 데이터 유지, 다르면 다시 다운로드!
- 캐시 제어 로직을 서버에서 완전히 관리
- 클라이언트는 단순히 이 값을 서버에 제공 (클라이언트는 캐시 매커니즘을 모름)
- ex)
  - 서버는 배타 오픈 기간인 3일동안 파일이 변경되어도 ETag를 동일하게 유지
  - 애플리케이션 배포 주기에 맞추어 ETag 모두 갱신

캐시와 조건부 요청 헤더

캐시 제어 헤더

- Cache-Control: 캐시 제어
- Pragma: 캐시 제어(하위 호환)
- Expires: 캐시 유효 기간(하위 호환)

Cache-Control - 캐시 지시어(directives)

- Cache-Control: max-age
  - 캐시 유효 기간, 초 단위
- Cache-Control: no-cache
  - 데이터는 캐시해도 되지만, 항상 조건부 요청을 통해 원(origin) 서버에 검증하고 사용

- Cache-Control: no-store
  - 데이터에 민감한 정보가 있으므로 저장하면 안됨 (메모리에서 사용하고 최대한 빨리 삭제)

하위 호환

Pragma - 캐시 제어

ex) Pragma: no-cache

- cache-control: no-cache 처럼 동작
- HTTP 1.0 하위 호환 
- 거의 사용 안함



Expires - 캐시 만료일 지정

ex) expires: 날짜

- 날짜로 캐시 만료일을 지정
- HTTP 1.0 부터 사용
- 지금은 더 유연한 Cache-Control: max-age권장
- Cache-Control: max-age와 함께 사용하면 expires는 무시



검증 헤더와 조건부 요청 헤더

- 검증 헤더 (Validator)
  - ETag, Last-Modified
- 조건부 요청 헤더
  - If-Match, If-None-Match: ETag값 사용
  - If-Modified-Since, If-Unmodified-Since: Last-Modified 값 사용

프록시 캐시

- Origin 서버에서 직접 데이터를 받아오면 시간이 오래 걸리기 때문에 기업들은 사용자와 가까운 지역에 프록시 캐시 서버를 만들어놓는다.

Cache-Control - 캐시 지시어(directives) - 기타

- Cache-Control: public
  - 응답이 public 캐시에 저장되어도 됨 (프록시 캐시 서버에)
- Cache-Control: private
  - 응답이 해당 사용자만을 위한 것임, private 캐시에 저장해야함 (기본값)

- Cache-Control: s-maxage
  - 프록시 캐시에만 적용되는 max-age
- Age: 60 (HTTP 헤더)
  - 오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)

캐시 무효화

Cache-Control - 확실한 캐시 무효화 응답

- 캐시를 저장하면 안되는 경우 밑에 헤더를 모두 추가해야 함
- Cache-Control: no-cache, no-store, must-revalidate
- Pragma: no-cache
  - HTTP 1.0 하위 호환

Cache-Control - 캐시 지시어(directives) - 확실한 캐시 무효화

서버에서 HTTP 응답 코드를 만들 때

- Cache-Control: 캐시 지시어 포맷
- Cache-Control: no-cache
  - 데이터는 캐시해도 되지만, 항상 조건부 요청을 통해 원 서버에 검증하고 사용
- Cache-Control: no-store
  - 데이터에 민감한 정보가 있으므로 저장하면 안됨 (메모리에서 사용하고 최대한 빨리 삭제)
- Cache-Control: must-revalidate
  - 캐시 만료 후 최초 조회 시 원 서버에 검증해야 함
  - 원 서버 접근 실패 시 반드시 오류가 발생해야 함 - 504(Gateway Timeout)
  - must-revalidate는 캐시 유효 시간이라면 캐시를 사용함
- Pragma: no-cache
  - HTTP 1.0 하위 호환

no-cache가 항상 원 서버에 검증하고 사용하는데, must-revalidate는 왜 필요하지?

- 프록시 서버가 no-cache 를 받았어도, 장애로 인해 원 서버에 접근할 수 없는 경우 캐시 서버 설정에 따라서 캐시 데이터를 반환할 수 있음
  - 오류 보다는 오래된 데이터라도 보여주자는 원리

- must-revalidate는 원서버에 접근할 수 없는 경우 항상 504 Gateway Timeout오류가 발생해야함


