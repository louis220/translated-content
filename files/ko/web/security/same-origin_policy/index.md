---
title: 동일 출처 정책
slug: Web/Security/Same-origin_policy
tags:
  - CORS
  - JavaScript
  - Same-origin policy
  - Security
  - URL
  - 교차 출처 정책
  - 동일 출처 정책
translation_of: Web/Security/Same-origin_policy
---
{{QuickLinksWithSubpages("/ko/docs/Web/Security")}}

**동일 출처 정책**(same-origin policy)은 어떤 {{Glossary("origin", "출처")}}에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호작용하는 것을 제한하는 중요한 보안 방식입니다. 동일 출처 정책은 잠재적으로 해로울 수 있는 문서를 분리함으로써 공격받을 수 있는 경로를 줄여줍니다.

## 출처의 정의

두 URL의 {{glossary("protocol", "프로토콜")}}, {{glossary("port", "포트")}}(명시한 경우), {{glossary("host", "호스트")}}가 모두 같아야 **동일한 출처**라고 말합니다. "스킴/호스트/포트튜플"이나 그냥 "튜플"(2개 이상의 요소가 전체를 구성하는 집합)이라고 하기도 합니다.

아래 표는 URL `http://store.company.com/dir/page.html`의 출처를 비교한 예시입니다.

| URL                                               | 결과 | 이유                                |
| ------------------------------------------------- | ---- | ----------------------------------- |
| `http://store.company.com/dir2/other.html`        | 성공 | 경로만 다름                         |
| `http://store.company.com/dir/inner/another.html` | 성공 | 경로만 다름                         |
| `https://store.company.com/secure.html`           | 실패 | 프로토콜 다름                       |
| `http://store.company.com:81/dir/etc.html`        | 실패 | 포트 다름 (`http://`는 80이 기본값) |
| `http://news.company.com/dir/other.html`          | 실패 | 호스트 다름                         |

### 출처 상속

`about:blank`와 `javascript:` URL은 출처 서버에 대한 정보를 가지고 있지 않습니다. 따라서 이런 URL에서 실행하는 스크립트는 해당 URL을 가지고 있는 문서의 출처를 상속합니다.

> **참고:** 예를 들어, `about:blank`는 부모 창에서 내용을 채워넣을 빈 팝업({{domxref("Window.open()")}} 등의 방식으로 생성)의 URL로 주로 사용됩니다. 만약 이 팝업이 JavaScript도 포함한다면 팝업을 생성한 스크립트의 출처를 따라가게 됩니다.

> **경고:** `data:` URL은 비어있는 보안 맥락을 새로 받습니다.

### Internet Explorer 예외사항

Internet Explorer는 동일 출처 정책에 두 가지 중요한 예외사항이 있습니다.

- 신뢰할 수 있는 사이트
  - : 양쪽 도메인 모두가 **높음** 단계의 보안 수준을 가지고 있는 경우 동일 출처 제약을 적용하지 않습니다.
- 포트
  - : Internet Explorer는 동일 출처 정책 검사에 포트를 포함하지 않습니다. 따라서 `http://company.com:81/index.html`과 `http://company.com/index.html`은 동일 출처로 간주하며, 제한을 두지 않습니다.

두 예외 모두 비표준이며 다른 브라우저에서는 지원하지 않습니다.

## 출처 변경

일부 제약이 있긴 하지만 페이지의 출처를 변경하는 것도 가능합니다. 스크립트로{{domxref("document.domain")}}의 값을 현재 도메인이나 현재 도메인의 상위 도메인으로 변경할 수 있는데, 상위 도메인으로 설정한 경우 (더 짧은) 상위 도메인을 동일 출처 검사에 사용합니다.

`http://store.company.com/dir/other.html` 문서의 스크립트가 다음 코드를 실행한 이후를 가정해보겠습니다.

```
document.domain = "company.com";
```

해당 페이지는 `http://company.com/dir/page.html`과의 동일 출처 검사를 통과할 수 있습니다. (단, `http://company.com/dir/page.html` 역시 자신의 `document.domain`을 `"company.com"`으로 설정해 이런 동작을 허용하겠다고 알려줘야 합니다. {{domxref("document.domain")}} 문서에서 더 알아보세요.) 그러나, `company.com`은 `document.domain`을 `othercompany.com`으로 설정할 수 **없습니다**. `company.com`의 상위 도메인이 아니기 때문입니다.

포트 번호는 브라우저가 따로 검사합니다. 모든 `document.domain`의 변화, 심지어 `document.domain = document.domain`조차 포트 번호를 {{jsxref("null")}}로 덮어씁니다. 따라서 `company.com:8080`과 `company.com`을 서로 연결하고자 할때 앞쪽에서 `document.domain = "company.com"`을 추가하는 것만 가지고는 **불가능**하며, 반드시 양쪽 모두 자신의 포트 숫자를 `null`로 설정해야 합니다.

> **참고:** `document.domain`을 통해 서브도메인이 부모에 안전하게 접근할 수 있도록 허용할 때, 반드시 부모와 자식 모두의 `document.domain`을 같은 값으로 지정해야 합니다. 부모 도메인을 자기 자신의 값으로 설정하는, 아무 의미도 없어 보이는 경우라도 지정이 필요합니다. 그렇지 않으면 권한 오류가 발생할 수 있습니다.

## 교차 출처 네트워크 접근

동일 출처 정책은 서로 다른 출처 사이에서 {{domxref("XMLHttpRequest")}} 혹은 {{htmlelement("img")}} 요소를 사용할 때 등의 상호작용을 통제합니다. 상호작용은 보통 다음의 세 가지 범주로 나뉩니다.

- 교차 출처 쓰기는 보통 허용합니다. 예시로는 링크, 리다이렉트, 양식 제출 등이 있습니다. 일부 HTTP 요청은 [프리플라이트](/ko/docs/Web/HTTP/CORS#Preflighted_requests)를 요구합니다.
- 교차 출처 삽입은 보통 허용합니다. 예시는 아래에 있습니다.
- 교차 출처 읽기는 보통 불허하지만, 종종 교차 출처 삽입 과정에서 읽기 권한이 누출됩니다. 가령 삽입한 이미지의 크기나, 삽입한 스크립트의 행동, [삽입한 리소스의 가용성](https://bugzilla.mozilla.org/show_bug.cgi?id=629094) 등을 읽을 수 있습니다.

다음은 교차 출처로 삽입할 수 있는 리소스의 일부 예제입니다.

- `<script src="..."></script>`로 추가하는 JavaScript. 그러나 구문 오류에 대한 상세 정보는 동일 출처 스크립트에서만 확인 가능합니다.
- `<link rel="stylesheet" href="...">`로 적용하는 CSS. CSS의 [느슨한 구문 규칙](https://scarybeastsecurity.blogspot.com/2009/12/generic-cross-browser-cross-domain.html)으로 인해, 교차 출처 CSS는 올바른 `Content-Type` 헤더를 필요로 합니다. 브라우저 별 제한은 다릅니다. [Internet Explorer](<https://docs.microsoft.com/en-us/previous-versions/windows/internet-explorer/ie-developer/compatibility/gg622939(v=vs.85)?redirectedfrom=MSDN>), [Firefox](https://www.mozilla.org/en-US/security/advisories/mfsa2010-46/), [Chrome](https://bugs.chromium.org/p/chromium/issues/detail?id=9877), [Safari](https://support.apple.com/ko-kr/HT4070)(CVE-2010-0051), [Opera](https://security.opera.com/cross-domain-data-theft-with-css-load-opera-security-advisories/)
- {{htmlelement("img")}}로 표시하는 이미지.
- {{htmlelement("video")}}와 {{htmlelement("audio")}}로 재생하는 미디어.
- {{htmlelement("object")}}와 {{htmlelement("embed")}}로 삽입하는 외부 리소스.
- {{cssxref("@font-face")}}로 적용하는 글씨체. 하지만 일부 브라우저는 동일 출처를 요구할 수도 있습니다.
- {{htmlelement("iframe")}}으로 삽입하는 모든 것. {{httpheader("X-Frame-Options")}} 헤더를 사용해 교차 출처 프레임의 대상이 되는 것을 방지할 수 있습니다.

### 교차 출처 접근 허용하기

[CORS](/ko/docs/Web/HTTP/CORS)를 사용해 교차 출처 접근을 허용하세요. CORS는 {{glossary("HTTP")}}의 일부로, 어떤 호스트에서 자신의 콘텐츠를 불러갈 수 있는지 서버에 지정할 수 있는 방법입니다.

### 교차 출처 접근 막기

- 교차 출처 쓰기를 방지하려면 추측 불가능한 토큰을 요청에 포함하세요. [교차 출처 요청 위조(Cross-Site Request Forgery, CSRF)](https://owasp.org/www-community/attacks/csrf) 토큰이라고 부릅니다. 토큰을 요구하는 페이지의 교차 출처 읽기도 막아야 합니다.
- 리소스의 교차 출처 읽기를 방지하려면 삽입 불가하도록 설정하세요. 리소스 삽입 과정에서 일부 정보가 새어 나가므로 방지해야 합니다.
- 교차 출처 삽입을 방지하려면, 리소스가 위에 나열한 삽입 가능 형태로 읽히지 않도록 해야 합니다. 브라우저는 Content-Type 헤더를 준수하지 않을 수도 있습니다. 즉, `<script>` 태그가 HTML 문서를 가리키도록 설정하는 경우, 브라우저는 HTML을 JavaScript로 분석하려고 시도합니다. 방지하려는 리소스가 사이트의 진입점이 아닌 경우 CSRF 토큰을 사용하는 것도 삽입 방지의 방법입니다.

## 교차 출처 스크립트 API 접근

{{domxref("HTMLIFrameElement.contentWindow", "iframe.contentWindow")}}, {{domxref("window.parent")}}, {{domxref("window.open")}}, {{domxref("window.opener")}} 등의 JavaScript API는 문서가 서로를 직접 참조할 수 있도록 합니다. 이때 두 문서의 출처가 같지 않을 경우 참조를 통해 {{domxref("Window")}}와 {{domxref("Location")}} 객체로의 매우 제한된 접근만 가능합니다. 아래의 두 구획에서 접근 가능한 항목을 볼 수 있습니다.

서로 다른 출처의 문서간 통신을 하려면 {{domxref("window.postMessage()")}}를 사용하세요.

명세: [HTML Living Standard § Cross-origin objects](https://html.spec.whatwg.org/multipage/browsers.html#cross-origin-objects)

### Window

교차 출처에서 `Window`의 다음 항목에 접근할 수 있습니다.

| 메서드                                       |
| -------------------------------------------- |
| {{domxref("window.blur")}}         |
| {{domxref("window.close")}}         |
| {{domxref("window.focus")}}         |
| {{domxref("window.postMessage")}} |

| 속성                                     |            |
| ---------------------------------------- | ---------- |
| {{domxref("window.closed")}}     | 읽기 전용. |
| {{domxref("window.frames")}}     | 읽기 전용. |
| {{domxref("window.length")}}     | 읽기 전용. |
| {{domxref("window.location")}} | 읽기/쓰기. |
| {{domxref("window.opener")}}     | 읽기 전용. |
| {{domxref("window.parent")}}     | 읽기 전용. |
| {{domxref("window.self")}}     | 읽기 전용. |
| {{domxref("window.top")}}         | 읽기 전용. |
| {{domxref("window.window")}}     | 읽기 전용. |

일부 브라우저는 위의 항목보다 더 많은 접근을 허용할 수도 있습니다.

### Location

교차 출처에서 `Location`의 다음 항목에 접근할 수 있습니다.

| 메서드                                   |
| ---------------------------------------- |
| {{domxref("location.replace")}} |

| 속성                                 |            |
| ------------------------------------ | ---------- |
| {{domxref("URLUtils.href")}} | 쓰기 전용. |

일부 브라우저는 위의 항목보다 더 많은 접근을 허용할 수도 있습니다.

## 교차 출처 데이터 저장소 접근

[Web Storage](/ko/docs/Web/API/Web_Storage_API)와 [IndexedDB](/ko/docs/Web/API/IndexedDB_API) 등 브라우저에 저장한 데이터로의 접근은 출처별로 제한됩니다. 각각의 출처는 별도의 저장소를 받으며, 한 출처의 JavaScript에서 다른 출처의 저장소를 읽거나 쓰는 것은 불가능합니다.

{{glossary("Cookie", "쿠키")}}는 출처에 대해 다른 정의를 사용합니다. 어떤 페이지는 자신의 도메인은 물론, 공개 접두사가 아닌 모든 부모 도메인에 대해 쿠키를 설정할 수 있습니다. Firefox와 Chrome은 [Public Suffix List](https://publicsuffix.org/)를 사용해 도메인의 공개 접두사 여부를 판별합니다. Internet Explorer는 별도의 내부 방식을 사용합니다. 브라우저는 주어진 도메인 및 하위 도메인에 대한 쿠키 접근을 허용하며, 사용한 {{glossary("protocol", "프로토콜")}}(HTTP/HTTPS)과 {{glossary("port", "포트")}}는 검사하지 않습니다. 쿠키를 설정할 때 `Domain`, `Path`, `Secure`, `HttpOnly` 플래그를 사용해 쿠키 접근을 제한할 수 있습니다. 쿠키를 읽을 때, 쿠키를 설정한 장소는 볼 수 없습니다. 오로지 안전한 HTTPS 연결만 사용하더라도, 보이는 쿠키 중 어떠한 것이라도 안전하지 않은 연결에서 설정한 것일 수 있습니다.

## 같이 보기

- [W3C의 Same-Origin Policy](https://www.w3.org/Security/wiki/Same_Origin_Policy)
- [web.dev의 Same-origin policy](https://web.dev/secure/same-origin-policy)

## 원본 문서 정보

- 저자: Jesse Ruderman
