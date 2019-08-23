---
title: ASP.NET Core에 대 한 클라이언트 IP safelist
author: damienbod
description: 승인 된 IP 주소 목록에 대해 원격 IP 주소의 유효성을 검사 하는 미들웨어 또는 작업 필터를 작성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: 02e44135ab1742d44691cfda8c4167f21d6efa4e
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975640"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core에 대 한 클라이언트 IP safelist

[Damien Bowden](https://twitter.com/damien_bod) 및 [Tom Dykstra](https://github.com/tdykstra)
 
이 문서에서는 ASP.NET Core 앱에서 IP safelist (허용 목록 라고도 함)를 구현 하는 세 가지 방법을 보여 줍니다. 다음을 사용할 수 있습니다.

* 미들웨어를 통해 모든 요청의 원격 IP 주소를 확인 합니다.
* 작업 필터는 특정 컨트롤러 또는 작업 메서드에 대 한 요청의 원격 IP 주소를 확인 합니다.
* Razor Pages 필터는 Razor 페이지에 대 한 요청의 원격 IP 주소를 확인 합니다.

각각의 경우 승인 된 클라이언트 IP 주소를 포함 하는 문자열은 앱 설정에 저장 됩니다. 미들웨어 또는 필터는 문자열을 목록으로 구문 분석 하 고, 원격 IP가 목록에 있는지 확인 합니다. 그렇지 않으면 HTTP 403 사용할 수 없음 상태 코드가 반환 됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="the-safelist"></a>Safelist

이 목록은 *appsettings* 파일에 구성 되어 있습니다. 세미콜론으로 구분 된 목록이 며 IPv4 및 IPv6 주소를 포함할 수 있습니다.

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>미들웨어

메서드 `Configure` 는 미들웨어를 추가 하 고 생성자 매개 변수에 safelist 문자열을 전달 합니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

미들웨어는 문자열을 배열로 구문 분석 하 고 배열에서 원격 IP 주소를 찾습니다. 원격 IP 주소를 찾을 수 없는 경우 미들웨어는 HTTP 401을 사용할 수 없음으로 반환 합니다. 이 유효성 검사 프로세스는 HTTP Get 요청에 대해 무시 됩니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>작업 필터

특정 컨트롤러 또는 작업 메서드에만 safelist을 사용 하려면 작업 필터를 사용 합니다. 예를 들면 다음과 같습니다. 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

작업 필터가 서비스 컨테이너에 추가 됩니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

그러면 컨트롤러 또는 작업 메서드에서 필터를 사용할 수 있습니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

샘플 앱에서 필터는 `Get` 메서드에 적용 됩니다. 따라서 `Get` API 요청을 전송 하 여 앱을 테스트 하는 경우 특성은 클라이언트 IP 주소의 유효성을 검사 합니다. 다른 HTTP 메서드를 사용 하 여 API를 호출 하 여 테스트 하는 경우 미들웨어는 클라이언트 IP의 유효성을 검사 합니다.

## <a name="razor-pages-filter"></a>Razor Pages 필터 

Razor Pages 앱에 대 한 safelist을 사용 하려면 Razor Pages 필터를 사용 합니다. 예를 들면 다음과 같습니다. 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

이 필터는 MVC Filters 컬렉션에 추가 하 여 사용할 수 있습니다.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

앱을 실행 하 고 Razor 페이지를 요청 하면 Razor Pages 필터에서 클라이언트 IP의 유효성을 검사 합니다.

## <a name="next-steps"></a>다음 단계

[ASP.NET Core 미들웨어에 대해 자세히 알아보세요](xref:fundamentals/middleware/index).
