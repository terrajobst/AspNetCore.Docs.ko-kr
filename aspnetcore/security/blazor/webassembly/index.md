---
title: ASP.NET Core Blazor WebAssembly 보안
author: guardrex
description: Blazor WebAssemlby 앱을 SPA(단일 페이지 애플리케이션)로 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: 652d4c61110f786396d9d5af4f131b817c40e333
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219248"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly 보안

작성자: [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor WebAssembly 앱은 SPA(단일 페이지 애플리케이션)와 동일한 방식으로 보호됩니다. 사용자를 SPA에 인증하는 여러 가지 방법이 있지만, [OIDC(Open ID Connect)](https://openid.net/connect/)와 같은 [oAuth 2.0 프로토콜](https://oauth.net/)기반의 구현이 가장 일반적이고 포괄적으로 사용하는 방법입니다.

## <a name="authentication-library"></a>인증 라이브러리

Blazor WebAssembly는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 라이브러리를 통해 OIDC를 사용한 앱 인증 및 권한 부여를 지원합니다. 이 라이브러리는 ASP.NET Core 백 엔드에 대해 원활하게 인증하기 위한 기본 형식 집합을 제공합니다. 이 라이브러리는 [ID 서버](https://identityserver.io/) 위에 빌드된 API 권한 부여 지원과 ASP.NET Core ID를 통합합니다. 이 라이브러리는 OP(OpenID 공급자)라고 하는 OIDC를 지원하는 타사 IP(ID 공급자)에 대해 인증할 수 있습니다.

Blazor WebAssembly의 인증 지원은 기본 인증 프로토콜 세부 정보를 처리하는 데 사용되는 *oidc-client.js* 라이브러리 위에 빌드됩니다.

SameSite 쿠키를 사용하는 것과 같이 SPA를 인증하기 위한 다른 옵션이 있습니다. 그러나 Blazor WebAssembly의 엔지니어링 설계에서는 oAuth 및 OIDC가 Blazor WebAssembly 앱의 인증을 위한 최고의 옵션으로 자리 잡았습니다. [JWT(JSON Web Token)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 기반의 [토큰 기반 인증](xref:security/anti-request-forgery#token-based-authentication)이 기능 및 보안상의 이유로 [쿠키 기반 인증](xref:security/anti-request-forgery#cookie-based-authentication) 대신 선택되었습니다.

* 일부 요청에서는 토큰이 전송되지 않으므로 토큰 기반 프로토콜을 사용하면 공격 노출 영역이 적어집니다.
* 토큰은 명시적으로 전송되므로 [CSRF(교차 사이트 요청 위조)](xref:security/anti-request-forgery)로부터 서버 엔드포인트를 보호할 필요가 없습니다. 따라서 Blazor WebAssembly 앱을 MVC 또는 Razor 페이지 앱과 함께 호스트할 수 있습니다.
* 토큰은 쿠키보다 사용 권한이 더 좁습니다. 예를 들어 토큰을 사용하여 사용자 계정을 관리하거나 사용자 암호를 변경할 수 없습니다. 이러한 작업은 해당 기능이 명시적으로 구현된 경우에만 가능합니다.
* 토큰은 수명이 짧아서 기본적으로 1시간이므로 공격 기간이 제한됩니다. 또한 토큰은 언제든지 해지할 수 있습니다.
* 자체 포함 JWT는 클라이언트와 서버에 인증 프로세스를 보증합니다. 예를 들어 클라이언트에게는 수신한 토큰이 적합하고 주어진 인증 프로세스의 일부로 내보내 졌는지 검색하고 확인할 수단이 있습니다. 타사가 인증 프로세스 중에 토큰을 바꾸려고 하는 경우 클라이언트는 바뀐 토큰을 감지하고 사용하지 않을 수 있습니다.
* OAuth 및 OIDC를 사용하는 토큰은 사용자 에이전트가 제대로 동작하지 않아도 앱의 보안을 보장합니다.
* OAuth 및 OIDC와 같은 토큰 기반 프로토콜을 사용하면 호스트형 앱과 독립 실행형 앱을 동일한 보안 특성 집합을 사용하여 인증하고 권한 부여할 수 있습니다.

## <a name="authentication-process-with-oidc"></a>OIDC를 사용하는 인증 프로세스

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` 라이브러리는 OIDC를 사용하여 인증 및 권한 부여를 구현하는 여러 가지 기본 형식을 제공합니다. 대체로 인증은 다음과 같이 작동합니다.

* 익명 사용자가 로그인 단추를 선택하거나 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성이 적용된 페이지를 요청하면 앱의 로그인 페이지(`/authentication/login`)로 리디렉션됩니다.
* 로그인 페이지에서 인증 라이브러리는 권한 부여 엔드포인트로의 리디렉션을 준비합니다. 권한 부여 엔드포인트는 Blazor WebAssembly 앱 외부에 있으며 별도의 원본에 호스트될 수 있습니다. 엔드포인트는 사용자가 인증되었는지 여부를 확인하고 응답으로 하나 이상의 토큰을 발급합니다. 인증 라이브러리는 인증 응답을 받기 위한 로그인 콜백을 제공합니다.
  * 사용자가 인증되지 않은 경우 사용자는 기본 인증 시스템, 일반적으로 ASP.NET Core ID로 리디렉션됩니다.
  * 사용자가 이미 인증된 경우에는 권한 부여 엔드포인트에서 적절한 토큰을 생성하고 브라우저를 다시 로그인 콜백 엔드포인트(`/authentication/login-callback`)로 리디렉션합니다.
* Blazor WebAssembly 앱이 로그인 콜백 엔드포인트(`/authentication/login-callback`)를 로드하면 인증 응답이 처리됩니다.
  * 인증 프로세스가 완료되면 사용자는 인증되고 필요에 따라 사용자가 요청한 원래의 보호된 URL로 다시 돌아갑니다.
  * 어떤 이유로든 인증 프로세스가 실패하는 경우 사용자는 로그인 실패 페이지(`/authentication/login-failed`)로 보내지고 오류가 표시됩니다.
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>호스트된 앱 및 타사 로그인 공급자에 대한 옵션

타사 공급자를 사용하여 호스트된 Blazor WebAssembly 앱을 인증하고 권한을 부여하는 경우 사용자를 인증하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 시나리오에 따라 선택하는 옵션이 달라집니다.

자세한 내용은 <xref:security/authentication/social/additional-claims>를 참조하세요.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>보호된 타사 API만 호출하도록 사용자 인증

타사 API 공급자에 대해 클라이언트 쪽 OAuth 흐름을 사용하여 사용자를 인증합니다.

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 이 시나리오에서는

* 앱을 호스트하는 서버에서 역할을 재생하지 않습니다.
* 서버의 API는 보호할 수 없습니다.
* 앱은 보호된 타사 API만 호출할 수 있습니다.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>타사 공급자를 사용하여 사용자를 인증하고 호스트 서버 및 타사에서 보호된 API 호출

타사 로그인 공급자를 사용하여 ID를 구성합니다. 타사 API 액세스에 필요한 토큰을 가져와 저장합니다.

사용자가 로그인하면 ID는 인증 프로세스의 일부로 액세스 및 새로 고침 토큰을 수집합니다. 이때 타사 API에 대한 API 호출을 수행하는 데 사용할 수 있는 몇 가지 방법이 있습니다.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>서버 액세스 토큰을 사용하여 타사 액세스 토큰 검색

서버에서 생성된 액세스 토큰을 사용하여 서버 API 엔드포인트에서 타사 액세스 토큰을 검색합니다. 여기에서 타사 액세스 토큰을 사용하여 클라이언트의 ID에서 직접 타사 API 리소스를 호출합니다.

이 방법은 사용하지 않는 것이 좋습니다. 이 방법을 사용하려면 퍼블릭 클라이언트에 대해 생성된 것처럼 타사 액세스 토큰을 처리해야 합니다. OAuth 용어에서 퍼블릭 앱은 비밀을 안전하게 저장하는 데 신뢰할 수 없으며, 기밀 클라이언트에 대해 액세스 토큰이 생성되므로 클라이언트 암호를 포함하지 않습니다. 기밀 클라이언트는 클라이언트 암호를 포함하는 클라이언트이며 비밀을 안전하게 저장할 수 있는 것으로 간주됩니다.

* 타사 액세스 토큰에는 타사에서 더 신뢰할 수 있는 클라이언트에 대한 토큰을 내보낸 사실을 기반으로 중요한 작업을 수행하기 위한 추가 범위가 부여될 수 있습니다.
* 마찬가지로, 신뢰할 수 없는 클라이언트에 대해서는 새로 고침 토큰을 발급하지 않아야 합니다. 발급할 경우, 다른 제한이 적용되지 않는 한 클라이언트에 무제한 액세스 권한이 제공됩니다.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>타사 API를 호출하기 위해 클라이언트에서 서버 API로 API 호출 수행

클라이언트에서 서버 API로 API 호출을 수행합니다. 서버에서 타사 API 리소스에 대한 액세스 토큰을 검색하고 필요한 호출이 무엇이든 해당 호출을 실행합니다.

이 방법을 사용하려면 타사 API를 호출하기 위해 서버를 통한 추가 네트워크 홉이 필요하지만, 궁극적으로 다음과 같은 더 안전한 환경이 생성됩니다.

* 서버는 새로 고침 토큰을 저장하고 앱이 타사 리소스에 대한 액세스 권한을 잃지 않도록 보장할 수 있습니다.
* 앱은 더 중요한 권한을 포함할 수 있는 서버의 액세스 토큰을 누출할 수 없습니다.
