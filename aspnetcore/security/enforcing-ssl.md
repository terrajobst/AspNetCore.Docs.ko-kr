---
title: ASP.NET Core에서 HTTPS 적용
author: rick-anderson
description: ASP.NET Core 웹 앱에 HTTPS/TLS를 요구 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: ab407436afb16687fa285a836b608ad2e6a4802f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894560"
---
# <a name="enforce-https-in-aspnet-core"></a>ASP.NET Core에서 HTTPS 적용

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서에서는 다음과 같은 내용을 살펴봅니다.

* 모든 요청에 대 한 HTTPS가 필요 합니다.
* 모든 HTTP 요청을 HTTPS로 리디렉션하는 방법.

API가 없습니다. 첫 번째 요청 시 중요 한 데이터를 보낸 클라이언트를 방지할 수 있습니다.

> [!WARNING]
> 수행할 **되지** 사용 하 여 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 중요 한 정보를 수신 하는 Web Api에서 합니다. `RequireHttpsAttribute` 브라우저는 HTTP에서 HTTPS로 리디렉션하 HTTP 상태 코드를 사용 합니다. API 클라이언트 이해 하지 못하거나 HTTP에서 HTTPS로 리디렉션 준수 될 수 있습니다. 이러한 클라이언트는 HTTP를 통해 정보를 보낼 수 있습니다. Web Api을 수행 해야합니다.
>
> * HTTP에서 수신 대기할 수 없습니다.
> * 상태 코드 400 (잘못 된 요청)를 사용 하 여 연결을 닫고 요청을 제공 하지 마십시오.

## <a name="require-https"></a>HTTPS 필요

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core는 프로덕션 웹 앱 호출을 좋습니다.

* HTTPS 리디렉션을 미들웨어 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) HTTP 요청을 HTTPS로 리디렉션할 수 있습니다.
* HSTS 미들웨어 ([UseHsts](#http-strict-transport-security-protocol-hsts)) 클라이언트로 HTTP 엄격한 전송 보안 프로토콜 (HSTS) 헤더를 보내도록 합니다.

> [!NOTE]
> 역방향 프록시 구성에 배포 된 앱 프록시 연결 보안 (HTTPS)을 처리할 수 있습니다. 프록시는 또한 HTTPS 간의 리디렉션으로 처리, 경우 HTTPS 리디렉션을 미들웨어를 사용할 필요가 없습니다. 프록시 서버도 HSTS 헤더를 작성을 처리 하는 경우 (예를 들어 [이상에서 IIS 10.0 (1709) 네이티브 HSTS 지원](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS 미들웨어는 앱에서 필요 하지 않습니다. 자세한 내용은 [옵트 아웃 HTTPS/HSTS의 프로젝트 생성 시](#opt-out-of-httpshsts-on-project-creation)합니다.

### <a name="usehttpsredirection"></a>UseHttpsRedirection

다음 코드 호출 `UseHttpsRedirection` 에 `Startup` 클래스:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

위의 강조 표시 된 코드:

* 기본값을 사용 하 여 [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* 기본값을 사용 하 여 [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) 의해 재정의 되지 않는 (null)은 `ASPNETCORE_HTTPS_PORT` 환경 변수 또는 [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)합니다.

영구 리디렉션 보다는 임시 리디렉션을 사용 하는 것이 좋습니다. 링크 캐싱을 개발 환경에서 불안정 한 동작이 발생할 수 있습니다. 비 개발 환경에서 앱이 영구 리디렉션 상태 코드를 전송 하려는 경우 참조를 [프로덕션 환경에서 영구적인 리디렉션을 구성](#configure-permanent-redirects-in-production) 섹션입니다. 사용 하는 것이 좋습니다 [HSTS](#http-strict-transport-security-protocol-hsts) 만 리소스를 보호 하는 클라이언트에 알릴 수 요청 (프로덕션)에 앱에 전송 해야 합니다.

### <a name="port-configuration"></a>포트 구성

포트를 안전 하지 않은 요청을 HTTPS로 리디렉션하는 미들웨어에 대 한 사용할 수 있어야 합니다. 사용 가능한 포트가 없는 경우:

* HTTPS로 리디렉션 발생 하지 않습니다.
* 미들웨어 "리디렉션에 대 한 https 포트를 확인 하지 못했습니다." 경고를 기록 합니다.

다음 방법 중 하나를 사용 하 여 HTTPS 포트를 지정 합니다.

* 설정할 [HttpsRedirectionOptions.HttpsPort](#options)합니다.
* 설정 된 `ASPNETCORE_HTTPS_PORT` 환경 변수 또는 [https_port 웹 호스트 구성 설정을](xref:fundamentals/host/web-host#https-port):

  **키**: `https_port`  
  **형식**: *string*  
  **기본값**: 기본값은 설정되지 않습니다.  
  **설정 방법**: `UseSetting`  
  **환경 변수**: `<PREFIX_>HTTPS_PORT` (접두사 `ASPNETCORE_` 사용 하는 경우는 [웹 호스트](xref:fundamentals/host/web-host).)

  구성 하는 경우는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 에서 `Program`:

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* 보안 체계를 사용 하 여 포트를 표시 합니다 `ASPNETCORE_URLS` 환경 변수입니다. 환경 변수는 서버를 구성합니다. 미들웨어는 HTTPS 포트를 통해 직접 검색 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>합니다. 역방향 프록시 배포에이 방법은 작동 하지 않습니다.
* 개발에서에 HTTPS URL을 설정 *launchsettings.json*합니다. IIS Express를 사용 하는 경우에 HTTPS를 사용 하도록 설정 합니다.
* 공용 edge 배포에 대 한 HTTPS URL 끝점을 구성 [Kestrel](xref:fundamentals/servers/kestrel) 서버 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) 서버. 만 **하나의 HTTPS 포트** 앱에서 사용 됩니다. 미들웨어를 통해 포트 검색 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>합니다.

> [!NOTE]
> 역방향 프록시 구성에서 앱 실행 될 때 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 사용할 수 없습니다. 이 섹션에서 설명한 다른 방법 중 하나를 사용 하 여 포트를 설정 합니다.

Kestrel 또는 HTTP.sys를에 지 서버는 공용으로 사용 하면 둘 다에서 수신 대기 하도록 Kestrel 또는 HTTP.sys를 구성 해야 합니다.

* 클라이언트가 리디렉션되는 위치 보안 포트 (일반적으로 프로덕션 및 개발에서 5001 443).
* 안전 하지 않은 포트 (일반적으로 프로덕션 환경에서 80) 및 개발에는 5000입니다.

안전 하지 않은 포트는 안전 하지 않은 요청을 받고 보안 포트에 클라이언트를 리디렉션할 클라이언트 앱에 대 한 순서에서 액세스할 수 있어야 합니다.

자세한 내용은 [Kestrel 끝점 구성을](xref:fundamentals/servers/kestrel#endpoint-configuration) 또는 <xref:fundamentals/servers/httpsys>합니다.

### <a name="deployment-scenarios"></a>배포 시나리오

클라이언트와 서버 간의 모든 방화벽 통신 포트가 트래픽에 대해 열려 있어야 합니다.

요청은 역방향 프록시 구성에서 전달 하는 경우 사용 하 여 [전달 된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer) HTTPS 리디렉션을 미들웨어를 호출 하기 전에 합니다. 헤더 미들웨어 업데이트를 전달 합니다 `Request.Scheme`를 사용 하 여는 `X-Forwarded-Proto` 헤더입니다. 미들웨어 허용 Uri 및 기타 보안 정책이 제대로 작동 하려면 리디렉션합니다. 전달 된 헤더 미들웨어를 사용 하지 않는 경우 백 엔드 앱 올바른 스키마 수신 및 리디렉션 루프가 하지 않을 수 있습니다. 일반적인 최종 사용자 오류 메시지 리디렉션이 너무 많습니다. 발생 한 경우

Azure App Service에 배포할 때의 지침에 따라 [자습서: 기존 사용자 지정 SSL 인증서를 Azure Web Apps에 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)을 참조하세요.

### <a name="options"></a>옵션

다음 코드 호출을 강조 표시 [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) 미들웨어 옵션을 구성 하려면:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

호출 `AddHttpsRedirection` 값을 변경 하는 데 필요한 전용인 `HttpsPort` 또는 `RedirectStatusCode`합니다.

위의 강조 표시 된 코드:

* 집합 [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) 에 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, 기본 값입니다. 필드를 사용 합니다 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스에 대 한 할당 `RedirectStatusCode`합니다.
* HTTPS 포트를 5001로 설정합니다. 기본값은 443입니다.

#### <a name="configure-permanent-redirects-in-production"></a>프로덕션 환경에서 영구 리디렉션 구성

미들웨어를 전송 하도록 기본값을 [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) 모든 리디렉션을 사용 하 여 합니다. 비 개발 환경에서 앱이 영구 리디렉션 상태 코드를 전송 하려는 경우 미들웨어 옵션 구성 된 비 개발 환경에 대 한 조건부 검사를 래핑하십시오.

구성 하는 경우는 `IWebHostBuilder` 에 *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a>HTTPS 리디렉션을 미들웨어에 대 한 대안 정보

HTTPS 리디렉션을 미들웨어를 사용 하는 대신 (`UseHttpsRedirection`) URL 재작성 미들웨어를 사용 하는 것 (`AddRedirectToHttps`). `AddRedirectToHttps` 리디렉션 실행 될 때 상태 코드 및 포트를 설정할 수도 있습니다. 자세한 내용은 [URL 재작성 미들웨어](xref:fundamentals/url-rewriting)합니다.

추가 리디렉션 규칙을 요구 하지 않고 HTTPS로 리디렉션, 하는 경우 HTTPS 리디렉션을 미들웨어를 사용 하는 것이 좋습니다 (`UseHttpsRedirection`)이이 항목에서 설명 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.1"

합니다 [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) HTTPS가 필요 하는 데 사용 됩니다. `[RequireHttpsAttribute]` 컨트롤러 또는 메서드를 데코레이팅 할 수 있습니다 또는 전체적으로 적용할 수 있습니다. 특성을 전역적으로 적용 하려면 다음 코드를 추가 합니다 `ConfigureServices` 에서 `Startup`:

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

위의 강조 표시된 코드는 모든 요청에 `HTTPS` 를 사용하도록 강제하며, 그 결과 HTTP 요청은 무시됩니다. 다음에 강조 표시된 코드는 모든 HTTP 요청을 HTTPS로 리디렉션합니다.

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

자세한 내용은 [URL 재작성 미들웨어](xref:fundamentals/url-rewriting)합니다. 또한 미들웨어 리디렉션 실행 될 때 상태 코드 또는 상태 코드와 포트를 설정 하려면 앱을 허용 합니다.

전역으로 HTTPS를 요구하는 것이 보안상 가장 안전한 모범 사례입니다 (`options.Filters.Add(new RequireHttpsAttribute());`). 적용 된 `[RequireHttps]` 모든 컨트롤러/Razor 페이지에는 특성에 전역적으로 HTTPS를 요구 하는 것 만큼 안전로 간주 되지 않습니다. 보장할 수 없습니다는 `[RequireHttps]` 새 컨트롤러 및 Razor 페이지는 추가 특성이 적용 됩니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="http-strict-transport-security-protocol-hsts"></a>HTTP 엄격한 전송 보안 프로토콜 (HSTS)

당 [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)하십시오 [HTTP 엄격한 전송 보안 (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) 응답 헤더를 사용 하 여 웹 앱에서 지정 하는 옵트인 보안 향상 된 기능입니다. 경우는 [HSTS를 지 원하는 브라우저](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) 이 헤더를 수신 합니다.

* 브라우저에는 모든 통신이 HTTP를 통해 보낼 수 없는 도메인에 대 한 구성을 저장 합니다. 브라우저 강제로 HTTPS를 통해 모든 통신을 수행합니다.
* 브라우저에서 신뢰할 수 없거나 잘못 된 인증서를 사용 하 여 사용자를 방지 합니다. 브라우저 사용자가 일시적으로 이러한 인증서를 신뢰 하는 프롬프트를 비활성화 합니다.

HSTS는 클라이언트에서 적용 하기 때문에 몇 가지 제한 사항이 있습니다.

* 클라이언트는 HSTS를 지원 해야 합니다.
* HSTS는 HSTS 정책을 설정 하려면 하나 이상의 성공적인 HTTPS 요청에 필요 합니다.
* 응용 프로그램이 모든 HTTP 요청을 확인 하 고 리디렉션 하거나 HTTP 요청을 거부 해야 합니다.

ASP.NET Core 2.1 이상을 사용 하 여 HSTS를 구현 합니다 `UseHsts` 확장 메서드. 다음 코드 호출 `UseHsts` 에 앱이 없는 경우 [개발 모드](xref:fundamentals/environments):

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

`UseHsts` 권장 되지 않습니다 개발에서 HSTS 설정이 항상 캐시할 수 있기 때문에 브라우저에서. 기본적으로 `UseHsts` 로컬 루프백 주소를 제외 합니다.

프로덕션 환경에 대 한 초기 설정 처음에 대 한 HTTPS를 구현 [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) 중 하나를 사용 하는 작은 값으로는 <xref:System.TimeSpan> 메서드. 값을 설정할 시간에서 전혀 보다 하루를 HTTP로 HTTPS 인프라를 되돌려야 하는 경우. HTTPS 구성의 유지 가능성에 확신한 후 늘릴 HSTS 최대 처리 기간 값 자주 사용 되는 값은 1 년입니다.

다음 예를 참조하십시오.

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* Strict-전송-보안 헤더의 미리 로드 매개 변수를 설정합니다. Preload의 일부가 아닌 합니다 [RFC HSTS 사양](https://tools.ietf.org/html/rfc6797), 있지만 HSTS 새로 설치할 때 사이트를 미리 로드 하려면 웹 브라우저에서 지원 됩니다. 자세한 내용은 [https://hstspreload.org/](https://hstspreload.org/)를 참조하세요.
* 사용 하도록 설정 [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), 호스트 하위 도메인에 HSTS 정책을 적용 합니다.
* 60 일로 Strict-전송-보안 헤더의 최대 처리 기간 매개 변수를 명시적으로 설정합니다. 설정 하지 않으면 기본값은 30 일입니다. 참조 된 [최대 처리 기간 지시문](https://tools.ietf.org/html/rfc6797#section-6.1.1) 자세한 내용은 합니다.
* 추가 `example.com` 를 제외 하는 호스트의 목록입니다.

`UseHsts` 다음 루프백 호스트를 제외:

* `localhost`은: IPv4 루프백 주소입니다.
* `127.0.0.1`은: IPv4 루프백 주소입니다.
* `[::1]`은: IPv6 루프백 주소입니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a>옵트아웃 HTTPS/HSTS 프로젝트 생성 시

연결 보안 공용 네트워크의에 지에서 처리 되는 일부 백 엔드 서비스 시나리오에서는 각 노드에 연결 보안 구성 필요 하지 않습니다. 웹 앱 또는 Visual Studio의 템플릿에서 생성 된 [새 dotnet](/dotnet/core/tools/dotnet-new) 명령 사용 [HTTPS 간의 리디렉션으로](#require-https) 및 [HSTS](#http-strict-transport-security-protocol-hsts)합니다. 이러한 시나리오를 필요로 하지 않는 배포에 대 한 있습니다 수 옵트아웃 HTTPS/HSTS 서식 파일에서 앱을 만들 때.

옵트아웃 하려면 HTTPS/HSTS입니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

선택 취소 합니다 **HTTPS에 대 한 구성** 확인란 합니다.

![새 ASP.NET Core 웹 응용 프로그램 대화 상자 표시 HTTPS 확인란의 선택을 취소에 대 한 구성입니다.](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli) 

`--no-https` 옵션을 사용합니다. 예

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Windows 및 macOS에서 ASP.NET Core HTTPS 개발 인증서를 신뢰 합니다.

.NET core SDK에는 HTTPS 개발 인증서를 포함합니다. 인증서는 첫 실행 경험의 일부로 설치 됩니다. 예를 들어 `dotnet --info` 다음과 유사한 출력을 생성 합니다.

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

.NET Core SDK를 설치하면 로컬 사용자 인증서 저장소에 ASP.NET Core HTTPS 개발 인증서가 설치됩니다. 인증서가 설치 되었지만 신뢰할 수 있는 합니다. 인증서 신뢰 dotnet 실행 하는 일회성 단계를 수행 `dev-certs` 도구:

```console
dotnet dev-certs https --trust
```

다음 명령은 `dev-certs` 도구에 대한 도움말을 제공합니다.

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Docker에 대 한 개발자 인증서를 설정 하는 방법

참조 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/6199)합니다.

::: moniker-end

## <a name="additional-information"></a>추가 정보

* <xref:host-and-deploy/proxy-load-balancer>
* [Apache 사용 하 여 Linux에서 ASP.NET Core를 호스트 합니다. HTTPS 구성](xref:host-and-deploy/linux-apache#https-configuration)
* [Nginx 사용 하 여 Linux에서 ASP.NET Core를 호스트 합니다. HTTPS 구성](xref:host-and-deploy/linux-nginx#https-configuration)
* [IIS에서 SSL 설정 하는 방법](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [OWASP HSTS 브라우저 지원](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
