---
title: ASP.NET Core Blazor에 대해 콘텐츠 보안 정책을 적용 합니다.
author: guardrex
description: ASP.NET Core Blazor 앱에서 CSP (콘텐츠 보안 정책)를 사용 하 여 XSS (사이트 간 스크립팅) 공격 으로부터 보호 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 1cfebf7b3d3bbb98a671b6f2db7c6518cda74b65
ms.sourcegitcommit: 51c86c003ab5436598dbc42f26ea4a83a795fd6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78964537"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor에 대해 콘텐츠 보안 정책을 적용 합니다.

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[XSS (교차 사이트 스크립팅)](xref:security/cross-site-scripting) 는 공격자가 하나 이상의 악의적인 클라이언트 쪽 스크립트를 앱의 렌더링 된 콘텐츠에 배치 하는 보안 취약점입니다. CSP (콘텐츠 보안 정책)는 브라우저에 유효한 지를 알리는 방식으로 XSS 공격 으로부터 보호 하는 데 도움이 됩니다.

* 스크립트, 스타일 시트 및 이미지를 포함 하 여 로드 된 콘텐츠의 원본입니다.
* 폼의 허용 되는 URL 대상을 지정 하 여 페이지에서 수행 하는 작업입니다.
* 로드할 수 있는 플러그 인입니다.

앱에 CSP를 적용 하기 위해 개발자는 하나 이상의 `Content-Security-Policy` 헤더 또는 `<meta>` 태그에 여러 CSP 콘텐츠 보안 *지시문* 을 지정 합니다.

페이지를 로드 하는 동안 브라우저에서 정책을 평가 합니다. 브라우저는 페이지의 소스를 검사 하 여 콘텐츠 보안 지시문의 요구 사항을 충족 하는지 확인 합니다. 리소스에 대해 정책 지시문이 충족 되지 않으면 브라우저에서 리소스를 로드 하지 않습니다. 예를 들어 타사 스크립트를 허용 하지 않는 정책을 고려해 보세요. 페이지에 `src` 특성의 타사 원본을 포함 하는 `<script>` 태그가 포함 되어 있으면 브라우저에서 스크립트가 로드 되지 않도록 방지 합니다.

CSP는 Chrome, Edge, Firefox, Opera 및 Safari를 비롯 한 대부분의 최신 데스크톱 및 모바일 브라우저에서 지원 됩니다. CSP는 Blazor 앱에 권장 됩니다.

## <a name="policy-directives"></a>정책 지시문

최소한 Blazor 앱에 대해 다음 지시문 및 소스를 지정 합니다. 필요에 따라 추가 지시문 및 소스를 추가 합니다. 다음 지시문은이 문서의 [정책 적용](#apply-the-policy) 섹션에 사용 됩니다. 여기에서 Blazor Weasembmboma 및 Blazor Server에 대 한 보안 정책 예제를 제공 합니다.

* [기본 uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; 페이지의 `<base>` 태그에 대 한 Url을 제한 합니다. 체계 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면 `self`를 지정 합니다.
* [블록 간-혼합 콘텐츠](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; 혼합 HTTP 및 HTTPS 콘텐츠 로드를 방지 합니다.
* [기본-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; 정책에 의해 명시적으로 지정 되지 않은 소스 지시문에 대 한 대체 (fallback)를 나타냅니다. 체계 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면 `self`를 지정 합니다.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash;는 이미지에 대 한 유효한 소스를 나타냅니다.
  * `data:` Url에서 이미지를 로드할 수 있도록 허용 하려면 `data:`를 지정 합니다.
  * HTTPS 끝점에서 이미지를 로드할 수 있도록 허용 하려면 `https:`를 지정 합니다.
* [개체-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; `<object>`, `<embed>`및 `<applet>` 태그에 대해 유효한 소스를 나타냅니다. 모든 URL 원본을 방지 하려면 `none`를 지정 합니다.
* [스크립트-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; 스크립트의 유효한 원본을 나타냅니다.
  * 부트스트랩 스크립트의 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정 하십시오.
  * 체계 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면 `self`를 지정 합니다.
  * Blazor Weasembomapp에서:
    * 다음 해시를 지정 하 여 필요한 Blazor Weasembomin인라인 스크립트를 로드 하도록 허용 합니다.
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * 문자열에서 코드를 만드는 데 `eval()` 및 메서드를 사용할 `unsafe-eval`를 지정 합니다.
  * Blazor Server 앱에서 스타일 시트에 대 한 대체 (fallback) 검색을 수행 하는 인라인 스크립트에 대 한 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 해시를 지정 합니다.
* [스타일-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; 스타일 시트의 유효한 원본을 나타냅니다.
  * 부트스트랩 스타일 시트에 대 한 `https://stackpath.bootstrapcdn.com/` 호스트 원본을 지정 합니다.
  * 체계 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면 `self`를 지정 합니다.
  * 인라인 스타일을 사용할 수 있도록 `unsafe-inline` 지정 합니다. 인라인 선언은 초기 요청 후 클라이언트 및 서버를 다시 연결 하는 Blazor Server 앱의 UI에 필요 합니다. 이후 릴리스에서는 `unsafe-inline` 더 이상 필요 하지 않도록 인라인 스타일이 제거 될 수 있습니다.
* [업그레이드-안전](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) 하지 않은 요청 &ndash;는 안전 하지 않은 (HTTP) 원본의 콘텐츠 URL을 HTTPS를 통해 안전 하 게 가져와야 함을 나타냅니다.

이전 지시문은 Microsoft Internet Explorer를 제외한 모든 브라우저에서 지원 됩니다.

추가 인라인 스크립트에 대 한 SHA 해시를 얻으려면:

* [정책 적용](#apply-the-policy) 섹션에 표시 된 CSP를 적용 합니다.
* 응용 프로그램을 로컬로 실행 하는 동안 브라우저의 개발자 도구 콘솔에 액세스 합니다. 브라우저는 CSP 헤더 또는 `meta` 태그가 있는 경우 차단 된 스크립트에 대 한 해시를 계산 하 고 표시 합니다.
* 브라우저에서 제공 하는 해시를 `script-src` 소스에 복사 합니다. 각 해시 주위에 작은따옴표를 사용 합니다.

콘텐츠 보안 정책 수준 2 브라우저 지원 매트릭스는 [사용 가능: 콘텐츠 보안 정책 수준 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)를 참조 하세요.

## <a name="apply-the-policy"></a>정책 적용

`<meta>` 태그를 사용 하 여 정책을 적용 합니다.

* `http-equiv` 특성의 값을 `Content-Security-Policy`로 설정 합니다.
* `content` 특성 값에 지시문을 추가 합니다. 지시문을 세미콜론 (`;`)으로 구분 합니다.
* `meta` 태그는 항상 `<head>` 내용에 저장 합니다.

다음 섹션에서는 Blazor Weasembmbambmbsemboma 및 Blazor Server에 대 한 예제 정책을 보여 줍니다. 이러한 예제는 각 Blazor릴리스에 대 한이 문서에 포함 되어 있습니다. 릴리스에 적절 한 버전을 사용 하려면이 웹 페이지의 **버전** 드롭다운 선택기를 사용 하 여 문서 버전을 선택 합니다.

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

*Wwwroot/index.html* 호스트 페이지의 `<head>` 내용에서 [정책 지시문](#policy-directives) 섹션에 설명 된 지시문을 적용 합니다.

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="opno-locblazor-server"></a>Blazor 서버

*Pages/_Host* 의 `<head>` 콘텐츠 페이지에서 [정책 지시문](#policy-directives) 섹션에 설명 된 지시문을 적용 합니다.

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Meta 태그 제한 사항

`<meta>` 태그 정책은 다음 지시문을 지원 하지 않습니다.

* [프레임-상위 항목](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [보고서-대상](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [샌드박스에서](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

위의 지시문을 지원 하려면 `Content-Security-Policy`라는 헤더를 사용 합니다. 지시문 문자열은 헤더의 값입니다.

## <a name="test-a-policy-and-receive-violation-reports"></a>정책 테스트 및 위반 보고서 받기

테스트를 통해 초기 정책을 빌드할 때 타사 스크립트가 실수로 차단 되지 않았는지 확인할 수 있습니다.

정책 지시문을 적용 하지 않고 일정 시간 동안 정책을 테스트 하려면 헤더 기반 정책의 `<meta>` 태그 `http-equiv` 특성 또는 헤더 이름을 `Content-Security-Policy-Report-Only`로 설정 합니다. 오류 보고서는 지정 된 URL에 JSON 문서로 전송 됩니다. 자세한 내용은 [MDN 웹 문서: 내용-보안-정책-보고서 전용](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)을 참조 하세요.

정책이 활성화 된 동안 위반에 대 한 보고는 다음 문서를 참조 하세요.

* [보고서-대상](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

`report-uri`는 더 이상 사용 하지 않는 것이 좋지만 모든 주요 브라우저에서 `report-to`를 지원할 때까지 두 지시문을 모두 사용 해야 합니다. `report-uri`에 대 한 지원이 브라우저에서 *언제 든* 지 삭제 될 수 있으므로 `report-uri` 독점적으로 사용 하지 마세요. `report-to` 완전 하 게 지원 되는 경우 정책에서 `report-uri`에 대 한 지원을 제거 합니다. `report-to`의 채택을 추적 하려면 [사용 가능: 보고 대상](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)을 참조 하세요.

릴리스 마다 앱 정책을 테스트 하 고 업데이트 합니다.

## <a name="troubleshoot"></a>문제 해결

* 브라우저의 개발자 도구 콘솔에 오류가 표시 됩니다. 브라우저는 다음에 대 한 정보를 제공 합니다.
  * 정책을 준수 하지 않는 요소입니다.
  * 차단 된 항목을 허용 하도록 정책을 수정 하는 방법입니다.
* 정책은 클라이언트의 브라우저에서 포함 된 모든 지시문을 지 원하는 경우에만 완전히 적용 됩니다. 현재 브라우저 지원 매트릭스는 [사용 가능: 내용-보안 정책](https://caniuse.com/#search=Content-Security-Policy)을 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [MDN 웹 문서: 콘텐츠-보안-정책](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [콘텐츠 보안 정책 수준 2](https://www.w3.org/TR/CSP2/)
* [Google CSP 계산기](https://csp-evaluator.withgoogle.com/)
