---
title: ASP.NET 코어에 대한 클라이언트 IP 보호 목록
author: damienbod
description: 미들웨어 또는 작업 필터를 작성하여 승인된 IP 주소 목록에 대해 원격 IP 주소의 유효성을 검사하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471806"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET 코어에 대한 클라이언트 IP 보호 목록

[데미안 보든과](https://twitter.com/damien_bod) [톰 다이크스트라](https://github.com/tdykstra)
 
이 문서에서는 ASP.NET 핵심 앱에서 IP 주소 허용 목록(허용 목록이라고도 함)을 구현하는 세 가지 방법을 보여 주십니다. 함께 제공되는 샘플 앱은 세 가지 방법을 모두 보여 줍니다. 다음을 사용할 수 있습니다.

* 미들웨어는 모든 요청의 원격 IP 주소를 확인합니다.
* MVC 작업 필터는 특정 컨트롤러 또는 작업 메서드에 대한 요청의 원격 IP 주소를 확인합니다.
* Razor 페이지 필터를 사용하여 Razor 페이지에 대한 요청의 원격 IP 주소를 확인합니다.

각각의 경우 승인된 클라이언트 IP 주소가 포함된 문자열이 앱 설정에 저장됩니다. 미들웨어 또는 필터:

* 문자열을 배열로 구문 분석합니다. 
* 원격 IP 주소가 어레이에 있는지 확인합니다.

배열에 IP 주소가 포함된 경우 액세스가 허용됩니다. 그렇지 않으면 HTTP 403 금지 상태 코드가 반환됩니다.

[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) [방법)](xref:index#how-to-download-a-sample)

## <a name="ip-address-safelist"></a>IP 주소 보호 목록

샘플 앱에서 IP 주소 보호 목록은 다음과 같은

* `AdminSafeList` *appsettings.json* 파일의 속성에 의해 정의됩니다.
* [인터넷 프로토콜 버전 4(IPv4)](https://wikipedia.org/wiki/IPv4) 및 인터넷 프로토콜 버전 [6(IPv6)](https://wikipedia.org/wiki/IPv6) 주소를 모두 포함할 수 있는 세미콜론 구분 문자열입니다.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

앞의 예제에서는 IPv4 주소 `127.0.0.1` 및 `192.168.1.5` IPv6 루프백 `::1` 주소(압축 형식용)가 `0:0:0:0:0:0:0:1`허용됩니다.

## <a name="middleware"></a>미들웨어

메서드는 `Startup.Configure` 앱의 `AdminSafeListMiddleware` 요청 파이프라인에 사용자 지정 미들웨어 형식을 추가합니다. 사용 가능한 목록은 .NET Core 구성 공급자와 함께 검색되고 생성자 매개 변수로 전달됩니다.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

미들웨어는 문자열을 배열로 구문 분석하고 배열의 원격 IP 주소를 검색합니다. 원격 IP 주소를 찾을 수 없는 경우 미들웨어는 HTTP 403 금지됨을 반환합니다. 이 유효성 검사 프로세스는 HTTP GET 요청에 대해 우회됩니다.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>작업 필터

특정 MVC 컨트롤러 또는 작업 메서드에 대한 safelist 기반 액세스 제어를 원하는 경우 작업 필터를 사용합니다. 다음은 그 예입니다.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

에서 `Startup.ConfigureServices`작업 필터를 MVC 필터 컬렉션에 추가합니다. 다음 예제에서는 작업 `ClientIpCheckActionFilter` 필터가 추가됩니다. 보호 목록 및 콘솔 로거 인스턴스는 생성자 매개 변수로 전달됩니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

그런 다음 [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) 특성을 사용하여 작업 필터를 컨트롤러 또는 작업 메서드에 적용할 수 있습니다.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

샘플 앱에서 작업 필터는 컨트롤러의 `Get` 작업 메서드에 적용됩니다. 다음을 전송하여 앱을 테스트할 때:

* HTTP GET 요청, `[ServiceFilter]` 특성은 클라이언트 IP 주소의 유효성을 검사합니다. 작업 메서드에 대한 `Get` 액세스가 허용되면 작업 필터 및 작업 메서드에 의해 다음 콘솔 출력의 변형이 생성됩니다.

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* `AdminSafeListMiddleware` GET 이외의 HTTP 요청 동사는 미들웨어가 클라이언트 IP 주소의 유효성을 검사합니다.

## <a name="razor-pages-filter"></a>면도기 페이지 필터

Razor 페이지 앱에 대한 세이프리스트 기반 액세스 제어를 하려면 Razor 페이지 필터를 사용합니다. 다음은 그 예입니다.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

에서 `Startup.ConfigureServices`MVC 필터 컬렉션에 추가하여 Razor 페이지 필터를 사용하도록 설정합니다. 다음 예제에서는 Razor `ClientIpCheckPageFilter` 페이지 필터가 추가됩니다. 보호 목록 및 콘솔 로거 인스턴스는 생성자 매개 변수로 전달됩니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

샘플 앱의 *색인* 면도기 페이지가 요청되면 Razor 페이지 필터는 클라이언트 IP 주소의 유효성을 검사합니다. 필터는 다음과 같은 콘솔 출력의 변형을 생성합니다.

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/middleware/index>
* [작업 필터](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
